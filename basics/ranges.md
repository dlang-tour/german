# Ranges

Wenn der Compiler auf ein `foreach`-Konstrukt trifft...

```
foreach (element; range)
{
    // Loop body...
}
```

wird es intern etwa wie folgt umgeschrieben:

```
for (auto __rangeCopy = range;
     !__rangeCopy.empty;
     __rangeCopy.popFront())
 {
    auto element = __rangeCopy.front;
    // Loop body...
}
```

Falls das Range-Objekt ein Referenztyp ist (z.B. `class`), wird es
verbraucht und ist daher für weitere Iterationen nicht mehr verfügbar
(es sei denn, der Schleifenrumpf bricht vor der letzten Iteration ab).
Falls das Range-Objekt ein Werttyp ist, wird eine Kopie der Range
erzeugt und - abhängig von der Definition - die ursprüngliche
Range verbraucht.
Die meisten Ranges der Standard-Bibliothek sind Strukturen (`struct`),
sodass eine Iteration normalerweise nicht zerstörend wirkt - allerdings
nicht garantiert. Sollte diese Garantie wichtig sein, sind **forward**
-Ranges erforderlich.

Jedes Objekt mit folgendem Interface wird **Range** genannt und kann
somit iteriert werden:

```
    struct Range
    {
        T front() const @property;
        bool empty() const @property;
        void popFront();
    }
 ```

Beachte: Obwohl `empty` und `front` gewöhnlich als `const`-Funktionen
definiert werden (was impliziert, dass ein Aufruf die Range nicht
modifiziert), ist dies nicht erforderlich.

Die Funktionen in `std.range` und `std.algorithm` stellen Bausteine
zur Verfügung, die dieses Interface nutzen. Ranges erlauben die
einfache Erstellung komplexer iterierender Algorithmen.
Auch erlauben Ranges die Erstellung von **Lazy**-Objekten, die
ihre Berechnungen nur ausführen, wenn dies wirklich nötig ist,
z.B. wenn während einer Iteration auf das nächste Range-Element
zugegriffen wird.

### Übungsaufgabe

Vervollständige den Quellcode, um eine `FibonacciRange` erzeugen,
die Zahlen der [Fibonacci-Folge](https://de.wikipedia.org/wiki/Fibonacci-Folge)
generiert. Der`assert`-Befehl am Ende stellt die Korrektheit deiner
Implementation sicher!

### Weiterführende Quellen

- [`std.algorithm`](http://dlang.org/phobos/std_algorithm.html)
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

struct FibonacciRange
{
    bool empty() const @property
    {
        // Wann endet die
        // Fibonacci-Folge?!
    }

    void popFront()
    {
    }

    int front() const @property
    {
    }
}

void main()
{
    import std.range : take;
    import std.array : array;

    FibonacciRange fib;

    // `take` erstellt eine weitere Range,
    // die maximal N Elemente zurückgibt.
    // Diese Range ist _lazy_ und nutzt
    // die Original-Range nur wenn nötig.
    auto fib10 = take(fib, 10);

    // In diesem Fall werden alle Elemente
    // genutzt und die Range in ein Array
    // aus Integer-Werten konvertiert.
    int[] the10Fibs = array(fib10);

    writeln("Die ersten 10 Fibonacci-Zahlen: ",
        the10Fibs);
    assert(the10Fibs ==
        [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]);
}
```
