---
aliases: heap, stack
tags: Java/ПодКапотом
---
Про stack и heap память в Java [Что такое Heap и Stack память в Java? — Программирование на Java, Android](https://javadevblog.com/chto-takoe-heap-i-stack-pamyat-v-java.html#:~:text=%D0%A0%D0%B0%D0%B7%D0%BD%D0%B8%D1%86%D0%B0%20%D0%BC%D0%B5%D0%B6%D0%B4%D1%83%20Stack%20%D0%B8%20Heap%20%D0%BF%D0%B0%D0%BC%D1%8F%D1%82%D1%8C%D1%8E%20%D0%B2%20Java&text=%D0%9A%D1%83%D1%87%D0%B0%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D1%83%D0%B5%D1%82%D1%81%D1%8F%20%D0%B2%D1%81%D0%B5%D0%BC%D0%B8%20%D1%87%D0%B0%D1%81%D1%82%D1%8F%D0%BC%D0%B8%20%D0%BF%D1%80%D0%B8%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F,%D1%81%D1%82%D0%B5%D0%BA%D0%B0%20%D1%81%D0%BE%D0%B4%D0%B5%D1%80%D0%B6%D0%B8%D1%82%D1%81%D1%8F%20%D1%81%D1%81%D1%8B%D0%BB%D0%BA%D0%B0%20%D0%BD%D0%B0%20%D0%BD%D0%B5%D0%B3%D0%BE.)

Пример
```java
public class Memory {

    public static void main(String[] args) { // строка 1
        int i=1; // строка 2
        Object object = new Object(); // строка 3
        Memory memory = new Memory(); // строка 4
        memory.exMethod(object); // строка 5
    } // строка 9

    private void exMethod(Object param) { // строка 6
        String string = param.toString(); // строка 7
        System.out.println(string );
    } // строка 8

}
```
![[Стек и куча Img.png]]
## Stack память в Java

Стековая память в Java работает по схеме LIFO (Последний-зашел-Первый-вышел). Всякий раз, когда вызывается метод, в памяти стека создается новый блок, который содержит примитивы и ссылки на другие объекты в методе. Как только метод заканчивает работу, блок также перестает использоваться, тем самым предоставляя доступ для следующего метода.  
Размер стековой памяти намного меньше объема памяти в куче.

>[!tip] В стеке хранятся ссылки и примитивы

## Java Heap память

Java Heap (куча) используется `Java Runtime` для выделения памяти под объекты и [JRE](https://javadevblog.com/chto-takoe-jdk-jre-i-jvm-v-java.html) классы. Создание нового объекта также происходит в куче. Здесь работает сборщик мусора: освобождает память путем удаления объектов, на которые нет каких-либо ссылок. Любой объект, созданный в куче, имеет глобальный доступ и на него могут ссылаться с любой части приложения.

>[!tip] В куче хранятся объекты
