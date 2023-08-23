---
tags: ORM
---
Если [предыдущая проблема](https://sysout.ru/n-1-problema-v-hibernate/)  извлечения данных с Hibernate была связана с большим количеством _select_, то на этот раз _select_ будет один. Но какой: если _select_ содержит два и более _join_, это приводит к выборке огромного количества лишних данных, которые передаются по сети, занимают оперативную память. Что также отрицательно сказывается на производительности. И таких _join_-ов тоже надо избегать. Ниже рассмотрим пример.

## Когда возникает проблема

**Проблема возникает при наличии нескольких join в одном select, что приводит к выборке лишнего количества данных.**

Столкнуться с проблемой можно как явно (просто написав Query с несколькими _join_), так и неявно, выполнив _find()_ для сущности с EAGER-коллекциями (Hibernate при этом сгенерирует SQL с несколькими _join_).

В обоих случаях с точки зрения объектной модели мы хотим получить сущность вместе с заполненными полями коллекций.

## Модель

Допустим, у нас есть пост с двумя коллекциями — тегов и картинок:
```java
@Data
@NoArgsConstructor
@Entity
public class Post {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private long id;

    private String title;

    @OneToMany(mappedBy = "post", cascade = CascadeType.ALL)
    private List<Image> images=new ArrayList<>();

    @ElementCollection
    private Set<String> tags = new HashSet<>();

    public Post(String title){
        this.title=title;
    }

    public void addImage(Image image){
        image.setPost(this);
        this.images.add(image);
    }
}
```
Коллекция тегов — _@ElementCollection_, коллекция картинок — двунаправленной отношение _@OneToMany_. На факт возникновения проблемы вид коллекции не влияет (и отношение _@ManyToMany_ тоже способно вызвать проблему).

Класс картинки:
```java
@Data
@NoArgsConstructor
@Entity
public class Image {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private long id;

    private String url;
    @ManyToOne
    private Post post;

    public Image(String url){
        this.url=url;
    }
}
```

По умолчанию поля коллекций заполняются лениво:

`fetch = FetchType.LAZY`

И это правильно, иначе при выборке _Post_ мы столкнемся с [N+1 select проблемой](https://sysout.ru/n-1-problema-v-hibernate/).

Но как же получить обе коллекции и при этом избегнуть как N+1 проблему, так и _select_-а c двумя _join_ (Cartesian product problem)?

## Способ: несколько select с 1 join
**Избегать проблемы можно, выполняя несколько select-ов для каждой коллекции.**
Нужно выполнить вместо одного _select_ с несколькими _join_ (в нашем случае двумя) несколько _select_-ов (в нашем случае два — по одному на коллекцию), в каждом из которых ровно один _join_.

В первом _select_ — получим коллекцию картинок, а во втором — тегов.
```java
@Test
@DisplayName("если FetchType.LAZY и выполнить отдельные select для заполнения коллекций, то проблемы нет")
public void givneLazy_whenSelectCollectionsByOne_thenOk() {
    HibernateUtil.doInHibernate(session -> {
        List<Post> posts = session
                .createQuery(
                        "select distinct p " +
                                "from Post p " +
                                "left join fetch p.images " +
                                "where p.id between :minId and :maxId ", Post.class)
                .setParameter("minId", 1L)
                .setParameter("maxId", 1L)
                .setHint(QueryHints.PASS_DISTINCT_THROUGH, false)
                .getResultList();

        posts = session
                .createQuery(
                        "select distinct p " +
                                "from Post p " +
                                "left join fetch p.tags t " +
                                "where p in :posts ", Post.class)
                .setParameter("posts", posts)
                .setHint(QueryHints.PASS_DISTINCT_THROUGH, false)
                .getResultList();
        
    });
```
В итоге поля коллекций будут заполнены.
>[!info]
>_distinct p_ использован для того, чтобы итоговый _List<`Post`>_ не содержал дублирующихся постов. При этом _.setHint(QueryHints.PASS_DISTINCT_THROUGH, false)_ не дает передаться ключевому слову _distinct_ в native SQL — он там не нужен, поскольку выбранные строки в любом случае будут все разные. Подробнее о _distinct_ [тут](https://sysout.ru/jpql-distinct-primer-na-hibernate-i-spring-boot/).

После выполнения тесты в консоли мы увидим два _select_, в каждом из которых по одному _join_:
```sql
select post0_.id as id1_1_0_, images1_.id as id1_0_1_, 
post0_.title as title2_1_0_, images1_.post_id as post_id3_0_1_, 
images1_.url as url2_0_1_, images1_.post_id as post_id3_0_0__, images1_.id as id1_0_0__ 
from Post post0_ 
left outer join Image images1_ on post0_.id=images1_.post_id 
where post0_.id between ? and ?
---------------------------------------------
select post0_.id as id1_1_, post0_.title as title2_1_, 
tags1_.Post_id as Post_id1_2_0__, tags1_.tags as tags2_2_0__ 
from Post post0_ 
left outer join Post_tags tags1_ on post0_.id=tags1_.Post_id 
where post0_.id in (?)
```
## Способ: _FetchMode.SUBSELECT_

И рассмотрим еще один способ заполнить коллекции постов, при этом избежав проблемы как декартова произведения, так и N+1.

Для этого аннотируем обе коллекции с _@Fetch(FetchMode.SUBSELECT)_:

```java
@OneToMany(mappedBy = "post", cascade = CascadeType.ALL)
@Fetch(FetchMode.SUBSELECT)
private List<Image> images = new ArrayList<>();

@ElementCollection
@Fetch(FetchMode.SUBSELECT)
private Set<String> tags = new HashSet<>();
```

Выполним тест и получим три SQL-запроса в консоли:

```java
@Test
@DisplayName("если поставить org.hibernate.annotations.FetchMode.SUBSELECT, и сделать обычный select, то тоже пофиксим проблему")
public void givenSubselect_whenSimpleSelect_thenOk() {
    List<Post> returnedPosts = HibernateUtil.doInHibernate(session -> {
        List<Post> posts = session.createQuery("select p from Post p", Post.class).getResultList();
        //достаточно обратиться к коллекциям одного элемента, чтобы заполнились коллекции всех элементов
        System.out.println(posts.get(0));
        return posts;
    });

    Assertions.assertEquals(5, returnedPosts.size());
    returnedPosts.forEach(System.out::println);
}
```

Генерируемые SQL запросы

```sql
select post0_.id as id1_1_, post0_.title as title2_1_ 
from Post post0_
------------------------------------------
select images0_.post_id as post_id3_0_1_, images0_.id as id1_0_1_,
       images0_.id as id1_0_0_, images0_.post_id as post_id3_0_0_, 
       images0_.url as url2_0_0_ 
from Image images0_ 
where images0_.post_id in (select post0_.id from Post post0_)
--------------------------------------
select tags0_.Post_id as Post_id1_2_0_, tags0_.tags as tags2_2_0_ 
from Post_tags tags0_ 
where tags0_.Post_id in (select post0_.id from Post post0_)
```

На этот раз после выборки постов  выбираются все картинки и все теги для все выбранных постов. Причем делается это только после обращения к коллекциям одного поста:

`System.out.println(posts.get(0));`

>[!Info]
>Для _Post_ переопределен метод _toString()_, который и обращается к коллекциям при выводе поста.

Если к коллекциям одного поста не обратиться, то никакие коллекции загружены не будут. Если же обратиться, то будут загружены коллекции для все выбранных постов. Вот так работает _FetchMode.SUBSELECT.

