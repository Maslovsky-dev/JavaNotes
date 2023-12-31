---
tags: SQL
---
## Основные показатели производительности SQL запроса
Для определения непроизводительных SQL запросов можно использовать различные инструменты анализа и профилирования, такие как планы выполнения, индексы, статистика и др .  При анализе стоит обращать внимание на следующие показатели:

- **Время выполнения.** Это означает, сколько времени занимает выполнение SQL запроса от начала до конца. Время выполнения зависит от многих факторов, таких как сложность запроса, объем данных, настройки базы данных, нагрузка на сервер и др.

- **Использование ресурсов.** Это означает, сколько ресурсов (таких как ЦП, память, диск, сеть и другие) потребляет SQL запрос во время его выполнения. Использование ресурсов влияет на производительность не только самого запроса, но и всей системы.

-  **План выполнения.** Это означает, как SQL сервер оптимизирует и выполняет SQL запрос. План выполнения показывает логическую и физическую структуру запроса, а также статистику по каждому шагу выполнения. План выполнения помогает понять, как SQL сервер работает с данными, какие операторы и индексы использует, какие ограничения и предупреждения возникают и другие.

Анализируя эти показатели, можно выявить проблемные места в SQL запросах и искать способы их улучшения или оптимизации.
### Анализ производительности SQL запросов
Для автоматизации процесса поиска неэффективных SQL запросов в Java приложениях разработано решение **SQLInspect**, с помощью которого извлекаются SQL-запросы из Java-кода через статический анализ строк. SQLInspect разбирает извлеченные запросы и проводит различные анализы. В результате можно исследовать исходный код, который обращается к определенной части базы данных или отвечает за создание конкретного SQL-запроса. Также SQLInspect использует метрики, связанные с SQL, и общие ошибки кодирования, чтобы выявлять неэффективные или дефективные SQL-запросы и идентифицировать плохо спроектированные классы (которые создают множество запросов через сложные пути управления).

Оптимизировать запросы можно несколькими подходами. В работе [1] анализируются методы, связанные с синтаксисом формирования SQL команд, включающие в себя использование псевдонимов для таблиц, учет особенностей условий поиска, анализ предложений DISTINCT, оценку источников данных и методов выбора записей, проверку операторов реляционной алгебры, и предлагают выбор оптимальных способов проектирования запросов. Для автоматизации этого процесса предлагается оптимизатор SQL запросов [2]. Структура предложенного оптимизатора включает три основных компонента: лексер, парсер и анализатор. На вход подается SQL-запрос, который оптимизатор обрабатывает последовательно через эти компоненты. Сначала парсер разбирает запрос и создает дерево, разделяя его на элементы (теги) с использованием словаря, определенного в лексере. Это необходимо для того, чтобы оптимизатор мог анализировать структуру SQL-запроса. Затем анализатор определяет места, которые могут быть подвергнуты оптимизации. Здесь оптимизация означает реструктуризацию дерева с помощью парсера и применение различных улучшений. В результате работы оптимизатора, пользователь, который его использовал, получает улучшенный SQL-запрос.
### Индексы и производительность
Помимо проблем, связанными с синтаксисом команд, стоит также рассмотреть проблему применения индексов: недостающие или избыточные индексы. В работе [3] выделены основные рекомендации по применению индексов в РБД:

- кластеризованные индексы лучше всего подходят для интервальных выборок и упорядоченных запросов, а некластеризованные индексы - для поиска одного объекта (строки);

- индексы с низкой селективностью (процент строк в таблице, которые соответствуют индексу) менее эффективны и могут быть проигнорированы оптимизатором запросов;

- при создании индекса следует учитывать наиболее часто используемые комбинированные условия и транзакции в базе данных;

- рекомендуется создавать индексы на внешние ключи;

- для редких запросов и отчетов пользователей рекомендуется создавать временные индексы;

## Как оценить сложность выполняемого запроса?

Стоимостной оптимизатор делает расчёт на основании двух показателей:
- **page_cost**, измеряется в единицах стоимости 1.0 – сколько страниц нужно считать для выполнения одного запроса (input-output).
- **сpu_cost** – как много операций сделал процессор для выполнения запроса.
Например, прошлись по строчке, считали информацию одной записи в таблице.
Стоимость будет 0,01.

