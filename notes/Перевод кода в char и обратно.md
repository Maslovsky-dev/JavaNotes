---
tags: Java/ОсновыЯзыка, Задачи
---  

Char + int = int

Преобразовать char в int можно следующим образом
```java 
public static char charExpression(int a) {
    int result;
    char resultChar, ch = '\\';
    result = ch + a;
    resultChar = (char) result;
    return resultChar;
}
```
