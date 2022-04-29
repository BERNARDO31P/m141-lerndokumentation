# 10. Tag
## MongoDB
MongoDB ist ein dokumentenorientiertes NoSQL-Datenbankmanagementsystem, das in der Programmiersprache C++ geschrieben ist. Da die Datenbank dokumentenorientiert ist, kann sie Sammlungen von JSON-ähnlichen Dokumenten verwalten. So können viele Anwendungen Daten auf natürlichere Weise modellieren, da die Daten zwar in komplexen Hierarchien verschachtelt werden können, dabei aber immer abfragbar und indizierbar bleiben.

Die Entwicklung von MongoDB begann im Oktober 2007 durch das Unternehmen 10gen, welches am 27. August 2013 in MongoDB, Inc. umbenannt wurde. Die Erstveröffentlichung fand im Februar 2009 statt. MongoDB wurde bis zum 15. Oktober 2018 als Open Source veröffentlicht und ist seitdem unter der proprietären SSPL verfügbar. Es ist die weitest verbreitete NoSQL-Datenbank (Stand: Oktober 2019).

[Quelle](https://de.wikipedia.org/wiki/MongoDB)

### Voraussetzungen
MongoDB verbraucht ungefähr 1GB RAM pro 100'000 Einträge. Swapping sollte deaktiviert sein, da dies sonst negative Effekte hat.

[Quelle](https://learn.fotoware.com/On-Premises/FotoWeb/05_Configuring_sites/Setting_the_MongoDB_instance_that_FotoWeb_uses/MongoDB_disk_and_memory_requirements#:~:text=MongoDB%20requires%20approximately%201%20GB,performance%20and%20should%20be%20avoided.)

### Installation
Um die neuste Version von MongoDB installieren zu können, muss der öffentliche GPG-Schlüssel importiert werden:

```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```

Jetzt kann man die offizielle Repository hinzufügen:

```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```

Danach Pakete aktualisieren mit `sudo apt update`

Und jetzt kann man MongoDB installieren: 
```bash
sudo apt install mongodb-org
```

Anschliessen muss man den Dienst noch starten:

```bash
sudo systemctl start mongod.service
```

### Test der Installation
Mit `sudo systemctl status mongod.service` kann man einsehen ob der Prozess richtig gestartet wurde. 

Jetzt kann man seinen ersten Befehl ausführen und überprüfen ob auch wirklich alles funktioniert:

```bash
mongo --eval 'db.runCommand({ connectionStatus: 1 })'
```

Dieser Befehl sollte ungefähr dies ausgeben, dann wurde alles korrekt installiert:

```text
Output
MongoDB shell version v4.4.0
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("1dc7d67a-0af5-4394-b9c4-8a6db3ff7e64") }
MongoDB server version: 4.4.0
{
	"authInfo" : {
		"authenticatedUsers" : [ ],
		"authenticatedUserRoles" : [ ]
	},
	"ok" : 1
}
```

## Erste Schritte mit MongoDB
Eine Datenbank direkt erstellen, kann man mit MongoDB nicht.

Man kann jedoch eine nicht vorhandene Datenbank auswählen und danach einfach Daten reinschreiben. Dadurch wird die Datenbank und die Sammlung (collection) erstellt.

```javascript
> use neueDB
> db.user.insert({name: "Bernardo de Oliveira", age: 21})
> show dbs
admin 0.000GB
neueDB 0.000GB
local  0.000GB
```

Mit der `.find()` Funktion kann man nach Daten suchen und diese anzeigen:

```javascript
> db.user.find()
{ "_id" : ObjectId("626b801a1f0a60871dc4fc1b"), "name" : "Bernardo de Oliveira", "age" : 21 }
```

Mit der `.delete()` Funktion kann man Daten wieder löschen :
```javascript
> db.user.remove({name: /^B/})
WriteResult({ "nRemoved" : 1 })
> db.user.find()
> 
```

