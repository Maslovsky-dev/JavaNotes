---
tags: ORM
---
Основная цель JPA Entity Graph - улучшить производительность приложения в runtime когда загружаются связанные сущности.

Поставщик JPA загружает все графы в один SELECT запрос, а затем избегает извлечения ассоциаций с большим количеством выбранных запросов. Это считается хорошим подходом для повышения производительности приложений.

>[!note]
>Обратите внимание, что поскольку использование LAZY не является обязательным требованием, провайдер персистентности по-прежнему может жадно загружать сущность Post, если он этого хочет. Таким образом, для правильного использования этой стратегии следует вернуться к официальной документации соответствующего провайдера сохранения.

Теперь, поскольку мы использовали аннотации для описания нашей стратегии извлечения данных, наше определение является **статичным**, и нет способа переключаться между LAZY и EAGER во время выполнения.

И вот здесь на сцену выходит Entity Graph

## Определение графа сущностей
https://www.baeldung.com/jpa-entity-graph#defining-an-entity-graph

Чтобы определить Entity Graph, мы можем либо использовать **аннотации** к сущности, либо действовать программно, используя JPA API.

### Определение диаграммы сущностей с аннотациями[](https://www.baeldung.com/jpa-entity-graph#1-defining-an-entity-graph-with-annotations)

**Аннотация @_NamedEntityGraph_  позволяет указать атрибуты, которые нужно включить, когда мы хотим загрузить сущность и связанные ассоциации.**

Итак, давайте сначала определим Entity Graph, который загружает  _Post_ и связанные с ним сущности  _User_ и _Comment_ s:

```java
@NamedEntityGraph(
  name = "post-entity-graph",
  attributeNodes = {
    @NamedAttributeNode("subject"),
    @NamedAttributeNode("user"),
    @NamedAttributeNode("comments"),
  }
)
@Entity
public class Post {

    @OneToMany(mappedBy = "post")
    private List<Comment> comments = new ArrayList<>();
    
    //...
}
```

В этом примере мы использовали _@NamedAttributeNode_ для определения связанных объектов, которые будут загружаться при загрузке корневого объекта.

Давайте теперь определим более сложный Entity Graph, где мы также хотим загрузить _User_ , связанные с _Comment_ .

Для этой цели мы будем использовать атрибут подграфа _@NamedAttributeNode_  . **Это позволяет ссылаться на именованный подграф, определенный с помощью аннотации _@NamedSubgraph_ :**

```java
@NamedEntityGraph(
  name = "post-entity-graph-with-comment-users",
  attributeNodes = {
    @NamedAttributeNode("subject"),
    @NamedAttributeNode("user"),
    @NamedAttributeNode(value = "comments", subgraph = "comments-subgraph"),
  },
  subgraphs = {
    @NamedSubgraph(
      name = "comments-subgraph",
      attributeNodes = {
        @NamedAttributeNode("user")
      }
    )
  }
)
@Entity
public class Post {

    @OneToMany(mappedBy = "post")
    private List<Comment> comments = new ArrayList<>();
    //...
}
```

Определение аннотации  _@NamedSubgraph_ аналогично @NamedEntityGraph _и_  позволяет указать атрибуты связанной ассоциации. Таким образом, мы можем построить полный граф.

В приведенном выше примере с определенным графом « post-entity-graph _-_ _with-comment-users»_   мы можем загрузить сообщение _,_ связанного с ним _пользователя,_ комментарии  и  _пользователей_ , связанных с _комментариями._

Наконец, обратите внимание, что в качестве альтернативы мы можем добавить определение Entity Graph, используя дескриптор развертывания _orm.xml_ :

```xml
<entity-mappings>
  <entity class="com.baeldung.jpa.entitygraph.Post" name="Post">
    ...
    <named-entity-graph name="post-entity-graph">
            <named-attribute-node name="comments" />
    </named-entity-graph>
  </entity>
  ...
</entity-mappings>
```

### Определение Entity Graph с помощью JPA API[](https://www.baeldung.com/jpa-entity-graph#2-defining-an-entity-graph-with-the-jpa-api)

Мы также можем определить Entity Graph через _EntityManager_ API, вызвав метод _createEntityGraph()_ :

```java
EntityGraph<Post> entityGraph = entityManager.createEntityGraph(Post.class);
```

Чтобы указать атрибуты корневого объекта, мы используем метод _addAttributeNodes()_ .

```java
entityGraph.addAttributeNodes("subject");
entityGraph.addAttributeNodes("user");
```

Точно так же, чтобы включить атрибуты из связанной сущности, мы используем addSubgraph _()_ для построения встроенного графа сущностей, а затем используем addAttributeNodes  _()_  , как мы делали выше.

```java
entityGraph.addSubgraph("comments")
  .addAttributeNodes("user");
```

Теперь, когда мы увидели, как создать Entity Graph, мы рассмотрим, как его использовать в следующем разделе.

## Использование диаграммы сущностей[](https://www.baeldung.com/jpa-entity-graph#creating-entity-graph-1)

### Типы диаграмм сущностей[](https://www.baeldung.com/jpa-entity-graph#creating-entity-graph-2)

JPA определяет два свойства или подсказки, которые поставщик постоянства может выбрать для загрузки или извлечения Entity Graph во время выполнения:

