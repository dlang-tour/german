# Datenbankverbindungen

Mit vibe.d können Datenbanken einfach in das Backend
eingebunden werden. **MongoDB** und **Redis** kommen
direkt in vibe.d eingebaut. Andere Datenbankadapter
können auf [code.dlang.org](https://code.dlang.org)
gefunden werden.

### MongoDB

Zugang zu MongoDB kann mithilfe der Klasse
[`MongoClient`](http://vibed.org/api/vibe.db.mongo.client/MongoClient)
erreicht werden.
Die Implementation hat keine extrene Abhängigkeiten und verwendet
asynchrone vibe.d Sockets. Hierdurch wird eine Anfrage nicht unnötig
blockiert.

    auto client = connectMongoDB("127.0.0.1");
    auto users = client.getCollection("users");
    users.insert(Bson("peter"));

### Redis

Redis kann ebenfalls asynchron verwendet werden und
benötigt auch keine externen Abhängigkeiten. Mit der
[`RedisDatabase`](http://vibed.org/api/vibe.db.redis.redis/RedisDatabase)
Klasse können Anfragen auf einen Redis-Server getätigt werden.
Zusätzlich gibt es Hilfsklassen wie
[`RedisList`](http://vibed.org/api/vibe.db.redis.types/RedisList),
welche die Nutzung vereinfachen und zum Beispiel erlauben Listen
in einer Redis-Datenbank zu benutzen.

### MySQL

MySQL kann mithilfe der
[mysql-native](http://code.dlang.org/packages/mysql-native)
Bibliothek eingebunden werden. Diese Bibliothek hat keine
externen Abhängigkeiten und unterstützt ebenfalls die
nicht-blockierenden vibe.d sockets.

### Postgresql

Eine volle Postgresql Implementation kann mit  der
[dpq2](http://code.dlang.org/packages/dpq2) Bibliothek
eingebunden werden. Sie basiert auf der offiziellen
*libpg* Bibliothek und kann das vibe.d Eventsystem
verwenden um asynchrone Aufgaben zu erledigen.

Alternativ kann [ddb](http://code.dlang.org/packages/ddb)
verwendet werden, welches vibe.d Sockets benutzt und keine
externen Abhängigkeiten besitzt.


