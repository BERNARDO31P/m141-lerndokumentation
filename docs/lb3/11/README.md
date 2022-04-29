# 11. Tag
## Auftrag 1
Das Importieren einer Sammlung in einer Datenbank geht wiefolgt:

```bash
mongoimport --db=cities --collection=cities --legacy --file=/home/vagrant/Downloads/mongo_cities1000.json
```

## Auftrag 2
Das Auflisten aller Städte aus der Timezone "Europa" geht wiefolgt:

```javascript
> use cities
> db.cities.find({
    timezone: /^Europe/
})
```

## Auftrag 3
Die Query berechnet die durchschnittliche Bevölkerung der Städte in der Zeitzone "Europa/London".

## Auftrag 4
Die Query sortiert die Städte der Zeitzone "Europa/London" nach Bevölkerung absteigend.