# 9. Tag
## Performance
Da eine langsame Datenbank, eine unbrauchbare Datenbank ist, ist die Performance ein sehr wichtiger Bestandteil von einer guten Datenbank. 

### Explain
Mit `EXPLAIN` kann man eine SQL Query analysieren. Man sieht was genau indexiert wird und wie viele Zeilen die Datenbank durchlaufen muss, um ans Resultat zu gelangen. 


**Beispiel**
```sql
EXPLAIN SELECT * FROM orderdetails d     
    INNER JOIN orders o ON d.orderNumber = o.orderNumber    
    INNER JOIN products p ON p.productCode = d.productCode     
    INNER JOIN productlines l ON p.productLine = l.productLine     
    INNER JOIN customers c on c.customerNumber = o.customerNumber      
WHERE o.orderNumber = "10101";
```

```text
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                              |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
|  1 | SIMPLE      | l     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    7 |   100.00 | NULL                                               |
|  1 | SIMPLE      | p     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  110 |    10.00 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | o     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  326 |    10.00 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | c     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  122 |    10.00 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | d     | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 2996 |     1.00 | Using where; Using join buffer (Block Nested Loop) |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
5 rows in set, 1 warning (0.00 sec)
```

Wir sehen dabei alle schlechten Zeichen von schlechten Queries:

``type``:
    ALL, das ist schlecht

``keys``:
    possible_keys und keys ist NULL

``Suchgrösse``:
    Wir machen Full-Table-Scans, x-fach. Genauer : 7 x 110 x 326 x 122 x 2996 = 91750822240 gescannte Elemente (rund 91 Mio.)


Um die Situation zu verbessern, fügen wir die Primary Key Indexe dazu (im Import-Script wurden diese durch die Lehrperson böswillig entfernt):
```sql
ALTER TABLE customers ADD PRIMARY KEY (customerNumber);
ALTER TABLE employees ADD PRIMARY KEY (employeeNumber);
ALTER TABLE offices ADD PRIMARY KEY (officeCode);
ALTER TABLE orderdetails ADD PRIMARY KEY (orderNumber, productCode);
ALTER TABLE orders ADD PRIMARY KEY (orderNumber), ADD KEY (customerNumber);
ALTER TABLE payments ADD PRIMARY KEY (customerNumber, checkNumber);
ALTER TABLE productlines ADD PRIMARY KEY (productLine);
ALTER TABLE products ADD PRIMARY KEY (productCode), ADD KEY (buyPrice), ADD KEY (productLine);
ALTER TABLE productvariants ADD PRIMARY KEY (variantId),ADD KEY (buyPrice),ADD KEY (productCode);
```

```sql
EXPLAIN SELECT * FROM orderdetails d     
    INNER JOIN orders o ON d.orderNumber = o.orderNumber    
    INNER JOIN products p ON p.productCode = d.productCode     
    INNER JOIN productlines l ON p.productLine = l.productLine     
    INNER JOIN customers c on c.customerNumber = o.customerNumber      
WHERE o.orderNumber = "10101";
```

Und hier das Resultat:
```text
+----+-------------+-------+------------+--------+------------------------+---------+---------+------------------------------+------+----------+-----------------------+
| id | select_type | table | partitions | type   | possible_keys          | key     | key_len | ref                          | rows | filtered | Extra                 |
+----+-------------+-------+------------+--------+------------------------+---------+---------+------------------------------+------+----------+-----------------------+
|  1 | SIMPLE      | o     | NULL       | const  | PRIMARY,customerNumber | PRIMARY | 4       | const                        |    1 |   100.00 | NULL                  |
|  1 | SIMPLE      | c     | NULL       | const  | PRIMARY                | PRIMARY | 4       | const                        |    1 |   100.00 | NULL                  |
|  1 | SIMPLE      | d     | NULL       | ref    | PRIMARY                | PRIMARY | 4       | const                        |    4 |   100.00 | Using index condition |
|  1 | SIMPLE      | p     | NULL       | eq_ref | PRIMARY,productLine    | PRIMARY | 17      | explaintesting.d.productCode |    1 |   100.00 | NULL                  |
|  1 | SIMPLE      | l     | NULL       | eq_ref | PRIMARY                | PRIMARY | 52      | explaintesting.p.productLine |    1 |   100.00 | NULL                  |
+----+-------------+-------+------------+--------+------------------------+---------+---------+------------------------------+------+----------+-----------------------+
5 rows in set, 1 warning (0.00 sec)
```

