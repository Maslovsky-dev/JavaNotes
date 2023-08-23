---
tags: Java/Коллекции
---
[Как работает ConcurrentHashMap / Хабр](https://habr.com/ru/articles/132884/)

Сегменты
volatile

Когда HashMap работает в многопоточной среде, поток захватил монитор. Другие потоки ждут

ConcurrentHashMap блокирует только один сегмент. 
