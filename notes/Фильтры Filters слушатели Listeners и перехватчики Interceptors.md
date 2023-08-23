---
tags: Spring/Web
---
Фильтры (Filters), слушатели (Listeners) и перехватчики (Interceptors) являются различными механизмами, используемыми в веб-приложениях Spring для обработки запросов и ответов, а также для добавления определенной логики до и после обработки запросов.

## Фильтры (Filters)
Это ИНТЕРФЕЙС из пакета javax.servlet, имплементации которого выполняют задачи фильтрации либо по пути ЗАПРОСА к ресурсу (сервлету, либо по статическому контенту), либо по пути ОТВЕТА от ресурса, либо в ОБОИХ направлениях.

Фильтры выполняют фильтрацию в методе **doFilter()**. Каждый фильтр имеет доступ к объекту FilterConfig. Из которого он получает параметры инициализации, и ссылку на ServletContext, который он может использовать, например, для загрузки ресурсов, необходимых для задач фильтрации. Фильтры настраиваются в дескрипторе развертывания веб-приложения.

В веб-приложении мы можем написать несколько фильтров, которые вместе называются цепочкой фильтров. Веб-сервер решает, какой фильтр вызывать первым, в соответствии с порядком регистрации фильтров.

Когда вызывается метод **doFilter**(ServletRequest request, ServletResponse response, FilterChain
chain) первого фильтра, веб-сервер создает объект FilterChain, представляющий цепочку фильтров, и передаёт её в метод.
![[FilterChain.png]]
- Фильтры - это часть стандартного веб-контейнера (например, Apache Tomcat) и применяются до и после обработки запроса и ответа. Они могут обрабатывать HTTP-запросы и ответы, а также выполнять некоторые операции, такие как логирование, аутентификация, авторизация, фильтрация запросов и другие общие операции.
- Фильтры находятся ниже уровня Spring и обрабатывают запросы и ответы независимо от контекста Spring.
- В Spring вы также можете определить собственные фильтры, реализовав интерфейс `javax.servlet.Filter`.
## Cлушатели (Listeners)
Listener (Слушатель) - это класс, который реализует интерфейс **javax.servlet.ServletContextListener**.
![[ContextListener.png|250]]

Он инициализируется только ОДИН раз при запуске веб-приложения и уничтожается при остановке веб-приложения.

Слушатель сидит и ждет, когда произойдет указанное событие, затем «перехватывает» событие и запускает собственное событие.

Чтобы создать свой Listener, достаточно создать класс, имплементирующий интерфейс `ServletContextListener` и поставить над ним аннотацию **@WebListener**

- Слушатели - это компоненты, которые позволяют приложению реагировать на различные события жизненного цикла веб-приложения, такие как запуск и остановка приложения, создание и уничтожение сессии, атрибуты контекста и другие события.
- Слушатели также являются частью стандартного веб-контейнера и не специфичны для Spring.
- В Spring вы можете определить свои собственные слушатели, реализовав интерфейсы `javax.servlet.ServletContextListener` или `javax.servlet.http.HttpSessionListener`.
## Перехватчики (Interceptors)
Это ИНТЕРФЕЙС из пакета org.aopalliance.intercept, предназначенный для AOP аспектно-ориентированного программирования.

Предположим, у нас есть простое веб-приложение Spring MVC, которое содержит один контроллер для обработки запроса на показ приветственного сообщения. **Мы хотим создать перехватчик, который будет записывать время обработки каждого запроса и выводить его в лог.**

Сначала определим перехватчик с помощью интерфейса `HandlerInterceptor`:
```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public class RequestTimeInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        long startTime = System.currentTimeMillis();
        request.setAttribute("startTime", startTime);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // Пустой метод, ничего не делаем после обработки контроллера.
    }
	//вызывается после того, как контроллер закончил свою работу и перед отправкой ответа клиенту
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        long startTime = (Long) request.getAttribute("startTime");
        long endTime = System.currentTimeMillis();
        long executionTime = endTime - startTime;
        System.out.println("Время обработки запроса: " + executionTime + " мс");
    }
}

```
Затем зарегистрируем этот перехватчик в конфигурации Spring: (Применяется ко всем контроллерами в приложении)
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new RequestTimeInterceptor());
    }
}
```

 Зарегистрируем этот перехватчик в конфигурации Spring: (Применяется для контроллеров соответствующих пути /greeting)
 ```java
 @Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new RequestTimeInterceptor()).addPathPatterns("/greeting");
    }
}
```
Теперь, когда мы отправляем запрос на наш контроллер, перехватчик будет фиксировать время обработки запроса и выводить его в консоль.

Обратите внимание, что перехватчик `RequestTimeInterceptor` вызывается до и после обработки контроллера. Метод `preHandle` вызывается перед выполнением контроллера, а метод `afterCompletion` **вызывается после того, как контроллер закончил свою работу и перед отправкой ответа клиенту**. Это позволяет нам контролировать и логировать различные этапы обработки запроса в приложении.

- Перехватчики - это механизмы Spring MVC, которые позволяют вам встраиваться в процесс обработки запросов и ответов перед и после выполнения контроллеров.
- Они работают на уровне Spring MVC и позволяют выполнять общие задачи, такие как аутентификация, логирование, изменение модели, контроль доступа и другие.
- Перехватчики специфичны для Spring MVC и являются частью контекста Spring.
- В Spring вы можете определить свои собственные перехватчики, реализовав интерфейс `org.springframework.web.servlet.HandlerInterceptor`.


## Отличие слушателя от перехватчика

Слушатели - это компоненты, которые позволяют приложению **реагировать на различные события жизненного цикла веб-приложения**, такие как запуск и остановка приложения, создание и уничтожение сессии, атрибуты контекста и другие события. Они реализуют интерфейсы `javax.servlet.ServletContextListener` или `javax.servlet.http.HttpSessionListener` и регистрируются в дескрипторе развертывания веб-приложения. Слушатели не специфичны для Spring и работают независимо от контекста Spring.

Перехватчики - это механизмы Spring MVC, которые позволяют **встраиваться в процесс обработки запросов и ответов перед и после выполнения контроллеров**. Они работают на уровне Spring MVC и позволяют выполнять общие задачи, такие как аутентификация, логирование, изменение модели, контроль доступа и другие. Перехватчики реализуют интерфейс `org.springframework.web.servlet.HandlerInterceptor` и регистрируются через конфигурацию Spring. Они являются частью контекста Spring и специфичны для Spring MVC.

