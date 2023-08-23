---
tags: ORM
aliases: n+1
---
N+1 проблема в Hibernate состоит в том, в некоторых ситуациях один _HQL select_ преобразуется N+1 _SQL select_-ов. Это отрицательно влияет на производительность, поэтому такого поведения нужно избегать.
>[!info]
>Эти дополнительные SQL _select_-ы нужны для заполнения поля, ссылающегося на другую сущность(и).
Здесь N — количество объектов, возвращаемых **первым явным _select_-ом**. Для каждого из них надо заполнить поле, вот и получается еще N _select_-ов. (+ 1 это первый select)

Ниже рассмотрим пример, демонстрирующий, как легко наткнуться на N+1 проблему в Hibernate. И как этой проблемы избежать.

>[!info]
>Сразу скажу, что выбирать мы будем комментарии — _select * from comment_ — это один _select_. А так как каждый комментарий ссылается на топик, то этот запрос повлечет дополнительные N селектов _select * from topic where topic.id=?_, подтягивающие топик для каждого комментария, где N — количество комментариев, полученных в первом запросе. То есть всего N+1 (вместо одного) _select_.

## Модель (Сущности)
Пример простой. Как уже говорилось, нас есть комментарии. Каждый из них относится к какому-то то топику, то есть отношение ManyToOne:
Таблицы в базе
![[Проблема N+1 Select Hibernate-1.png]]

Класс Comment:
```java
@Data
@NoArgsConstructor
@Entity
public class Comment {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private long id;
    private String text;
    @ManyToOne//(fetch = FetchType.LAZY)
    private Topic topic;

    public Comment(String text){
        this.text=text;
    }
}
```
Класс Topic:
```java
@Data
@NoArgsConstructor
@Entity
public class Topic {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private long id;
    private String title;
    public Topic(String title){
        this.title=title;
    }
}
```
Если не указать стратегию явно, для поля topic подразумевается стратегия fetch = FetchType.EAGER. (Эта стратегия считается стратегией по умолчанию для всех полей, аннотированных @ManyToOne). Это означает, что при выборе комментариев (*select c from Comment c*) Hibernate будет заполнять значением поле topic. Для этого он выполнит дополнительный select для каждого комментария. А значит, возникнет n+1 проблема.

Продемонстрируем это в тесте.

## Демонстрация N+1 проблемы

Для этого заполним базу пятью топиками с пятью комментариями (по одному на каждый) в методе _@BeforeAll_ — то есть перед всеми тестами мы заполнили базу.

А затем просто выполним _select_ для комментариев:

`select c from Comment c`

Заполнение и выборка представлены двумя методами: _@BeforeAll_, выполняющимся перед тестом, и самим тестом _@Test_:

```java
public class NPlus1Test {

    @BeforeAll
    private static void createTopics() {
        HibernateUtil.doInHibernate(session -> {
            for (int i = 0; i < 5; i++) {
                Topic topic = new Topic("topic" + i);
                Comment comment = new Comment("comment" + i);
                comment.setTopic(topic);
                session.persist(comment);
                session.persist(topic);
            }
        });
    }

    @Test
    @DisplayName("если fetch = FetchType.EAGER, то получаем в консоли N+1 select: 5+1")
    public void whenEager_thenNplus1Problem() {
        HibernateUtil.doInHibernate(session -> {
            Query<Comment> query = session.createQuery("select c from Comment c");

            List<Comment> comments = query.getResultList();
            Assertions.assertEquals(5, comments.size());
            comments.forEach(comment -> System.out.println(comment.getText() + " " + comment.getTopic().getTitle()));

        });
    }
}
```

В консоли для самого теста получим

```
Hibernate: select comment0_.id as id1_0_, comment0_.text as text2_0_, comment0_.topic_id as topic_id3_0_ from Comment comment0_

Hibernate: select topic0_.id as id1_1_0_, topic0_.title as title2_1_0_ from Topic topic0_ where topic0_.id=?
Hibernate: select topic0_.id as id1_1_0_, topic0_.title as title2_1_0_ from Topic topic0_ where topic0_.id=?
Hibernate: select topic0_.id as id1_1_0_, topic0_.title as title2_1_0_ from Topic topic0_ where topic0_.id=?
Hibernate: select topic0_.id as id1_1_0_, topic0_.title as title2_1_0_ from Topic topic0_ where topic0_.id=?
Hibernate: select topic0_.id as id1_1_0_, topic0_.title as title2_1_0_ from Topic topic0_ where topic0_.id=?

comment0 topic0
comment1 topic1
comment2 topic2
comment3 topic3
comment4 topic4
```

То есть мы написали один _select_, а в консоли видно, что на самом деле их выполняется 6. В этом и состоит проблема.

Происходит так потому, что для каждого комментария (а их выбралось пять в первом _select_-е) Hibernate под капотом выполняет дополнительный _select_ для заполнения поля _Topic_. То есть делает _select_ из _Topic_. В конце для наглядности мы  выводим значения _comment.text_ и _comment.topic.title._

