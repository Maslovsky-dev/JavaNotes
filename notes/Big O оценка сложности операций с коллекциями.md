---
tags: Java/Коллекции, Алгоритмы
---

Какого типа задачи решаем при работе с коллекциями (основные операции)?
- Получение элемента по индексу
- Получение элемента по значению
- Добавление элемента в конец, в середину
- Удаление элемента с конца, середины
![[Big O оценка сложности операций с коллекциями-1.png]]
## Пример задач и оценка их сложности
![[Big O оценка сложности операций с коллекциями-2.png]]

 O(1) означает, что алгоритм выполняется за фиксированное константное в ремя.
[[JPA Hibernate ORM]]Это самые эффективные алгоритмы.
- O(log n) — более эффективный алгоритм. Скорость его выполнения рассчитывается логарифмически, то есть зависит от логарифма n.
- O(n) — это сложность линейных алгоритмов. n здесь и дальше обозначает размер входных данных: чем больше n, тем дольше выполняется алгоритм.
- O(√n) — квадратичный алгоритм, скорость которого зависит от квадратного корня из n. Он менее эффективен, чем логарифмический, но эффективнее линейного.
- O(n²) чем больше n, тем выше сложность. Но зависимость тут не линейная, а квадратичная, то есть скорость возрастает намного быстрее.
Это неэффективные алгоритмы, например с вложенными циклами.