Nachrechnen: 1 x 1 x 4 x 1 x 1 = 4....besser ;-)


## Performance Tips
### Tip 1: Attribute indexieren
Wenn Sie folgenden Befehl auf einer Tabelle mit 500 Einträge durchführen (ohne Indexierung), forcieren Sie MySQL zu einem "Full-Table-Scan" - alle Werte werden angeguckt.

`select customer_id, customer_name from customers where customer_id='140385';`

Auch der EXPLAIN-Befehl liefert gute Informationen wie schlecht das ist:
```sql
mysql> explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  500 |    10.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

``key``:
    ist NULL...Full-Table-Scan

``rows``:
    500, das sind alle Einträge die er durchsucht


**Lösung**
Index erstellen:

`create index customer_id ON customers (customer_Id);`

```sql
mysql> Explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
| id | select_type | table     | partitions | type | possible_keys | key         | key_len | ref   | rows | filtered | Extra |
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | customers | NULL       | ref  | customer_id   | customer_id | 13      | const |    1 |   100.00 | NULL  |
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
```


### Tip 2: UNIONS verwenden
Wenn Sie ein einer Query mit zu vielen "or" arbeiten, kann das zu einem Full-Table-Scan führen auch wenn Sie Indexe verwenden. Mit UNIONs vermeidet man dieses Problem.

**Lösung**
Beispiel-Abfrage (schlecht, obwohl first_name und last_name indexiert sind):

`select * from students where first_name like  'Ade%'  or last_name like 'Ade%';`

Besser:

`select  from students where first_name like  'Ade%'  union all select  from students where last_name like  'Ade%' ;`


### Tip 3: Wildcards
Ganz einfach:

*Vermeiden Sie führende Wildcards bei Selects*

Schlecht:

`mysql> select * from students where first_name like  '%Ade';`


### Tip 4: Full-Text-Searches nutzen
```sql
mysql>alter table students ADD FULLTEXT (first_name, last_name);
mysql>select * from students where match(first_name, last_name) AGAINST ('Ade');

-- EXPLAIN

mysql> explain Select * from students where match(first_name, last_name) AGAINST ('Ade');
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
| id | select_type | table    | partitions | type     | possible_keys | key        | key_len | ref   | rows | filtered | Extra                         |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
|  1 | SIMPLE      | students | NULL       | fulltext | first_name    | first_name | 0       | const |    1 |   100.00 | Using where; Ft_hints: sorted |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
```


### Tip 5: MySQL Query Caching aktivieren
MySQL unterstützt das Caching von Abfragen - nutzen Sie sie:

```sql

mysql> show variables like 'query_cache_%' ;
+------------------------------+----------+
| Variable_name                | Value    |
+------------------------------+----------+
| query_cache_limit            | 1048576  |
| query_cache_min_res_unit     | 4096     |
| query_cache_size             | 16777216 |
| query_cache_type             | OFF      |
| query_cache_wlock_invalidate | OFF      |
+------------------------------+----------+
5 rows in <b>set</b> (0.00 sec)
```

Passen Sie die Werte auf Ihre Bedürfnisse an (Config-File):
```yaml
query_cache_type=1          # 1 ist eingestellt / 0 ist aus
query_cache_size = 10M      # default ist 1MB / erhöhen auf 10MB macht Sinn
query_cache_limit=1M        # Individuelle Query-Resultate die gecacht werden, 1MB macht Sinn    
```


### Tip 6: Datenbank-Design
Ich nehme bewusst diesen Punkt als letzter Punkt, da dieser der wichtigste ist.
Das Design von einer Datenbank ist fundamental für eine schnelle Datenbank. 

- Tabellen normalisieren / keine redudanten Daten <- ausser, Sie wissen was Sie tun
- Geschickte Datentypen verwenden
- NULL-Werte vermeiden
- Zu viele Spalten(Attribute) im Design vermeiden
- Bei SQL-Joins vorsichtig sein (nur die Tabellen einbeziehen, die auch nötig sind) - nur Daten ausgeben die nötig sind (keine "Select *")