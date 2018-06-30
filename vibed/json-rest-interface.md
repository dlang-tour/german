# JSON REST-Interface

Vibe.d erlaubt es schnell ein JSON webservice zu erstellen.
Wenn man eine JSON-Ausgabe im folgenden Format für eine
Anfrage an `/api/v1/chapters` implementieren will:

    [
      {
        "title": "Hello",
        "id": 1,
        "sections": [
          {
            "title": "World",
            "id": 1
          }
        ]
      },
      {
        "title": "Advanced",
        "id": 2,
        "sections": []
      }
    ]

erstellt man zuerst ein Interface, welches die
`getChapters()` Funktion definiert und D
`struct`s, welche **1:1** serialisiert werden:

    interface IRest
    {
        struct Section {
            string title;
            int id;
        }
        struct Chapter {
            string title;
            int id;
            Section[] sections;
        }
        @path("/api/v1/chapters")
        Chapter[] getChapters();
    }

Um die Daten wirklich zu füllen wird eine Klasse erstellt,
die dieses Interface implementiert und die eigentliche
Logik beinhaltet:

    class Rest: IRest {
        Chapter[] getChapters() {
          // fill
        }
    }

Nun kann eine Instanz der `Rest` Klasse auf einem
`URLRouter` objekt registriert werden. Das Interface
funktioniert somit schon.

    auto router = new URLRouter;
    router.registerRestInterface(new Rest);

Vibe.d *REST-Interface Generatoren* unterstützen
auch `POST`-Anfragen, bei denen die Anfrageparameter
auf die Funktionsargumente übersetzt werden.

Das `interface` kann nun zusätzlich als Client
verwendet werden, um Anfragen an einen beliebigen
Server mit dieser API zu senden und die Daten zu
deserialisieren. Hiermit kann man Code zwischen Server
und Client teilen.

    auto api = new RestInterfaceClient!IRest("http://127.0.0.1:8080/");
    // Sendet GET /api/v1/chapters und deserialisiert
    // die Antwort zu einem IRest.Chapter[]
    auto chapters = api.getChapters();

## {SourceCode:disabled}

```d
import vibe.d;

interface IRest
{
    struct Section
    {
        string title;
        int id;
    }

    struct Chapter
    {
        string title;
        int id;
        Section[] sections;
    }

    @path("/api/v1/chapters")
    Chapter[] getChapters();

    /*
    Post data as:
        { "title": "D Language" }
    */
    @path("/api/v1/add-chapter")
    @method(HTTPMethod.POST)
    int addChapter(string title);
}

class Rest: IRest
{
    private Chapter[] chapters_;

    this()
    {
        chapters_ = [ Chapter("Hello", 1,
                [ Section("World", 1) ] ),
                 Chapter("Advanced", 2) ];
    }

    Chapter[] getChapters()
    {
        return chapters_;
    }

    int addChapter(string title)
    {
        import std.algorithm : map, max, reduce;
        // Generate the next highest ID
        auto newId = chapters_.map!(x => x.id)
                            .reduce!max + 1;
        chapters_ ~= Chapter(title, newId);
        return newId;
    }
}

shared static this()
{
    auto router = new URLRouter;
    router.registerRestInterface(new Rest);

    auto settings = new HTTPServerSettings;
    settings.port = 8080;
    listenHTTP(settings, router);
}
```
