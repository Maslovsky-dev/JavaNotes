---
tags: Spring/Security
--- 
`PasswordEncoder` в Spring Security - это интерфейс, который предоставляет методы для шифрования паролей. Он используется для безопасного хранения и проверки паролей пользователей в вашем приложении.

При регистрации нового пользователя в системе или при изменении пароля существующего пользователя, пароль должен быть зашифрован, прежде чем быть сохраненным в базе данных или хранилище. Когда пользователь пытается войти в систему, введенный им пароль также должен быть зашифрован и сравнен с сохраненным зашифрованным паролем.

Spring Security предоставляет несколько реализаций интерфейса `PasswordEncoder`, таких как `BCryptPasswordEncoder`, `StandardPasswordEncoder`, `NoOpPasswordEncoder` и другие. Рекомендуется использовать одну из алгоритмически сильных реализаций, таких как `BCryptPasswordEncoder`, чтобы обеспечить безопасное хранение паролей.

Пример использования `BCryptPasswordEncoder`:
```java
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

public class PasswordEncoderExample {

    public static void main(String[] args) {
        String rawPassword = "secretPassword";
        BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();

        // Шифрование пароля перед сохранением в базу данных
        String encodedPassword = passwordEncoder.encode(rawPassword);

        System.out.println("Зашифрованный пароль: " + encodedPassword);

        // Проверка пароля при аутентификации пользователя
        boolean matches = passwordEncoder.matches(rawPassword, encodedPassword);
        System.out.println("Пароль совпадает: " + matches);
    }
}

```
В этом примере мы создаем объект `BCryptPasswordEncoder` и используем его для шифрования и сравнения пароля. Метод `encode()` шифрует пароль, и он будет каждый раз генерировать новую зашифрованную строку при каждом вызове. Метод `matches()` используется для проверки соответствия пароля с заданным хэшем.
