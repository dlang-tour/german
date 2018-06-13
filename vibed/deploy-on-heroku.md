# Deploy on Heroku

### Voraussetzungen

- Heroku [Account](https://signup.heroku.com/login)
- [Git](https://git-scm.com/) installiert
- Die Anwendung muss ohne Probleme mit dmd kompilieren (`dub build`).

### 1 : Die Anwendung einrichten

Heroku benötigt einen Weg zur Kommunikation mit der Anwendung.
Daher wird eine globale `PORT` Umgebungsvariable gesetzt, welche
die Anwendung benutzen muss.
Für die Entwicklung sollte ein Standardport (hier __8080__) benutzt werden:

```d
shared static this() {
  // ...
  auto settings = new HTTPServerSettings;
  // Standardport falls $PORT Umgebungsvariable nicht gesetzt.
  settings.port = environment.get("PORT", "8080").to!ushort;
  listenHTTP(settings, router);
}
```

Zusätzlich wird eine `Procfile` benötigt, welche eine Textdatei
im Hauptverzeichnis der Anwendung darstellt. Diese Datei beinhaltet
welcher Befehl ausgeführt werden muss, um die Anwendung auszuführen.

Die `Procfile` in der Beispielanwendung sieht wie folgt aus:

```
web: ./hello-world
```

### 2 : Die Anwendung vorbereiten

Zuerst muss eine Anmeldung über die Heroku Command-Line erfolgen.
Nutze hierzu das [Heroku Toolbelt](https://toolbelt.heroku.com/standalone).

Dies erlaubt Zugang an das Heroku Command-Line Interface (CLI),
mit dem die Anwendung bearbeitet und skalliert werden kann.

Nach der Installation des Toolbelt muss folgender Befehl ausgeführt werden:

```
$ heroku login
```

### 3 : Erstellen der Anwendung

Öffne das [heroku dashboard](https://dashboard.heroku.com) und erstelle
eine neue Anwendung. Der Name der Anwendung wird später benötigt.

Alternativ kann die Command-Line verwendet werden:

```
$ heroku create
Creating app... done, ⬢ rocky-hamlet-67506
https://rocky-hamlet-67506.herokuapp.com/ | https://git.heroku.com/rocky-hamlet-67506.git
```

Der Name der Anwendung ist hier *rocky-hamlet-67506*.

### Mit git veröffentlichen

Um die Anwendung direkt zu veröffentlichen wird `git` verwendet.
Ein neuer git Endpoint wird erstellt um neue Versionen zu
veröffentlichen. Hier wird der Name der Anwendung aus dem
Vorherigen Teil verwendet. Hier ist dieser *rocky-hamlet-67506*

```
$ heroku git:remote -a rocky-hamlet-67506
```

Dieser Endpoint ist nun zur git Config hinzugefügt:

```
$ git remote -v
heroku    https://git.heroku.com/rocky-hamlet-67506.git (fetch)
heroku    https://git.heroku.com/rocky-hamlet-67506.git (push)
```

### Das buildpack hinzufügen

Buildpacks werden verwendet um Assets oder kompilierten Code zu generieren.

Für mehr Informationen, siehe [Heroku Dokumentation](https://devcenter.heroku.com/articles/buildpacks)

Zum veröffentlichen kann das
[Vibe.d buildpack](https://github.com/MartinNowak/heroku-buildpack-d)
genutzt werden:

```
$ heroku buildpacks:set https://github.com/MartinNowak/heroku-buildpack-d
```

Standardmäßig benutzt das Buildpack die neuste `dmd` Compiler Version.
Es ist möglich GDC oder LDC und eine spezifische Version zu verwenden
indem man eine `.d-compiler` Datei zum Projekt hinzufügt.

`dmd`, `ldc` oder `gdc` kann für die neusten Versionen und
`dmd-2.0xxx`, `ldc-1.0xxx` oder `gdc-4.9xxx` können zum auswählen
einer spezifischen Version benutzt werden.

### Die Anwendung veröffentlichen

Zuerst muss die Anwendung wie gewohnt erstellt und getestet werden.

Um die Anwendung nun zu veröffentlichen lädt man die neuste Version
per git zum Heroku Endpoint hoch.

```
$ git add .
$ git commit -am "My first vibe.d release"
$ git push heroku master
Counting objects: 9, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 997 bytes, done.
Total 9 (delta 0), reused 0 (delta 0)

-----> Fetching custom git buildpack... done
-----> D (dub package manager) app detected
-----> Building libevent
-----> Building libev
-----> Downloading DMD
-----> Downloading dub package manager
-----> Setting PATH:
-----> Initializing toolchain
-----> Building app
       Running dub build ...
Building configuration "application", build type release
Running dmd (compile)...
Compiling diet template 'index.dt' (compat)...
Linking...
       Build was successful
-----> Discovering process types
       Procfile declares types -> web
-----> Compiled slug size: 3.5MB
-----> Launching... done, v4
       https://rocky-hamlet-67506.herokuapp.com/ deployed to Heroku
To git@heroku.com:rocky-hamlet-67506.git
 * [new branch]      master -> master
```

Nun kann die Anwendung im Browser mit folgendem Befehl geöffnet werden:

```
$ heroku open
```

### Dyno-Container skalieren

Nach dem Veröffentlichen läuft die Anwendung auf einem Web-Dyno.
Der Dyno ist quasi ein einfacher Container, der die Befehle der
Procfile ausführt.

Mithilfe des `ps` Befehls kann geprüft werden, wie viele Dynos laufen:

```
$ heroku ps
Free dyno hours quota remaining this month: 550h 0m (100%)
For more information on dyno sleeping and how to upgrade, see:
https://devcenter.heroku.com/articles/dyno-sleeping

No dynos on ⬢ rocky-hamlet-67506
```

Standardmäßig wird die Anwendung auf einem gratis Dyno
veröffentlicht, der keine Anfragen verarbeitet. Gratis Dynos
pausieren nach einer halben Stunde Inaktivität. Deshalb kann
beim aufwachen ein Delay von ein paar Sekunden entstehen.

Um den Dyno zu starten, führt man folgenden Befehl aus:

```
$ heroku ps:scale web=1
```

### Die Logs ansehen

Heroku verarbeitet logs als Reihe an Ereignissen, die aus der
Ausgabe der Anwendung und Komponenten erstellt wird. Hierdurch
hat man einen einzelnen Kanal für alle Ereignisse.

```
$ heroku logs --tail
```

## Mehr Informationen

Nach dem Veröffentlichen der Anwendung auf Heroku kann man
zusätzliche Fatures mit Add-Ons aktivieren. Zum Beispiel:

- [Postgresql](https://elements.heroku.com/addons/heroku-postgresql)
- [MongoDb](https://elements.heroku.com/addons/mongohq)
- [Logging](https://elements.heroku.com/addons#logging)
- [Caching](https://elements.heroku.com/addons#caching)
- [Error and exceptions](https://elements.heroku.com/addons#errors-exceptions)
