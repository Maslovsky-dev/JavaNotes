---
tags: Java/ФИ
---
Java представила поддержку функционального программирования в выпуске Java версии 8. Этот конкретный выпуск также представил несколько новых концепций, в частности лямбда-выражения, ссылки на методы и множество функциональных интерфейсов. При обсуждении последних, есть несколько функциональных интерфейсов, а именно Потребитель (Consumer), Поставщик (Supplier), Предикат (Predicat) и Функция (Function), которые являются наиболее важными.

## Predicate (предикат)

Интерфейс Predicate представляет собой логическую функцию аргумента. Он в основном используется для фильтрации данных из потока (stream) Java. Метод фильтра потока принимает предикат для фильтрации данных и возврата нового потока, удовлетворяющего предикату. У предиката есть метод test(), который принимает аргумент и возвращает логическое значение.

```java
public void testPredicate() {
    List<String> names = Arrays.asList("Smith", "Samueal", "Catley", "Sie");
    Predicate<String> nameStartsWithS = str -> str.startsWith("S");
    names.stream().filter(nameStartsWithS).forEach(System.out::println);
}
```
## Поставщики и потребители
## Consumer (потребитель)

Consumer — это функциональный интерфейс, который принимает один параметр на вход и не возвращает никаких выходных данных. На языке непрофессионала, как следует из названия, реализация этого интерфейса потребляет вводимые данные. Пользовательский интерфейс имеет два метода:

```java
void accept(T t);
default Consumer<T> andThen(Consumer<? super T> after);
```

Метод accept является единым абстрактным методом (SAM), который принимает один аргумент типа T. Тогда как другой метод andThen является методом по умолчанию и используется для [[Ассоциация агрегация композиция|композиции]].

Ниже приведен пример интерфейса consumer. Мы создали потребительскую реализацию, которая использует строку, а затем просто выводит ее на экран. Метод forEach принимает реализацию потребительского интерфейса.

```java
public void whenNamesPresentConsumeAll() {
	Consumer<String> printConsumer = t -> System.out.println(t);
	Stream<String> cities = Stream.of("Sydney", "Dhaka", "New York", "London");
	cities.forEach(printConsumer);
}
```

Интерфейс Consumer имеет специфические типы реализаций для типов integer, double и long -> IntConsumer, DoubleConsumer и LongConsumer, как показано ниже:

```java
IntConsumer void accept(int x);
DoubleConsumer void accept(double x);
LongConsumer void accept(long x);
```

## Supplier (поставщик)

Supplier — это простой интерфейс, указывающий, что данная реализация является поставщиком какого то результа. Этот интерфейс, однако, не накладывает никаких дополнительных ограничений, которые реализация поставщика должна возвращать при каждом новом получении результата.

У поставщика есть только один метод get() и нет никаких других методов по умолчанию или статических методов.

```java
public void supplier() {
    Supplier<Double> doubleSupplier1 = () -> Math.random();
    DoubleSupplier doubleSupplier2 = Math::random;

    System.out.println(doubleSupplier1.get());
    System.out.println(doubleSupplier2.getAsDouble());
}
```

Одно из основных применений этого интерфейса это использование для включения отложенного выполнения. Это означает отсрочку выполнения до тех пор, пока оно не понадобится. Например, в классе Optional есть метод orElseGet. Этот метод срабатывает, если у option нет данных. Это показано ниже:

```java
public void supplierWithOptional() {
    Supplier<Double> doubleSupplier = () -> Math.random();
    Optional<Double> optionalDouble = Optional.empty();
    System.out.println(optionalDouble.orElseGet(doubleSupplier));
}
```
## Function (функция)

Интерфейс Function — это более общий интерфейс, который принимает один аргумент и выдает результат. В нем применяется единый абстрактный метод (SAM), который принимает аргумент типа T и выдает результат типа R. Одним из распространенных вариантов использования этого интерфейса является метод Stream.map. Пример использования показан ниже:

```java
public void testFunctions() {
    List<String> names = 
    Arrays.asList("Smith", "Gourav", "John", "Catania");
    
    Function<String, Integer> nameMappingFunction = String::length;
    
    List<Integer> nameLength = names.stream()
      .map(nameMappingFunction).collect(Collectors.toList());
    System.out.println(nameLength);
}
```
## Операторы

>[!NOTE] Это частный случай ==функции==, когда на входе и на выходе значения одного и того же типа

## BinaryOperator<`T`>

BinaryOperator<`T`> принимает в качестве параметра два объекта типа T, выполняет над ними бинарную операцию и возвращает ее результат также в виде объекта типа T:

``` java
public interface BinaryOperator<T> {
    T apply(T t1, T t2);
}
```

```java
import java.util.function.BinaryOperator;
 
public class LambdaApp {
 
    public static void main(String[] args) {
         
        BinaryOperator<Integer> multiply = (x, y) -> x*y;
         
        System.out.println(multiply.apply(3, 5)); // 15
        System.out.println(multiply.apply(10, -2)); // -20
    }
}
```

##   UnaryOperator<`T`>

UnaryOperator<`T`> принимает в качестве параметра объект типа T, выполняет над ними операции и возвращает результат операций в виде объекта типа T:

```java
public interface UnaryOperator<T> {
    T apply(T t);
}
```

``` java
import java.util.function.UnaryOperator;
 
public class LambdaApp {
 
    public static void main(String[] args) {
         
        UnaryOperator<Integer> square = x -> x*x;
        System.out.println(square.apply(5)); // 25
    }
}
```

Более подробно об интерфейсах можно прочитать в статьях “[Функциональные интерфейсы в Java 8 → Consumer, Supplier, Predicate и Function. Что к чему и зачем нужны](https://habr.com/ru/post/677610/)” и “[Встроенные функциональные интерфейсы](https://metanit.com/java/tutorial/9.3.php)”.
