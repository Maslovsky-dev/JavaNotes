---
tags: Java/Коллекции
--- 
Интерфейс от которого наследуются Set, List и Queue

## В чем разница между классами java.util.Collection и java.util.Collections?
### java.util.Collection – один из основных интерфейсов Java Collections Framework.
`public interface Collection<E> extends Iterable<E>` это корневой интерфейс в иерархии коллекций.

JDK не предоставляет каких-либо прямых реализаций этого интерфейса: он предоставляет реализации более конкретных под-интерфейсов, таких как Set и List.

Этот интерфейс обычно используется для передачи коллекций и управления ими там, где требуется максимальная универсальность.

### java.util.Collections – это класс, набор статических методов для работы с коллекциями.

Этот класс состоит исключительно из статических методов, которые работают с коллекциями или возвращают их. Он содержит полиморфные алгоритмы, которые работают с коллекциями, «обертки», которые возвращают новую коллекцию, поддерживаемую указанной коллекцией, и несколько других случаев. Все методы этого класса вызывают исключение NullPointerException, если предоставленные им коллекции или объекты класса имеют значение null.
### Методы класса Collections
1. [Метод Collections.sort()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header1)
2. [Метод Collections.binarySearch()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header2)
3. [Методы Collections.reverse(), Collections.shuffle()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header3)
4. [Метод Collections.fill()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header4)
5. [Методы Collections.max(), Collections.min()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header5)
6. [Метод Collections.copy()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header6)
7. [Метод Collections.rotate()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header7)
8. [Метод Collections.checkedCollection()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header8)
9. [Метод Collections.frequency()](https://www.examclouds.com/ru/java/java-core-russian/algoritm-collections#header9)