В PostgreSQL есть специальная таблица **pg_class**, которая хранит статистические данные, на основании которых базируется работа стоимостного оптимизатора.

## Анализ выполнения запроса (если есть проблемы с производительностью)
![[EXPLAIN операция sql.png]]
![[Пример выполнения EXPLAIN sql.png]]
- [ ] что выдает explain
## Как узнать, сколько места занимает таблица в памяти?

Команда `SHOW TABLE STATUS`: В MySQL или MariaDB вы можете выполнить команду `SHOW TABLE STATUS`, указав имя таблицы, чтобы получить информацию о таблице, включая размер данных и индексов. В столбце `Data_length` будет указан размер данных таблицы, а в столбце `Index_length` - размер индексов таблицы. Сумма этих двух значений даст общий размер таблицы в байтах.

## Методы сканирования таблиц

![[Методы сканирования таблиц.png]]

## Что такое индексы?
[[Индексы SQL]]

## Хранимые процедуры
Хранимые процедуры позволяют повысить производительность, расширяют возможности программирования и поддерживают функции безопасности данных. В большинстве СУБД при первом запуске хранимой процедуры она компилируется (выполняется синтаксический анализ и генерируется план доступа к данным) и в дальнейшем её обработка осуществляется быстрее.

Хранимая процедура — объект базы данных, представляющий собой набор SQL-инструкций, который хранится на сервере. Хранимые процедуры очень похожи на обыкновенные процедуры языков высокого уровня, у них могут быть входные и выходные параметры и локальные переменные, в них могут производиться числовые вычисления и операции над символьными данными, результаты которых могут присваиваться переменным и параметрам. В хранимых процедурах могут выполняться стандартные операции с базами данных (как DDL, так и DML). Кроме того, в хранимых процедурах возможны циклы и ветвления, то есть в них могут использоваться инструкции управления процессом исполнения.

Хранимые процедуры позволяют повысить производительность, расширяют возможности программирования и поддерживают функции безопасности данных. В большинстве СУБД при первом запуске хранимой процедуры она компилируется (выполняется синтаксический анализ и генерируется план доступа к данным) и в дальнейшем её обработка осуществляется быстрее.

## Триггер (trigger)
Это хранимая процедура особого типа, которую пользователь не вызывает непосредственно, а исполнение которой обусловлено действием по модификации данных: добавлением, удалением или изменением данных в заданной таблице реляционной базы данных. Триггеры применяются для обеспечения целостности данных и реализации сложной бизнес-логики.

Триггер **запускается сервером автоматически** и все производимые им модификации данных рассматриваются как выполняемые в транзакции, в которой выполнено действие, вызвавшее срабатывание триггера. Соответственно, в случае обнаружения ошибки или нарушения целостности данных может произойти откат этой транзакции.

Момент запуска триггера определяется с помощью ключевых слов **BEFORE** (триггер запускается до выполнения связанного с ним события) или **AFTER** (после события). В случае, если триггер вызывается до события, он может внести изменения в модифицируемую событием запись.

Триггеры могут быть привязаны не к таблице, а к представлению (**VIEW**). В этом случае с их помощью реализуется механизм «обновляемого представления». В этом случае ключевые слова BEFORE и AFTER влияют лишь на последовательность вызова триггеров, так как собственно событие (удаление, вставка или обновление) не происходит.


## Источники
1. [Петрова А. Н., Калмыков И. Н. Исследование рекомендаций по устранению причин неэффективности запросов таблицам базы данных // Вестник евразийской науки. 2016. № 3 (8). C. 137.](https://cyberleninka.ru/article/n/issledovanie-rekomendatsiy-po-ustraneniyu-prichin-neeffektivnosti-zaprosov-tablitsam-bazy-dannyh)
2. [Третьяков И. А., Кожекина Е. Н., Журавлёв И. В. Оптимизация SQL-запросов // Вестник Донецкого национального университета. Серия Г: Технические науки. 2021. № 2. C. 39–49.](https://elibrary.ru/item.asp?id=46179212)
3. [Corlăţan C. G. [и др.]. Query Optimization Techniques in Microsoft SQL Server. // Database Systems Journal. 2014. № 2 (5).](http://dbjournal.ro/archive/16/16_4.pdf)