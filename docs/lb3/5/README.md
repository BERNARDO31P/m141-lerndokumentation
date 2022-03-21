# 5. Tag
## Datentypen
| **Datentyp** | **Speicherplatz** | **Optionen**          | **Beschreibung**                                                                                                                                            |
|--------------|-------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TINYINT      | 1 Byte            | [(M)] [U] [Z]         | Ganzzahlen von 0 bis 255 oder von -128 bis 127                                                                                                              |
| SMALLINT     | 2 Bytes           | [(M)] [U] [Z]         | Ganzzahlen von 0 bis 65.535 oder von -32.768 bis 32.767                                                                                                     |
| MEDIUMINT    | 3 Bytes           | [(M)] [U] [Z]         | Ganzzahlen von 0 bis 16.777.215 oder von -8.388.608 bis 8.388.607                                                                                           |
| INT          | 4 Bytes           | [(M)] [U] [Z]         | Ganzzahlen von 0 bis ~4,3 Mill. oder von -2.147.483.648 bis 2.147.483.647                                                                                   |
| INTEGER      | 4 Bytes           | [(M)] [U] [Z]         | Alias für INT                                                                                                                                               |
| BIGINT       | 8 Bytes           | [(M)] [U] [Z]         | Ganzzahlen von 0 bis 2^64-1 oder von -(2^63) bis (2^63)-1                                                                                                   |
| FLOAT        | 4 Bytes           | [(M,D)] [U] [Z]       | Fließkommazahl mit Vorzeichen. Wertebereich von -(3,402823466×1038) bis -(1,175494351×10-38), 0 und 1,175494351×10-38 bis 3,402823466×1038                  |
| DOUBLE       | 8 Bytes           | [(M,D)] [U] [Z]       | Fließkommazahl mit Vorzeichen. Wertebereich von -(1,79769×10308) bis -(2.22507×10-308), 0 und 2.22507×10-308 bis 1,79769×10308                              |
| REAL         | 8 Bytes           | [(M,D)] [U] [Z]       | Alias für DOUBLE                                                                                                                                            |
| DECIMAL      | M+x Bytes         | [(M,D)] [U] [Z]       | Fließkommazahl mit Vorzeichen. Speicherbedarf: x=1 wenn D=0, sonst x=2. Ab MySQL 5.1 binär gespeichert, zuvor als String                                    |
| NUMERIC      | M+x Bytes         | [(M,D)] [U] [Z]       | Alias für DECIMAL                                                                                                                                           |
| DATE         | 3 Bytes           | -                     | Datum im Format 'YYYY-MM-DD'. Wertebereich von 01.01.1000 bis 31.12.9999                                                                                    |
| DATETIME     | 8 Bytes           | -                     | Datumsangabe im Format 'YYYY-MM-DD hh:mm:ss'. Wertebereich entspricht DATE                                                                                  |
| TIMESTAMP    | 4 Bytes           | -                     | Zeitstempel. Wertebereich: 1.1.1970 bis 19.01.2038. Das Format variiert in den MySQL-Versionen                                                              |
| TIME         | 3 Bytes           | -                     | Zeit zwischen -838:59:59 und 839:59:59. Ausgabe: hh:mm:ss                                                                                                   |
| YEAR         | 1 Byte            | [(2\|4)]               | Jahr zwischen 1901 bis 2155 bei (4) und zwischen 1970 bis 2069 bei (2)                                                                                      |
| CHAR         | M Byte(s)         | (M) [BINARY]          | Zeichenkette fester Länge M. Wertebereich für M: 0 bis 255                                                                                                  |
| VARCHAR      | L+1 Bytes         | (M) [BINARY]          | Zeichenkette variabler Länge, Maximum ist M. Wertebereich für M: 0 bis 255                                                                                  |
| BINARY       | M Bytes           | (M)                   | Zum Speichern binärer Strings, unabhängig vom Zeichensatz. Wertebereich für M: 0 bis 255. Weiterer Typ: VARBINARY                                           |
| BLOB         | L+2 Bytes         | (M)                   | Binäres Objekt mit variablen Daten. Weitere Typen: TINYBLOB, MEDIUMBLOB und LONGBLOB. M ist ab Version 4.1 definierbar                                      |
| TEXT         | L+2 Bytes         | (M)                   | Wie BLOB. Ignoriert beim Sortieren & Vergleichen Groß- und Kleinschreibung. Weitere Typen: TINYTEXT, MEDIUMTEXT, LONGTEXT. M ist ab Version 4.1 definierbar |
| ENUM         | 1 oder 2 Bytes    | ('val1', 'val2', ...) | Liste von Werten (val1, val2, ...). 65.535 eineindeutige Elemente sind maximal möglich                                                                      |
| SET          | x Bytes           | ('val1', 'val2', ...) | String-Objekt mit verschiedenen Variablen. 64 sind maximal möglich. Speicherbedarf: x ist 1, 2, 3, 4 oder 8                                                 |

**Legende**
- ^	    =	Potenzzeichen
- \[ \]	    =	Optionaler Parameter
- BINARY	=	Attribut für die Sortierung
- D	    =	Anzahl der Kommastellen bei einer Dezimalzahl
- L	    =	Stringlänge (Berechnung Speicherbedarf)
- M	    =	Maximale Anzahl der gezeigten Stellen
- Mill.	=	Milliarden
- U	    =	UNSIGNED (Zahl ohne Vorzeichen)
- Z	    =	Zerofill

[Quelle](https://www.schmager.de/mysql.php)

## Indexierungstypen
| **Indexierungstyp** | **Beschreibung**                                                                                                                                                                                                                                                                             |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Primary Key         | Je Tabelle ist nur ein Primary Key möglich, dies ist typischerweise die ID-Spalte. Jeder Eintrag in dieser Spalte muss eindeutig sein, eine Doppelung ist ausgeschlossen. Der Primary Key unterscheidet sich nicht von Unique, nur dass es maximal einen Primary Key pro Tabelle geben darf. |
| Unique              | Spalten mit einem Index des Typs Unique können nur eindeutige Werte enthalten. Dass zwei Datensätze den selben Wert in der Spalte enthalten ist ausgeschlossen.                                                                                                                              |
| Index               | Spalten die mittels Index gekennzeichnet werden können im Gegensatz zu Unique und Primary Key auch doppelte Werte in der Spalte enthalten.                                                                                                                                                   |
| Fulltext            | Der Typ Fulltext ist geeignet für Spalten die längere Texte enthalten. Jedes Wort wird dabei indiziert und eine Suche nach einzelnen Wörtern oder Teilwörtern ist möglich. Diesen Typ zu wählen macht nur Sinn, wenn mit der Volltextsuche von MySQL gearbeitet wird.                        |

[Quelle](https://www.php-einfach.de/mysql-tutorial/optimierung-von-mysql-verwendung-des-index/)
