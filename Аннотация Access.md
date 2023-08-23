---
tags: ORM
--- 
Аннотация `@Access` в Hibernate используется для определения способа доступа к полям или свойствам сущности. Эта аннотация позволяет контролировать, как Hibernate получает доступ к данным сущности, используя ли прямой доступ к полям (field-based access) или доступ через геттеры и сеттеры (property-based access).

Hibernate поддерживает два вида доступа: field-based access и property-based access.

## Field-based access (доступ на основе полей)
По умолчанию, если вы не указываете аннотацию `@Access`, Hibernate использует field-based access. Это означает, что Hibernate получает доступ к полям сущности напрямую, обращаясь к ним без использования геттеров и сеттеров. В этом случае, поля должны быть объявлены как `private` или `protected`.

Пример использования field-based access:
```java
@Entity
public class Product {
    @Id
    private Long id;

    private String name;

    private double price;

    // Геттеры и сеттеры
    // ...
}
```

## Property-based access (доступ на основе свойств)
Если вы хотите, чтобы Hibernate получал доступ к данным сущности через геттеры и сеттеры, вы можете использовать аннотацию `@Access` с атрибутом `AccessType.PROPERTY`.

Пример использования property-based access:
```java
@Entity
@Access(AccessType.PROPERTY)
public class Product {
    private Long id;

    private String name;

    private double price;

    @Id
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}

```

В этом случае, Hibernate будет использовать геттеры и сеттеры для доступа к данным сущности. Поля могут быть объявлены с любым модификатором доступа (**private, protected, public**).


Использование аннотации `@Access` полезно, если вам нужно контролировать способ доступа к данным в сущности. Вы можете выбрать наиболее удобный способ доступа в зависимости от вашей предметной области и требований приложения.
