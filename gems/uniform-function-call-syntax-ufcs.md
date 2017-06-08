# Uniform Function Call Syntax (UFCS)

**UFCS** (dt. etwa: einheitliche Funktionsaufruf-Syntax) ist 
ein Hauptmerkmal von D, dass Wiederverwendbarkeit und 
Skalierbarkeit durch klar definierte Kapselung ermöglicht.

UCFS erlaubt es, den Aufruf der freien Funktion `fun(a)` als
Memberfunktionsaufruf `a.fun()` zu schreiben.

Wenn `a.fun()` vom Compiler gesehen wird, und der Typ von `a`
keine Memberfunktion `fun()` besitzt, wird versucht eine 
globale Funktion zu finden, deren erster Parameter dem von `a`
entspricht.

Dieses Feature ist besonders bei der Verkettung von 
Funktionsaufrufen nützlich.

    foo(bar(a))

kann dank UFCS auch so geschrieben werden:

    a.bar().foo()

Ferner können in D für Funktionen ohne Argumente die runden 
Klammern weggelassen werden. Somit kann _jede_ Funktion wie 
ein Property verwendet werden:

    import std.uni : toLower;
    "D rockt!".toLower; // "d rockt!"

UFCS ist besonders beim Umgang mit *Ranges* wichtig, wo mehrere
Algorithmen zu komplexen Operationen zusammengefügt werden
können, und dies mit klaren und wartbaren Code:

    import std.algorithm : group;
    import std.range : chain, retro, front, retro;
    [1, 2].chain([3, 4]).retro; // 4, 3, 2, 1
    [1, 1, 2, 2, 2].group.dropOne.front; // tuple(2, 3u)

### Weiterführende Quellen

- [UFCS in _Programming in D_](http://ddili.org/ders/d.en/ufcs.html)
- [_Uniform Function Call Syntax_](http://www.drdobbs.com/cpp/uniform-function-call-syntax/232700394) by Walter Bright
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode}

```d
import std.stdio : writefln, writeln;
import std.algorithm.iteration : filter;
import std.range : iota;

void main()
{
    "Hallo, %s".writefln("Welt");

    10.iota // Zahlen von 0 bis 9
      // Filter für gerade Zahlen
      .filter!(a => a % 2 == 0)
      .writeln(); // Ausgabe in stdout

    // Traditioneller Stil:
    writeln(filter!(a => a % 2 == 0)
    			   (iota(10)));
}
```