Встает вопрос, как избавиться от проблемы: как сократить количество неожиданных _select_-ов, либо вовсе их убрать?

### Может поставить  _fetch = FetchType.LAZY?_

Если сменить для поля _topic_ сущности _Comment_ стратегию извлечения, то есть поставить над полем аннотацию _fetch = FetchType.LAZY_ (ленивое извлечение), то _сразу_ после выполнения запроса — то есть после

`query.getResultList())`

поле _Topic_ не заполняется и соответствующий _select … from topic_  не выполняется. То есть _сразу_ N+1 проблема не возникнет.

Но когда значение поля _Topic_ нам понадобится (а рано или поздно наверняка понадобится), и мы к нему обратимся с помощью:

`comment.getTopic()`

дополнительный _select_ все же выполнится — просто это произойдет позже.

Так как же этого избежать? Есть выход — **_join fetch**._ С помощью него мы сделаем в одном запросе выборку из обеих таблиц сразу.

## Join Fetch

Для выбора комментариев напишем не простой _select_, а _select_ с _join fetch_, который преобразуется в SQL с _inner join._
``` java
@Test
@DisplayName("если пофиксить проблему с помощью  join fetch, то получаем в консоли один select")
public void whenJoinFetch_thenNoProblem() {
    HibernateUtil.doInHibernate(session -> {
        Query<Comment> query = session.createQuery("select c from Comment c join fetch c.topic t", Comment.class);

        List<Comment> comments = query.getResultList();
        Assertions.assertEquals(5, comments.size());
        comments.forEach(comment -> System.out.println(comment.getText() + " " + comment.getTopic().getTitle()));
    });
}
```
В консоли мы увидим только один sql запрос
```
Hibernate: select comment0_.id as id1_0_0_, topic1_.id as id1_1_1_, comment0_.text as text2_0_0_, comment0_.topic_id as topic_id3_0_0_, topic1_.title as title2_1_1_ from Comment comment0_ inner join Topic topic1_ on comment0_.topic_id=topic1_.id
```

и также заполненное поле topic:

```
comment0 topic0
comment1 topic1
comment2 topic2
comment3 topic3
comment4 topic4
```

Чтобы sql-запросы отображались в консоли, в файле _hibernate.cfg.xml_ включена настройка:

`<property name="hibernate.show_sql">true</property>`

## EntityGraph
[[Entity Graph]] - позволяет улучшить производительность во время выполнения запросов к базе данных при загрузке связанных ассоциаций и основных полей объекта. JPA Entity Graph загружает данные в один запрос выбора, избегая повторного обращения к базе данных. Это считается хорошим подходом для повышения производительности приложений.

**Вариант 1**. Пишем аннотацию[@EntityGraph](https://habr.com/users/EntityGraph) над методом findByFullNameContaining в ClientRepository.

```java
public interface ClientRepository extends JpaRepository<Client, Long> {
    @EntityGraph(type = EntityGraph.EntityGraphType.FETCH, attributePaths = "emailAddresses")
    List<Client> findByFullNameContaining(String name);
}
```
По умолчания @EntityGraph имеет тип **_EntityGraphType.FETCH_** но, для того чтобы понимать, что происходит я его указываю, и он применяет стратегию _FetchType.EAGER_ к указанным атрибутам, то есть к emailAddresses.

**Вариант 2.** Пишем аннотацию @NamedEntityGraph над классом Client.

```java
@Data
@NoArgsConstructor
@Entity
@Table(name = "client")
@NamedEntityGraph(name = "client_entity-graph", attributeNodes = @NamedAttributeNode("emailAddresses"))
public class Client {
    @Id
    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "full_name")
    private String fullName;

    @Column(name = "mobile_number")
    private String mobileNumber;

    @OneToMany(cascade = CascadeType.ALL, mappedBy = "client")
    private List<EmailAddress> emailAddresses;

    public Client(String fullName, String mobileNumber, List<EmailAddress> emailAddresses) {
        this.fullName = fullName;
        this.mobileNumber = mobileNumber;
        this.emailAddresses = emailAddresses;
    }
}
```

В данном случае также будет использоваться "жадная" загрузка указанной связной сущности emailAddresses.

Также необходимо исправить аннотацию над ClientRepository.

```java
public interface ClientRepository extends JpaRepository<Client, Long> {
    @EntityGraph(type = EntityGraph.EntityGraphType.FETCH, value = "client_entity-graph")
    List<Client> findByFullNameContaining(String name);
}
```

## Итоги

Также важно не увлечься _join fetch_, чтобы избежать [[Проблема декартова произведения Cartesian product problem в Hibernate|Cartesian product problem]], о которой речь идет в [следующей статье](https://sysout.ru/problema-dekartova-proizvedeniya-cartesian-product-problem/).
