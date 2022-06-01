# 13. Tag
## Datenbank design MongoDB
Bei normalen relationalen Datenbanken teilen man die Daten auf. Man versucht Redundanz so klein wie möglich zu halten.

Bei MongoDB ist alles Dokument-basierend, d.h. man kann auch ein Dokument als Wert angeben.

**Beispiel**
```json
{
    "name": "Bernardo",
    "klassen": ["IN19-23b", "IN20-24b"],
    "rechte": {
        "klassenZeigen": true,
        "notenZeigen": true
    }
}
```

## Übung zu Indexierung und Performance
### Auftrag 1 / 2
```javascript
populatePhones = function(area, start, stop) {
    for(var i = start; i < stop; i++) {
    var country = 1 + ((Math.random() * 8) << 0);
    var num = (country * 1e10) + (area * 1e7) + i;
    var fullNumber = "+" + country + " " + area + "-" + i;
    db.phones.insert({
        _id: num,
        components: {
        country: country,
        area: area,
        prefix: (i * 1e-4) << 0,
        number: i
        },
        display: fullNumber
    });
    print("Inserted number " + fullNumber);
    }
    print("Done!");
}


populatePhones(800, 5550000, 5650000);
```
Diese Funktion generiert in einer Schleife random Daten. 

Mit `db.phones.find().limit(2)` kann man zwei Einträge einsehen:
```json
{ "_id" : 28005550000, "components" : { "country" : 2, "area" : 800, "prefix" : 555, "number" : 5550000 }, "display" : "+2 800-5550000" }

{ "_id" : 78005550001, "components" : { "country" : 7, "area" : 800, "prefix" : 555, "number" : 5550001 }, "display" : "+7 800-5550001" }
```

### Auftrag 3
```javascript
db.getCollectionNames().forEach(function(collection) {
     print("Indexes for the " + collection + " collection:");
     printjson(db[collection].getIndexes());
});
```

Mit dem kann man alle Indices auflisten:
```text
Indexes for the phones collection:
[{ 
    "v" : 2, 
    "key" : { "_id" : 1 }, 
    "name" : "_id_" 
}]
```

### Auftrag 4
Jetzt testen wir die Performance mit diesen vordefinierten Indices:

```javascript
db.phones.explain("executionStats").find({display: "+1 800-5650001"})
```

Und das ergibt:
```json
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "phones.phones",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"display" : {
				"$eq" : "+1 800-5650001"
			}
		},
		"winningPlan" : {
			"stage" : "COLLSCAN",
			"filter" : {
				"display" : {
					"$eq" : "+1 800-5650001"
				}
			},
			"direction" : "forward"
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 0,
		"executionTimeMillis" : 2,
		"totalKeysExamined" : 0,
		"totalDocsExamined" : 4343,
		"executionStages" : {
			"stage" : "COLLSCAN",
			"filter" : {
				"display" : {
					"$eq" : "+1 800-5650001"
				}
			},
			"nReturned" : 0,
			"executionTimeMillisEstimate" : 0,
			"works" : 4345,
			"advanced" : 0,
			"needTime" : 4344,
			"needYield" : 0,
			"saveState" : 4,
			"restoreState" : 4,
			"isEOF" : 1,
			"direction" : "forward",
			"docsExamined" : 4343
		}
	},
	"serverInfo" : {
		"host" : "m141vm",
		"port" : 27017,
		"version" : "4.4.13",
		"gitVersion" : "df25c71b8674a78e17468f48bcda5285decb9246"
	},
	"ok" : 1
}
```

Da ich die Funktion nicht komplett durchlaufen liess, habe ich auch weniger Daten zum Durchlaufen, deswegen ist die Laufzeit 2ms: `"executionTimeMillis" : 2,`

### Auftrag 5
Jetzt erstellen wir neue Indices:
```javascript
db.phones.createIndex(
  { display : 1 },
  { unique : true, dropDups : true } // soll unique sein, also droppen wir Duplikate
)
```

