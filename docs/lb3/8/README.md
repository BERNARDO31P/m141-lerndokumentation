# 8. Tag
## Stored procedures
Die Idee hinter stored procedures ist die Kapselung. Tabellen werden hinter stored procedures versteckt. Dadurch können Redundanzen besser gepflegt werden, die Sicherheit ist besser, da Schnittstellen zur Applikation definiert sind und in der Praxis sind diese schneller, da diese im Hintergrund gecached werden.

### Erstellen
```sql
CREATE
    [ DEFINER = { user | CURRENT_USER }]
    PROCEDURE sp_name ([ proc_parameter [ ,...]])
    [ characteristic ...] routine_body

proc_parameter :
    [ IN | OUT | INOUT ] param_name type

characteristic :
    COMMENT ’ string ’
    | LANGUAGE SQL
    | [ NOT ] DETERMINISTIC
    | { CONTAINS SQL | NO SQL |
    READS SQL DATA | MODIFIES SQL DATA }
    | SQL SECURITY { DEFINER | INVOKER }
```
Hier eine kurze Erklärung:

**proc_parameter**: Pro Parameter / Eingabe- oder Ausgabewert (oder beides)

**characteristic**:
- Deterministisch oder nicht (zum Beispiel: Alter vs Geburtstag)
- In welcher Form liegt die SP vor
- Mit welchen Rechten wird die Prozedur ausgeführt (Definier oder Invoker)

#### Beispiel
```sql
mysql > DELIMITER //
mysql > CREATE PROCEDURE GetAllProducts (OUT products VARCHAR)
-> BEGIN
-> SELECT * FROM products ;
-> END ;
-> //
mysql > DELIMITER ;
```

#### Variablen
**Können wir Variablen in SP verwenden?**
```sql
DECLARE varName INT DEFAULT 0;
```

**Wie definieren wir den Wert von Variablen?**
```sql
DECALRE varName INT DEFAULT 0;
SET varName = 10;
```

**Könnne wir Werte aus einer Tabelle in Variablen verwenden?**
```sql
SELECT COUNT (*) INTO varName FROM list;
```


### Anzeigen
Das Anzeigen von stored procedures geht folgendermassen:
```sql
SHOW PROCEDURES STATUS WHERE db = 'datenbank_name';
```

### Anwenden
```sql
mysql > CALL GetAllProducts(@products);
mysql > SELECT @products;
```
Dies führt die vorher definierte storage procedure aus.


## Views
Die Idee hinter views ist das Filtern. Somit können sensitive Daten ausgeblendet werden. Komplizierte Abfragen können zusammengefasst werden und die tatsächliche Tabellenstruktur wird versteckt. 

### Erstellen
```sql
CREATE
    [ OR REPLACE ]
    [ ALGORITHM = { UNDEFINED | MERGE | TEMPTABLE }]
    [ DEFINER = { user | CURRENT_USER }]
    [ SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [( column_list )]
AS s e l e c t _ s t a t e me n t
    [ WITH [ CASCADED | LOCAL ] CHECK OPTION ]
```

#### Beispiel
Die Syntax oben ist die Theorie, in der Praxis sieht dies so aus:
```sql
CREATE VIEW name AS SELECT * FROM tabellen_name;
```

#### Probleme
**View existiert bereits?**
```sql
CREATE OR REPLACE VIEW name AS SELECT * FROM tabellen_name;
```

**Sie wollen andere Attribute-Namen?**
```sql
CREATE VIEW name AS SELECT vorname AS vornameNeu, nachname AS nachnameNeu FROM tabellen_name;
```


### Anzeigen
Das Anzeigen von views geht folgendermassen:
```sql
SHOW FULL TABLES WHERE table_type = 'VIEW';
```

### Anwenden
Da Views eigene "Tabellen" sind, können diese mit einem normalen SELECT aufgerufen werden:
```sql
mysql > SELECT * FROM name;
```


## Triggers
Die Idee hinter triggers ist, dass bei einer Aktion, automatisch eine weiter ausgeführt wird.

### Beispiele
- Wenn ein neuer Benutzer erfasst wird, soll auch ein neues Konto erstellt werden
- Wenn ein Benutzer gelöscht wird, soll eine Abschlussrechnung generiert werden
- usw.

### Anzeigen
Das Anzeigen von triggers geht folgendermassen:
```sql
SHOW TRIGGERS IN datenbank_name
```

```sql
DELIMITER //
CREATE TRIGGER validate_sales_amount
BEFORE INSERT ON sales
FOR EACH ROW
IF NEW.sales_amount>10000 THEN
SIGNAL SQLSTATE '45000'
SET MESSAGE_TEXT = 'Sale has exceeded the allowed amount of 10000.';
END IF//
DELIMITER ;
```