# Postgres_HW
 
```sql
edu=# \d test_a
                          Таблица "public.test_a"
 Столбец  |   Тип   | Правило сортировки | Допустимость NULL | По умолчанию 
----------+---------+--------------------+-------------------+--------------
 column_a | numeric |                    |                   | 
 descrip  | text    |                    |                   | 

edu=# \d test_b
                          Таблица "public.test_b"
 Столбец  |   Тип   | Правило сортировки | Допустимость NULL | По умолчанию 
----------+---------+--------------------+-------------------+--------------
 column_b | numeric |                    |                   | 
 descrip  | text    |                    |                   | 
 
                                               
edu=# Select * from test_a;
 column_a | descrip 
----------+---------
        1 | a
        2 | b
        3 | c
        1 | d
        4 | e
        5 | g
(6 строк)

edu=# Select * from test_b;
 column_b | descrip 
----------+---------
        2 | i
        2 | k
        1 | p
        3 | m
        5 | n
        6 | o
        1 | q
(7 строк)

edu=# Select a.column_a,b.column_b, a.descrip, b.descrip  from test_a a join test_b b on a.column_a = b.column_b;
 column_a | column_b | descrip | descrip 
----------+----------+---------+---------
        1 |        1 | a       | q
        1 |        1 | a       | p
        2 |        2 | b       | k
        2 |        2 | b       | i
        3 |        3 | c       | m
        1 |        1 | d       | q
        1 |        1 | d       | p
        5 |        5 | g       | n
(8 строк)

edu=# Select a.column_a,b.column_b, a.descrip, b.descrip  from test_a a left join test_b b on a.column_a = b.column_b;
 column_a | column_b | descrip | descrip 
----------+----------+---------+---------
        1 |        1 | a       | q
        1 |        1 | a       | p
        2 |        2 | b       | k
        2 |        2 | b       | i
        3 |        3 | c       | m
        1 |        1 | d       | q
        1 |        1 | d       | p
        4 |          | e       | 
        5 |        5 | g       | n
(9 строк)

edu=# Select * from test_a full outer join test_b on test_a.column_a = test_b.column_b;
 column_a | descrip | column_b | descrip 
----------+---------+----------+---------
        1 | a       |        1 | q
        1 | a       |        1 | p
        2 | b       |        2 | k
        2 | b       |        2 | i
        3 | c       |        3 | m
        1 | d       |        1 | q
        1 | d       |        1 | p
        4 | e       |          | 
        5 | g       |        5 | n
          |         |        6 | o
(10 строк)
```
