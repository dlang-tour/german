# Einstieg & Asynchrone I/O

Mit den normalen Optionen von dub wird statt der `main()`
Funktion ein spezieller `shared static this()`
Modulkonstruktor verwendet:

    import vibe.d;
    shared static this() {
        // Vibe.d code here
    }

Ein Modulkonstruktor wird vor der `main()` genau einmal
ausgeführt. Vibe.d verwendet seine eigene `main()` Funktion
und versteckt darin die Event-Loop und Verarbeitungsmethoden.

Vibe.d verwendet *Fiber* um asynchrone I/O zu implementieren:
jedes mal wenn ein Socket blockiert, zum Beispiel weil wir
auf Daten warten, verwendet der aktuelle Fiber ein `yield`
Aufruf um den Aktuellen Kontext zu pausieren und bei einem
anderen weiter zu arbeiten. Sobald in diesem Beispiel Daten
vorhanden sind, wird die Ausführung bei nächster Gelegenheit
fortgesetzt:

    // Könnte Code blockieren.
    // Sobald Daten vorhanden sind
    // setzt vibe.d hier fort.
    line = connection.readLine();
    // Könnte auch blockieren.
    connection.write(line);

Dieser Code sieht aus, als wäre er *synchron* und würde den
aktuellen Thread blockieren, aber mithilfe der Fiber wird
eigentlich noch weiterer Code im Hintergrund ausgeführt.
Der Code sieht sauber und konkret aus, benutzt aber im
Hintergrund asynchrone I/O um tausende Verbindungen
gleichzeitig auf einem einzelnen Kern zu erlauben.

Alle vibe.d Features benutzen Fiber-basierte asynchrone I/O,
daher muss man sich beim schreiben der Anwendung keine
Gedanken machen, dass eine langsame MongoDB Verbindung die
gesamte Anwendung aufhängen könnte.

Schau dir dieses Beispiel an um zu sehen, wie man einen
einfachen TCP basierten Echo-Server erstellt.

## {SourceCode:disabled}

```d
import vibe.d;

shared static this()
{
    // Warte auf TCP-Port 8080 auf Verbindungen.
    // Bei jeder neuen Verbindung wird der
    // Delegate im zweiten Argument ausgeführt.
    // conn ist hierbei das Verbindungsobjekt
    // zum Client.
    listenTCP(8080, (TCPConnection conn) {
        string line;
        conn.write("ECHO server says Hi!\r\n");
        conn.write("Type 'quit' to close.\r\n");
        while (line != "quit") {
            line = cast(string) conn.readLine();
            conn.write("ECHO: " ~ line
              ~ "\r\n");
        }

        // Das Verlassen des Delegates schließt
        // die Verbindung zum Client.
    });
}
```
