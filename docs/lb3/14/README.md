# 14. Tag
## Replikation bei MySQL
### Auftrag 1
1. Es werden 3 MySQL Nodes erstellt und ein Router
2. Es wird ein Cluster Netzwerk erstellt
3. Der Master kann lesen und schreiben
4. Die Slaves können nur lesen

### Auftrag 2
Funktioniert.

### Auftrag 3
Leider funktioniert das Cluster nicht. Die Slaves können sich nicht mit dem Master verbinden.
In den Router Logs steht, dass er auf Verbindungen wartet -> "Waiting for cluster members..".


## ProxySQL und Spring Boot
### Auftrag 1
Das Projekt enthält kein docker-compose File.

### Auftrag 2
Unter Windows ist diese Projekt nicht wirklich gut ausführbar. "sh Dateien"