---
tags: Spring
---

Аннотации `@Profile` и `@Conditional` являются часто используемыми механизмами в Spring Framework для условного конфигурирования бинов и компонентов. Они позволяют создавать бины или выполнять конфигурацию только при выполнении определенных условий.
## **@Profile:**

- Аннотация `@Profile` позволяет определить, в каких окружениях или профилях приложения должен быть активным определенный бин или компонент.
- Каждый бин или компонент может быть помечен аннотацией `@Profile` с указанием имени профиля, в котором он должен быть активным. Вы можете указать несколько профилей через атрибут `value`.
- При запуске приложения вы можете активировать определенные профили с помощью свойств окружения или конфигурации, и только бины с соответствующими аннотациями `@Profile` будут созданы и добавлены в контекст приложения.
 Пример:
 ```java
@Component
@Profile("development")
public class DevelopmentDataSource implements DataSource {
    // Implementation for development environment
}

@Component
@Profile("production")
public class ProductionDataSource implements DataSource {
    // Implementation for production environment
}

```
## Как активировать профиль
- **Использование свойства spring.profiles.active в командной строке при запуске приложения**

Вы можете активировать профиль, передав свойство spring.profiles.active в командной строке следующим образом:

```sh
java -jar your-app.jar --spring.profiles.active=development
```

- **Использование свойства spring.profiles.active в файле application.properties**

Вы также можете активировать профиль, добавив свойство spring.profiles.active в файл application.properties следующим образом:

```yml
spring.profiles.active=profile1,profile2
```

- **Использование тега <`profiles`> в файле конфигурации XML**

Вы также можете активировать профиль, добавив тег <`profiles`> в файл конфигурации XML следующим образом:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                            http://www.springframework.org/schema/beans/spring-beans.xsd">

   <profiles>
      <profile name="profile1">
        <context:property-placeholder location="classpath:profile1.properties"/>
      </profile>
      <profile name="profile2">
        <context:property-placeholder location="classpath:profile2.properties"/>
      </profile>
   </profiles>

</beans>
```
- **Использование класса ConfigurableEnvironment**
```java
ConfigurableEnvironment env = applicationContext.getEnvironment();
env.setActiveProfiles("development");
```

## Логические операции над профилями
В Spring Framework также можно использовать логические операции над профилями с помощью аннотации `@Profile`. Это позволяет определить активацию компонентов или конфигураций на основе сложных логических условий, связанных с профилями.

Для создания логических операций над профилями можно использовать следующие ключевые слова:

- `!`: Отрицание (NOT). Применяется перед профилем для инвертирования его состояния.

- `&` или `&&`: Логическое "И" (AND). Используется для комбинирования нескольких профилей, чтобы все они были активны.

- `|` или `||`: Логическое "ИЛИ" (OR). Используется для комбинирования нескольких профилей, чтобы хотя бы один из них был активен.


Пример использования логических операций над профилями:
```java
@Configuration
@Profile("!production & development")
public class DevAndNotProdConfig {
    // Конфигурация для профиля "development" и если "production" не активен
}

@Configuration
@Profile("test | qa")
public class TestOrQaConfig {
    // Конфигурация для профилей "test" или "qa"
}
```
В первом примере `DevAndNotProdConfig` будет активирован, если профиль "development" активен и профиль "production" не активен.

Во втором примере `TestOrQaConfig` будет активирован, если профиль "test" или профиль "qa" активен.

Использование логических операций над профилями может быть полезным, когда необходимо создавать более сложные условия активации компонентов или конфигураций в зависимости от комбинаций профилей.

[[Профили Sping и Maven]]
## **@Conditional:**

- Аннотация `@Conditional` позволяет определить условие, которое должно быть выполнено, чтобы бин или конфигурация стала активной.
- Вы можете создать свой собственный класс, реализующий интерфейс `Condition`, и аннотировать его с `@Conditional`, либо использовать предопределенные условия из Spring, такие как `@ConditionalOnProperty`, `@ConditionalOnBean`, `@ConditionalOnClass` и т. д.
- Если условие указанное в `@Conditional` оценивается как `true`, то бин или конфигурация с этой аннотацией становится активным и добавляется в контекст приложения.
Пример
```java
@Configuration
public class MyConfig {
    @Bean
    @Conditional(MyCondition.class)
    public MyBean myBean() {
        return new MyBean();
    }
}
```

```java
public class MyCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        // Your custom condition logic
        // Return true if the condition is met, false otherwise
    }
}
```
![[Conditional условия.png]]
