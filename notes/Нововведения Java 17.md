---
tags: Java 
---
https://skillbox.ru/media/base/chto-novogo-v-java-17/
## Switch-выражения из Java 14

Чтобы получить значение из switch-выражения, раньше приходилось создавать отдельную переменную и постоянно использовать break;. Вот как это выглядело:

```java
String season;
switch (month) {
    case JANUARY:
    case FEBRUARY:
        season = "winter";
        break;
    case MARCH:
    case APRIL:
    case MAY:
        season = "spring";
        break;
    case JUNE:
    case JULY:
    case AUGUST:
        season = "summer";
        break;
    case SEPTEMBER:
    case OCTOBER:
    case NOVEMBER:
        season = "autumn";
        break;
    case DECEMBER:
        season = "winter";
        break;
    default:
        throw new IllegalArgumentException();
    	}  
```

В Java 14 появился новый формат записи, который помогает получать результат выбора и записывать выражение компактнее. Если перечислены все возможные варианты, ветка default теперь не нужна:

``` java
String season = switch (month) {
    case JANUARY, FEBRUARY -> "winter"; //несколько вариантов
    case MARCH, APRIL, MAY -> "spring";
    case JUNE, JULY, AUGUST -> "summer";
    case SEPTEMBER, OCTOBER, NOVEMBER -> "autumn";
    case DECEMBER -> "winter"; //oдин вариант
}
```

Но веткой default можно задать сообщение об ошибке:

``` java
String season = switch (month) {
	case "JAN", "FEB" -> "winter";
	default -> throw new IllegalArgumentException("no such case:" + month);
}
```

Если в значение (case) нужно записать выражение, его заключают в фигурные скобки {} и для возврата значения используют ключевое слово yield:

``` java
String season = switch (month) {
    case JANUARY, FEBRUARY -> "winter";
    case MARCH, APRIL, MAY -> "spring";
    case JUNE, JULY, AUGUST -> "summer";
    case SEPTEMBER, OCTOBER, NOVEMBER -> {
        System.out.println("winter is coming!");
        yield "autumn";
    }
    case DECEMBER -> "winter";
}
```

Switch-выражениям не обязательно возвращать определённое значение:

``` java
switch (order) {
	case NATURAL -> Arrays.sort(strings, Comparator.naturalOrder());
	case REVERSE -> Arrays.sort(strings, Comparator.reverseOrder());
} 
```

