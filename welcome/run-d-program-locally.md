# D Programme lokal ausführen

Eine D-Installation beinhaltet unter anderem den D Compiler `dmd`,
ein Skripttool `rdmd` und den Paketmanager `dub`.

### DMD 

DMD (Digital Mars D) ist der Referenz-Compiler von D und wird für den Einstieg
empfohlen, da er die Spezifikation der Sprache am genauesten implementiert.
Er übersetzt D-Quelldateien in ausführbaren Maschinencode.
Auf der Kommandozeile kann DMD mit dem Dateinamen aufgerufen werden:

    dmd hallo.d

DMD bietet viele Optionen zur Beeinflussung des Kompliliervorgangs.
Diese sind in der [Online-Dokumentation](https://dlang.org/dmd.html#switches))
oder per `dmd --help` zu finden.
Neben DMD gibt es noch weitere [D-Compiler](https://wiki.dlang.org/Compilers).

### Fliegende Kompilierung mit `rdmd`

Das Programm `rdmd`wird zusammen mit DMD angeboten. Es sorgt für die Kompilierung
aller Abhängigkeiten und führt die erzeugte Datei automatisch aus:

    rdmd hallo.d

Auf UNIX Systemen ist die Ausführung einer D-Quelldatei analog zu Skriptsprachen möglich.
Dazu wird der Datei die Ausführberechtigung erteilt und das Shebang-Kommando
`#!/usr/bin/env rdmd` als erste Zeile eingefügt.

Weitere Informationen sind in der [Online-Dokumentation](https://dlang.org/rdmd.html)
oder per `rdmd --help` zu finden.

### Paketmanager `dub`

Der Standard-Paketmanager für D ist [`dub`](http://code.dlang.org). Wenn `dub` lokal
installiert ist, kann ein neues Projekt `hello` mit folgenden Kommando angelegt
werden:

    dub init hello

Wenn der Paketmanager per `dub` innerhalb eines Projektes ausgeführt wird,
werden alle Abhängigkeiten des Projektes herunterladen, kompiliert, gelinkt
und anschließend wird die Anwendung ausgeführt.
Das Kommando `dub build` verhält sich ähnlich, führt aber die Anwendung nicht aus.

Weitere Informationen sind in der [Online-Dokumentation](https://code.dlang.org/docs/commandline)
oder per `dub --help` zu finden.

Alle vorhandene `dub` Pakete können durch das [Web-Portal](https://code.dlang.org)
von `dub` durchsucht werden.
