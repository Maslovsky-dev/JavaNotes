---
tags: Паттерны
--- 
## Dao
Data Access Object (DAO) нужен для абстрагирования и инкапсулирования доступа к источнику данных. DAO управляет соединением с источником данных для получения и записи данных.
[Паттерн Data Access Object - JavaTutor.net](https://javatutor.net/articles/j2ee-pattern-data-access-object)

Допустим, у нас есть сущность "User", которую хотим сохранить, загрузить или обновить в базе данных.
```java
public class User {
    private int id;
    private String username;
    private String email;

    // Конструкторы, геттеры и сеттеры
}
```
Создадим класс для представления сущности "User":
```java
public interface UserDao {
    void saveUser(User user);
    User getUserById(int id);
    void updateUser(User user);
    void deleteUser(int id);
}
```

Создадим интерфейс DAO, который определит абстрактные методы для работы с сущностью "User":
```java
import java.util.ArrayList;
import java.util.List;

public class UserDaoImpl implements UserDao {
    private List<User> userList = new ArrayList<>();

    @Override
    public void saveUser(User user) {
        userList.add(user);
    }

    @Override
    public User getUserById(int id) {
        for (User user : userList) {
            if (user.getId() == id) {
                return user;
            }
        }
        return null;
    }

    @Override
    public void updateUser(User user) {
        for (User u : userList) {
            if (u.getId() == user.getId()) {
                u.setUsername(user.getUsername());
                u.setEmail(user.getEmail());
                return;
            }
        }
    }

    @Override
    public void deleteUser(int id) {
        userList.removeIf(user -> user.getId() == id);
    }
}
```

Реализуем DAO для работы с базой данных. В данном примере, для упрощения, мы будем использовать простой список для хранения пользователей:
```java
public class Main {
    public static void main(String[] args) {
        UserDao userDao = new UserDaoImpl();

        // Создание нового пользователя и сохранение его в базе данных
        User user1 = new User(1, "user1", "user1@example.com");
        userDao.saveUser(user1);

        // Загрузка пользователя по его id
        User loadedUser = userDao.getUserById(1);
        System.out.println("Loaded User: " + loadedUser.getUsername() + " - " + loadedUser.getEmail());

        // Обновление данных пользователя
        loadedUser.setUsername("newUser1");
        userDao.updateUser(loadedUser);

        // Удаление пользователя
        userDao.deleteUser(1);
    }
}
```

Теперь мы можем использовать DAO для работы с объектами User:
