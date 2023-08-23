---
tags: Spring/Web
---
Когда контроллер обрабатывает запрос и получает данные, он должен вернуть имя представления (например, "index" или "home") и модель данных (данные, которые будут использоваться в представлении). Затем `ViewResolver` берет логическое имя представления и находит соответствующий шаблон или файл представления, который должен быть отображен. Результатом является готовое представление, в котором данные из модели могут быть вставлены, и это представление отправляется обратно пользователю в ответе.

Spring Framework предоставляет различные встроенные реализации `ViewResolver` для различных видов шаблонов представлений, таких как JSP, **Thymeleaf**, FreeMarker, Velocity и т. д.

Пример конфигурации JSP-представлений в Spring MVC с использованием `InternalResourceViewResolver`:
```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public ViewResolver jspViewResolver() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB-INF/views/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```
