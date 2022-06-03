# Funktionale Programmierung

D legt besonderen Wert auf *funktionale Programmierung* und
bietet erstklassige Unterstützung für die Entwicklung im
funktionalen Stil.
In D kann eine Funktion als `pure` (dt.: rein, pur) deklariert
werden, und so anzeigen, dass für eine Eingabe eine Ausgabe
generiert wird, die nur von dieser abhängig ist.
Reine (`pure`) Funktionen dürfen weder auf globale veränderliche Zustände
zugreifen noch diese verändern und dürfen selbst nur Funktionen
aufrufen, die ihrerseits als `pure` markiert sind.

    int add(int lhs, int rhs) pure {
        // Fehler: impureFunction();
        return lhs + rhs;
    }

Diese Variante von `add` wird als **starke reine Funktion**
(engl.: strongly pure function) bezeichnet, weil ihr
zurückgegebenes Ergebnis nur von den Eingangsparametern
abhängt, ohne diese zu modifizieren. D erlaubt auch **schwache
reine Funktionen** (engl.: weakly pure function),
die veränderliche Parameter haben können:

    void add(ref int result, int lhs, int rhs) pure {
        result = lhs + rhs;
    }

Diese Funktionen werden immer noch als rein angenommen und
können auf globale veränderliche Zustande weder zugreifen
noch diese ändern. Nur Eingangsparameter dürfen Änderungen
erfahren.
Wegen der durch `pure` auferlegten Einschränkungen eignen
sich reine Funktionen ideal für Multithreading-Umgebungen
zur Vermeidung von Data-Racing. Auch können reine Funktionen
einfach zwischengespeichert werden und erlauben eine Reihe
von Optimierungen durch den Compiler.

Das Attribut `pure` wird für Template-Funktionen und
`auto`-Funktionen automatisch vom Compiler abgeleitet
(dies gilt auch für `@safe`, `nothrow` und `@nogc`).

### Weiterführende Quellen

- [Functional DLang Garden](https://garden.dlang.io/)

## {SourceCode}

```d
import std.bigint : BigInt;

/**
 * Berechnet die Potenz anhand
 * der Basis und des Exponenten
 * Returns:
 *     Ergebnis als
 *     Integer beliebiger Größe
 */
BigInt bigPow(uint base, uint power) pure
{
    BigInt result = 1;

    foreach (_; 0 .. power)
        result *= base;

    return result;
}

void main()
{
    import std.datetime : benchmark, to;
    import std.functional : memoize;
    import std.stdio : writefln, writeln;


    // memoize speichert das Ergebnis des
    // Funktionaufrufs abhängig vom den
    // Eingangsparametern zwischen.
    // Reine Funktionen sind dafür bestens
    // geeignet!
    alias fastBigPow = memoize!(bigPow);

    void test()
    {
        writefln(".uintLength() = %s ",
            fastBigPow(5, 10000).uintLength);
    }

    foreach (i; 0 .. 10)
        benchmark!test(1)[0]
            .to!("msecs", double)
            .writeln("brauchte: Millisekunden");
}
```
