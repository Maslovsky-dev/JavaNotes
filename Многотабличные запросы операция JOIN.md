---
tags: SQL
---
[Многотабличные запросы, оператор JOIN](https://sql-academy.org/ru/guide/multi-table-request-join)
## Общая структура многотабличного запроса

```sql
SELECT поля_таблиц
FROM таблица_1
[INNER] | [[LEFT | RIGHT | FULL][OUTER]] JOIN таблица_2
    ON условие_соединения
[INNER] | [[LEFT | RIGHT | FULL][OUTER]] JOIN таблица_n
    ON условие_соединения]
```

Соединение бывает:

- внутренним INNER (по умолчанию)
- внешним OUTER, при этом внешнее соединение делится на левое LEFT, правое RIGHT и полное FULL

Запрос с внутренним соединением, выглядит следующим образом

```sql
SELECT family_member, member_name, amount * unit_price AS price FROM Payments
INNER JOIN FamilyMembers ON Payments.family_member = FamilyMembers.member_id
```

|family_member|member_name|price|
|---|---|---|
|1|Headley Quincey|2000|
|2|Flavia Quincey|2100|
|3|Andie Quincey|100|
|4|Lela Quincey|350|
|4|Lela Quincey|300|
|5|Annie Quincey|100|
|2|Flavia Quincey|120|
|2|Flavia Quincey|5500|
|5|Annie Quincey|230|
|3|Andie Quincey|2200|
|2|Flavia Quincey|66000|
|1|Headley Quincey|40|
|3|Andie Quincey|100|
|3|Andie Quincey|1200|

В данном запросе мы сопоставляем записи из таблицы Payments и записи из таблицы FamilyMembers.

Чтобы сопоставление работало, мы указываем как именно записи из двух разных таблиц должны находить друг друга. Это условие указывается после ON:

```sql
ON Payments.family_member = FamilyMembers.member_id
```

В нашем случае поле family_member указывает на идентификатор в таблице FamilyMembers и таким образом помогает однозначному сопоставлению.

>[!NOTE] В большинстве случаев условием соединения является равенство столбцов таблиц (таблица_1.поле = таблица_2.поле)
>Однако точно так же можно использовать и другие операторы сравнения.

## [[INNER JOIN]]

## [[OUTER JOIN]]

## Что лучше использовать join или подзапросы?
Обычно лучше использовать JOIN, поскольку в большинстве случаев он более понятен и лучше оптимизируется СУБД (но 100% этого гарантировать нельзя).

Так же JOIN имеет заметное преимущество над подзапросами в случае, когда список выбора SELECT содержит столбцы более, чем из одной таблицы.

Подзапросы лучше использовать в случаях, когда нужно вычислять агрегатные значения и использовать их для сравнений во внешних запросах.
