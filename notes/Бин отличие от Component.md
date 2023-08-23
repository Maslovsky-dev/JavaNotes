---
tags: Spring
---
**Bean** – это объект, управляемый Спрингом. Другими словами - Spring-бины − это **классы, созданием экземпляров которых и установкой в них зависимостей управляет контейнер фреймворка Spring**
## Отличие Bean и Component
В Spring Framework **@Component** - это аннотация уровня класса, тогда как **@Bean** - это аннотация **уровня метода** и имя метода служит именем бина. **@Component** используется для автоматического сканирования компонентов и автоматического внедрения зависимостей. **@Bean** используется в тех случаях, когда автоматическая конфигурация невозможна. Например, когда вы хотите связать компоненты из сторонних библиотек (у вас нет исходного кода, поэтому вы не можете аннотировать его классы с помощью **@Component**), поэтому автоматическая конфигурация невозможна. Аннотация **@Bean** возвращает объект, который Spring должен зарегистрировать как бин в контексте приложения. Тело метода несет логику, отвечающую за создание экземпляра.

Пример Bean
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```

Пример Component
```java
import org.springframework.stereotype.Component;

@Component
public class MyComponent {

    public void doSomething() {
        System.out.println("Doing something...");
    }
}
```
