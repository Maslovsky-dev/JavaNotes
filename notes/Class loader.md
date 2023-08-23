---
tags: Java/ПодКапотом
---
**Загружает скомпилированные классы в JVM**
![[Компиляция и загрузка классов.png]]
[Java Interview Questions Made Easy What is the Classloader - YouTube](https://www.youtube.com/watch?v=J_vAwYpRbhs)

Рассмотрим приложение состоящее из двух классов. Эти 2 класса сначала компилируются в файлы A.class и B.class (байт-код). Далее виртуальной машине Java нужно определить как загрузить эти классы в основную память и как их выполнять. На этом этапе подключается ClassLoader.

ClassLoader необходим для загрузки .class файлов в JVM **во время выполнения программы**

Существуют разные классы:
- Которые мы написали для приложения
- Системные классы, встроенные в Java
## Порядок работы загрузчиков
1. Bootstrap Class Loader
2. Extension Class Loader
3. System/Application Class Loader

Они работают по принципу делегирования: каждый загрузчик класса сначала делегирует загрузку класса своему родительскому загрузчику. Только если родительский загрузчик не может загрузить класс, текущий загрузчик пытается загрузить его сам.

**Bootstrap Class Loader**

Это самый верхний загрузчик классов в иерархии загрузчиков классов. Он не является частью Java Runtime Environment (JRE), а написан на нативном коде для выполнения JVM. Он загружает стандартные классы JDK из файла rt.jar и другие необходимые классы для JVM [geeksforgeeks.org](https://www.geeksforgeeks.org/classloader-in-java/).

**Extension Class Loader**

Extension Class Loader является дочерним загрузчиком классов для Bootstrap Class Loader. Он загружает классы из расширений ядра Java из соответствующей библиотеки расширений JDK. Он загружает файлы из каталога jre/lib/ext или любого другого каталога, указанного в системном свойстве java.ext.dirs [geeksforgeeks.org](https://www.geeksforgeeks.org/classloader-in-java/).

**System/Application Class Loader**

Это загрузчик классов приложения, также известный как System Class Loader. Он загружает классы приложений из переменной среды CLASSPATH, -classpath или параметра командной строки -cp. Application Class Loader является дочерним классом Extension Class Loader [geeksforgeeks.org](https://www.geeksforgeeks.org/classloader-in-java/).

Пример кода, который иллюстрирует, какие загрузчики классов загружают разные классы:

```java
public void printClassLoaders() throws ClassNotFoundException {
    System.out.println("Classloader of this class:"
        + PrintClassLoader.class.getClassLoader());
    System.out.println("Classloader of DriverManager:"
        + DriverManager.class.getClassLoader());
    System.out.println("Classloader of ArrayList:"
        + ArrayList.class.getClassLoader());
}

```

Результат:

```
Classloader of this class:[email protected] //Метод toString не переопределен
Classloader of DriverManager:[email protected]
Classloader of ArrayList:null
```

В данном примере для класса, где содержится пример метода, загружается загрузчик классов приложения. Загрузчик классов расширения загружает класс DriverManager. И, наконец, загрузчик классов bootstrap загружает класс ArrayList. Загрузчик классов **bootstrap отображается как null**, потому что он написан на нативном коде, а не на Java, поэтому он не отображается как класс Java [baeldung.com](https://www.baeldung.com/java-classloaders).
