---
tags: Java/Обобщения
--- 
Это такие generic-типы, которые используются без задания типа-параметра. 
![[Сырые типы Raw type.png]]
Данная конструкция допустима, но часто приводит к появлению `warning` от компилятора. Это связано с риском возникновения ситуации, которая называется **heap pollution**, это когда переменная, относящаяся к параметризированному типу, начинает ссылаться на непараметризированный объект. 

>[!NOTE]
>Применение raw type, в общем случае, недопустимо. Их поддержка в Java существует исключительно для поддержания совместимости с ранними версиями.

Оправдать применение обобщённого типа без параметров оправдано только в таких случаях:

-   Используется Java 5 или более ранняя, выпущенная до 2002 года.
-   В литерале class. Когда List<`String`>.class не выполняется, тогда требуется писать List.class.


``` java 
public class Box<T> {
    public void set(T t) { /* ... */ }
    // ...
}
```

Чтобы создать объект класса Box<`T`>, нужно указать каким типом он параметризован (Integer)

`Box<Integer> intBox = new Box<>();`

Если тип опущен, то это объект будет иметь сырой тип данных

`Box rawBox = new Box();`

Для обратной совместимости присваивание параметризованного типа его необработанному типу разрешено:

```java
    //Norm
    public static <T1, T2> Pair<T1, T2> of(T1 value1, T2 value2) {
        return new Pair<>(value1, value2);
    }
    //Raw
        public static <T1, T2> Pair<T1, T2> of(T1 value1, T2 value2) {
        return new Pair(value1, value2);
    }
```
 Источник [Raw Types (The Java™ Tutorials > Learning the Java Language > Generics (Updated))](https://docs.oracle.com/javase/tutorial/java/generics/rawTypes.html)
