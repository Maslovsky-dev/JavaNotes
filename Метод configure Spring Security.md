---
aliases: цепочка фильтров, настройка Spring Security 
tags: Spring/Security
---
Метод `configure` используется для настройки Spring Security в вашем приложении. Когда вы хотите создать настраиваемую конфигурацию Spring Security, вы должны расширить класс `WebSecurityConfigurerAdapter` и переопределить метод `configure(HttpSecurity http)`.

В классе, который наследует `WebSecurityConfigurerAdapter`, вы можете определить различные правила аутентификации и авторизации, указать пути доступа, настроить форму входа, добавить настраиваемые фильтры безопасности и многое другое.

Вот пример простой настройки Spring Security, который требует аутентификации для всех запросов, кроме страницы входа:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        // Настройка in-memory аутентификации с двумя пользователями.
        auth.inMemoryAuthentication()
            .withUser("user1").password("{noop}password1").roles("USER")
            .and()
            .withUser("user2").password("{noop}password2").roles("USER");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/login").permitAll() // Разрешить доступ к странице входа без аутентификации
            .anyRequest().authenticated() // Все остальные запросы требуют аутентификации
            .and()
            .formLogin()
            .loginPage("/login") // Указываем путь к пользовательской странице входа
            .defaultSuccessUrl("/home") // После успешного входа перенаправляем на /home
            .and()
            .logout()
            .logoutUrl("/logout") // Указываем путь для выхода из системы
            .logoutSuccessUrl("/login?logout") // После выхода перенаправляем на /login?logout
            .invalidateHttpSession(true) // Очищаем сессию после выхода из системы
            .deleteCookies("JSESSIONID"); // Удаляем JSESSIONID cookie после выхода
    }
}

```
