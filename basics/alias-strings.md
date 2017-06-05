# Alias & Strings

Nun, da uns Arrays bekannt sind und wir mit den Basis-Typen 
und dem Schlüsselwort`immutable` beschäftigt haben, wird es
Zeit zwei neue Konstrukte in einer Zeile einzuführen:

    alias string = immutable(char)[];
    
Der Begriff `string` wird durch das Schlüsselwort `alias` definiert, 
und zwar als Slice bestehend aus `immutable(char)`s.
Das bedeutet, sobald ein `string` konstruiert wurde, wird sich sein 
Inhalt nie mehr ändern. Und damit sind wir bei dem zweiten Konstrukt:
Willkommen UTF-8 `string`!

Aufgrund ihrer Unveränderlichkeit (engl.: immutablility) können 
`string`s über verschiedene Threads hinweg geteilt werden. Da `string` 
ein Slice ist, können Teile jede Speicher-Allokationen entnommnen werden.
Die Standard-Funktion `std.algorithm.splitter`](https://dlang.org/phobos/std_algorithm_iteration.html#.splitter) 
z.B. teilt einen String anhand von Zeilensprüngen (newline-Zeichen) 
ohne jede Speicher-Allokation.

Neben dem  UTF-8 `string` gibt es zwei weitere Typen:

    alias wstring = immutable(wchar)[]; // UTF-16
    alias dstring = immutable(dchar)[]; // UTF-32

Diese Varianten können durch Nutzung der `to`-Methode aus `std.conv` 
einfach ineinander konvertiert werden:

    dstring myDstring = to!dstring(myString);
    string myString   = to!string(myDstring);

### Unicode Strings

Ein einfacher `string` ist als ein Array aus 8-bit Unicode [code
units](http://unicode.org/glossary/#code_unit) definiert. Alle Array-Operationen 
können auf Strings angewandt werden, aber dies wird nur auf der Code-Unit-Ebene 
funktionieren, nicht aber auf Zeichen-Ebene! Gleichzeitig interpretieren die
Algorithmen der Standard-Bibliothek Strings als Sequenzen aus 
[Code Points](http://unicode.org/glossary/#code_point). Darüber hinaus gibt es
die Option der Behandlung der Sequenz als
[Grapheme](http://unicode.org/glossary/#grapheme) durch explizite Nutznung von
[`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html).

Diese kleine Beispiel verdeutlicht die unterschiedlichen Interpretationen:

    string s = "\u0041\u0308"; // Ä

    writeln(s.length); // 3

    import std.range : walkLength;
    writeln(s.walkLength); // 2

    import std.uni : byGrapheme;
    writeln(s.byGrapheme.walkLength); // 1

Hier ist die tatsächliche Länge des Arrays `s` 3, weil es 3 Code Units
enthält: `0x41`, `0x03` and `0x08`. Von diesen definieren zwei einen einzelnen
Code Point (kombinierende diakritische Zeichen) und 
[`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html)
(Funktion der Standard-Bibliothek zur Berechnung der Länge beliebiger Ranges)
zählt zwei Code Points. Schließlich ermittelt `byGrapheme` in einer aufwändigeren
Berechnung, dass sich diese beiden Code Points zu einem einzigen angezeigten 
Zeichen zusammensetzen.
Korrekte Unicode-Verarbeitung kann sehr kompliziert sein. Trotzdem dürfen 
D-Entwickler String-Variablen als magische Byte-Arrays betrachten und sich darauf
verlassen, dass die Standard-Bibliothek "das Richtige tut". 
Die meiste Unicode-Funktionalität wird in dem Modul 
[`std.uni`](https://dlang.org/library/std/uni.html) bereitgestellt, Grundlegenderes
in [`std.utf`](https://dlang.org/library/std/utf.html).

### Mehrzeilige Strings

Für die Erzeugung mehrzeiliger Strings bietet sich die 
`string str = q{ ... }`-Syntax an:

    string multiline = q{ This
        may be a
        long document
    };

### Raw Strings

Auch ist es möglich Raw Strings zu benutzen, um die aufwändige Verarbeitung
von reservierten Symbolen zu minimieren. Raw Strings können mittels Backticks
(invertierte Hochkommanta) (`` ` ... ` ``) oder den r(aw)-Präfix (`r" ... "`) 
deklariert werden.

    string raw  =  `raw "string"`; // raw "string"
    string raw2 = r"raw "string";  // raw "string"

### Weiterführendes

- [Unicode gem](https://tour.dlang.org/tour/en/gems/unicode)
- [Characters in _Programming in D_](http://ddili.org/ders/d.en/characters.html)
- [Strings in _Programming in D_](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - UTF en-/decoding algorithms
- [std.uni](http://dlang.org/phobos/std_uni.html) - Unicode algorithms

## {SourceCode}

```d
import std.stdio;
import std.range: walkLength;
import std.uni : byGrapheme;
import std.string: format;

void main() {
    // format generiert einen String mittels
    // printf-artiger Syntax. D erlaubt 
    // native UTF-String-Verarbeitung!
    string str = format("%s %s", "Hellö",
        "Wörld");
    writeln("My string: ", str);
    writeln("Array length (code unit count)"
        ~ " of string: ", str.length);
    writeln("Range length (code point count)"
        ~ " of string: ", str.walkLength);
    writeln("Character length (grapheme count)"
        ~ " of string: ",
        str.byGrapheme.walkLength);

    // Strings sind einfache Arrays! 
    // Somit funktionieren alle Array-
    // Operation auch mit Strings!
    import std.array: replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm: endsWith;
    writefln("Does %s end with 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv: to;
    // Konverierung nach UTF-32
    dstring dstr = to!dstring(str);
    // .. die natürlich gleich aussieht!
    writeln("My dstring: ", dstr);
}
```
