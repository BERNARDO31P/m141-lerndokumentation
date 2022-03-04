# 3. Tag
## MySQL
MySQL ist eines der weltweit verbreitetsten relationalen Datenbankverwaltungssysteme. Es ist als Open-Source-Software sowie als kommerzielle Enterpriseversion für verschiedene Betriebssysteme verfügbar und bildet die Grundlage für viele dynamische Webauftritte.

MySQL wurde seit 1994 vom schwedischen Unternehmen MySQL AB entwickelt. Im Februar 2008 wurde MySQL AB vom Unternehmen Sun Microsystems übernommen, das seinerseits im Januar 2010 von Oracle gekauft wurde.

Der Name MySQL setzt sich zusammen aus dem Vornamen My, den die Tochter des MySQL-AB-Mitbegründers Michael Widenius trägt, und SQL.

Verwendet wird die Version 8.0.27-0. Dies ist die neuste Version auf Ubuntu 21.04

## Installationsumgebung
- Betriebssystem: Ubuntu 21.04
- Kernel Version: 5.11.0-49
- Abhändigkeiten
 - adduser
 - mysql-common
 - lsb_base
 - mysql-client
 - mysql-server-core
 - passwd
 - perl
 - psmisc
 - debconf


## Installationsprozess
Zum MySQL installieren führt man diesen Befehl aus: `apt-get install mysql-server`

Danach kann man MYSQL noch einmalig konfigurieren. Dies geht mit diesem Befehl `sudo mysql_secure_installation`


## Testen der Installation
Zum Befehle in MYSQL ausführen zu können, müssen wir in die MYSQL Konsole. Dies erfolgt mit diesem Befehl `sudo mysql`

Danach habe ich die Installation mit folgendem Befehl überprüft `SELECT user,authentication_string,plugin,host FROM mysql.user;`

Wenn die Benutzer in einer Tabelle aufgelistet werden, wurde MYSQL erfolgreich installiert.
