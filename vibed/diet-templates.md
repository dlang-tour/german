# DIET-Templates

Um Webseiten einfacher zu gestallten unterstützt vibe.d
[DIET-Templates](https://vibed.org/templates/diet),
welche eine Vereinfachung von HTML darstellen und
Einbindung von D-Code erlauben.
DIET basiert auf
[Pug/Jade templates](https://pugjs.org/).

    doctype html
    html(lang="en")
      head
        // D-Code wird ausgeführt
        title #{pageTitle}
        // Attribute
        script(type='text/javascript')
          if (foo) bar(1 + 5)
        // ID = body-id
        // class = the-style
        body#body-id.the-style
          h1 DIET template

Der Syntax basiert auf Einrückung und benötigt daher
keine schließenden Tags.

Alle DIET-Templates sind Kompiliert und werden im
Speicher für maximale Performance gehalten.
DIET-Templates erlauben eingebetteten D-Code, der
beim Rendern der Seite ausgeführt wird. Einzelne
Ausdrücke können in einem `#{ 1 + 1 }` eingebunden
werden und überall im Template verwendet werden.
Komplette Zeilen D-Code können mit einem
vorausgestellten `-` in einer eigenen Zeile eingebettet
werden:

    - foreach(title; titles)
      h1 #{title}

Komplexe Ausdrücke können so dargestellt werden.
Sogar Funktionen können definiert werden und HTML
ausgeben.

DIET-Templates werden mithilfe von **CTFE**
Kompiliert und leben im `views` Ordner in einem
Standard vibe.d Projekt. Um ein DIET-Template wirklich
anzuzeigen wird die `render` funktion auf einem
`HTTPServerResponse` Objekt verwendet:

    void foo(HTTPServerResponse res) {
        string pageTitle = "Hello";
        int test = 10;
        res.render!("my-template.dt", pageTitle, test);
    }

In einem Web-Interface wird stattdessen die
globale Methode `render` benutzt.

Alle D-Variablen, die als Template-Argument übergeben
werden, sind im DIET-Template verfügbar.

## {SourceCode:disabled}

```d
doctype html
html
  head
    title D statement test
  body
    - import std.algorithm : min;
    p Four items ahead:
    - foreach( i; 0 .. 4 )
      - auto num = i+1;
      p Item
        b= num
    p Prints 8:
    p= min(10, 2*6, 8)
```
