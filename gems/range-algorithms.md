# Range Algorithmen

Die Standardmodule [std.range](http://dlang.org/phobos/std_range.html)
und [std.algorithm](http://dlang.org/phobos/std_algorithm.html)
bieten eine Vielfalt an großartigen Funktionen, die, basierend
auf *Ranges* als Grundbausteinen, zu komplexen Operationen 
zusammengefügt werden konnen, und dies auf eine lesbare Art und Weise.

Das Großartige an diesen Algorithmen ist die Anwendbarkeit
auf eigens definerte Ranges, sodass direkt vom Reichtum
der Standardbibliothek profitiert werden kann.

### std.algorithm

`filter` - Generiert eine neue Range aus gefilterten Elementen, wobei
die Filterbedingung als Lambdafunktion angegeben werden kann:

    filter!"a > 20"(range);
    filter!(a => a > 20)(range);

`map` - Generiert eine neue Range unter Benutzung des als Template-Parameter
definierten Prädikats:

    [1, 2, 3].map!(x => to!string(x));

`each` - Alternative Variante von `foreach` für Ranges:

    [1, 2, 3].each!(a => writeln(a));

### std.range
`take` - Begrenzt die Range auf *N* Elemente:

    theBigBigRange.take(10);

`zip` - Iteriert parallel über zwei Ranges und bindet die Elemente 
zu Tupeln:

    assert(zip([1,2], ["hello","world"]).front
      == tuple(1, "hello"));

`generate` - Erzeugt eine Range basierend auf einer Funktion, 
die bei jedem Iterationsschritt aufgerufen wird, z.B.:

    alias RandomRange = generate!(x => uniform(1, 1000));

`cycle` - Erzeugt eine Range, welche die gegebene Eingangs-Range 
für immer wiederholt:

    auto c = cycle([1]);
    // Range wird nie leer werden!
    assert(!c.empty);


### Weiterführende Quellen

- [Ranges in _Programming in D_](http://ddili.org/ders/d.en/ranges.html)
- [Mehr Ranges in _Programming in D_](http://ddili.org/ders/d.en/ranges_more.html)

## {SourceCode}

```d
import std.algorithm : canFind, map,
  filter, sort, uniq, joiner, chunkBy, splitter;
import std.array : array, empty;
import std.range : zip;
import std.stdio : writeln;
import std.string : format;

void main()
{
    string text = "Diese Tour gibt dir 
    einen Überblick über diese mächtige
    und ausdrucksstarke Programmiersprache,
    die direkt zu effizientem, *nativem*
    Maschinencode kompiliert.";
    // Trennbedingung
    alias pred = c => canFind(" ,.\n", c);
    // als Algorithmus: Arbeitet *lazy*
    // ohne Speicherallokationen!
    auto words = text.splitter!pred
      .filter!(a => !a.empty);

    auto wordCharCounts = words
      .map!"a.count";

    // Ausgabe der Zeichenanzahl 
    // pro Word beginnend mit 
    // der geringsten Anzahl!
    zip(wordCharCounts, words)
      // Array-Konvertierung für's Sortieren
      .array()
      .sort()
      // keine Kopie nötig!
      .uniq()
      // Alle Wörter mit gleicher Anzahl 
      // an Zeichen in eine Reihe:
      // chunkBy generiert eine Range aus
      // Ranges, getrennt durch die 
      // Länge
      .chunkBy!(a => a[0])
      // Diese Elemente werden verbunden
      // zu einer Zeile
      .map!(chunk => format("%d -> %s",
          chunk[0],
          // nur die Wörter
          chunk[1]
            .map!(a => a[1])
            .joiner(", ")))
      // joiner verbindet, aber *lazy*!
      // Jetzt die Zeilen mit Zeilenvorschub
      .joiner("\n")
      // ausgeben
      .writeln();
}
```
