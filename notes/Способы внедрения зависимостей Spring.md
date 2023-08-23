---
tags: Spring
---

В Спринге связать бины можно различными способами, рассмотрим их на примере:
1) `KoscheiTheDeathless` связывается с `Ocean1` через сеттер `setOcean` и аннотации `@Autowired` над ней.
```java
@Component
public class MyClass {
    private Dependency dependency;

    @Autowired
    public void setDependency(Dependency dependency) {
        this.dependency = dependency;
    }
    // ...
}
```

2) `Ocean1` связан с `Island2` через поле и аннотацию, которая подтянет бин `Island2` через метод `getIsland()` в классе `AppConfig`. Обратите внимание, что данный метод обозначен аннотацией `@Bean`, что автоматически подтягивает аргументы в метод. В качестве аргумента выступает бин `Wood3`.

3) Бин `Wood3` помечен как компонент, который конструируется через связывание по `@Autowired` с помощью конструктора.

Напрямую вешать аннотацию @Autowired над полем не рекомендуется, т.к. в таком случае сложно отследить сам процесс связывания.

4) **Внедрение через аннотации полей (Field Injection):** В этом способе зависимости внедряются непосредственно в поля класса с использованием аннотаций. Вы просто помечаете поля класса, которые требуют внедрения, аннотацией `@Autowired`, и Spring автоматически внедряет соответствующие зависимости.
Например:
```java
@Component
public class MyClass {
    @Autowired
    private Dependency dependency;

    // ...
}
```

## Минусы при внедрении зависимостей и их решение

## Внедрение зависимостей через поле

Внедрение зависимостей через поля не рекомендуется в Spring. [Это связано с тем, что такой подход не позволяет использовать final-поля для присвоения зависимостей](https://habr.com/ru/articles/334636/) Кроме того, такой подход может привести к трудностям при тестировании (проблема при использовании mock заглушек)

Есть несколько способов решения этой проблемы. Один из них - использование конструктора
## Внедрение зависимостей через конструктор
- Один из минусов внедрения зависимостей через конструктор заключается в том, что при наличии большого количества зависимостей конструктор может стать очень длинным и трудночитаемым. Решение этой проблемы может быть использование аннотации `@Autowired` над каждым параметром конструктора. Это позволяет автоматически внедрять зависимости без необходимости явного объявления setter-методов для каждой зависимости.

Например:
```java
@Component
public class MyClass {
    private Dependency1 dependency1;
    private Dependency2 dependency2;
    
    @Autowired
    public MyClass(Dependency1 dependency1, Dependency2 dependency2) {
        this.dependency1 = dependency1;
        this.dependency2 = dependency2;
    }
    // ...
}
```

- Еще один минус внедрения зависимостей через конструктор состоит в том, что при добавлении новых зависимостей необходимо изменять существующий конструктор и все места его вызова. Это может привести к большому количеству изменений кода. Решением этой проблемы является использование аннотации `@Autowired` над конструктором класса и указание ему параметра по умолчанию.

Например:
```java
@Component
public class MyClass {
    private Dependency1 dependency1;
    private Dependency2 dependency2;
    
    @Autowired(required = false)
    public MyClass(Dependency1 dependency1, Dependency2 dependency2) {
        this.dependency1 = dependency1;
        this.dependency2 = dependency2;
    }
    
    @Autowired
    public MyClass(Dependency1 dependency1) {
        this(dependency1, null);
    }
    // ...
}
```