- _jakarta.persistence.fetchgraph —_ из базы данных извлекаются только указанные атрибуты. **Поскольку в этом руководстве мы используем Hibernate, мы можем заметить, что, в отличие от спецификаций JPA, атрибуты, статически настроенные как _EAGER_ , также загружаются.**
- _jakarta.persistence.loadgraph —_ в дополнение к указанным атрибутам  также извлекаются атрибуты, статически сконфигурированные как _EAGER ._

**В любом случае первичный ключ и версия всегда загружаются.**

### Загрузка графа сущностей[](https://www.baeldung.com/jpa-entity-graph#creating-entity-graph-3)

Мы можем получить Entity Graph различными способами.

**Начнем с использования  метода _EntityManager.find_ ().** Как мы уже показали, режим по умолчанию основан на статических метастратегиях _FetchType.EAGER_ и _FetchType.LAZY_ .

Итак, давайте вызовем метод _find()_ и просмотрим журнал:

```java
Post post = entityManager.find(Post.class, 1L);
```

Вот журнал, предоставленный реализацией Hibernate:

```sql
select
    post0_.id as id1_1_0_,
    post0_.subject as subject2_1_0_,
    post0_.user_id as user_id3_1_0_ 
from
    Post post0_ 
where
    post0_.id=?
```

Как видно из лога, сущности _User_ и _Comment_ не загружаются.

Мы можем переопределить это поведение по умолчанию, вызвав перегруженный метод _find()_ ,который принимает подсказки как _карту._ Затем мы можем **указать тип графика, который мы хотим загрузить:**

```java
EntityGraph entityGraph = entityManager.getEntityGraph("post-entity-graph");
Map<String, Object> properties = new HashMap<>();
properties.put("jakarta.persistence.fetchgraph", entityGraph);
Post post = entityManager.find(Post.class, id, properties);
```

Если мы снова заглянем в лог, то увидим, что эти сущности теперь загружены и только в одном запросе на выборку:

```sql
select
    post0_.id as id1_1_0_,
    post0_.subject as subject2_1_0_,
    post0_.user_id as user_id3_1_0_,
    comments1_.post_id as post_id3_0_1_,
    comments1_.id as id1_0_1_,
    comments1_.id as id1_0_2_,
    comments1_.post_id as post_id3_0_2_,
    comments1_.reply as reply2_0_2_,
    comments1_.user_id as user_id4_0_2_,
    user2_.id as id1_2_3_,
    user2_.email as email2_2_3_,
    user2_.name as name3_2_3_ 
from
    Post post0_ 
left outer join
    Comment comments1_ 
        on post0_.id=comments1_.post_id 
left outer join
    User user2_ 
        on post0_.user_id=user2_.id 
where
    post0_.id=?
```

**Давайте посмотрим, как мы можем добиться того же, используя JPQL:**

```java
EntityGraph entityGraph = entityManager.getEntityGraph("post-entity-graph-with-comment-users");
Post post = entityManager.createQuery("select p from Post p where p.id = :id", Post.class)
  .setParameter("id", id)
  .setHint("jakarta.persistence.fetchgraph", entityGraph)
  .getSingleResult();
```

**И, наконец, давайте посмотрим на пример _Criteria_ API:**

```java
EntityGraph entityGraph = entityManager.getEntityGraph("post-entity-graph-with-comment-users");
CriteriaBuilder criteriaBuilder = entityManager.getCriteriaBuilder();
CriteriaQuery<Post> criteriaQuery = criteriaBuilder.createQuery(Post.class);
Root<Post> root = criteriaQuery.from(Post.class);
criteriaQuery.where(criteriaBuilder.equal(root.<Long>get("id"), id));
TypedQuery<Post> typedQuery = entityManager.createQuery(criteriaQuery);
typedQuery.setHint("jakarta.persistence.loadgraph", entityGraph);
Post post = typedQuery.getSingleResult();
```

В каждом из них **тип графика дается как подсказка** . В то время как в первом примере мы использовали _Map,_ в двух последующих примерах мы использовали метод _setHint()_.

## Пример использования

Предположим, у нас есть две сущности: `Author` и `Book`. Каждая сущность имеет связь One-to-Many, где один автор может быть связан с несколькими книгами.
```java
@Entity
public class Author {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;

    @OneToMany(mappedBy = "author")
    private List<Book> books;
    
    // геттеры, сеттеры и другие методы
}

@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String title;
    
    @ManyToOne
    @JoinColumn(name = "author_id")
    private Author author;
    
    // геттеры, сеттеры и другие методы
}
```

Допустим, мы хотим выполнить запрос для загрузки автора и его книг с помощью Entity Graph. Создадим Entity Graph, указав, что хотим жадно загрузить ассоциированное поле `books`:
```java
import javax.persistence.*;

public class Main {
    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("my-persistence-unit");
        EntityManager em = emf.createEntityManager();

        EntityGraph<Author> graph = em.createEntityGraph(Author.class);
        graph.addAttributeNodes("books");

        Author author = em.find(Author.class, 1L, Collections.singletonMap("javax.persistence.loadgraph", graph));
        
        em.close();
        emf.close();
    }
}

```

В этом примере Entity Graph `graph` описывает, что при запросе автора с id 1, мы хотим жадно загрузить его книги. Затем мы передаем этот граф как параметр в метод `find`, чтобы выполнить запрос с указанным жадным загружаемым полем.
