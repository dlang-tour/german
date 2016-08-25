# D Programme lokal ausführen

Eine D Installation beinhaltet unter anderem den D Compiler `dmd`,
ein Skripttool `rdmd` und den Packetmanager `dub`.

### DMD Compiler

Der *DMD* Compiler kompiliert D Dateien und erstellt eine Binärdatei.
Auf der Kommandozeile kann *DMD* mit dem Dateinamen aufgerufen werden:

    dmd hallo.d

Es gibt viele Optionen, die das Verhalten des *DMD* Compilers verändern.
Diese sind in der [Online-Dokumentation](https://dlang.org/dmd.html#switches))
oder per `dmd --help` zu finden.

### Fliegende Kompilierung mit `rdmd`

Das Programm `rdmd`, welches zusammen mit dem DMD Compiler angeboten wird,
sorgt dafür, dass alle Abhängigkeiten kompiliert sind und führt die kompilierte
Datei automatisch aus:

    rdmd hallo.d

Auf UNIX System kann das Shebangkommando `#!/usr/bin/env rdmd` als erste Zeile
in einer D-Quelldatei mit Ausführberechtigungen eingefügt werden und erlaubt eine
Ausführung der Datei analog zu Skriptsprachen.

Weitere Informationen sind in der [Online-Dokumentation](https://dlang.org/rdmd.html)
oder per `rdmd --help` zu finden.

### Paketmanager `dub`

Der Standardpaketmanager für D ist [`dub`](http://code.dlang.org). Wenn `dub` lokal
installiert ist, kann ein neues Project `hello` mit folgenden Kommando angelegt
werden:

    dub init hello

Wenn der Paketmanager per `dub` innerhalb eines Projektes ausgeführt wird,
werden alle Abhängigkeiten des Projektes herunterladen, kompiliert, gelinkt
und anschließend wird die Anwendung ausgeführt.
Das Kommando `dub build` verhält sich ähnlich, aber führt die Anwendung nicht aus.

Weitere Informationen sind in der [Online-Dokumentation](https://code.dlang.org/docs/commandline)
oder per `dub --help` zu finden.

All vorhandene `dub` Pakete können durch das [Web portal](https://code.dlang.org)
von `dub` durchsucht werden.