Подробнее о switch-выражениях пишут на сайте OpenJDK — [JEP 361: Switch Expressions](https://openjdk.java.net/jeps/361)

## Обновленный InstanceOf
Чтобы проверить, к какому классу относится объект, используют оператор instanceof. Если нужно проверить объект и привести его к нужному виду, раньше объявляли переменную, присваивали ей тип, а затем проверяли объект:

```java
Object string = "this is string!";

if(string instanceof String){
   String realString = (String) string;
   System.out.println(realString);
}
```

Начиная с Java 16 присвоение не требуется. Значение переменной можно задать прямо в выражении:

```java
if(string instanceof String realString){
   System.out.println(realString);
}
```


Подробности: [JEP 394: Pattern Matching for instanceof](https://openjdk.java.net/jeps/394).
## Класс record
Класс record — одна из самых упоминаемых фич новой версии Java. Она позволяет быстро писать иммутабельные POJO-классы, с ней не нужно повторять одинаковые методы: геттеры, toString(), equals() и hashCode().

Напишем старым методом класс Student c двумя полями — именем и названием факультета:
```java
public class Student {
   private final String name;
   private final CourseType courseType;

   public Student(String name, CourseType courseType) {
       this.name = name;
       this.courseType = courseType;
   }

   public String getName() {
       return name;
   }

   public CourseType getCourseType() {
       return courseType;
   }

   @Override
   public boolean equals(Object o) {
       if (this == o) {
           return true;
       }
       if (o == null || getClass() != o.getClass()) {
           return false;
       }
       Student student = (Student) o;
       return name.equals(student.name) && courseType == student.courseType;
   }

   @Override
   public int hashCode() {
       return Objects.hash(name, courseType);
   }

   @Override
   public String toString() {
       return "Student{" +
              "name='" + name + '\'' +
              ", courseType=" + courseType +
              '}';
   }
}
```

У класса всего два поля, но много бойлерплейтного кода.

До того как в Java появился класс record, нам помогал плагин и библиотека [Lombok](https://projectlombok.org/) — она позволяет указать аннотациями, какие методы нужно сгенерировать для класса на этапе компиляции. С ней класс может выглядеть так:
```java
@Data
@RequiredArgsConstructor
public class Student {
    private final String name;
    private final CourseType courseType;
}
```
@Data генерирует геттеры, сеттеры, equals(), hashCode() и toString(). @RequiredArgsConstructor создаёт конструктор с итоговыми параметрами класса и добавляет аргументы в порядке объявления. Такая запись намного компактнее, но это даётся ценой лишней зависимости и плагина для среды разработки.
>[!Note]
>Класс record избавляет от бойлерплейтного кода. Для этого не нужны внешние плагины, достаточно встроенных возможностей языка. Чтобы создать класс, нужно указать только два поля — конструктор, геттеры, equals(), hashCode() и toString() уже включены в класс.

```java
public record Student(String name, CourseType courseType) {}
```

### Особенности класса record
У класса record есть ограничения и особенности:

- все объявленные поля получают модификатор final;
- все поля класса объявляются в заголовке, дополнительные объявить нельзя:
```java
//ошибка компиляции:
public record Student(String name, CourseType courseType) {
	private int id;
}
```
- Можно объявлять static-поля класса;
- класс record неявно объявлен как final, поэтому его нельзя наследовать;
- он не может быть абстрактным и наследовать другие классы;
- можно добавлять свои конструкторы;
- для конструктора можно использовать проверку аргументов;
- можно переопределить стандартные методы — геттеры, toString(), equals() и hashCode();
- можно добавлять статические и нестатические методы.

## Sealed классы (запечатанные)
Sealed class дословно переводится как «запечатанный класс». В этом классе нужно сразу объявить список классов-наследников, потому что кроме них наследников быть не может. Это похоже на enum, только в разрезе наследования.

Посмотрим, как это выглядит в коде:

`   sealed class Person permits Student, Teacher, Curator {}   `

Классы Student, Teacher и Curator должны быть в том же пакете или модуле, что и Person. Кроме этого, у них обязательно должен быть один из модификаторов:

- final, если класс запрещён к дальнейшему наследованию:

`   public final class Student extends Person{}   `

- sealed, если наследование допустимо, но с заранее указанным списком наследников:

`   public sealed class Teacher extends Person permits MathTeacher, LanguageTeacher {}   `

- non-sealed, когда для класса нужно снять любые ограничения по наследованию:

`   public non-sealed class Curator extends Person {}   `

У интерфейсов тоже может быть модификатор sealed:
```java
sealed interface Person
   permits Student, Teacher, Curator {
}
```
С учётом record мы можем имплементировать интерфейс и записать класс Student:

`   public record Student(String name) implements Person{}   `

Здесь record по умолчанию final, поэтому ограничения класса sealed соблюдены.

>[!Note]
>Запечатанные классы помогают установить ограничение на число наследников, когда их набор определён и его не собираются часто менять. Это похоже на перечисление (enum), но sealed class гибче, потому что одни ветки наследования можно открыть для расширения, а другие ограничить только для использования.

## GC
 [В Java 11 и java 17по умолчанию используется сборщик мусора **G1GC (Garbage First Garbage Collector)**](https://learn.microsoft.com/ru-ru/java/openjdk/reasons-to-move-to-java-11)
В java 17 также доступен экспериментальный GC - ZGC. [Можно включить ZGC, добавив параметр `-XX:+UseZGC` при запуске программы](https://habr.com/ru/companies/dcmiran/articles/578300/)
