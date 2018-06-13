# Vibe.d Web Framework

[Vibe.d](http://vibed.org) ist ein sehr flexibles Web
Framework für D. Zum Beispiel wurde diese Tour mit vibe.d
erstellt. Hier sind einige Highlights von vibe.d:

* _Fiberbasiert_ für *asynchrone I/O*
  vibe.d erlaubt es high-performance HTTP(S) Webserver und
  Webservices zu erstellen. Es kann Code, der synchron
  aussieht, aber im Hintergrund asynchrone Aufgaben, wie
  die Verbindungsverwaltung erledigt, geschrieben werden.
  Siehe das nächste Kapitel für ein detailliertes Beispiel.
* Ein einfach zu benutzender JSON REST und Web-Interface
  Generator.
* Out-of-the-box unterstützung für Redis und MongoDB um
  einfache Backend-Systeme mit guter Performance zu
  schreiben.
* Zusätzlich können beliebige TCP oder UDP Clients und
  Server in diesem Framework geschrieben werden.

Beachte dass Beispiele in diesem Kapitel nicht online
ausgeführt werden können, da sie eine Netzwerkverbindung
benötigen würden. Dies ist aus Sicherheitsgründen
ausgeschaltet.

Der einfachste Weg ein vibe.d Projekt zu erstellen ist `dub`
zu verwenden. Erstelle hierzu ein neues Projekt mit dem
*vibe.d* template:

    dub init <project-name> -t vibe.d
    cd <project-name>
    dub

`dub` sorgt dafür, dass vibe.d heruntergeladen und für das
Projekt erstellt und eingebunden wird.

Das Buch [D Web development](https://www.packtpub.com/web-development/d-web-development)
gibt eine vollständige Einführung in das Framework.

