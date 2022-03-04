# 4. Tag
## Storage Engines bei MySQL
Folgende Storage Engines werden von MYSQL unterstützt:
- InnoDB
- MyISAM
- Memory
- CSV
- Merge
- Archive
- Federated
- Blackhole
- Example

Eine Datenbank-Engine (oder Speicher-Engine) ist die zugrundeliegende Softwarekomponente, die ein Datenbankverwaltungssystem (DBMS) verwendet, um Daten einer Datenbank zu erstellen, zu lesen, zu aktualisieren und zu löschen. Die meisten Datenbankverwaltungssysteme besitzen ihre eigene Programmierschnittstelle (API), die es dem Benutzer erlaubt, die darunterliegende Engine zu verwenden, ohne den Umweg über die Benutzeroberfläche des DBMS gehen zu müssen.

Der Begriff "Datenbank-Engine" wird häufig austauschbar mit "Datenbank-Server" oder "Datenbankverwaltungssystem" verwendet. "Datenbankinstanz" bezieht sich auf die Prozesse und Speicherstrukturen der laufenden Datenbank-Engine.

[Quelle](https://de.wikipedia.org/wiki/Datenbank-Engine)

### InnoDB
InnoDB ist ein freies Speichersubsystem für das Datenbankmanagementsystem MySQL. Sein Hauptvorteil gegenüber anderen Speichersubsystemen für MySQL ist, dass Transaktionssicherheit und referenzielle Integrität über Fremdschlüssel gewährleistet werden.

[Quelle](https://de.wikipedia.org/wiki/InnoDB)

### MyISAM
MyISAM (My Indexed Sequential Access Method) ist eine Storage-Engine des Datenbankverwaltungssystems MySQL. Sie baut, um einige Erweiterungen ergänzt, auf dem älteren ISAM-System auf und war bis MySQL 5.1 Standard-Storage-Engine. Ab Version 5.5 wurde sie durch InnoDB als Standard-Storage-Engine abgelöst.

MyISAM zeichnet sich durch hohe Effizienz im Vergleich zu anderen von MySQL unterstützten Tabellentypen aus und unterstützt ab Version 3.23.23 von MySQL eine leistungsfähige Volltextsuche. 

[Quelle](https://de.wikipedia.org/wiki/MyISAM)


## Benutzer und Berechtigungen
Man kann für das ganze Datenbank-System Benutzer definieren.
Diesen Benutzern kann man auf Datenbank- und Tabellenebenene Rechte verteilen. 

## Server-Konfiguration
Auf dem Server ist die Repeatable-Read Isolation aktiviert.
Dieses Isolation-Level verursacht Phantom-Datensätze, dies bedeutet, dass die gleiche SQL Query nicht immer die gleichen Ergebnisse liefert.

## Server-Betrieb
Langsame Queries müssen erkennt und beseitigt werden. Hierfür kann man das Protokollieren aktivieren.

Mit `SET GLOBAL slow_query_log=1;` kann man die Protokollierung aktivieren.
Danach mit `SET GLOBAL slow_query_log_file="/var/log/mysql/slow.log";` kann man definieren, wo das Protokol gespeichert wird.
