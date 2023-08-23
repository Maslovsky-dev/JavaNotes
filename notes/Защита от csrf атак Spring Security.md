---
tags: Spring/Security
--- 
Spring Security предоставляет встроенную защиту от CSRF (Cross-Site Request Forgery) атак, которая помогает предотвратить атаки на ваше веб-приложение. CSRF-атака происходит, когда злоумышленник отправляет запрос от имени авторизованного пользователя без его согласия, используя механизмы авторизации, которые уже установлены в браузере пользователя.

В Spring Security, для активации защиты от CSRF, вы можете использовать метод `csrf()` в методе `configure(HttpSecurity http)` вашего класса, который наследует `WebSecurityConfigurerAdapter`.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.web.csrf.CookieCsrfTokenRepository;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/public/**").permitAll()
            .antMatchers("/private/**").authenticated()
            .and()
            .formLogin()
            .loginPage("/login")
            .defaultSuccessUrl("/home")
            .and()
            .logout()
            .logoutUrl("/logout")
            .logoutSuccessUrl("/login?logout")
            .invalidateHttpSession(true)
            .deleteCookies("JSESSIONID")
            .and()
            .csrf()
            .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse());
    }
}

```

В этом примере мы настроили следующее:

1. Защита CSRF включена по умолчанию с использованием метода `csrf()`.
2. Мы используем `CookieCsrfTokenRepository` для хранения CSRF-токена в Cookie. `withHttpOnlyFalse()` позволяет JavaScript-коду получить доступ к этому Cookie для включения CSRF-токена в запросы.
3. `antMatchers()` определяет пути и разрешения для запросов. Например, "/public/**" доступен для всех, а "/private/**" доступен только аутентифицированным пользователям.
4. `formLogin()` определяет путь к пользовательской странице входа и URL для перенаправления после успешного входа.
5. `logout()` определяет путь для выхода из системы и URL для перенаправления после выхода.

Важно отметить, что защита CSRF включена по умолчанию в Spring Security, и вам не нужно явно включать ее, если вы не отключали ее ранее. Использование Cookie для хранения CSRF-токена предпочтительнее, поскольку это делает атаки CSRF более сложными. Когда вы разрабатываете веб-приложение с помощью Spring Security, рекомендуется оставлять защиту CSRF включенной и использовать подходящий `csrfTokenRepository` в зависимости от вашего конкретного случая.
