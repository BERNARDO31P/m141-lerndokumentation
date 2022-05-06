# 12. Tag
## Anwendung modifizieren
1. Erfassen Sie auf der MongoDB-Instanz einen Systemadministrator

```javascript
use admin
db.createUser(
    {
        user: "userAdmin",
        pwd: "PW123",
        roles: [
        { role: "userAdminAnyDatabase", db: "admin" }
        ]
    }
)
```

2. Erfassen Sie auf der MongoDB-Instanz einen Applikationsbenutzer für diese Applikation

```javascript
use admin
db.createUser(
    {
        user: "appUser",
        pwd: "PW123",
        roles: [
        { role: "readWriteAnyDatabase", db: "admin" }
        ]
    }
)
```

3. Passen Sie die URI in der Datei server.js entsprechend an
**Vorher**
```javascript
const db = process.env.MONGODB_URI || 'mongodb://localhost:27017/mfgdashboard';
```

**Nachher**
```javascript
const db = process.env.MONGODB_URI || 'mongodb://appUser:PW123@localhost:27017/mfgdashboard?authSource=admin';
```

**Wichtig**
Man muss natürlich angeben, in welcher Datenbank/Tabelle die Anmeldedaten gespeichert sind, dies geht wie folgt: `?authSource=admin`