---
tags: Spring/Boot 
---
Основные сущности фреймворка Spring Boot – это стартеры. Зависимости с названиями вида spring-boot-starter-xxx выполняют две основных задачи.

Во-первых, они добавляют набор типичных сторонних библиотек-зависимостей, а во- вторых, регистрируют типичные бины и их конфигурации. Кроме того, со Spring Boot в проекте появляется ряд таких полезностей, как embedded-сервер, конфигурация web- приложения без web.xml, метрики, properties вынесенные из кода во внешние файлы.

>[!Example]
> Например, **spring-boot-starter-data-jpa** даст вам готовый комплект всего необходимого для использования JPA: драйвер, совместимую с ним версию Hibernate, библиотеки Persistence API и Spring Data. В контексте приложения появятся все нужные для JPA репозиториев бины.

Таким образом, Spring Boot ускоряет и упрощает разработку, дает возможность избавиться от boilerplate-кода в проекте и сфокусироваться на бизнес-задачах.

Это бывает особенно важно в микросервисной архитектуре, когда создается большое количество приложений.

С другой стороны, такая избыточность естественно приводит к большей тяжеловесности
и медлительности приложения.

## Конфигурация Spring Boot приложения
Spring Boot приложение может обращаться к свойствам из внешних источников:

- переменным среды ОС,
- к Java System Properties,
- к специальным свойствам Spring Boot приложения, заданным в _application.properties_.
- и многим другим — всего [более десятка источников](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config)

Все эти свойства во время работы приложения собираются в бине _Environment_. Если внедрить бин _Environment_, то в режиме отладки можно посмотреть все эти списки свойств:

![Бин Environment](https://sysout.ru/wp-content/uploads/2021/04/Environment.png)

### _application.properties_

Зададим в _application.properties_ пару свойств:
```
app.n=1
app.m=2
```

>[!Note]
> Аналог _application.properties_ — _application.yml_. Можно задавать свойства в любом формате, без разницы.

### System Properties

Чтобы задать System Property, на вкладке Run Configuration  пропишем VM options:
```
-Dprop1=prop1
```


![VM Options](https://sysout.ru/wp-content/uploads/2021/04/vm-options.png "VM Options")

VM Options
## Доступ к свойствам

Можно внедрить отдельное свойство с помощью @Value.  Также можно внедрить же целую серию свойств с помощью класса

### Внедрение свойства с помощью _@Value_

Чтобы внедрить свойство в помощью @Value, используем ${…}:
```java
@SpringBootApplication
public class MainApplication  {


    @Value("${app.n}")
    private int n;

    @Value("${app.m}")
    private int m;

    @Value("${prop1}")
    private String prop1;

    @Value("${TEMP}")
    private String temp;

    @PostConstruct
    void print(){
        System.out.println(n);
        System.out.println(m);
        System.out.println(temp);
        System.out.println(prop1);
    }

    public static void main(String[] args) throws InterruptedException {
             SpringApplication.run(MainApplication.class, args);

    }
}
```

Мы внедрили свойства:

- из _application.properties_ 
- и переменную среды _TEMP_
- System Property, заданное в VM options

>[!Info]
> Обратите внимание, что если бы имена свойств случайно совпали, то переменная среды перекрыла бы свойство из _application.properties_, а System Property перекрыло бы переменную среды. Приоритеты источников указаны по ссылке на все источники свойств выше.

### _@ConfigurationProperties_

Можно также выделить серию свойств из файла _application.properties_ и создать для нее отдельный класс, который можно внедрять. На первый взгляд такой вариант кажется сложнее, но он подходит, если свойств много.

Например, выделим из  _application.properties_ свойства, начинающиеся с _app_ (у нас это все свойства, других нет):
```
app.n=1
app.m=2
```

Создадим под них класс _AppProperties_:
```java
@Configuration
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private int n;
    private int m;

   //getters/setters
}
```
Класс аннотирован _@Configuration_ и _@ConfigurationProperties_, и внутри указан префикс _app_ — это означает, что в _AppProperties_ внедрятся свойства, начинающиеся с _app_.

Теперь бин _AppProperties_  будет содержать свойства из _application.properties._ Его можно внедрять и через него получать свойства как поля бина:
```java
@SpringBootApplication
public class MainApplication  {
 
    @Autowired
    private AppProperties appProperties;

    @PostConstruct
    void print(){
        System.out.println(appProperties.getN());
        System.out.println(appProperties.getM());
    }


    public static void main(String[] args) throws InterruptedException {
             SpringApplication.run(MainApplication.class, args);
    }
}
```

## Из чего состоит аннотация SpringBootAplication
`@SpringBootApplication` представляет собой комбинацию трех других аннотаций: `@Configuration`, `@EnableAutoConfiguration` и `@ComponentScan`. Она используется для упрощения конфигурации и запуска приложения на основе Spring Boot.

1. `@Configuration`: Эта аннотация указывает, что класс содержит определения бинов и конфигурационные настройки для Spring контекста. В контексте `@SpringBootApplication` класс считается источником конфигурации.

2. `@EnableAutoConfiguration`: Данная аннотация включает автоматическую конфигурацию Spring Boot на основе зависимостей, добавленных в проект. Spring Boot пытается автоматически настроить бины, DataSource, JPA, и другие компоненты, исходя из наличия классов и зависимостей в проекте.

3. `@ComponentScan`: Эта аннотация указывает Spring'у, где искать компоненты (бины) для добавления в контекст. По умолчанию Spring ищет компоненты только в текущем пакете и его подпакетах. `@ComponentScan` расширяет этот поиск на весь проект.


В итоге, аннотация `@SpringBootApplication` включает в себя все три аннотации, что позволяет удобно и быстро настроить и запустить Spring Boot приложение.

- [ ] Доделать [11 вопросов на собеседовании по Spring Boot, которые заставляют задуматься / Хабр](https://habr.com/ru/articles/544472/)
