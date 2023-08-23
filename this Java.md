---
tags: Java/ООП
---

Если параметр конструктора имеет такое же имя как поле класса, то для доступа к полю класса используется префикс this.

This - это ссылка на текущий экземпляр в контексте которого исполняется конструктор или любой другой метод. Обычно, явно писать this не нужно, но в случае конфликта имен поля и локальной переменной или параметра, приходится использовать this
``` java
public class Point {

	public int x = 0;
	public int y = 0;
	//constructor
		public Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
}
```

https://comaqa.gitbook.io/java-automation/oop-v-java/klyuchevoe-slovo-this
## Использование this() в конструкторе
Вариант использования ключевого слова _this() -_ с его помощью можно вызвать один конструктор из другого. Вызов _this()_ может находиться только в первой строчке конструктора:
```java
public class Toy {
    String name;
    int cost;
    String manufacturer;
    int age;

    public Toy(String name, int cost, String manufacturer, int age) {
        this(name, cost, manufacturer);
        this.age = age;
        System.out.println("В конструкторе с четырьмя параметрами");
    }

    public Toy(String name, int cost, String manufacturer) {
        this();
        this.name = name;
        this.cost = cost;
        this.manufacturer = manufacturer;
        System.out.println("В конструкторе с тремя параметрами");
    }

    public Toy() {
        System.out.println("В конструкторе по умолчанию");
    }
}
```

## Ссылка на объект из которого вызван метод
Иногда будет требоваться, чтобы метод ссылался на вызвавший его объект. Чтобы это было возможно, в Java определено ключевое слово **this**. Оно может использоваться внутри любого метода для ссылки на текущий объект. **То есть this всегда служит ссылкой на объект, для которого был вызван метод**. Ключевое слово this можно использовать везде, где допускается ссылка на объект типа текущего класса.
