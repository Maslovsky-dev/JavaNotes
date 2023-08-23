---
tags: Java/Многопоточность
--- 
Race Condition (состояние гонки) в Java - это ситуация, когда результат выполнения программы зависит от того, в каком порядке выполняются потоки или операции, и этот порядок не определен. В результате, возникают непредсказуемые и нежелательные результаты, такие как неправильные значения переменных, неконсистентное состояние данных или исключения.

Race Condition возникает, когда два или более потока одновременно обращаются к общему ресурсу и выполняют над ним некоторые операции, в результате которых возникают проблемы. Примеры ресурсов, к которым могут возникать состояния гонки, включают общие переменные, файлы, сетевые соединения и т.д.

## Пример race condition
```java
public class Counter {
    private int count = 0;

    public void increment() {
        count = count + 1;
    }

    public void decrement() {
        count = count - 1;
    }

    public int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) {
        Counter counter = new Counter();

        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.decrement();
            }
        });

        thread1.start();
        thread2.start();

        try {
            thread1.join();
            thread2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Результат: " + counter.getCount());
    }
}

```

В этом примере создается класс `Counter`, который содержит переменную `count`. Два потока, `thread1` и `thread2`, одновременно увеличивают и уменьшают значение `count`. Каждый поток выполняет операцию 1000 раз. Ожидается, что результат будет равен 0, так как каждая операция увеличивает и уменьшает значение на 1. Однако, из-за состояния гонки результат может быть непредсказуемым.

Race Condition возникает из-за неправильной синхронизации или отсутствия синхронизации при доступе к общему ресурсу. Чтобы избежать состояний гонки, необходимо использовать синхронизацию, такие как блокировки (`synchronized`), атомарные операции (`Atomic` классы), мониторы и другие механизмы, чтобы гарантировать правильный порядок доступа к общим ресурсам.

### Возможное исправление
```java
public class Counter {
    private int count = 0;
    private Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count = count + 1;
        }
    }

    public void decrement() {
        synchronized (lock) {
            count = count - 1;
        }
    }

    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
}
```
В этом исправленном примере используется блокировка `synchronized` для синхронизации доступа к общей переменной `count`. Теперь операции инкремента и декремента происходят последовательно, и результат будет всегда предсказуемым.

