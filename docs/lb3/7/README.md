# 7. Tag
[Diverse Backup-Vorgehen](lb3/6/?id=backup-import-export)
[Diverse Import-Vorgehen](lb3/6/?id=backup-import-export)

## Percona XtraBackup
[Quelle](https://www.capterra.ch/software/205293/percona-xtrabackup-for-mysql)

### Was ist Percona XtraBackup?
Percona XtraBackup ist eine kostenlose, quelloffene, vollständige Online-Backup-Lösung für alle Versionen von Percona Server für MySQL, MySQL und MariaDB. Percona XtraBackup führt nicht-blockierende, stark komprimierte, hochsichere Online-Backups von InnoDB-, XtraDB- und HailDB-Speicher-Engines auf transaktionalen Systemen durch, so dass Anwendungen während geplanter Wartungsfenster voll verfügbar bleiben. Percona XtraBackup kann Streaming-, komprimierte und inkrementelle MySQL-Backups durchführen.

### Wer verwendet Percona XtraBackup?
Percona XtraBackup ist die ideale Backup-Lösung für jedes Unternehmen, das MySQL einsetzt. Es führt nicht-blockierende Backups von Standalone-, Master/Slave- und geclusterten MySQL-Umgebungen durch.

### Wo kann Percona XtraBackup bereitgestellt werden?
Cloud, SaaS, webbasiert


## Etherpad
### Installation
Zuerst muss man sein System auf den neusten Stand bringen.
```linux
sudo apt-get update
sudo apt-get upgrade
```

Danach benötigt man diverse Pakete, damit man Etherpad kompilieren kann.
```linux
sudo apt install libssl-dev pkg-config gcc g++ make build-essential
```

Jetzt kann man NodeJS herunterladen.
```linux
cd /opt
wget https://nodejs.org/dist/v12.17.0/node-v12.17.0-linux-x64.tar.xz
tar xJf node-v*-linux-x64.tar.xz
mkdir /opt/nodejs/ && mv node-*-linux-x64/* /opt/nodejs
```

Falls noch keine MySQL Instanz existiert, kann man diese installieren.
```linux
sudo apt-get install mysql-server mysql-client
```

Jetzt kann man die notwendige Struktur für Etherpad erstellen.
```sql
create database etherpad_db;
create user 'etherpad'@'localhost' identified with mysql_native_password by 'password';
grant all privileges on etherpad_db.* to 'etherpad'@'localhost';
flush privileges;
quit
```

Nachdem die Datenbank konfiguriert wurde, benötigt das System auch einen dedizierten Benutzer.
```linux
sudo adduser --home /opt/etherpad --shell /bin/bash etherpad
sudo install -d -m 755 -o etherpad -g etherpad /opt/etherpad
sudo su - etherpad
PATH=$PATH:/opt/nodejs/bin
echo "PATH=$PATH:/opt/nodejs/bin" >> ~/.profile
```

Mit `node --version` kann man überprüfen ob die Installation erfolgreich war. Die Ausgabe müsste `v12.17.0` sein.

Jetzt kann man Etherpad herunterladen.
```linux
git clone https://github.com/ether/etherpad-lite.git ~/etherpad-lite
```

Jetzt geht man ins heruntergeladene Verzeichnis und startet Etherpad.
```linux
cd /opt/etherpad/etherpad-lite/
bin/run.sh
```

Jetzt muss man die Einstellungen erstellen, hierfür nutzen wir ein Template.
```linux
cp settings.json.template settings.json
```

In diesen Einstellungen muss man jetzt einige Sachen anpassen:
"ip": "0.0.0.0" mit "ip": "127.0.0.1" suchen und ersetzen.

Folgende Zeilen:
```json
"dbType" : "dirty",
"dbSettings" : {
    "filename" : "var/dirty.db"
},
```

mit diesen ersetzen:
```json
"dbType" : "mysql",
"dbSettings" : {
    "user"    : "etherpad",
    "host"    : "localhost",
    "port"    : 3306,
    "password": "password",
    "database": "etherpad_db"
},
```

Danach "trustProxy": false mit "trustProxy": true ersetzen.
Zuletzt noch dem Admin Benutzer das Passwort zuteilen:
```json
"users": {
    "admin": {
        // "password" can be replaced with "hash" if you install ep_hash_auth
        "password": "password",
        "is_admin": true
    },
...
}
```
Hier ist zu beachten, dass dieser Block auskommentiert wurde, man muss die Kommentarzeichen entfernen, damit es funktioniert.

Jetzt kann man noch diese Befehle ausführen um alle Abhängigkeiten zu installieren und Etherpad zu starten.
```linux
~/etherpad-lite/bin/installDeps.sh
~/etherpad-lite/bin/run.sh
```

### Tests
Mit `USE etherpad_db;` kann man die Datenbank betreten.
Danach kann man mit diversen Befehlen die Struktur anzeigen:
```sql
SHOW TABLES; // Zeigt die Tabellen in der DB
SHOW COLUMNS FROM store; // Zeigt die Spalten in der Tabelle
DESCRIBE store; // Zeigt die Spalten und Informationen in der Tabelle
```

Dabei ist mir aufgefallen, dass alles in die store Tabelle gespeichert wird.
Der Aufbau ist sehr einfach, Schlüssel -> Wert.
