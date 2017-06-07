# Klassen

D unterstützt die Verwendung von Klassen und Interfaces ähnlich wie
Java or C++.

Jeder `class`-Typ erbt implizit von [`Object`](https://dlang.org/phobos/object.html).

    class Foo { } // erbt von Object
    class Bar: Foo { } // Bar ist auch ein Foo

In D werden Klassen generell mit `new` auf dem Heap instanziiert:

    auto bar = new Bar;

Klassenobjekte sind immer Referenztypen. Anders als `struct`s, 
die beim Aufruf kopiert werden (engl: call by value), werden Objekte
nur als Referenz weitergereicht (engl.: call by reference).

    Bar bar = foo; // bar zeigt auf foo

Der Garbage Collector stellt sicher, dass der genutzte Speicher wieder
freigegeben wird, sobald keine Referenzen mehr auf das Objekt existieren.

### Vererbung

Wenn eine Memberfunktion einer Basisklasse überschrieben wird, muss das
Schlüsselwort `override` benutzt werden, um dies anzuzeigen. So wird 
ungewolltes Überschreiben von Funktionen vermieden.

    class Bar: Foo {
        override functionFromFoo() {}
    }

In D können Klassen nur von Klassen erben.

### Final und abstract Memberfunktionen

-  Eine Funktion kann als `final` markiert werden, um das Überschreiben 
zu verbieten
-  Eine Funktion kann als `abstract` markiert werden, um das Überschreiben 
zu erzwingen
- Eine ganze Klasse kann als `abstract` deklariert werden, um sicherzustellen,
dass sie nicht instanziiert wird
- `super(..)` dient zum expliziten Aufruf des Basiskonstruktors

### Prüfen der Identität

Der Inhalt von Klassenobjekten wird mithilfe der Operatoren `==` and `!=` 
verglichen. Daher ist der Vergleich gegen `null` nicht zulässig, da `null`
keinen Inhalt besitzt.
Das `is` vergleicht die Identität von Objekten. Um auf Nicht-Identität zu prüfen, 
sollte `e1 !is e2` verwendet werden.

```d
MyClass c;
if (c == null)  // Fehler
    ...
if (c is null)  // Ok
    ...
```

Bei `struct`-Objekten werden alle Bits verglichen, für andere Operanden-Typen
ist Identität gleichwertig mit Gleichheit.

### Weiterführende Quellen

- [Klassen in _Programming in D_](http://ddili.org/ders/d.en/class.html)
- [Vererbung in _Programming in D_](http://ddili.org/ders/d.en/inheritance.html)
- [Objekte in _Programming in D_](http://ddili.org/ders/d.en/object.html)
- [Spezifikation: Klassen in D](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Ausgefallener Typ der für alles genutzt 
werden kann...
*/
class Any {
    // protected kann nur bei Vererbung
    // von Klassen gesehen werden
    protected string type;

    this(string type) {
        this.type = type;
    }

    // public ist implizit
    final string getType() {
        return type;
    }

    // Diese Fuktion muss  
    // implementiert werden!
    abstract string convertToString();
}

class Integer: Any {
    // nur von dieser Klasse sichtbar
    private {
        int number;
    }

    // Konstruktor
    this(int number) {
        // ruft Konstruktor der
        // Basisklasse auf
        super("integer");
        this.number = number;
    }

    // Dies ist implizit. 
    public:

    override string convertToString() {
        import std.conv : to;
        // Das Schweizer-Taschenmesser 
        // der Konvertierung...
        return to!string(number);
    }
}

class Float: Any {
    private float number;

    this(float number) {
        super("float");
        this.number = number;
    }

    override string convertToString() {
        import std.string : format;
        // Präzision anpassen
        return format("%.1f", number);
    }
}

void main()
{
    Any[] anys = [
        new Integer(10),
        new Float(3.1415f)
        ];

    foreach (any; anys) {
        writeln("Typ von any = ", 
            any.getType());
        writeln("Inhalt = ",
            any.convertToString());
    }
}
```
