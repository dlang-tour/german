# Webserver

Mit vibe.d kann man sehr einfach HTTP(S) Webserver
erstellen:

    auto settings = new HTTPServerSettings;
    settings.port = 8080;
    listenHTTP(settings, &foo);

Dieser Code startet einen Webserver auf Port 8080,
bei dem alle Anfragen von einer `foo` Funktion
verarbeitet werden:

    void foo(HTTPServerRequest req,
        HTTPServerResponse res) { ... }

Um normale Benutzung und Konfiguration von verschiedenen
Pfäden zu erleichtern, wird eine `URLRouter` Klasse
bereitgestellt, mit der `GET`, `POST`, etc. Pfäde
registriert werden können. Hierzu können zum Beispiel
die `.get("pfad", handler)` oder `.post("pfad", handler)`
Methoden des `URLRouter` Objekts verwendet werden.
Alternativ kann eine eigene *Web-Interface* Klasse
erstellt werden, welche die Serverpfäde als Methoden
implementiert:

    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    listenHTTP(settings, router);

Alle Pfäde werden in der eigenen `WebService` Klasse
automatisch mit einem einfachem Schema hergeleitet:
* `index()` verarbeitet Anfragen an `/index`
* `getName()` verarbeitet `GET`-Anfragen an `/name`
* `postUsername()` vearbeitet `POST`-Anfragen
  an `/username`

Benutzerdefinierte Pfäde können mit einem
`@path("/hello/world")` Attribut an die
dementsprechende Methode festgelegt werden.
Parameter für Anfragen werden über Funktionsargumente
übergeben. Man kann auch Parameter im Pfad angeben und
diese mit einem Argument, das mit `_` anfängt, auslesen:

    @path("/my/api/:id")
    void foo(int _id)

Bei einem Web-Interface muss man keine `HTTPServerResponse` oder
`HTTPServerRequest` Argumente zu den Funktionen angeben. Vibe.d
überprüft jedoch trotzdem, ob man eins angegeben hat und
übergibt die Objekte falls benötigt.

## {SourceCode:disabled}

```d
import vibe.d;

class WebService
{
    /*
    Session-Variablen wie diese erlauben Daten
    mit einzelnen Nutzern zu verbinden. Diese
    Daten leben für die gesamte Lebenszeit der
    Session vom Nutzer.
    */
    private SessionVar!(string, "username")
        username_;

    /*
    Anfragen an den Root-Pfad ("/") führen
    standardmäßig die index Methode aus.
    */
    void index(HTTPServerResponse res)
    {
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <form action="/username" method="POST">
        Your name:
        <input type="text" name="username">
        <input type="submit" value="Submit">
        </form>
        </body>
        </html>};

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    /*
    Das @path Attribut kann benutzt werden um
    Benutzerdefinierte Pfäde festzulegen. Hier
    werden Anfragen an "/name" an die getName
    Methode geleitet.
    */
    @path("/name")
    void getName(HTTPServerRequest req,
            HTTPServerResponse res)
    {
        import std.string : format;

        // Generate header info <li>
        // tags by inspecting the request's
        // headers property.
        string[] headers;
        foreach(key, value; req.headers) {
            headers ~=
                "<li>%s: %s</li>"
                .format(key, value);
        }
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <h1>Your name: %s</h1>
        <h2>Headers</h2>
        <ul>
        %s
        </ul>
        </body>
        </html>}.format(username_,
                headers.join("\n"));

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    void postUsername(string username,
            HTTPServerResponse res)
    {
        username_ = username;
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <h1>Your name: %s</h1>
        </body>
        </html>}.format(username_);

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }
}

void helloWorld(HTTPServerRequest req,
        HTTPServerResponse res)
{
    res.writeBody("Hello");
}

void main()
{
    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    router.get("/hello", &helloWorld);

    auto settings = new HTTPServerSettings;
    // Für Session-Unterstützung benötigt.
    settings.sessionStore =
        new MemorySessionStore;
    settings.port = 8080;
    listenHTTP(settings, router);
    runApplication();
}
```
