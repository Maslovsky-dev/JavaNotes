---
tags: Java/JDBC
---
Структура `PreparedStatement` выглядит следующим образом:

1. Подготовка запроса:

```java
Connection connection = DriverManager.getConnection(url, username, password); 
String sql = "SELECT * FROM table_name WHERE column = ?"; 
PreparedStatement statement = connection.prepareStatement(sql);
```

2. Установка параметров:

```java
// Устанавливаем значения параметров запроса
statement.setString(1, "value"); // Заменяет первый параметр в запросе на указанное значение
statement.setInt(2, 10); // Пример установки значения типа int
statement.setDouble(3, 3.14); // Пример установки значения типа double
// и так далее...

```

Параметры обозначаются в запросе символом `?`, их порядковый номер начинается с 1.

3. Выполнение запроса и получение результатов:

```java
// Выполняем запрос типа SELECT
ResultSet resultSet = statement.executeQuery();

// Или выполняем запросы типа INSERT, UPDATE или DELETE
int rowsAffected = statement.executeUpdate();

```

Для запросов SELECT результаты возвращаются в виде объекта `ResultSet`, который можно использовать для итерации по полученным записям и получения значений столбцов.

4. Освобождение ресурсов:

```java
statement.close(); connection.close();
```

Важно закрывать `PreparedStatement` и `Connection` после их использования для освобождения ресурсов базы данных.
