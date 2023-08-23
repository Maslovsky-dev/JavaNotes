---
tags: Java/Коллекции
---
1. **Обработка null ключей:** `TreeMap` поддерживает `null` значения в качестве ключей, но с некоторыми ограничениями:
    - Если вы используете естественный порядок (без явно заданного компаратора), то `TreeMap` не допускает `null` ключей. Попытка вставить `null` ключ приведет к `NullPointerException`.

    - Если вы используете компаратор, который допускает `null`, то `TreeMap` сможет работать с `null` ключами.

2. **Обработка null значений:** `TreeMap` допускает `null` значения в качестве значений. Вы можете вставлять и извлекать `null` значения из `TreeMap` без проблем.


Пример использования `TreeMap` с компаратором, который допускает `null` ключи:

```java
import java.util.*;

public class TreeMapExample {
    public static void main(String[] args) {
        // Создание TreeMap с компаратором, допускающим null
        TreeMap<String, Integer> treeMap = new TreeMap<>(Comparator.nullsFirst(Comparator.naturalOrder()));

        treeMap.put("apple", 5);
        treeMap.put("banana", 3);
        treeMap.put(null, 10); // Допустимо
        treeMap.put("cherry", null); // Допустимо

        System.out.println(treeMap); // Вывод: {null=10, apple=5, banana=3, cherry=null}
    }
}

```
