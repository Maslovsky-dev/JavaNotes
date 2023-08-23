---
tags: Java/Многопоточность
---
Фреймворк **Fork/Join**, представленный в JDK 7 – это набор классов и интерфейсов, позволяющих использовать преимущества многопроцессорной архитектуры современных компьютеров.

Он разработан для выполнения задач, которые можно рекурсивно разбить на
маленькие подзадачи, которые можно решать параллельно.

Этап **Fork**: большая задача разделяется на несколько меньших подзадач, которые в свою очередь также разбиваются на меньшие. И так до тех пор, пока задача не становится тривиальной и решаемой последовательным способом.

Этап **Join**: далее (опционально) идёт процесс «свёртки», т.е. решения подзадач некоторым образом объединяются пока не получится решение всей задачи.
Решение всех подзадач (в т.ч. и само разбиение на подзадачи) происходит параллельно.
![[Fork Join схема.png]]

>[!Success] Преимущество этого фреймворка в том, что он использует work- stealing алгоритм
>Потоки, которые завершили выполнение собственных подзадач, могут «украсть» подзадачи у других потоков, которые всё ещё заняты.


Пример:
```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

public class ForkJoinExample extends RecursiveTask<Integer> {
    private static final int THRESHOLD = 5;
    private final int[] array;
    private final int start;
    private final int end;

    public ForkJoinExample(int[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        if (end - start <= THRESHOLD) {
            // Выполнение задачи напрямую, если размер подзадачи мал
            int sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        } else {
            // Разбиение задачи на подзадачи и их параллельное выполнение
            int middle = (start + end) / 2;
            ForkJoinExample leftTask = new ForkJoinExample(array, start, middle);
            ForkJoinExample rightTask = new ForkJoinExample(array, middle, end);

            leftTask.fork();  // Запуск левой подзадачи асинхронно
            int rightResult = rightTask.compute();  // Выполнение правой подзадачи синхронно
            int leftResult = leftTask.join();  // Ожидание и получение результата левой подзадачи

            return leftResult + rightResult;
        }
    }

    public static void main(String[] args) {
        int[] array = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinExample task = new ForkJoinExample(array, 0, array.length);
        int result = forkJoinPool.invoke(task);

        System.out.println("Сумма элементов массива: " + result);

        forkJoinPool.shutdown();
    }
}

```
