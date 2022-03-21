# 6. Tag
## Backup, Import, Export
### Export in CSV
```sql
SELECT pokemon.pok_name FROM pokemon
INNER JOIN pokemon_moves ON pokemon.pok_id = pokemon_moves.pok_id
WHERE pokemon_moves.move_id = 7
INTO OUTFILE '/var/lib/mysql-files/pokemonsWithFirePunch.csv' -- Achtung: Pfad aufpassen!!! <- Siehe Script
FIELDS ENCLOSED BY '"' -- Jedes Feld wird mit "" eingefasst
TERMINATED BY ';' -- Feld wird mit einem Semikolon beendet
LINES TERMINATED BY '\n'; -- Zeilenendings nach Linux-Style (\r\n für Windows)
```

### Import mit CSV
```sql
LOAD DATA INFILE '/var/lib/mysql-files/pokemonsWithFirePunch.csv' 
INTO TABLE discounts 
FIELDS TERMINATED BY ';' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

### Backup-Script
```bash
#!/bin/bash
# Add the backup dir location, MySQL root password, MySQL and mysqldump location
DATE=$(date +%d-%m-%Y)
BACKUP_DIR="/tmp/test-backup"
MYSQL_USER='root'
MYSQL_PASSWORD='root'
MYSQL=/usr/bin/mysql
MYSQLDUMP=/usr/bin/mysqldump

# To create a new directory in the backup directory location based on the date
mkdir -p $BACKUP_DIR/$DATE

# To get a list of databases
databases=`$MYSQL -u$MYSQL_USER -p$MYSQL_PASSWORD -e "SHOW DATABASES;" | grep -Ev "(Database|information_schema)"
# To dump each database in a separate file
for db in $databases; do
    echo $db 
    $MYSQLDUMP --single-transaction --force --opt --skip-lock-tables --user=$MYSQL_USER -p$MYSQL_PASSWORD --database 
done

# Delete the files older than 10 days
find $BACKUP_DIR/* -mtime +10 -exec rm {} \;
```

#### Cron-Eintrag
Wenn man ein Script automatisch zu einer bestimmten Zeit ausführen möchte, kann man dies mit einem Cronjob tun. 
Hierfür gibt man einfach `crontab -e` ein und fügt die bearbeitete Zeile ein: `[minute] [hour] [day_of_month] [month day_of_week] command_to_run`

### mydumper
```bash
#!/bin/bash

DB_NAME='pokemon'
DB_HOST='localhost'
DB_USER='root'
DB_PASS='root'
DB_DUMP='/tmp/testmydumper'

mkdir -p $DB_DUMP

mydumper \
--database=$DB_NAME \
--host=$DB_HOST \ 
--user=$DB_USER \ 
--password=$DB_PASS \ 
--outputdir=$DB_DUMP \ 
--rows=500000 \ 
--compress \ 
--build-empty-files \ 
--threads=2 \ 
--compress-protocol
```

### myloader
```bash
#!/bin/bash

DB_NAME='pokemon'
DB_HOST='localhost'
DB_USER='root'
DB_PASS='root'
DB_DUMP='/tmp/testmydumper'

myloader \
--database=$DB_NAME \
--directory=$DB_DUMP \
--queries-per-transation=50000 \
--threads=10 \
--compress-protocol \
--verbose=3
```