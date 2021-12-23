# Postgres_HW
# Домашняя работа по дисциплине "Проектирование и сопровождение баз данных PostgreSQL "

## Ссылки

- [Домашняя работа 1](#домашняя-работа-1)
- [Домашняя работа 2](#домашняя-работа-2)
- [Домашняя работа 3](#домашняя-работа-3)
- [Домашняя работа 4](#домашняя-работа-4)
- [Домашняя работа 5](#домашняя-работа-5)
- [Домашняя работа 6](#домашняя-работа-6)
- [Домашняя работа 7](#домашняя-работа-7)
- [Домашняя работа 8](#домашняя-работа-8)
- [Домашняя работа 9](#домашняя-работа-9)
- [Домашняя работа 10](#домашняя-работа-10)
- [Домашняя работа 11](#домашняя-работа-11)

## Домашняя работа 1

<details>
<summary>Сама работа</summary>

---

### Задание

Подумать над выбором предметной области для выполнения финальной (экзаменационной) работы.
Выбирайте предметную область, которая вам интересна
и в которой вы разбираетесь или хотите разобраться.

Сделать краткое описание выбранной предметной области (1-2 страницы).
Если описание получится более объемным, не беда.
Ведь это описание затем войдет в финальный отчет.

Попытаться сформулировать требования к будущей базе данных.

### Работа

#### Цель работы

Описать словесно выбранную предметную область – "".

#### Описание предметной области


[Наверх](#ссылки)

---

</details>

## Домашняя работа 2

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 3 (упражнения 1-4)

### Работа

#### Упражнение 1

Выполним запрос:

```sql
demo=# insert into aircrafts values ('SU9', 'Sukhoi SUperJet-100', 300);
ERROR:  0A000: cannot insert into column "model" of view "aircrafts"
DETAIL:  View columns that are not columns of their base relation are not updatable.
```

Ошибка нам говорит о том, что
колонки, не являющимися базовыми колонками своего отношения не могут обновляться через представление.

Посмотрим, какие колонки не являются базовыми для отношения:

```sql
demo=# \d+ aircrafts
                                       View "bookings.aircrafts"
    Column     |     Type     | Collation | Nullable | Default | Storage  |         Description
---------------+--------------+-----------+----------+---------+----------+-----------------------------
 aircraft_code | character(3) |           |          |         | extended | Aircraft code, IATA
 model         | text         |           |          |         | extended | Aircraft model
 range         | integer      |           |          |         | plain    | Maximal flying distance, km
View definition:
 SELECT ml.aircraft_code,
    ml.model ->> lang() AS model,
    ml.range
   FROM aircrafts_data ml;
```

Такой колонкой является `model`, соответственно.

#### Упражнение 2

Пример запроса:

```sql
demo=# select * from aircrafts order by range desc;
 aircraft_code |        model        | range
---------------+---------------------+-------
 773           | Боинг 777-300       | 11100
 763           | Боинг 767-300       |  7900
 319           | Аэробус A319-100    |  6700
 320           | Аэробус A320-200    |  5700
 321           | Аэробус A321-200    |  5600
 733           | Боинг 737-300       |  4200
 SU9           | Сухой Суперджет-100 |  3000
 CR2           | Бомбардье CRJ-200   |  2700
 CN1           | Сессна 208 Караван  |  1200
(9 rows)
```

#### Упражнение 3

Пример такого запроса:

```sql
demo=# UPDATE aircrafts SET range = range*2 WHERE model LIKE 'Sukhoi SuperJet%';
UPDATE 1
```

#### Упражнение 4

Пример такого запроса:

```sql
demo=# DELETE FROM aircrafts WHERE aircraft_code = '123';
DELETE 0
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 3

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 4 (упражнения 2, 4, 8, 12, 15, 21, 30, 33, 35)

### Работа

#### Упражнение 2

Создадим таблицу, наполним её данными и сделаем выборку по ней.

```sql
demo=# CREATE TABLE test_numeric
( measurement numeric,
description text
);
CREATE TABLE

demo=# insert into test_numeric values
demo-# (1234567890.0987654321, 'Точность 20 знаков, масштаб 10 знаков'),
demo-# (1.5, 'Точность 2 знака, масштаб 1 знак'),
demo-# (0.12345678901234567890, 'Точность 21 знак, масштаб 20 знаков'),
demo-# (1234567890, 'Точность 10 знаков, масштаб 0 знаков (целое число)');
INSERT 0 4

demo=# select * from test_numeric;
      measurement       |                    description
------------------------+----------------------------------------------------
  1234567890.0987654321 | Точность 20 знаков, масштаб 10 знаков
                    1.5 | Точность 2 знака, масштаб 1 знак
 0.12345678901234567890 | Точность 21 знак, масштаб 20 знаков
             1234567890 | Точность 10 знаков, масштаб 0 знаков (целое число)
(4 rows)
```


#### Упражнение 4

Выполним эксперименты с плавающей точкой:

```sql
demo=# select 2e-45::real > 1e-45::real;
 ?column?
----------
 f
(1 row)

demo=# select 4e-324::double precision > 3e-324::double precision;
 ?column?
----------
 f
(1 row)
```

#### Упражнение 8

```sql
demo=# CREATE TABLE test_serial
( id serial PRIMARY KEY,
name text);
CREATE TABLE

demo=# INSERT INTO test_serial (name) VALUES ('Вишневая');
INSERT 0 1

demo=#INSERT INTO test_serial ( id, name ) VALUES ( 2, 'Прохладная' );
INSERT 0 1
```

При выполнении этой команды СУБД выдаст сообщение об ошибке. Почему?

```sql
demo=# INSERT INTO test_serial (name) VALUES ('Грушевая');
ERROR:  23505: duplicate key value violates unique constraint "test_serial_pkey"
DETAIL:  Key (id)=(2) already exists.
```

Потому что автоматическое значение нумерации serial увеличилось на 1 и этот id уже свободен. Повторяющееся значение ключа нарушает ограничение уникальности "test_serial_pkey".

```sql
demo=# INSERT INTO test_serial (name) VALUES ('Грушевая');
INSERT 0 1
demo=# INSERT INTO test_serial (name) VALUES ('Зеленая');
INSERT 0 1
demo=# DELETE FROM test_serial WHERE id = 4;
DELETE 1
demo=# INSERT INTO test_serial (name) VALUES ('Луговая');
INSERT 0 1
```

Теперь сделаем выборку. В нумерации образовалась «дыра». Это из-за того, что при формировании нового значения из последовательности поиск максимального значения, уже имеющегося в столбце, не выполняется.

```sql
demo=# SELECT * FROM test_serial;
 id |    name
----+------------
  1 | Вишневая
  2 | Прохладная
  3 | Грушевая
  5 | Луговая
(4 rows)
```

#### Упражнение 12

```sql
demo=# show datestyle;
 DateStyle
-----------
 ISO, DMY
(1 row)

demo=# set datestyle to 'MDY';
SET

demo=# show datestyle;
 DateStyle
-----------
 ISO, MDY
(1 row)

demo=# SELECT '18-05-2016'::date;
ERROR:  22008: date/time field value out of range: "18-05-2016"
LINE 1: SELECT '18-05-2016'::date;
               ^
HINT:  Perhaps you need a different "datestyle" setting.

demo=# SELECT '05-18-2016'::date;
    date    
------------
 2016-05-18
(1 row)

demo=# SELECT '05-18-2016'::date;
    date    
------------
 2016-05-18
(1 row)

demo=# SET datestyle TO 'Postgres, DMY';
SET

demo=# show datestyle;
   DateStyle
---------------
 Postgres, DMY
(1 row)

demo=# select current_date;
 current_date
--------------
 04-10-2020
(1 row)

demo=# set datestyle to 'SQL, DMY';
SET
demo=# select current_date;
 current_date
--------------
 04/10/2020
(1 row)

demo=# set datestyle to 'German, DMY';
SET
Time: 0.519 ms
demo=# select current_date;
 current_date
--------------
 04.10.2020
(1 row)
```

#### Упражнение 15

Проэкспериментируем с преобразованием даты в строку.

```sql
demo=# SELECT to_char(current_timestamp, 'ddыmm');
 to_char
---------
 04ы10
(1 row)

demo=# SELECT to_char(current_timestamp, 'MM/DD/YYYY');
  to_char
------------
 10/04/2020
(1 row)

demo=# SELECT to_char(current_timestamp, 'DD-MM-YYYY')::date;
  to_char
------------
 2020-10-04
(1 row)
```

#### Упражнение 21

Предположу, что интервал "1 месяц" добавит единицу ко времени месяца.
В первом случае выведет 2016-02-29, а во втором — 2016-03-29.

```sql
demo=# SELECT ('2016-01-31'::date +'1 mon'::interval) AS new_date;
      new_date
---------------------
 2016-02-29 00:00:00
(1 row)

demo=# SELECT ('2016-02-29'::date +'1 mon'::interval) AS new_date;
      new_date
---------------------
 2016-03-29 00:00:00
(1 row)
```

Отлично, я угадал! =)

#### Упражнение 30

Предположения:

```sql
INSERT INTO test_bool VALUES (TRUE, 'yes');
INSERT INTO test_bool VALUES (yes, 'yes');
-- "yes" — невалидный boolean

INSERT INTO test_bool VALUES ('yes', true);
-- "'yes'" — то же самое, но в виде строки.
-- Если в SQL слабая типизация, то ошибка будет как выше.
-- Если сильная — ошибка типа.
-- Ну и true — невалидная строка.

INSERT INTO test_bool VALUES ('yes', TRUE);
-- То же самое, как выше.

INSERT INTO test_bool VALUES ('1', 'true');
-- '1' — вообще строка.
-- Если типизация сильная, ошибка типа.
-- Если типисация слабая, то будет '1' -> 1 -> true, т.е. без ошибки.

INSERT INTO test_bool VALUES (1, 'true');
-- Зависит от силы типизации, см. выше.

INSERT INTO test_bool VALUES ('t', 'true');
-- Типизация, но надо смотреть, является ли t — булевым значением.

INSERT INTO test_bool VALUES ('t', truth);
-- Ошибка со строкой. Надо в кавычки заворачивать.

INSERT INTO test_bool VALUES (true, true);
-- Ошибка со строкой. Надо в кавычки заворачивать.

INSERT INTO test_bool VALUES (1::boolean, 'true');
-- Явное преобразование. Скорее всего, ошибки не будет.

INSERT INTO test_bool VALUES (111::boolean, 'true');
-- Явное преобразование. Скорее всего, ошибка будет,
-- т.к. не понятно, можно ли 111 привести в boolean.
```

Итог:

```sql
demo=# INSERT INTO test_bool VALUES (TRUE, 'yes');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (yes, 'yes');
ERROR:  42703: column "yes" does not exist
LINE 1: INSERT INTO test_bool VALUES (yes, 'yes');
                                      ^

demo=# INSERT INTO test_bool VALUES ('yes', true);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('yes', TRUE);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('1', 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (1, 'true');
ERROR:  42804: column "a" is of type boolean but expression is of type integer
LINE 1: INSERT INTO test_bool VALUES (1, 'true');
                                      ^
HINT:  You will need to rewrite or cast the expression.

demo=# INSERT INTO test_bool VALUES ('t', 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('t', truth);
ERROR:  42703: column "truth" does not exist
LINE 1: INSERT INTO test_bool VALUES ('t', truth);
                                           ^

demo=# INSERT INTO test_bool VALUES (true, true);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (1::boolean, 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (111::boolean, 'true');
INSERT 0 1
```

Вывод: типизация слабая, но неявные преобразования идут через строковые значения.
Целые числа надо преобразовывать явно.

#### Упражнение 33

```sql
demo=# CREATE TABLE food(food text[][]);
CREATE TABLE
demo=# insert into food values ('{ { "сосиска", "макароны", "кофе" },{ "котлета", "каша", "кофе" },{ "сосиска", "каша", "кофе" },{ "котлета", "каша", "чай" } }'::text[][]);
demo=# select food[1][1] from food;
  food
---------
 сосиска
(1 row)
```

#### Упражнение 35

```sql
demo=# SELECT '[{"мяч":"футбол"},{"бита":"бейсбол"},{"ракетка":"тенис"}]'::json -> 2;
      ?column?
---------------------
 {"ракетка":"тенис"}
(1 строка)


demo=#
demo=# SELECT '[{"мяч":"футбол"},{"бита":"бейсбол"},{"ракетка":"тенис"}]'::json -> 'мяч';
 ?column?
----------

(1 строка)


demo=#
demo=# SELECT '["мяч","бита","ракетка"]'::json ->> 0;
 ?column?
----------
 мяч
(1 строка)


demo=#
demo=# SELECT '{"мяч":"футбол","бита":"бейсбол","ракетка":"тенис"}'::json ->> 'бита';
 ?column?
----------
 бейсбол
(1 строка)


demo=#
demo=# SELECT '{"игры": {"компьютерные": ["dark","souls"], "спортивные":4}}'::json #> '{игры,компьютерные,0}';
 ?column?
----------
 "dark"
(1 строка)


demo=#
demo=# SELECT '{"игры": {"компьютерные": ["dark","souls"], "спортивные":4}}'::json #>> '{игры,компьютерные,0}';
 ?column?
----------
 dark
(1 строка)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 4

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 5 (упражнения 2, 9, 17, 18)

### Работа

#### Упражнение 2

Посмотрите, какие ограничения уже наложены на атрибуты таблицы «Успеваемость» (progress).
В качестве примера рассмотрим такой вариант. Добавьте в таблицу progress еще один атрибут — «Форма проверки знаний» (test_form), который может принимать только два значения: «экзамен» или «зачет». Тогда набор допустимых значений атрибута «Оценка» (mark) будет зависеть от того, экзамен или зачет предусмотрены по данной дисциплине. Если предусмотрен экзамен, тогда допускаются значения 3, 4, 5, если зачет — тогда 0 (не зачтено) или 1 (зачтено).

```sql
edu=# ALTER TABLE progress
ADD CHECK (
( test_form = 'экзамен' AND mark IN ( 3, 4, 5 ) )
OR
( test_form = 'зачет' AND mark IN ( 0, 1 ) )
);
edu=# \d progress
                               Таблица "public.progress"
     Столбец     |     Тип      | Правило сортировки | Допустимость NULL | По умолчанию 
-----------------+--------------+--------------------+-------------------+--------------
 record_book     | numeric(5,0) |                    | not null          | 
 acad_year       | text         |                    | not null          | 
 term            | numeric(1,0) |                    | not null          | 
 discipline_code | smallint     |                    | not null          | 
 mark            | numeric(1,0) |                    | not null          | 
 test_form       | text         |                    | not null          | 
Индексы:
    "progress_pkey" PRIMARY KEY, btree (record_book, acad_year, term, discipline_code)
    "no_double_mark" UNIQUE CONSTRAINT, btree (record_book, acad_year, term, discipline_code, mark)
Ограничения-проверки:
    "progress_acad_year_check" CHECK (ltrim(acad_year, ' '::text) <> ''::text)
    "progress_check" CHECK (test_form = 'экзамен'::text AND (mark = ANY (ARRAY[3::numeric, 4::numeric, 5::numeric])) OR test_form = 'зачет'::text AND (mark = ANY (ARRAY[0::numeric, 1::numeric])))
    "progress_mark_check" CHECK (mark >= 0::numeric AND mark <= 5::numeric)
    "progress_term_check" CHECK (term = 1::numeric OR term = 2::numeric)
    "progress_test_form_check" CHECK (test_form = 'экзамен'::text OR test_form = 'зачет'::text)
Ограничения внешнего ключа:
    "progress_discipline_code_fkey" FOREIGN KEY (discipline_code) REFERENCES disciplines(discipline_code) ON UPDATE CASCADE
    "progress_record_book_fkey" FOREIGN KEY (record_book) REFERENCES students(record_book) ON UPDATE CASCADE ON DELETE CASCADE



edu=# ALTER TABLE progress ADD CHECK
edu(# (test_form='экзамен' and mark IN (2,3,4,5)) OR
edu(# (test_form='зачет'   and mark IN (0,1)));
ALTER TABLE
```

Проверьте, как будет работать новое ограничение в модифицированной таблице progress. Для этого выполните команды INSERT, как удовлетворяющие ограничению, так и нарушающие его.

```sql
edu=# INSERT INTO students VALUES (7, 'student 1', 658, 7685);
INSERT 0 1
edu=# INSERT INTO progress VALUES (4, 'Mathematics', '2',2, 4, 'экзамен');
INSERT 0 1
edu=# INSERT INTO progress VALUES (4, 'Mathematics', '2',2, 0, 'зачет');
ОШИБКА: новая строка в отношении “progress” нарушает ограничение-проверку "progress_mark_check"
ПОДРОБНОСТИ: Ошибочная строка содержит (4, 'Mathematics', '2',2, 0, 'зачет').
```

В таблице уже было ограничение на допустимые значения атрибута mark. Как вы думаете, не будет ли оно конфликтовать с новым ограничением? Проверьте эту гипотезу. Если ограничения конфликтуют, тогда удалите старое ограничение и снова попробуйте добавить строки в таблицу.

Они конфликтуют, в таком случае удалим ограничение, и попробуем добавить строки снова.

```sql
edu=# ALTER TABLE progress DROP CONSTRAINT progress_mark_check;
edu=# INSERT INTO progress VALUES (4, 'Mathematics', '2',2, 0, 'зачет');

INSERT 0 1
```

#### Упражнение 9

В таблице «Студенты» (students) есть текстовый атрибут name, на который наложено ограничение NOT NULL. Как вы думаете, что будет, если при вводе новой строки в эту таблицу дать атрибуту name в качестве значения пустую строку?

```sql
edu=# INSERT INTO students VALUES (2, '', 979, 74673);
INSERT 0 1
```

Добавим ограничение ( name <> '' )

```sql
edu=# ALTER TABLE students ADD CHECK ( name <> '' );
ALTER TABLE
edu=# INSERT INTO students VALUES (3, '', 979, 74673);
ОШИБКА: новая строка в отношении “progress” нарушает ограничение-проверку
"students_name_check"
```

Посмотрим что теперь будет при вставке строки с пустым значением

```sql
edu=# INSERT INTO students VALUES (3, ' ', 979, 74673);
INSERT 0 1
```

Добавим ограничение ( trim (name) <> '' )

```sql
edu=# ALTER TABLE students ADD CHECK (trim (name) <> '');
ALTER TABLE
edu=# INSERT INTO students VALUES (3, ' ', 979, 74673);
ОШИБКА: новая строка в отношении “progress” нарушает ограничение-проверку
"students_name_check"
```

Есть ли подобные слабые места в таблице «Успеваемость» (progress)?
- Есть, с поля с текстовым типом могут быть вставлены пустые строки.

#### Упражнение 17

Подумайте, какие представления было бы целесообразно создать для нашей базы данных «Авиаперевозки». Необходимо учесть наличие различных групп пользователей, например: пилоты, диспетчеры, пассажиры, кассиры. Создайте представления и проверьте их в работе.

Диспетчер

```sql
demo=# CREATE VIEW dispatcher_info AS SELECT
demo-#  flight_no,
demo-#  scheduled_departure,
demo-#  status,
demo-#  model,
demo-#  range
demo-# FROM flights f
demo-# LEFT JOIN aircrafts a on f.aircraft_code = a.aircraft_code;
CREATE VIEW
demo=# SELECT * FROM  dispatcher_info;
 flight_no |  scheduled_departure   |  status   |        model        | range
-----------+------------------------+-----------+---------------------+-------
 PG0405    | 2016-09-13 08:35:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0404    | 2016-10-03 18:05:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0405    | 2016-10-03 08:35:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0402    | 2016-11-07 11:25:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0405    | 2016-10-14 08:35:00+03 | On Time   | Airbus A321-200     |  5600
 PG0404    | 2016-10-14 18:05:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0403    | 2016-10-14 10:25:00+03 | Delayed   | Airbus A321-200     |  5600
 PG0402    | 2016-10-14 11:25:00+03 | On Time   | Airbus A321-200     |  5600
 PG0405    | 2016-10-23 08:35:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0402    | 2016-10-21 11:25:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0403    | 2016-10-21 10:25:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0404    | 2016-10-21 18:05:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0405    | 2016-10-21 08:35:00+03 | Scheduled | Airbus A321-200     |  5600
 PG0402    | 2016-10-04 11:25:00+03 | Arrived   | Airbus A321-200     |  5600
 PG0402    | 2016-09-25 11:25:00+03 | Arrived   | Airbus A321-200     |  5600
```

#### Упражнение 18

Подумайте, какие еще таблицы было бы целесообразно дополнить столбцами типа json/jsonb. Вспомните, что, например, в таблице «Билеты» (tickets) уже есть столбец такого типа — contact_data. Выполните модификации таблиц и измените в них одну-две строки для проверки правильности ваших решений.

В таблицу bookings в качестве json поля можно добавить информамцию о периоде действия брони.

```sql
demo=# ALTER TABLE bookings ADD COLUMN booking_period jsonb;
ALTER TABLE

demo=# UPDATE bookings
demo-# SET booking_period='{"booking_start": "06.10.2020", "booking_end": "16.10.2020"}'
demo-# WHERE book_ref='000181';
UPDATE 1

demo=# SELECT * FROM bookings WHERE book_ref='000181';
 book_ref |       book_date        | total_amount |   booking_period
----------+------------------------+--------------+-------------------------------------------------------------
 000181   | 2016-10-08 12:28:00+03 |    131800.00 | {"booking_end": "16.10.2020", "booking_start": "06.10.2020"}
(1 строка)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 5

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 6 (упражнения 2, 7, 9, 13, 19, 21, 23)

### Работа

#### Упражнение 2

Этот запрос выбирает из таблицы «Билеты» (tickets) всех пассажиров с именами, состоящими из трех букв (в шаблоне присутствуют три символа «_»): 

```sql
SELECT passenger_name
FROM tickets
WHERE passenger_name LIKE '___ %';
```

Предложите шаблон поиска в операторе LIKE для выбора из этой таблицы всех пассажиров с фамилиями, состоящими из пяти букв.

```sql
demo=# SELECT passenger_name
demo-# FROM tickets
demo-# WHERE passenger_name LIKE '% _____';
   passenger_name
--------------------
   passenger_name  
------------------
 ILYA POPOV
 VLADIMIR POPOV
 PAVEL GUSEV
 LEONID ORLOV
 EVGENIY GUSEV
 NIKOLAY FOMIN
 EKATERINA ILINA
 ANTON POPOV
 ARTEM BELOV
 VLADIMIR POPOV
 ALEKSEY ISAEV
 ...

demo=# SELECT count(passenger_name)
demo=# FROM tickets
demo=# WHERE passenger_name LIKE '% _____';
 count 
-------
 14272
(1 row)
```

#### Упражнение 7

Самые крупные самолеты в нашей авиакомпании — это Boeing 777-300. Выяснить, между какими парами городов они летают, поможет запрос: 

```sql
SELECT DISTINCT departure_city, arrival_city
FROM routes r
JOIN aircrafts a ON r.aircraft_code = a.aircraft_code
WHERE a.model = 'Boeing 777-300'
ORDER BY 1;
```

Модифицируйте запрос таким образом, чтобы каждая пара городов была выведена только один раз

```sql
demo=# SELECT DISTINCT departure_city, arrival_city
FROM routes r
JOIN aircrafts a ON r.aircraft_code = a.aircraft_code
WHERE a.model = 'Boeing 777-300' AND departure_city > arrival_city
ORDER BY 1;
 departure_city | arrival_city 
----------------+--------------
 Москва         | Екатеринбург
 Новосибирск    | Москва
 Пермь          | Москва
 Сочи           | Москва
(4 строки)

или

SELECT DISTINCT r.departure_city, r.arrival_city
 FROM routes r
JOIN routes rr ON r.arrival_city = rr.departure_city
 AND rr.arrival_city = r.departure_city and r.arrival_city > rr.arrival_city
JOIN aircrafts a ON r.aircraft_code = a.aircraft_code
 WHERE a.model = 'Boeing 777-300'
ORDER BY 1;

```

#### Упражнение 9

Для ответа на вопрос, сколько рейсов выполняется из Москвы в Санкт-Петербург, можно написать совсем простой запрос: 

```sql
SELECT count( * )
FROM routes
WHERE departure_city = 'Москва'
AND arrival_city = 'Санкт-Петербург'
```

А с помощью какого запроса можно получить результат в таком виде?

```sql
 departure_city |  arrival_city   | count
----------------+-----------------+-------
 Москва         | Санкт-Петербург |    12

demo=# SELECT  departure_city , arrival_city , count( * )
	FROM routes
	Group by  departure_city , arrival_city
	Having departure_city = 'Москва'
	AND arrival_city = 'Санкт-Петербург';
	
 departure_city |  arrival_city   | count
----------------+-----------------+-------
 Москва         | Санкт-Петербург |    12
(1 строка)
```

#### Упражнение 13

Ответить на вопрос о том, каковы максимальные и минимальные цены билетов на все направления, может такой запрос: 

```sql
SELECT
f.departure_city,
f.arrival_city,
max( tf.amount ),
min( tf.amount )
FROM flights_v f
JOIN ticket_flights tf ON f.flight_id = tf.flight_id
GROUP BY 1, 2
ORDER BY 1, 2;
```

А как выявить те направления, на которые не было продано ни одного билета?

```sql
demo=# SELECT
demo-# f.departure_city,
demo-# f.arrival_city,
demo-# max( tf.amount ),
demo-# min( tf.amount )
demo-# FROM flights_v f
demo-# LEFT JOIN ticket_flights tf ON f.flight_id = tf.flight_id
demo-# GROUP BY 1, 2
demo-# ORDER BY 1, 2;

      departure_city      |       arrival_city       |    max    |   min
--------------------------+--------------------------+-----------+----------
 Абакан                   | Архангельск              |           |
 Абакан                   | Грозный                  |           |
 Абакан                   | Кызыл                    |           |
 Абакан                   | Москва                   | 101000.00 | 33700.00
 Абакан                   | Новосибирск              |   5800.00 |  5800.00
 Абакан                   | Томск                    |   4900.00 |  4900.00
 Анадырь                  | Москва                   | 185300.00 | 61800.00
 Анадырь                  | Хабаровск                |  92200.00 | 30700.00
 Анапа                    | Белгород                 |  18900.00 |  6300.00
 Анапа                    | Москва                   |  36600.00 | 12200.00
```

#### Упражнение 19

В разделе 6.4 мы использовали рекурсивный алгоритм в общем табличном выражении. Изучите этот пример, чтобы лучше понять работу рекурсивного алгоритма:

```sql
WITH RECURSIVE ranges ( min_sum, max_sum )
AS (
VALUES( 0, 100000 ),
( 100000, 200000 ),
( 200000, 300000 )
UNION ALL
SELECT min_sum + 100000, max_sum + 100000
FROM ranges
WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
)
SELECT * FROM ranges;
```

##### Задание 1

Модифицируйте запрос, добавив в него столбец level (можно назвать его и iteration). Этот столбец должен содержать номер текущей итерации, поэтому нужно увеличивать его значение на единицу на каждом шаге. Не забудьте задать начальное значение для добавленного столбца в предложении VALUES.

```sql
demo=# WITH RECURSIVE ranges ( min_sum, max_sum, iteration )
AS (
VALUES( 0,
100000,0 ),
( 100000, 200000 ,0 ),
( 200000, 300000 ,0 )
UNION ALL
SELECT min_sum + 100000, max_sum + 100000, iteration+1
FROM ranges
WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
)
SELECT * FROM ranges;

min_sum | max_sum | iteration 
---------+---------+-----------
       0 |  100000 |         0
  100000 |  200000 |         0
  200000 |  300000 |         0
  100000 |  200000 |         1
  200000 |  300000 |         1
  300000 |  400000 |         1
  200000 |  300000 |         2
  300000 |  400000 |         2
  400000 |  500000 |         2
  300000 |  400000 |         3
  400000 |  500000 |         3
  500000 |  600000 |         3
  400000 |  500000 |         4
  500000 |  600000 |         4
  600000 |  700000 |         4
  500000 |  600000 |         5
  600000 |  700000 |         5
  700000 |  800000 |         5
  600000 |  700000 |         6

```

##### Задание 2

Для завершения экспериментов замените UNION ALL на UNION и выполните запрос. Сравните этот результат с предыдущим, когда мы использовали UNION ALL.

```sql
demo=# WITH RECURSIVE ranges ( min_sum, max_sum )
AS (
VALUES( 0,
100000 ),
( 100000, 200000 ),
( 200000, 300000 )
UNION
SELECT min_sum + 100000, max_sum + 100000
FROM ranges
WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
)
SELECT * FROM ranges;

 min_sum | max_sum
---------+---------
       0 |  100000
  100000 |  200000
  200000 |  300000
  300000 |  400000
  400000 |  500000
  500000 |  600000
  600000 |  700000
  700000 |  800000
  800000 |  900000
  900000 | 1000000
 1000000 | 1100000
 1100000 | 1200000
 1200000 | 1300000
(13 строк)
```

#### Упражнение 21

В тексте главы был приведен запрос, выводящий список городов, в которые нет рейсов из Москвы.

```sql
demo=# SELECT DISTINCT a.city
FROM airports a
WHERE NOT EXISTS (
SELECT * FROM routes r
WHERE r.departure_city = 'Москва'
AND r.arrival_city = a.city
)
AND a.city <> 'Москва'
ORDER BY city;

         city         
----------------------
 Благовещенск
 Иваново
 Иркутск
 Калуга
 Когалым
 Комсомольск-на-Амуре
 Кызыл
 Магадан
 Нижнекамск
 Новокузнецк
 Стрежевой
 Сургут
 Удачный
 Усть-Илимск
 Усть-Кут
 Ухта
 Череповец
 Чита
 Якутск
 Ярославль
(20 строк)

```

Можно предложить другой вариант, в котором используется одна из операций над множествами строк: объединение, пересечение или разность.
Вместо знака «?» поставьте в приведенном ниже запросе нужное ключевое слово — UNION, INTERSECT или EXCEPT — и обоснуйте ваше решение.

```sql
demo=# SELECT city
FROM airports
WHERE city <> 'Москва'
EXCEPT
SELECT arrival_city
FROM routes
WHERE departure_city = 'Москва'
ORDER BY city;

         city         
----------------------
 Благовещенск
 Иваново
 Иркутск
 Калуга
 Когалым
 Комсомольск-на-Амуре
 Кызыл
 Магадан
 Нижнекамск
 Новокузнецк
 Стрежевой
 Сургут
 Удачный
 Усть-Илимск
 Усть-Кут
 Ухта
 Череповец
 Чита
 Якутск
 Ярославль
(20 строк)

```

EXCEPT потому что требуется исключить те города, в которые нет рейсов из Москвы.

#### Упражнение 23

Предположим, что департамент развития нашей авиакомпании задался вопросом: каким будет общее число различных маршрутов, которые теоретически можно проложить между всеми городами? Если в каком-то городе имеется более одного аэропорта, то это учитывать не будем, т. е. маршрутом будем считать путь между городами, а не между аэропортами. Здесь мы используем соединение таблицы с самой собой на основе неравенства значений атрибутов.

```sql
SELECT count( *)
FROM ( SELECT DISTINCT city FROM airports ) AS a1
JOIN ( SELECT DISTINCT city FROM airports ) AS a2
ON a1.city <> a2.city;
 count 
-------
 10100
(1 строка)

```

Перепишите этот запрос с общим табличным выражением.

```sql


demo=# WITH a1 AS 
(SELECT DISTINCT city FROM airports),
a2 AS (SELECT DISTINCT city FROM airports )
Select count(*)
From a1 inner join a2 on  a1.city <> a2.city;

 count 
-------
 10100
(1 строка)



WITH city_from AS
( SELECT DISTINCT city FROM airports )
SELECT count( * )
FROM city_from f
JOIN ( SELECT DISTINCT city FROM airports ) AS a2
ON f.city <> a2.city;
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 6

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 7 (упражнения 1, 2, 4)

### Работа

#### Упражнение 1

Добавьте в определение таблицы aircrafts_log значение по умолчанию current_timestamp и соответствующим образом измените команды INSERT, приведенные в тексте главы.

```sql
demo=# CREATE TEMP TABLE aircrafts_log AS
SELECT * FROM aircrafts WITH DATA;
SELECT 9

demo=# ALTER TABLE aircrafts_log
ADD COLUMN log_timestamp TIMESTAMP DEFAULT (current_timestamp);
ALTER TABLE

demo=# SELECT * FROM aircrafts_log;
 aircraft_code |        model        | range |       log_timestamp        
---------------+---------------------+-------+----------------------------
 773           | Boeing 777-300      | 11100 | 2021-12-22 20:43:15.375481
 763           | Boeing 767-300      |  7900 | 2021-12-22 20:43:15.375481
 321           | Airbus A321-200     |  5600 | 2021-12-22 20:43:15.375481
 319           | Airbus A319-100     |  6700 | 2021-12-22 20:43:15.375481
 733           | Boeing 737-300      |  4200 | 2021-12-22 20:43:15.375481
 CN1           | Cessna 208 Caravan  |  1200 | 2021-12-22 20:43:15.375481
 CR2           | Bombardier CRJ-200  |  2700 | 2021-12-22 20:43:15.375481
 SU9           | Sukhoi SuperJet-100 |  6000 | 2021-12-22 20:43:15.375481
 320           | Airbus A320-200     |  5700 | 2021-12-22 20:43:15.375481
(9 строк)

demo=# TRUNCATE aircrafts_log;
TRUNCATE TABLE

demo=# CREATE TEMP TABLE aircrafts_tmp AS
SELECT * FROM aircrafts WITH NO DATA;
CREATE TABLE AS

demo=# ALTER TABLE aircrafts_tmp
ADD PRIMARY KEY ( aircraft_code );
ALTER TABLE

demo=# ALTER TABLE aircrafts_tmp
ADD UNIQUE ( model );
ALTER TABLE

demo=# WITH add_row AS (INSERT INTO aircrafts_tmp
SELECT * FROM aircrafts RETURNING *)
INSERT INTO aircrafts_log
SELECT add_row.aircraft_code, add_row.model, add_row.range
FROM add_row;
INSERT 0 9

demo=# SELECT * FROM aircrafts_log;
 aircraft_code |        model        | range |       log_timestamp        
---------------+---------------------+-------+----------------------------
 773           | Boeing 777-300      | 11100 | 2021-12-22 20:43:51.464377
 763           | Boeing 767-300      |  7900 | 2021-12-22 20:43:51.464377
 321           | Airbus A321-200     |  5600 | 2021-12-22 20:43:51.464377
 319           | Airbus A319-100     |  6700 | 2021-12-22 20:43:51.464377
 733           | Boeing 737-300      |  4200 | 2021-12-22 20:43:51.464377
 CN1           | Cessna 208 Caravan  |  1200 | 2021-12-22 20:43:51.464377
 CR2           | Bombardier CRJ-200  |  2700 | 2021-12-22 20:43:51.464377
 SU9           | Sukhoi SuperJet-100 |  6000 | 2021-12-22 20:43:51.464377
 320           | Airbus A320-200     |  5700 | 2021-12-22 20:43:51.464377
(9 строк)
```

#### Упражнение 2

В предложении RETURNING можно указывать не только символ «∗», означающий выбор всех столбцов таблицы, но и более сложные выражения, сформированные на основе этих столбцов. В тексте главы мы копировали содержимое таблицы «Самолеты» в таблицу aircrafts_tmp, используя в предложении RETURNING именно «∗». Однако возможен и другой вариант запроса:

```sql

WITH add_row AS
( INSERT INTO aircrafts_tmp
SELECT * FROM aircrafts
RETURNING aircraft_code, model, range,
current_timestamp, 'INSERT'
)
INSERT INTO aircrafts_log
SELECT ? FROM add_row;


demo=# WITH add_row AS
( INSERT INTO aircrafts_tmp
SELECT * FROM aircrafts
RETURNING aircraft_code, model, range,
current_timestamp, 'INSERT'
)
INSERT INTO aircrafts_log
SELECT add_row.aircraft_code, add_row.model, add_row.range, current_timestamp FROM add_row;
INSERT 0 9
demo=# Select * from aircrafts_log;
 aircraft_code |        model        | range |       log_timestamp        
---------------+---------------------+-------+----------------------------
 773           | Boeing 777-300      | 11100 | 2021-12-22 20:54:17.522852
 763           | Boeing 767-300      |  7900 | 2021-12-22 20:54:17.522852
 321           | Airbus A321-200     |  5600 | 2021-12-22 20:54:17.522852
 319           | Airbus A319-100     |  6700 | 2021-12-22 20:54:17.522852
 733           | Boeing 737-300      |  4200 | 2021-12-22 20:54:17.522852
 CN1           | Cessna 208 Caravan  |  1200 | 2021-12-22 20:54:17.522852
 CR2           | Bombardier CRJ-200  |  2700 | 2021-12-22 20:54:17.522852
 SU9           | Sukhoi SuperJet-100 |  6000 | 2021-12-22 20:54:17.522852
 320           | Airbus A320-200     |  5700 | 2021-12-22 20:54:17.522852
(9 строк)

```

#### Упражнение 4

В тексте главы в предложениях ON CONFLICT команды INSERT мы использовали только выражения, состоящие из имени одного столбца.
Однако в таблице «Места» (seats) первичный ключ является составным и включает два столбца.
Напишите команду INSERT для вставки новой строки в эту таблицу и предусмотрите возможный конфликт добавляемой строки со строкой, уже имеющейся в таблице.
Сделайте два варианта предложения ON CONFLICT: первый — с использованием перечисления имен столбцов для проверки наличия дублирования, второй — с использованием предложения ON CONSTRAINT.

Для того чтобы не изменить содержимое таблицы «Места», создайте ее копию и выполняйте все эти эксперименты с таблицей-копией.



```sql
demo=# CREATE TEMP TABLE seats_tmp
 AS SELECT * FROM SEATS;
SELECT 1339
demo=# ALTER TABLE seats_tmp ADD PRIMARY KEY (aircraft_code, seat_no);
ALTER TABLE

demo=# \d seats_tmp
                                  Таблица "pg_temp_3.seats_tmp"
     Столбец     |          Тип          | Правило сортировки | Допустимость NULL | По умолчанию
-----------------+-----------------------+--------------------+-------------------+
 aircraft_code   | character(3)          |                    | not null          |
 seat_no         | character varying(4)  |                    | not null          |
 fare_conditions | character varying(10) |                    |                   |
Индексы:
    "seats_tmp_pkey" PRIMARY KEY, btree (aircraft_code, seat_no)

demo=# INSERT INTO seats_tmp
SELECT aircraft_code, seat_no, fare_conditions
 FROM seats ON CONFLICT DO NOTHING;
INSERT 0 0

demo=# INSERT INTO seats_tmp
 VALUES ( 319, '2A', 'Business' )
 ON CONFLICT ON CONSTRAINT seats_tmp_pkey
 DO UPDATE SET aircraft_code = excluded.aircraft_code,
 seat_no = excluded.seat_no
 RETURNING *;
 aircraft_code | seat_no | fare_conditions
---------------+---------+-----------------
 319           | 2A      | Business
(1 строка)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 7

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 8 (упражнения 1, 3)

### Работа

#### Упражнение 1

Предположим, что для какой-то таблицы создан уникальный индекс по двум столбцам: column1 и column2. В таблице есть строка, у которой значение атрибута column1 равно ABC, а значение атрибута column2 - NULL. Мы решили добавить в таблицу еще одну строку с такими же значениями ключевых атрибутов, т.е. column1 - ABC, а column2 - NULL.

Как вы думаете, будет ли операция вставки новой строки успешной или завершится с ошибкой? Объясните ваше решение.

- Ошибки не будет, тк  в уникальных индексах можно использовать NULL. При этом NULL != NULL, и сравнение идти будет по связке атрибутов, а значения  с NULL  заведомо совпать не могут.

#### Упражнение 3

Обратимся к таблице «Перелеты» (ticket_flights). В ней имеется столбец «Класс обслуживания» (fare_conditions), который отличается от остальных тем, что в нем могут присутствовать лишь три различных значения: Comfort, Business и Economy. 

Выполните запросы, подсчитывающие количество строк, в которых атрибут fare_conditions принимает одно из трех возможных значений. Каждый из запросов выполните три-четыре раза, поскольку время может немного изменяться, и подсчитайте среднее время.

```sql
demo=# \timing on
Секундомер включён.

demo=# SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Comfort';
 count 
-------
 17291
(1 строка)

Время: 873,736 мс

demo=# SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Business';
 count  
--------
 107642
(1 строка)

Время: 136,537 мс

demo=# SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Economy';
 count  
--------
 920793
(1 строка)

Время: 176,089 мс

```

Проделайте те же эксперименты с таблицей ticket_flights. Будет ли различаться среднее время выполнения запросов для различных значений атрибута fare_conditions? Почему это имеет место?

Сделаем индекс к полю fare_conditions.

```sql

demo=# create index on ticket_flights (fare_conditions);
CREATE INDEX
Время: 1350,560 мс (00:01,351)
demo=# \d ticket_flights;
                                Таблица "bookings.ticket_flights"
     Столбец     |          Тип          | Правило сортировки | Допустимость NULL | По умолчанию 
-----------------+-----------------------+--------------------+-------------------+--------------
 ticket_no       | character(13)         |                    | not null          | 
 flight_id       | integer               |                    | not null          | 
 fare_conditions | character varying(10) |                    | not null          | 
 amount          | numeric(10,2)         |                    | not null          | 
Индексы:
    "ticket_flights_pkey" PRIMARY KEY, btree (ticket_no, flight_id)
    "ticket_flights_fare_conditions_idx" btree (fare_conditions)
Ограничения-проверки:
    "ticket_flights_amount_check" CHECK (amount >= 0::numeric)
    "ticket_flights_fare_conditions_check" CHECK (fare_conditions::text = ANY (ARRAY['Economy'::character varying::text, 'Comfort'::character varying::text, 'Business'::character varying::text]))
Ограничения внешнего ключа:
    "ticket_flights_flight_id_fkey" FOREIGN KEY (flight_id) REFERENCES flights(flight_id)
    "ticket_flights_ticket_no_fkey" FOREIGN KEY (ticket_no) REFERENCES tickets(ticket_no)
Ссылки извне:
    TABLE "boarding_passes" CONSTRAINT "boarding_passes_ticket_no_fkey" FOREIGN KEY (ticket_no, flight_id) REFERENCES ticket_flights(ticket_no, flight_id)


demo=# SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Comfort';
 count 
-------
 17291
(1 строка)

Время: 9,531 мс

demo=# SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Business';
 count  
--------
 107642
(1 строка)

Время: 33,550 мс

demo=# SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Economy';
 count  
--------
 920793
(1 строка)

Время: 104,998 мс

```

Индекс значительно уменьшил время для выборки .

[Наверх](#ссылки)

---

</details>

## Домашняя работа 8

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 9 (упражнения 2, 3)

### Работа

#### Упражнение 2

Модифицируйте сценарий выполнения транзакций: в первой транзакции вместо фиксации изменений выполните их отмену с помощью команды ROLLBACK и посмотрите, будет ли удалена строка и какая конкретно.

```sql

1 session
demo=# begin;
BEGIN
demo=*# update aircrafts_tmp set range =2200 where aircraft_code = 'CN2';
UPDATE 0
demo=*# select * from aircrafts_tmp;
 aircraft_code |        model        | range 
---------------+---------------------+-------
 773           | Boeing 777-300      | 11100
 763           | Boeing 767-300      |  7900
 321           | Airbus A321-200     |  5600
 319           | Airbus A319-100     |  6700
 733           | Boeing 737-300      |  4200
 SU9           | Sukhoi SuperJet-100 |  6000
 320           | Airbus A320-200     |  5700
 CN1           | Cessna 208 Caravan  |  2100
 CR2           | Bombardier CRJ-200  |  1900
(9 строк)

demo=*# update aircrafts_tmp set range =2300 where aircraft_code = 'CN1';
UPDATE 1
demo=*# update aircrafts_tmp set range =1900 where aircraft_code = 'CR2';
UPDATE 1
demo=*# Rollback;
ROLLBACK



2 session

demo=# begin;
BEGIN
demo=*# Select * from aircrafts_tmp where range <2000;
 aircraft_code |       model        | range 
---------------+--------------------+-------
 CR2           | Bombardier CRJ-200 |  1900
(1 строка)

demo=*# Delete from aircrafts_tmp where range<2100;
DELETE 1
demo=!# end;




demo=# select * from aircrafts_tmp;
 aircraft_code |        model        | range 
---------------+---------------------+-------
 773           | Boeing 777-300      | 11100
 763           | Boeing 767-300      |  7900
 321           | Airbus A321-200     |  5600
 319           | Airbus A319-100     |  6700
 733           | Boeing 737-300      |  4200
 SU9           | Sukhoi SuperJet-100 |  6000
 320           | Airbus A320-200     |  5700
 CN1           | Cessna 208 Caravan  |  2100
(8 строк)

```

В итоге изменения проведенные в первой транзакции не сохранились из-за команды ROLLBACK, и вторая транзакция прошла независимо от первой. Поэтому удалилась строка, подходящая условию, которая была в изначальном состоянии таблицы. 

#### Упражнение 3

Когда говорят о таком феномене, как потерянное обновление, то зачастую в качестве примера приводится операция UPDATE, в которой значение какого-то атрибута изменяется с применением одного из действий арифметики. Например: 

```sql
UPDATE aircrafts_tmp SET range = range + 200 WHERE aircraft_code = 'CR2';
```

При выполнении двух и более подобных обновлений в рамках параллельных транзакций, использующих, например, уровень изоляции Read Committed, будут учтены все такие изменения (что и было показано в тексте главы). Очевидно, что потерянного обновления не происходит. Предположим, что в одной транзакции будет просто присваиваться новое значение.
```sql
UPDATE aircrafts_tmp
SET range = 2100
WHERE aircraft_code = 'CR2';
```
А в параллельной транзакции будет выполняться аналогичная команда:
```sql
UPDATE aircrafts_tmp
SET range = 2500
WHERE aircraft_code = 'CR2';
```
Очевидно, что сохранится только одно из значений атрибута range. Можно ли говорить, что в такой ситуации имеет место потерянное обновление? Если оно имеет место, то что можно предпринять для его недопущения? Обоснуйте ваш ответ.

- Транзакции пройдут последовательно и нет, обновление не потеряется, просто оно будет перезаписано другой транзакцией.   С пользовательской стороны кажется, что было потеряно обновление и чтобы избежать потерь, можно использовать более высокий уровень изоляции.

[Наверх](#ссылки)

---

</details>

## Домашняя работа 9

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 10 (упражнения 3, 6, 8)

### Работа

#### Упражнение 3

Самостоятельно выполните команду EXPLAIN для запроса, содержащего общее табличное выражение (CTE). Посмотрите, на каком уровне находится узел плана, отвечающий за это выражение, как он оформляется. Учтите, что общие табличные выражения всегда материализуются, т. е. вычисляются однократно и результат их вычисления сохраняется в памяти, а затем все последующие обращения в рамках запроса направляются уже к этому материализованному результату.

```sql
demo=# EXPLAIN WITH exp AS
 (SELECT DISTINCT city FROM airports)
SELECT count(*) FROM exp AS exp1 JOIN exp AS exp2 ON exp1.city<>exp2.city;
                               QUERY PLAN                                
-------------------------------------------------------------------------
 Aggregate  (cost=262.11..262.12 rows=1 width=8)
   CTE exp
     ->  HashAggregate  (cost=3.30..4.31 rows=101 width=17)
           Group Key: airports.city
           ->  Seq Scan on airports  (cost=0.00..3.04 rows=104 width=17)
   ->  Nested Loop  (cost=0.00..232.55 rows=10100 width=0)
         Join Filter: (exp1.city <> exp2.city)
         ->  CTE Scan on exp exp1  (cost=0.00..2.02 rows=101 width=32)
         ->  CTE Scan on exp exp2  (cost=0.00..2.02 rows=101 width=32)
(9 строк)

```

#### Упражнение 6

Выполните команду EXPLAIN для запроса, в котором использована какая-нибудь из оконных функций. Найдите в плане выполнения запроса узел с именем WindowAgg. Попробуйте объяснить, почему он занимает именно этот уровень в плане.

```sql
demo=#  SELECT book_ref, total_amount, SUM(total_amount) OVER()
 FROM bookings ORDER BY 1 LIMIT 10;
 book_ref | total_amount |      sum       
----------+--------------+----------------
 00000F   |    265700.00 | 20766980900.00
 000012   |     37900.00 | 20766980900.00
 000068   |     18100.00 | 20766980900.00
 000181   |    131800.00 | 20766980900.00
 0002D8   |     23600.00 | 20766980900.00
 0002DB   |    101500.00 | 20766980900.00
 0002E0   |     89600.00 | 20766980900.00
 0002F3   |     69600.00 | 20766980900.00
 00034E   |     73300.00 | 20766980900.00
 000352   |    109500.00 | 20766980900.00
(10 строк)

demo=# EXPLAIN SELECT book_ref, total_amount, SUM(total_amount) OVER()
 FROM bookings ORDER BY 1 LIMIT 10;
                                            QUERY PLAN                          
                   
--------------------------------------------------------------------------------
-------------------
 Limit  (cost=0.42..0.87 rows=10 width=45)
   ->  WindowAgg  (cost=0.42..11796.09 rows=262788 width=45)
         ->  Index Scan using bookings_pkey on bookings  (cost=0.42..8511.24 row
s=262788 width=13)
(3 строки)
```
Сначала выполняется оконное агрегирование.
WindowAgg занимает этот уровень в плане выполнения, потому что сначала происходит оконное агрегирование.

#### Упражнение 8

Замена коррелированного подзапроса соединением таблиц является одним из способов повышения производительности. Предположим, что мы задались вопросом: сколько маршрутов обслуживают самолеты каждого типа? При этом нужно учитывать, что может иметь место такая ситуация, когда самолеты какого-либо типа не обслуживают ни одного маршрута. Поэтому необходимо использовать не только представление «Маршруты» (routes), но и таблицу «Самолеты» (aircrafts). Это первый вариант запроса, в нем используется коррелированный подзапрос. 
```sql
demo=# EXPLAIN ANALYZE
SELECT a.aircraft_code AS a_code,
a.model,
( SELECT count( r.aircraft_code )
FROM routes r
WHERE r.aircraft_code = a.aircraft_code
) AS num_routes
FROM aircrafts a
GROUP BY 1, 2
ORDER BY 3 DESC;
                                                       QUERY PLAN                                           
             
------------------------------------------------------------------------------------------------------------
-------------
 Sort  (cost=236.31..236.34 rows=9 width=56) (actual time=14.199..14.202 rows=9 loops=1)
   Sort Key: ((SubPlan 1)) DESC
   Sort Method: quicksort  Memory: 25kB
   ->  HashAggregate  (cost=1.11..236.17 rows=9 width=56) (actual time=10.088..14.137 rows=9 loops=1)
         Group Key: a.aircraft_code
         Batches: 1  Memory Usage: 24kB
         ->  Seq Scan on aircrafts a  (cost=0.00..1.09 rows=9 width=48) (actual time=7.049..7.056 rows=9 loo
ps=1)
         SubPlan 1
           ->  Aggregate  (cost=26.10..26.11 rows=1 width=8) (actual time=0.775..0.776 rows=1 loops=9)
                 ->  Seq Scan on routes r  (cost=0.00..25.88 rows=89 width=4) (actual time=0.376..0.744 rows
=79 loops=9)
                       Filter: (aircraft_code = a.aircraft_code)
                       Rows Removed by Filter: 631
 Planning Time: 10.923 ms
 Execution Time: 14.351 ms
(14 строк)


А в этом варианте коррелированный подзапрос раскрыт и заменен внешним
соединением:

demo=# EXPLAIN ANALYZE
SELECT a.aircraft_code AS a_code,
a.model,
count( r.aircraft_code ) AS num_routes
FROM aircrafts a
LEFT OUTER JOIN routes r
ON r.aircraft_code = a.aircraft_code
GROUP BY 1, 2
ORDER BY 3 DESC;
                                                          QUERY PLAN                                        
                  
------------------------------------------------------------------------------------------------------------
------------------
 Sort  (cost=31.83..31.85 rows=9 width=56) (actual time=2.216..2.221 rows=9 loops=1)
   Sort Key: (count(r.aircraft_code)) DESC
   Sort Method: quicksort  Memory: 25kB
   ->  HashAggregate  (cost=31.60..31.69 rows=9 width=56) (actual time=2.191..2.201 rows=9 loops=1)
         Group Key: a.aircraft_code
         Batches: 1  Memory Usage: 24kB
         ->  Hash Right Join  (cost=1.20..28.05 rows=710 width=52) (actual time=0.058..1.392 rows=711 loops=
1)
               Hash Cond: (r.aircraft_code = a.aircraft_code)
               ->  Seq Scan on routes r  (cost=0.00..24.10 rows=710 width=4) (actual time=0.006..0.288 rows=
710 loops=1)
               ->  Hash  (cost=1.09..1.09 rows=9 width=48) (actual time=0.037..0.038 rows=9 loops=1)
                     Buckets: 1024  Batches: 1  Memory Usage: 9kB
                     ->  Seq Scan on aircrafts a  (cost=0.00..1.09 rows=9 width=48) (actual time=0.019..0.02
4 rows=9 loops=1)
 Planning Time: 0.360 ms
 Execution Time: 2.362 ms
(14 строк)

```
Исследуйте планы выполнения обоих запросов. Попытайтесь найти объяснение различиям в эффективности их выполнения. Чтобы получить усредненную картину, выполните каждый запрос несколько раз. 

Предложите аналогичную пару запросов к базе данных «Авиаперевозки». Проведите необходимые эксперименты с вашими запросами.


```sql
demo=# EXPLAIN ANALYZE SELECT f.flight_id, (SELECT max(tf.amount)
 FROM ticket_flights tf
 WHERE f.flight_id = tf.flight_id)
 FROM flights_v f WHERE f.flight_id<100
 GROUP BY 1
 ORDER BY 1;
                                                                     QUERY PLAN                             
                                        
------------------------------------------------------------------------------------------------------------
----------------------------------------
 Group  (cost=0.57..2178750.69 rows=100 width=36) (actual time=217.293..19669.898 rows=99 loops=1)
   Group Key: f.flight_id
   ->  Nested Loop  (cost=0.57..74.69 rows=100 width=4) (actual time=0.072..3.163 rows=99 loops=1)
         ->  Nested Loop  (cost=0.43..42.87 rows=100 width=8) (actual time=0.043..2.411 rows=99 loops=1)
               ->  Index Scan using flights_pkey on flights f  (cost=0.29..11.04 rows=100 width=12) (actual 
time=0.021..0.424 rows=99 loops=1)
                     Index Cond: (flight_id < 100)
               ->  Index Only Scan using airports_pkey on airports dep  (cost=0.14..0.32 rows=1 width=4) (ac
tual time=0.013..0.013 rows=1 loops=99)
                     Index Cond: (airport_code = f.departure_airport)
                     Heap Fetches: 99
         ->  Index Only Scan using airports_pkey on airports arr  (cost=0.14..0.32 rows=1 width=4) (actual t
ime=0.004..0.004 rows=1 loops=99)
               Index Cond: (airport_code = f.arrival_airport)
               Heap Fetches: 99
   SubPlan 1
     ->  Aggregate  (cost=21786.75..21786.76 rows=1 width=32) (actual time=198.646..198.646 rows=1 loops=99)
           ->  Seq Scan on ticket_flights tf  (cost=0.00..21786.58 rows=69 width=6) (actual time=151.522..19
8.626 rows=40 loops=99)
                 Filter: (f.flight_id = flight_id)
                 Rows Removed by Filter: 1045686
 Planning Time: 6.057 ms
 Execution Time: 19670.173 ms
(19 строк)

```

А в этом варианте коррелированный подзапрос раскрыт и заменен внешним соединением:

```sql
demo=# EXPLAIN ANALYZE SELECT f.flight_id, max(tf.amount)
 FROM flights_v f
 LEFT OUTER JOIN ticket_flights tf ON f.flight_id = tf.flight_id
WHERE f.flight_id<100
 GROUP BY 1
 ORDER BY 1;
                                                                           QUERY PLAN                       
                                                     
------------------------------------------------------------------------------------------------------------
-----------------------------------------------------
 GroupAggregate  (cost=22139.35..22164.03 rows=100 width=36) (actual time=326.446..328.585 rows=99 loops=1)
   Group Key: f.flight_id
   ->  Sort  (cost=22139.35..22147.24 rows=3157 width=10) (actual time=326.373..326.812 rows=3988 loops=1)
         Sort Key: f.flight_id
         Sort Method: quicksort  Memory: 283kB
         ->  Hash Join  (cost=20.97..21955.86 rows=3157 width=10) (actual time=2.071..325.100 rows=3988 loop
s=1)
               Hash Cond: (f.arrival_airport = arr.airport_code)
               ->  Hash Join  (cost=16.63..21942.90 rows=3157 width=14) (actual time=2.004..323.141 rows=398
8 loops=1)
                     Hash Cond: (f.departure_airport = dep.airport_code)
                     ->  Hash Right Join  (cost=12.29..21929.94 rows=3157 width=18) (actual time=1.950..321.
088 rows=3988 loops=1)
                           Hash Cond: (tf.flight_id = f.flight_id)
                           ->  Seq Scan on ticket_flights tf  (cost=0.00..19172.26 rows=1045726 width=10) (a
ctual time=0.037..134.945 rows=1045726 loops=1)
                           ->  Hash  (cost=11.04..11.04 rows=100 width=12) (actual time=0.097..0.097 rows=99
 loops=1)
                                 Buckets: 1024  Batches: 1  Memory Usage: 13kB
                                 ->  Index Scan using flights_pkey on flights f  (cost=0.29..11.04 rows=100 
width=12) (actual time=0.020..0.065 rows=99 loops=1)
                                       Index Cond: (flight_id < 100)
                     ->  Hash  (cost=3.04..3.04 rows=104 width=4) (actual time=0.050..0.051 rows=104 loops=1
)
                           Buckets: 1024  Batches: 1  Memory Usage: 12kB
                           ->  Seq Scan on airports dep  (cost=0.00..3.04 rows=104 width=4) (actual time=0.0
04..0.023 rows=104 loops=1)
               ->  Hash  (cost=3.04..3.04 rows=104 width=4) (actual time=0.062..0.062 rows=104 loops=1)
                     Buckets: 1024  Batches: 1  Memory Usage: 12kB
                     ->  Seq Scan on airports arr  (cost=0.00..3.04 rows=104 width=4) (actual time=0.010..0.
031 rows=104 loops=1)
 Planning Time: 0.746 ms
 Execution Time: 328.656 ms
(24 строки)
```
[Наверх](#ссылки)

---

</details>

## Домашняя работа 10

<details>
<summary>Сама работа</summary>

---

### Предисловие

Программирование на стороне сервера.

Выполняется на основе [презентации](https://edu.postgrespro.ru/sqlprimer/sqlprimer-2019-msu-09.pdf)
и главы 4 учебного пособия "Администрирование информационных систем".
Учебную базу данных можно наполнить информацией, функциями и триггерами
с помощью команд, выполняемых в командной строке Debian:

```bash
createdb ais -U postgres
psql -d ais -f adj_list.sql -U postgres
```

Файл `adj_list.sql` находится в составе архива исходных текстов, прилагаемых к учебному пособию.
Они находятся [здесь](http://www.morgunov.org/docs/inf_sys_admin_prg.tgz)

Эти исходные тексты также есть в виртуальной машине ОС Debian
в каталоге `/home/WORK/Databases/Admin_DB/UTF-8`.
В составе этих примеров есть и те,
которые не приведены в тексте пособия,
но они могут быть полезными при выполнении финального задания.

### Задание

Нужно проделать упражнения 12-18 на стр. 86-88 учебного пособия "Администрирование информационных систем".
Почти все эти упражнения являются простыми, ознакомительными, не требующими программирования.

http://www.morgunov.org/programming.html
http://www.morgunov.org/docs/inf_sys_admin.pdf
### Работа

#### Упражнение 12

```bash
psql -d ais -c "Select * from Personnel"

emp_nbr | emp_name |         address         | birth_date 
---------+----------+-------------------------+------------
       0 | вакансия |                         | 2014-05-19
       1 | Иван     | ул. Любителей языка C   | 1962-12-01
       2 | Петр     | ул. UNIX гуру           | 1965-10-21
       3 | Антон    | ул. Ассемблерная        | 1964-04-17
       4 | Захар    | ул. им. СУБД PostgreSQL | 1963-09-27
       5 | Ирина    | просп. Программистов    | 1968-05-12
       6 | Анна     | пер. Перловый           | 1969-03-20
       7 | Андрей   | пл. Баз данных          | 1945-11-07
       8 | Николай  | наб. ОС Linux           | 1944-12-01
(9 строк)

psql -d ais -c "Select * from Org_chart"

      job_title      | emp_nbr | boss_emp_nbr |  salary   
---------------------+---------+--------------+-----------
 Президент           |       1 |              | 1000.0000
 Вице-президент 1    |       2 |            1 |  900.0000
 Вице-президент 2    |       3 |            1 |  800.0000
 Архитектор          |       4 |            3 |  700.0000
 Ведущий программист |       5 |            3 |  600.0000
 Программист C       |       6 |            3 |  500.0000
 Программист Perl    |       7 |            5 |  450.0000
 Оператор            |       8 |            5 |  400.0000
(8 строк)

psql -d ais -c "Select * from Personnel_org_chart"
 emp_nbr |   emp   | boss_emp_nbr | boss  
---------+---------+--------------+-------
       1 | Иван    |              | 
       2 | Петр    |            1 | Иван
       3 | Антон   |            1 | Иван
       4 | Захар   |            3 | Антон
       5 | Ирина   |            3 | Антон
       6 | Анна    |            3 | Антон
       7 | Андрей  |            5 | Ирина
       8 | Николай |            5 | Ирина
(8 строк)


psql -d ais -c "select * from create_paths"
 level1 | level2 | level3 | level4  
--------+--------+--------+---------
 Иван   | Антон  | Ирина  | Андрей
 Иван   | Антон  | Ирина  | Николай
 Иван   | Петр   |        | 
 Иван   | Антон  | Захар  | 
 Иван   | Антон  | Анна   | 
(5 строк)
```

#### Упражнение 13

Проверка структуры дерева на предмет отсутствия циклов.

```sql
ais=# update org_chart set boss_emp_nbr = 4 where emp_nbr = 3;
UPDATE 1
ais=# select * from tree_test();
 tree_test 
-----------
 Cycles
(1 строка)

ais=# select* from org_chart;
      job_title      | emp_nbr | boss_emp_nbr |  salary   
---------------------+---------+--------------+-----------
 Президент           |       1 |              | 1000.0000
 Вице-президент 1    |       2 |            1 |  900.0000
 Архитектор          |       4 |            3 |  700.0000
 Ведущий программист |       5 |            3 |  600.0000
 Программист C       |       6 |            3 |  500.0000
 Программист Perl    |       7 |            5 |  450.0000
 Оператор            |       8 |            5 |  400.0000
 Вице-президент 2    |       3 |            4 |  800.0000
(8 строк)

ais=# update org_chart set boss_emp_nbr = 8 where emp_nbr = 3;
UPDATE 1
ais=# select * from tree_test();
 tree_test 
-----------
 Cycles
(1 строка)

ais=# select* from org_chart;
      job_title      | emp_nbr | boss_emp_nbr |  salary   
---------------------+---------+--------------+-----------
 Президент           |       1 |              | 1000.0000
 Вице-президент 1    |       2 |            1 |  900.0000
 Архитектор          |       4 |            3 |  700.0000
 Ведущий программист |       5 |            3 |  600.0000
 Программист C       |       6 |            3 |  500.0000
 Программист Perl    |       7 |            5 |  450.0000
 Оператор            |       8 |            5 |  400.0000
 Вице-президент 2    |       3 |            8 |  800.0000
(8 строк)

```

#### Упражнение 14

Обход дерева снизу вверх.

```sql
ais=# select * from up_tree_traversal(6);
 emp_nbr | boss_emp_nbr 
---------+--------------
       6 |            3
       3 |            1
       1 |            ø
(3 rows)
ais=# select * from up_tree_traversal2(6) as (emp int, boss int);
 emp | boss 
-----+------
   6 |    3
   3 |    1
   1 |    ø
(3 rows)
ais=# select * from up_tree_traversal (( select emp_nbr from personnel where emp_name = 'Захар' ));
 emp_nbr | boss_emp_nbr 
---------+--------------
       4 |            3
       3 |            1
       1 |            ø
(3 rows)
```

#### Упражнение 15
Выполните операцию удаления поддерева с помощью функции delete_subtree(). Параметром функции является код работника.
SELECT * FROM delete_subtree( 6 );
Аналогично работе с функцией up_tree_traversal() используйте
подзапрос для получения кода работника по его имени. После удале-
ния поддерева посмотрите, что стало с организационной структурой,
с помощью двух представлений Personnel_org_chart и Create_paths.
Удаление поддерева

```sql
ais=# select * from create_paths;
 level1 | level2 | level3 | level4  
--------+--------+--------+---------
 Иван   | Антон  | Ирина  | Андрей
 Иван   | Антон  | Ирина  | Николай
 Иван   | Петр   | ø      | ø
 Иван   | Антон  | Захар  | ø
 Иван   | Антон  | Анна   | ø
(5 rows)
ais=# select * from personnel_org_chart;
 emp_nbr |   emp   | boss_emp_nbr | boss  
---------+---------+--------------+-------
       1 | Иван    |            ø | ø
       2 | Петр    |            1 | Иван
       3 | Антон   |            1 | Иван
       4 | Захар   |            3 | Антон
       5 | Ирина   |            3 | Антон
       6 | Анна    |            3 | Антон
       7 | Андрей  |            5 | Ирина
       8 | Николай |            5 | Ирина
(8 rows)
ais=# select * from delete_subtree(4);
 delete_subtree 
----------------
 
(1 row)
ais=# select * from create_paths;
 level1 | level2 | level3 | level4  
--------+--------+--------+---------
 Иван   | Антон  | Ирина  | Андрей
 Иван   | Антон  | Ирина  | Николай
 Иван   | Петр   | ø      | ø
 Иван   | Антон  | Анна   | ø
(4 rows)
ais=# select * from personnel_org_chart;
 emp_nbr |   emp   | boss_emp_nbr | boss  
---------+---------+--------------+-------
       1 | Иван    |            ø | ø
       2 | Петр    |            1 | Иван
       3 | Антон   |            1 | Иван
       5 | Ирина   |            3 | Антон
       6 | Анна    |            3 | Антон
       7 | Андрей  |            5 | Ирина
       8 | Николай |            5 | Ирина
(7 rows)
ais=# select * from delete_subtree(( select emp_nbr from personnel where emp_name = 'Анна' )); delete_subtree 
----------------
 
(1 row)
ais=# select * from create_paths;
 level1 | level2 | level3 | level4  
--------+--------+--------+---------
 Иван   | Антон  | Ирина  | Андрей
 Иван   | Антон  | Ирина  | Николай
 Иван   | Петр   | ø      | ø
(3 rows)
ais=# select * from personnel_org_chart;
 emp_nbr |   emp   | boss_emp_nbr | boss  
---------+---------+--------------+-------
       1 | Иван    |            ø | ø
       2 | Петр    |            1 | Иван
       3 | Антон   |            1 | Иван
       5 | Ирина   |            3 | Антон
       7 | Андрей  |            5 | Ирина
       8 | Николай |            5 | Ирина
(6 rows)
```

#### Упражнение 16

Если в таблице «Организационная структура» осталось мало
данных, то дополните ее данными и выполните удаление элемента
иерархии и продвижение дочерних элементов на один уровень вверх
(т. е. к «бабушке»).
SELECT * FROM delete_and_promote_subtree( 5 );
Аналогично работе с функцией up_tree_traversal() используйте под-
запрос для получения кода работника по его имени.
После удаления элемента иерархии посмотрите, что стало
с организационной структурой, с помощью двух представлений
Personnel_org_chart и Create_paths.

```sql
ais=# select * from delete_and_promote_subtree(( select emp_nbr from personnel where emp_name='Антон' ));
 delete_and_promote_subtree 
----------------------------
 
(1 row)
ais=# select * from create_paths;
 level1 | level2 | level3  | level4 
--------+--------+---------+--------
 Иван   | Петр   | ø       | ø
 Иван   | Ирина  | Николай | ø
 Иван   | Ирина  | Андрей  | ø
(3 rows)
ais=# select * from personnel_org_chart;
 emp_nbr |   emp   | boss_emp_nbr | boss  
---------+---------+--------------+-------
       1 | Иван    |            ø | ø
       2 | Петр    |            1 | Иван
       7 | Андрей  |            5 | Ирина
       8 | Николай |            5 | Ирина
       5 | Ирина   |            1 | Иван
(5 rows)
```

#### Упражнение 17
Представление Create_paths позволяет отобразить только
четыре уровня иерархии. Модифицируйте его так, чтобы оно могло
работать с пятью уровнями иерархии.

```sql
ais=# create view create_paths_5 (level1, level2, level3, level4, level5) as
select O1.emp as e1, O2.emp as e2, O3.emp as e3, O4.emp as e4, O5.emp as e5
 from personnel_org_chart as O1
 left outer join personnel_org_chart as O2
 on O1.emp = O2.boss
 left outer join personnel_org_chart as O3
 on O2.emp = O3.boss
 left outer join personnel_org_chart as O4
 on O3.emp = O4.boss
 left outer join personnel_org_chart as O5
 on O4.emp = O5.boss where O1.emp = 'Иван';
CREATE VIEW

ais=# select * from create_paths_5;
 level1 | level2 | level3  | level4 | level5 
--------+--------+---------+--------+--------
 Иван   | Ирина  | Андрей  | ø      | ø
 Иван   | Ирина  | Николай | ø      | ø
 Иван   | Петр   | ø       | ø      | ø
(3 rows)
```

#### Упражнение 18
Самостоятельно ознакомьтесь с таким средством работы
с таблицами базы данных, как курсоры (cursors). Воспользуйтесь технической документацией на PostgreSQL, глава «PL/pgSQL – SQL Procedural Language». Напишите небольшую функцию с применением курсора.

```sql
ais=# alter table personnel add column ne_goden text;
ALTER TABLE

ais=# create or replace function army() returns void as
 $$
 declare curs cursor for select * from personnel where (cast(current_date as date)-cast(birth_date as date))/365 > 27;
 begin
 open curs;
 move curs;
 while found loop
 update personnel set ne_goden='yes' where current of curs;
 move curs;
 end loop;
 close curs;
 end
 $$
 language plpgsql;
CREATE FUNCTION

ais=# select * from army();
 army 
------
 
(1 row)

ais=# select * from personnel;
 emp_nbr | emp_name |         address         | birth_date | ne_goden 
---------+----------+-------------------------+------------+----------
       0 | вакансия |                         | 2014-05-19 | ø
       1 | Иван     | ул. Любителей языка C   | 1962-12-01 | yes
       2 | Петр     | ул. UNIX гуру           | 1965-10-21 | yes
       3 | Антон    | ул. Ассемблерная        | 1964-04-17 | yes
       4 | Захар    | ул. им. СУБД PostgreSQL | 1963-09-27 | yes
       5 | Ирина    | просп. Программистов    | 1968-05-12 | yes
       6 | Анна     | пер. Перловый           | 1969-03-20 | yes
       7 | Андрей   | пл. Баз данных          | 1945-11-07 | yes
       8 | Николай  | наб. ОС Linux           | 1944-12-01 | yes
(9 rows)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 11

<details>
<summary>Сама работа</summary>

---

### Предисловие

Полнотекстовый поиск.

Задание выполняется на основе презентации 10 "Полнотекстовый поиск"
и главы 12 документации на [Постгрес](https://postgrespro.ru/docs/postgresql/12/textsearch)

### Задание

Придумать и реализовать пример использования полнотекстового поиска,
аналогичный (можно более простой или более сложный) тому примеру с библиотечным каталогом,
который был приведен в презентации.
Можно использовать исходные тексты, приведенные в [презентации](https://edu.postgrespro.ru/sqlprimer/sqlprimer-2019-msu-10.tgz).

### Работа

```sql
demo=# create table books (book_id integer primary key, book_description text);
CREATE TABLE
demo=# \copy books from '/home/nz/Downloads/books2.txt';
COPY 1000
demo=# alter table books add column ts_description tsvector;
ALTER TABLE
demo=# update books set ts_description = to_tsvector('russian', book_description);
UPDATE 1000

demo=# SELECT * FROM books WHERE ts_description @@ to_tsquery('SQL');
-[ RECORD 1 ]----+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
book_id          | 807
book_description | Маркин, Александр Васильевич. Построение запросов и программирование на SQL [Текст] : учебное пособие : [для студентов высших учебных заведений, обучающихся по специальности 230201 "Информационные системы и технологии"] / А. В. Маркин. - Москва : Диалог-МИФИ, 2011. - 344 с.
ts_description   | '2011':33 '230201':21 '344':34 'sql':9 'александр':2 'васильевич':3 'высш':15 'диалог':31 'диалог-миф':30 'заведен':17 'запрос':5 'информацион':22 'маркин':1,28 'миф':32 'москв':29 'обуча':18 'пособ':12 'построен':4 'программирован':7 'систем':23 'специальн':20 'студент':14 'текст':10 'технолог':25 'учебн':11,16

demo=# SELECT book_id, book_description FROM books WHERE ts_description @@ plainto_tsquery('александр');
book_id  | book_description
---------+----------------------------------------------------------------------------------------------------------------------------------------
      16 | Солодовников, Александр Самуилович. Введение в линейную алгебру и линейное программирование [Текст] / А. С. Солодовников. - Москва : Просвещение, 1966. - 182, [1] с.
      23 | Малявко, Александр Антонович. Формальные языки и компиляторы [Текст] : учебное пособие для вузов : [по направлению "Информатика и вычислительная техника"] / А. А. Малявко. - Москва : Юрайт, 2018. - 431 с.
      49 | Вальвачев, Александр Николаевич. Программирование на языке ПАСКАЛЬ для персональных ЭВМ ЕС [Текст] : справочное пособие / А. Н. Вальвачев, В. С. Крисевич. - Минск : Вышэйшая школа, 1989. - 222, [1] с.
      55 | Кубенский, Александр Александрович. Функциональное программирование [Текст] : учебник и практикум для академического бакалавриата : [по инженерно-техническим направлениям] / А. А. Кубенский. - Москва : Юрайт, 2017. - 345, [3] с.
      70 | Егоров, Александр Иванович. Введение в теорию управления системами с распределенными параметрами [Текст] : учебное пособие / А. И. Егоров, Л. Н. Знаменская. - Санкт-Петербург ; Москва ; Краснодар : Лань, 2017. - 288 с.
     115 | Водовозов, Александр Михайлович. Микроконтроллеры для систем автоматики [Текст] : учебное пособие / Водовозов A. M. - Москва ; Вологда : Инфра-Инженерия, 2017. - 163, [1] с.
     141 | Малявко, Александр Антонович. Параллельное программирование на основе технологий OpenMP, MPI, CUDA [Текст] : учебное пособие для академического бакалавриата / А. А. Малявко. - Москва : Юрайт, 2017. - 115, [1] с.
     156 | Голиков, Александр Ильич. Применение функции чувствительности в многокритериальной оптимизации [Текст] / А. И. Голиков, Г. Г. Коткин. - Москва : ВЦ АН СССР, 1986. - 34, [2] с.
     247 | Кузнецов, Александр Сергеевич. Теория вычислительных процессов [Текст] : [учебник для студентов вузов по специальностям: "Программное обеспечение вычислительной техники и автоматизированных систем", "Прикладная информатика"] / А. С. Кузнецов, Р. Ю. Царев, А. Н. Князьков. - Красноярск : КрасГАУ, 2015. - 210 с.
     267 | Горстко, Александр Борисович. Азбука программирования. [Текст] : руководство / А. Б. Горстко, С. В. Кочковая. - Москва : Знание, 1988. - 140, [3] с.
     296 | Кузнецов, Александр Сергеевич. Теория вычислительных процессов [Текст] : [учебник для студентов вузов по специальностям: "Программное обеспечение вычислительной техники и автоматизированных систем", "Прикладная информатика"] / А. С. Кузнецов, Р. Ю. Царев, А. Н. Князьков. - Красноярск : СФУ, 2015. - 183 с.
     330 | Павлов, Александр Анатольевич. Линейные модели в нелинейных системах управления [Текст] / А. А. Павлов. - Киев : Технiка, 1982. - 166, [1] с.
     342 | Гонтаренко, Александр Николаевич. Котонные ластичные машины для верхнего трикотажа [Текст] / А. Н. Гонтаренко, В. Д. Худин. - Москва : Легкая и пищевая промышленность, 1981. - 237, [2] с.
     396 | Дадаян, Александр Арсенович. Алгебра и начала анализа [Текст] / А. А. Дадаян, И. А. Новик ; под ред. А. А. Дадаяна ; [рец.: доц. каф. В. Т. Воднев, И. Н. Бурдина]. - Минск : Вышэйшая школа, 1980. - 367 с.
     444 | Медведев, Александр Васильевич. Основы теории обучающихся систем [Текст] : учебное пособие / А. В. Медведев. - Красноярск : [б. и.], 1982. - 107 с.
     622 | Рудаков, Александр Иванович. Язык Паскаль и технология программирования [Текст] : учеб. пособие / А. И. Рудаков, Г. М. Рудакова. - Красноярск : КГУ, 1992. - 95 с.
     497 | Кузьмин, Александр Васильевич. Основы программирования систем числового программного управления [Текст] : [учебное пособие для студентов высших учебных заведений по направлению "Конструкторско-технологическое обеспечение машиностроительных производств"] / А. В. Кузьмин, А. Г. Схиртладзе. - Старый Оскол : ТНТ, 2013. - 239 с.
     525 | Здзиарски, Джонатан. IPhone SDK [Текст] : разработка приложений / Джонатан Здзиарски ; [пер. с англ. Александры Маленковой]. - Санкт-Петербург : БХВ-Петербург, 2013. - 488 с.
     548 | Кузин, Александр Владимирович. Микропроцессорная техника [Текст] : учебник : [по группам специальностей "Информатика и вычислительная техника", "Электротехника"] / А. В. Кузин, М. А. Жаворонков. - Москва : Академия, 2013. - 303, [1] с.
     559 | Попов, Александр Михайлович. Экономико-математические методы и модели [Текст] : высшая математика для экономистов : учебник для бакалавров : [для студентов вузов по специальностям экономики и управления] / А. М. Попов, В. Н. Сотников ; под ред. А. М. Попова. - Москва : Юрайт, 2013. - 479 с.
     587 | Могилев, Александр Владимирович. Технологии поиска и хранения информации. Технологии автоматизации управления [Текст] : [учебник для учащихся старших классов физико-математического, информационно-технологического и других профилей] / А. В. Могилев, Л. В. Листрова. - Санкт-Петербург : БХВ-Петербург, 2012. - 320 с.
     625 | Прокопенко, Александр Владимирович. Синтез систем реального времени с гарантированной доступностью программно-информационных ресурсов [Текст] / А. В. Прокопенко, М. А. Русаков, Р. Ю. Царев. - Красноярск : СФУ, 2013. - 91 с.
     664 | Пупков, Александр Николаевич. Информатика и программирование [Текст] : [учебное пособие для студентов вузов] / А. Н. Пупков, В. В. Самарин, Р. Ю. Царев. - Красноярск : СФУ, 2012. - 156 с.
     723 | Бычков, Александр Григорьевич. Сборник задач по теории вероятностей, математической статистике и методам оптимизации [Текст] : [учебное пособие для студентов учебных заведений среднего профессионального образования] / А. Г. Бычков. - Москва : ФОРУМ, 2012. - 221, [1] с.
     728 | Перова, Александра Алексеевна. Электронные вычислительные машины и основы программирования [Текст] : учебник для техникумов / А. А. Перова, В. П. Феоктистов. - Москва : Статистика, 1978. - 285, [1] с.
     730 | Микушин, Александр Владимирович. Цифровые устройства и микропроцессоры [Текст] : [учебное пособие для студентов вузов по направлению Телекоммуникации] / А. В. Микушин, А. М. Сажнев, В. И. Сединин. - Санкт-Петербург : БХВ-Петербург, 2010. - 818 с.
     807 | Маркин, Александр Васильевич. Построение запросов и программирование на SQL [Текст] : учебное пособие : [для студентов высших учебных заведений, обучающихся по специальности 230201 "Информационные системы и технологии"] / А. В. Маркин. - Москва : Диалог-МИФИ, 2011. - 344 с.
     815 | Корбут, Александр Антонович. Дискретное программирование [Текст] / А. А. Корбут, Ю. Ю. Финкельштейн ; под ред. Д. Б. Юдина. - Москва : Наука, 1969. - 368 с.
     846 | Суранов, Александр Яковлевич. LabVIEW 8.20 [Текст] : справочник по функциям / А. Я. Суранов. - Москва : ДМК Пресс, 2007. - 534, [1] с.
     875 | Хоторн, Роб. Разработка баз данных Microsoft SOL Server 2000 на примерах [Текст] / Р. Хоторн ; ред.: В. В. Александров, А. В. Журавлев, В. В. Александров. - Москва ; Санкт-Петербург : Вильямс, 2001. - 460 с.
     919 | Базисный фортран [Текст] / авт. текста А. Л. Александров, авт. текста Ю. Ю. Галимов, авт. текста Б. Ш. Кауфман, авт. текста Н. Б. Лебедева, авт. текста, ред. Н. П. Брусенцов. - Москва : МГУ, 1982. - 199 с.
     929 | Замулин, Александр Васильевич. Типы данных в языках программирования и базах данных [Текст] / А. В. Замулин ; отв. ред. В. Е. Котов. - Новосибирск : Наука, Сибирское отделение, 1987. - 147, [3] с.
     981 | Брудно, Александр Львович. Московские олимпиады по программированию [Текст] / А.Л. Брудно, Л.И. Каплан; Под ред. Б.Н. Наумова. - Москва : Наука, 1990. - 208 с.
(33 rows)

demo=# SELECT count(*) FROM books WHERE ts_description @@ to_tsquery('C++');
 count 
-------
    39
(1 row)
```

[Наверх](#ссылки)

---

</details>
