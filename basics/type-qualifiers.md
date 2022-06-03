# Typqualifizierer

D besitzt eine statische Typisierung. Wenn eine Variable deklariert wurde, kann der
Typ nicht mehr verändert werden. Dies erlaubt dem Kompiler viele Fehler früh
zu erkennen und Restriktionen können schon während der Kompilierung erkannt werden.
Ein gutes Typsystem leistet die benötigte Unterstützung für die Sicherheit
und bessere Wartbarkeit großer Programme.

D stellt verschiedene Typqualifizierer zur Verfügung aber die meist Genutzten sind
`const` und `immutable`.

### `immutable`

Zusätzlich zu der statischen Typisierung, stellt D Typqualifizierer (auch "Speicherklassen" genannt)
bereit welche weitere Beschränkungen erlauben. Zum Beispiel kann ein `immutable` (unveränderbares)
Objekt nur einmal initialisiert werden und kann danach nicht mehr verändert werden.

    immutable int err = 5;
    // oder: immutable arr = 5 (der Typ int wird automatisch inferriert)
    err = 5; // FEHLER

Wegen ihrer Unveränderbarkeit, können `immutable` Objekte sicher und ohne Syn­chro­ni­sa­ti­on
zwischen Threads geteilt werden. Dies impliziert auch, dass `immutable` Objekte perfekt
gecacht werden können.

### `const`

`const` Objekte können ebenfalls nicht verändert werden. Diese Restriktion ist
jedoch nur für den aktuellen Geltungsbereich gültig. Ein `const` Zeiger
kann sowohl von einem unveränderbaren (`immutable`) Objekt als auch von einem
_veränderbaren_ Objekt erzeugt werden.
Das bedeutet, dass die Restriktion nur für den aktuellen Bereich gilt und in
einem anderen Kontext verändert werden kann. Nur durch `immutable` ist garantiert,
dass der Wert sich niemals ändern kann. Es ist typisch für APIs `const` Argumente
zu akzeptieren, da so garantiert wird, dass keine Modifikation stattfindet.
Zusätzlich kann die Funktion hierdurch veränderbare und nicht veränderbare Argumente
verarbeiten.

    void foo(const char[] s)
    {
        // if not commented out, next line will
        // result in error (can't modify const):
        // s[0] = 'x';

        import std.stdio : writeln;
        writeln(s);
    }

    // thanks to `const`, both calls will compile:
    foo("abcd"); // string is an immutable array
    foo("abcd".dup); // .dup returns a mutable copy

Sowohl `immutable` als auch `const` sind _transitiv_. Dies gewährleistet, dass
sobald ein Typ `const` ist alle Subkomponenten dieses Types ebenfalls `const` sind.

### In der Tiefe

#### Grundlegende Referenzen

- [Immutable in _Programmieren in D_](http://ddili.org/ders/d.en/const_and_immutable.html)
- [Bereiche in _Programmieren in D_](http://ddili.org/ders/d.en/name_space.html)

#### Weiterführende Referenzen

- [const(FAQ)](https://dlang.org/const-faq.html)
- [Typqualifizierer in D](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    /**
    * Variablen sind stan­dard­mä­ßig
    * veränderbar (mutable)
    */
    int m = 100; // mutable
    writeln("m: ", typeof(m).stringof);
    m = 10; // OK

    /**
    * Zeiger zu veränderbarem Speicher:
    */
    // Ein const pointer zu veränderbarem
    // Speicher ist erlaubt
    const int* cm = &m;
    writeln("cm: ", typeof(cm).stringof);
    // `const` kann nicht modifiziert werden:
    // *cm = 100; // Fehler!

    // Da `immutable` Unveränderbarkeit
    // garantiert kann es nicht zu
    // veränderbarem Speicher zeigen.
    // immutable int* im = &m; // Fehler!

    /**
    * Zeiger zu readonly Speicher:
    */
    immutable v = 100;
    writeln("v: ", typeof(v).stringof);
    // v = 5; // Fehler!

    // `const` kann zu readonly Speicher zeigen
    // und bleibt auch readonly.
    const int* cv = &v;
    writeln("*cv: ", typeof(cv).stringof);
    // *cv = 10; // Fehler!
}
```
