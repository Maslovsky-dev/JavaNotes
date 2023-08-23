---
tags: SQL
--- 
[Исключение дубликатов, оператор DISTINCT](https://sql-academy.org/ru/guide/distinct-operator)
Синтаксис оператора

``` sql
SELECT [DISTINCT] поля_таблиц FROM наименование_таблицы;
```

Возвращает уникальные значения поля таблицы.

## Для нескольких полей
При использовании оператора DISTINCT для двух и более колонок будут удаляться записи, которые имеют одинаковые значения по всем полям.

То есть для такой таблицы

|first_name|last_name|
|---|---|
|John|Scott|
|William|Dawson|
|Raul|Hartman|
|William|Hartman|
|John|Scott|
|John|Hartman|
запрос с оператором DISTINCT вернул бы все сочетания имён и фамилий кроме дублирующихся «John Scott».
```sql
SELECT DISTINCT first_name, last_name FROM User;
```

| first_name | last_name |
|------------|-----------|
| John       | Scott     |
| William    | Dawson    |
| Raul       | Hartman   |
| William    | Hartman   |
| John       | Hartman   |


[[Теория множеств]]
