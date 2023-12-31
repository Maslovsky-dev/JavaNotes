---
tags: ORM
---
Стратегии наследования нужны для того, чтобы дать понять провайдеру (Hibernate)
КАК отображать в БД сущности-наследники. Для этого нужно декорировать
родительский класс аннотацией @Inheritance и указать один из типов отображения: SINGLE_TABLE, TABLE_PER_CLASS, JOINED.
![[Стратегии маппинга иерархии при наследовании.png]]
## TABLE_PER_CLASS (отдельная таблица для каждого конкретного класса сущностей)
Каждый класс-наследник имеет свою таблицу.
Во всех таблицах подклассов хранятся все поля этого класса плюс те, которые унаследованы от суперкласса.
![[Table per class стратегия.png]]
![[Применение стратегии Table per class.png]]
### Плюсы и минусы
**Плюсы**
Если нужно получить отдельную сущность, то можно обратиться к соответствующей таблице (не нужно искать во всех таблицах).
**Минусы**
Плохая поддержка полиморфизма (polymorphic relationships), т.к. для выборки всех классов иерархии потребуется большое количество отдельных sql-запросов для каждой
таблицы-наследника или использование UNION запроса для соединения таблиц всех
наследников в одну таблицу.

Повторение одних и тех же атрибутов в таблицах. При TABLE PER CLASS не работает стратегия генератора первичных ключей IDENTITY, поскольку может быть несколько объектов подкласса, имеющих один и тот же идентификатор, и запрос базового класса приведет к получению объектов с одним и тем же идентификатором (даже если они принадлежат разным типам).
### Сохранение и загрузка данных
![[Сохранение и загрузка данных table per class.png]]

## SINGLE_TABLE (одна таблица на всю иерархию классов)
![[Single Table стратегия.png]]
Например, если есть entity Employee c классами-потомками FullTimeEmployee и PartTimeEmployee, то при такой стратегии все FullTimeEmployee и PartTimeEmployee записываются в таблицу Employee, и при этом в таблице появляется дополнительная колонка с именем DTYPE, в которой будут записаны значения, определяющие принадлежность к классу.

>[!note]
>По умолчанию эти значения формируются из имён классов, в этом примере либо «FullTimeEmployee», либо «PartTimeEmployee». Но можно их поменять в аннотации у каждого класса-наследника: **@DiscriminatorValue("F")**. Если хотим поменять **имя колонки**, то необходимо указать её новое имя в параметре аннотации у класса-родителя:
**@DiscriminatorColumn(name=EMP_TYPE)**.

![[Применение стратегии Single table.png]]
>[!note]
>Эта стратегия обеспечивает хорошую поддержку полиморфных отношений между
сущностями и запросами, которые охватывают всю иерархию классов сущностей:

### Сохранение и загрузка данных
![[Сохранение и загрузка данных стратегии Single Table.png]]

## JOINED (стратегия соединения)
В этой стратегии корневой класс иерархии представлен отдельной таблицей, а каждый наследник имеет свою таблицу, в которой отображены только поля этого класса-наследника.

То есть таблица подкласса не содержит столбцы для полей, унаследованных от родительского класса, за исключением поля для первичного ключа @Id, который должен быть определен только в родительской таблице.
![[Joined стратегия.png]]
>[!note]
>**Столбец первичного ключа** в таблице подкласса служит внешним ключом первичного ключа таблицы суперкласса. Также в **таблице родительского класса** добавляется столбец DiscriminatorColumn с DiscriminatorValue **для определения типа наследника**.

![[Применение JOINED стратегии соединения.png]]
>[!warning]
>Эта стратегия обеспечивает **хорошую поддержку полиморфных отношений**, но
требует выполнения одной или нескольких операций соединения таблиц при создании экземпляров подклассов сущностей. В глубоких иерархиях классов это **может привести к
недопустимому снижению производительности**. Точно так же запросы, которые
покрывают всю иерархию классов, требуют операций соединения между таблицами подклассов, что приводит к снижению производительности:

### Сохранение и загрузка данных
![[Сохранение и загрузка данных Joined стратегии.png]]
