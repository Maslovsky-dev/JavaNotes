---
tags: ORM
--- 
![[Маппинг enum hibernate.png]]
## Аннотация @Enumerated
Можно использовать аннотацию `@Enumerated` для указания, как Hibernate должен маппить enum поле. Возможные значения для параметра `EnumType` - `ORDINAL` и `STRING`.

## Использование `EnumType.ORDINAL`

```java
public enum Gender {
    MALE,
    FEMALE
}

@Entity
public class Person {
    // ...
    @Enumerated(EnumType.ORDINAL)
    private Gender gender;
    // ...
}
```

В этом случае Hibernate сохраняет значение enum поля как целочисленный индекс, начиная с 0 для первого элемента.

## Использование `EnumType.STRING`:

```java
    // ...
    @Enumerated(EnumType.STRING)
    private Gender gender;
    // ...
```
В этом случае Hibernate сохраняет значение enum поля как строку.

## Converter

В JPA с версии 2.1 можно использовать Converter для конвертации Enum -а в некое его значение для сохранения в БД и получения из БД. Все, что нужно сделать, это создать новый класс, который реализует интерфейс **javax.persistence.AttributeConverter** и аннотировать его с помощью **@Converter**.

![[Converter enum ов.png]]
Тут установили @Converter(autoApply=true), чтобы JPA автоматически применял логику преобразования ко всем сопоставленным атрибутам типа Category.

В противном случае пришлось бы поместить аннотацию @Converter непосредственно над полем Category у каждой сущности, где оно имеется. В результате в столбце таблицы будут храниться значения: "S", "M" или "T".

То есть можно просто установить *собственные правила преобразования перечислений* в соответствующие значения базы данных, если использовать **интерфейс
AttributeConverter.**

>[!success]
>
Более того, можно безопасно добавлять новые значения enum или изменять существующие, не нарушая уже сохраненные данные. Это решение просто в реализации и устраняет все недостатки с @Enumerated(EnumType.ORDINAL), @Enumerated(EnumType.STRING) и методами обратного вызова.
