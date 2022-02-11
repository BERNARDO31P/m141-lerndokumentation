# 1. Tag
Heute ging es um die Aufteilung von SQL. SQL besteht aus mehreren Teilen, welche unterschiedliche Dinge tun können.

## SQL
Dies ist die Sprache welche verwendet wird um Daten zu lesen, auch SQL DQL (Data Query Language) genannt.
Schlüsselwörter sind `select`

Beispiel:
```sql
SELECT * FROM users;
```

## SQL DDL
Diese Sprache wird für das Definieren, Bearbeiten und Löschen der Tabellen und Datenbanken gebraucht.
Schlüsselwörter sind `create, alter, drop`

Beispiel:
```sql
CREATE TABLE users
(
username varchar(32),
password varchar(128)
);
```

## SQL DML
Diese Sprache wird für das Einfügen, Bearbeiten, Aktualisieren und Löschen von Daten gebraucht.
Schlüsselwörter sind `insert, update, delete`

Beispiel:
```sql
INSERT INTO users VALUES (`bernardo`, `123456`);
```