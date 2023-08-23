---
tags: SQL
--- 
## [Понятие внутреннего соединения](https://sql-academy.org/ru/guide/inner-join#ponyatie-vnutrennego-soedineniya)

Внутреннее соединение — соединение, при котором находятся пары записей из двух таблиц, удовлетворяющие условию соединения, тем самым образуя новую таблицу, содержащую поля из первой и второй исходных таблиц.

![Схема внутреннего соединения](https://sql-academy.org/static/guidePage/inner-join/inner-join-example.png "Схема внутреннего соединения")

Так как в нашем условии указано равенство полей Payments.good_id и Goods.good_id, то при внутреннем соединении в итоговой выборке окажутся только записи, где в обоих таблица есть одинаковое значение good_id.

##  [Использование WHERE для соединения таблиц](https://sql-academy.org/ru/guide/inner-join#ispolzovanie-where-dlya-soedineniya-tablic)

Для внутреннего соединения таблиц также можно использовать оператор WHERE. Например, вышеприведённый запрос, написанный с помощью INNER JOIN, будет выглядеть так:

```sql
SELECT family_member, member_name FROM Payments, FamilyMembers
    WHERE Payments.family_member = FamilyMembers.member_id
```

|family_member|member_name|
|---|---|
|1|Headley Quincey|
|2|Flavia Quincey|
|3|Andie Quincey|
|4|Lela Quincey|
|4|Lela Quincey|
|5|Annie Quincey|
|2|Flavia Quincey|
|2|Flavia Quincey|
|5|Annie Quincey|
|3|Andie Quincey|
|2|Flavia Quincey|
|1|Headley Quincey|
|3|Andie Quincey|
|3|Andie Quincey|