Jetzt benötigt der Befehlt keine Millisekunde mehr: `"executionTimeMillis" : 0,`


## Übung zu Sharding und Replication
### Replication
#### Vorbereitung
Wenn die Authentifizierung aktiviert ist, muss ein Keyfile angegeben werden. Dies geht wie folgt:
`sudo openssl rand -base64 756 > sudo /etc/mongod-keyfile`

Danach muss die Hauptinstanz konfiguriert werden:
```yaml
security:
  authorization: enabled
  keyFile: /etc/mongod-keyfile
...

replication:
  replSetName: "book"
```

#### Auftrag 1
Wir brauchen Data-Directories für 3 MongoDB-Instanzen:
```bash
mkdir ./mongo1 ./mongo2 ./mongo3
```

#### Auftrag 2
Jetzt starte ich die erste Instanz:
```bash
sudo mongod --replSet book --dbpath ./mongo1 --port 27011 --keyFile "/etc/mongod-keyfile"
```

und starte die zwei weiteren Instanzen in neuen Terminals:
```bash
sudo mongod --replSet book --dbpath ./mongo2 --port 27012 --keyFile "/etc/mongod-keyfile"
sudo mongod --replSet book --dbpath ./mongo3 --port 27013 --keyFile "/etc/mongod-keyfile"
```

#### Auftrag 3
**Achtung**
Diese Befehl funktioniert nur, wenn der Benutzer die Root Rolle besitzt!

Jetzt starte ich die Initialisierung in der `mongo` shell:
```javascript
rs.initiate()
rs.status()
```

Und so kann man die Hosts hinzufügen:
```javascript
rs.add("localhost:27011")
rs.add("localhost:27012")
rs.add("localhost:27013")
```

Damit man Daten von anderen Hosts auslesen kann, muss man dies erlauben: `rs.secondaryOk()`

#### Auftrag 4
Zum testen fügen wir einen Testeintrag hinzu: `db.echo.insert({ say : 'HELLO!' })`

und jetzt loggen wir uns im secondary ein: `mongo --authenticationDatabase "admin" -u "admin" -p "Sonne-123" --host localhost --port 27011`

und lesen den Eintrag aus: `db.echo.find()`
```json
{ 
    "_id" : ObjectId("627e13bf19f943f9546c8dee"),
    "say" : "HELLO!" 
}
```


### Sharding
#### Auftrag 1
Ich erstelle neue mongo Instanzen:
```bash
mkdir ./mongo4 ./mongo5
mongod --shardsvr --dbpath ./mongo4 --port 27014 # Separates Terminal
mongod --shardsvr --dbpath ./mongo5 --port 27015 # Separates Terminal
```

#### Auftrag 2
Jetzt wird noch ein Konfigurationsserver benötigt:
```bash
mkdir ./mongoconfig
mongod --configsvr --replSet configSet --dbpath ./mongoconfig --port 27016
```

#### Auftrag 3
Nach diesem Schritt kann man sich beim Konfigurationsserver anmelden:
`mongo --host localhost --port 27016`

Jetzt wird der Konfigurationsserver initialisiert:
```javascript
rs.initiate({
    _id: 'configSet',
    configsvr: true,
    members: [
      {
        _id: 0,
        host: 'localhost:27016'
      }
    ]
})
```

#### Auftrag 4
Wir benötigen noch einen weiteren Dienst : `mongos` Der Dienst mongos ist dafür verantwortlich, den Cluster zu managen und die Clientanfragen entgegegen zu nehmen:

`mongos --configdb configSet/localhost:27016 --port 27020`

#### Auftrag 5
Jetzt muss mongos auch wissen, welche Hosts verfügbarsind:
```javascript
sh.addShard('localhost:27014')
sh.addShard('localhost:27015')
```

Jetzt aktivieren wir das sharding für die Datenbank test:
```javascript
use admin
db.runCommand({ enablesharding : "test" })
db.runCommand({ shardcollection : "test.cities", key : {name : 1} })
```

