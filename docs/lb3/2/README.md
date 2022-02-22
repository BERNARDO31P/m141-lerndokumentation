# 2. Tag
## ACID
[Quelle](https://de.wikipedia.org/wiki/ACID)

### A - Atomarität
Eine Transaktion ist eine Folge von Datenbank-Operationen, die entweder ganz oder gar nicht ausgeführt wird (Alles-oder-nichts-Eigenschaft). Praktisch werden die einzelnen Datenbankanweisungen, aus denen sich die Transaktion zusammensetzt, natürlich nacheinander ausgeführt, aber global erst dann „für gültig erklärt und in Kraft gesetzt“, wenn sie erfolgreich vollständig abgeschlossen sind. Sollte sich jedoch während der Transaktion herausstellen, dass diese nicht vollständig abgeschlossen werden kann, wird der ursprüngliche Bereich als gültig erklärt und ein Rollback durchgeführt, d. h., alle bis dahin ausgeführten Anweisungen wieder rückgängig gemacht, sofern notwendig – oder einfach der zwischenzeitlich für die Änderungen genutzte Speicherbereich wieder freigegeben und die Gültigkeit beim Bisherigen belassen.

### C - Konsistenz
Konsistenz heißt, dass eine Transaktion nach Beendigung einen konsistenten Datenbankzustand hinterlässt, falls die Datenbank davor auch konsistent war. Dies beinhaltet, dass alle im Datenbankschema definierten Integritätsbedingungen vor dem Abschluss der Transaktion überprüft werden. Ist das nicht möglich, oder tritt ein Fehler auf, wird die gesamte Transaktion rückgängig gemacht.

### I - Isolation
Durch das Prinzip der Isolation wird verhindert/eingeschränkt, dass sich nebenläufig in Ausführung befindliche Transaktionen gegenseitig beeinflussen. Realisiert wird dies üblicherweise durch Sperrverfahren, die vor einem Datenzugriff die benötigten Daten für andere Transaktionen sperren. Sperrverfahren schränken die Nebenläufigkeit ein und können zu Blockierungen führen. In vielen Datenbanksystemen kann das verwendete Isolationsverfahren daher so konfiguriert werden, dass bestimmte eigentlich unerwünschte Effekte zugelassen werden um eine höhere Nebenläufigkeit zu erreichen. Der transaktionale Isolationsgrad definiert dabei die erlaubte Art der Beeinflussung, verbreitete Einstellungen sind dabei `READ COMMITTED`, `REPEATABLE READ` sowie `SERIALIZABLE`.

### D - Dauerhaftigkeit
Der Begriff Dauerhaftigkeit sagt aus, dass Daten nach dem erfolgreichen Abschluss einer Transaktion garantiert dauerhaft in der Datenbank gespeichert sind. Die dauerhafte Speicherung der Daten muss auch nach einem Systemfehler (Software-Fehler oder Hardware-Ausfall) garantiert sein. Insbesondere darf es nach einem Ausfall des Hauptspeichers nicht zu Datenverlusten kommen. Dauerhaftigkeit kann durch das Schreiben eines Transaktionslogs sichergestellt werden. Ein Transaktionslog erlaubt es, nach einem Systemausfall alle noch fehlenden Schreib-Operationen in der Datenbank auszuführen.

## BASE
BASE ist grundsätzlich das Gegenstück von ACID. 

**BA** Basic Availability
**S** Soft-state
**E** Eventual consistency 

Es sagt aus, dass man mit gewissen Nachteilen auskommen kann. Ein gutes Beispiel ist Instagram, ihnen ist es egal ob ein Beitrag erst einige Sekunden später in einem Land verfügbar ist. Der Nutzer bemerkt dies nicht.
Eine Bank muss die Konsistenz der Daten sicherstellen und arbeitet daher mit ACID.

## CAP-Theorem
[Quelle](https://de.wikipedia.org/wiki/CAP-Theorem)

Dieses Dreieck sagt aus, dass nicht alle folgenden Eigenschaften berücksichtigt werden können.

Möchte man, dass überall die Daten gleich sind sowie, dass diese sofort verfügbar sind, so muss man die Ausfalltoleranz runterschrauben.

Möchte man, dass überall die Daten gleich sind sowie, dass Ausfälle berücksichtigt werden, so muss man die Verfügbarkeit runterschrauben.

### C - Konsistenz
Die Konsistenz der gespeicherten Daten. In verteilten Systemen mit replizierten Daten muss sichergestellt sein, dass nach Abschluss einer Transaktion auch alle Replikate des manipulierten Datensatzes aktualisiert werden. Diese Konsistenz sollte nicht verwechselt werden mit der Konsistenz der ACID-Transaktionen, die nur die innere Konsistenz eines Datenbestandes betrifft.

### A - Verfügbarkeit
Die Verfügbarkeit im Sinne akzeptabler Antwortzeiten. Alle Anfragen an das System werden stets beantwortet.

### P - Ausfalltoleranz
Die Ausfalltoleranz der Rechner-/Servernetze. Das System arbeitet auch weiter, wenn es partitioniert wird, d. h., wenn Knoten nicht mehr miteinander kommunizieren können (z. B., um die Daten untereinander konsistent zu halten). Dies kann durch den Verlust von Nachrichten, den Ausfall einzelner Netzknoten oder den Abbruch von Verbindungen im Netz (der Partition des Netzes) auftreten.

## Transaktion
Eine Transaktion besteht aus mehreren Befehlen, welche alle Erfolgreich ausgeführt werden müssen, da sonst die Transaktion rückgängig gemacht wird.

## SQLite
Eine transaktionale Datenbank ist eine Datenbank, in der alle Änderungen und Abfragen atomar, konsistent, isoliert und dauerhaft (ACID) zu sein scheinen. SQLite implementiert serialisierbare Transaktionen, die atomar, konsistent, isoliert und dauerhaft sind, selbst wenn die Transaktion durch einen Programmabsturz, einen Absturz des Betriebssystems oder einen Stromausfall des Computers unterbrochen wird.

[Quelle](https://www.sqlite.org/transactional.html)