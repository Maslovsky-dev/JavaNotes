---
tags: Java/ПодКапотом
---

https://itsobes.ru/JavaSobes/kakie-v-java-byvaiut-vidy-ssylok/

**Обычная жесткая ссылка** – любая переменная ссылочного типа. Очистится сборщиком мусора не раньше, чем станет неиспользуемой (перестанет быть доступной из [GC roots](https://stackoverflow.com/q/27186799/5521491)). Ключевое слово **new**

**SoftReference** – мягкая ссылка. Объект не станет причиной израсходования всей памяти – **гарантированно будет удален до** возникновения `OutOfMemoryError`. Может быть раньше, зависит от реализации сборщика мусора. Может быть удален сборщиком мусора, даже если по крайней мере одна слабая ссылка на него все еще существует. Слабые ссылки используются для ссылок на объекты, которые не должны существовать долгое время. Например, на объекты, которые используются только для временного хранения данных.

**WeakReference** – слабая ссылка. GC удаляет сразу же как только видит объект

**PhantomReference** – фантомная ссылка. Ссылка, которая не позволяет получить доступ к ссылаемому объекту после того, как он был собран сборщиком мусора. Объект, на который имеется фантомная ссылка, доступен только через очередь ссылок. Фантомные ссылки используются для того, чтобы получать уведомления о том, что объект был собран сборщиком мусора, например, для завершения работы с объектом или освобождения ресурсов.


Особенности каждого типа ссылок связаны с работой [[Garbage collector]]

## Примеры

## WeakReference

```java
import java.lang.ref.WeakReference;
import java.util.HashMap;
import java.util.Map;

public class WeakReferenceExample {
    public static void main(String[] args) {
        // Создаем словарь для кэширования данных с использованием WeakReference
        Map<String, WeakReference<String>> cache = new HashMap<>();

        // Помещаем данные в кэш с использованием слабой ссылки
        String key = "dataKey";
        String data = "Some cached data";
        cache.put(key, new WeakReference<>(data));

        // Пробуем получить данные из кэша
        WeakReference<String> weakRef = cache.get(key);
        if (weakRef != null) {
            String cachedData = weakRef.get();
            if (cachedData != null) {
                System.out.println("Data from cache: " + cachedData);
            } else {
                System.out.println("Cached data has been collected by the garbage collector.");
            }
        } else {
            System.out.println("Data not found in cache.");
        }

        // Очищаем ссылку на данные
        cache.remove(key);

        // Запускаем сборщик мусора
        System.gc();

        // Пробуем снова получить данные из кэша
        String cachedDataAfterGC = weakRef.get();
        if (cachedDataAfterGC != null) {
            System.out.println("Data from cache after GC: " + cachedDataAfterGC);
        } else {
            System.out.println("Cached data has been collected by the garbage collector.");
        }
    }
}

```
