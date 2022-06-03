# Compile Time Function Evaluation (CTFE)

CTFE (dt.: Funktionsauswertung zur Kompilierzeit) ist ein
Mechanismus, der dem Compiler die Ausführung von Funktionen
zur **Kompilierzeit** ermöglicht. Für den Gebrauch dieses
Features sind es keine speziellen D-Befehle nötig -
immer, wenn eine Funktion nur auf zur Kompilierzeit
bekannten Werten beruht, kann der D-Compiler die
Entscheidung treffen, die Funktion schon während der
Kompilierung auszuwerten.

    // Ergebnis wird zur Kompilierzeit
    // berechnet. Der erzeugte Machinencode
    // enthält keinen Funktionsaufruf!
    static val = sqrt(50);

Schlüsselwörter wie `static`, `immutable` oder `enum`
weisen den Compiler an, CTFE zu nutzen, wann immer
dies möglich ist. Großartig daran ist, dass die Funktion
nicht neu geschrieben werden muss:

    int n = berechneZurLaufzeit();
    // Die gleiche Funktion wie oben, aber
    // dieses Mal klassisch zur Laufzeit
    // ausgeführt.
    auto val = sqrt(n);

Ein herausragendes Beispiel in D ist die
[std.regex](https://dlang.org/phobos/std_regex.html)-Bibliothek.
Sie bietet einen `ctRegex`-Typ (ct: compile time), der
*String Mixins* und CTFE nutzt, um während der Kompilierung
hochoptimierte Reguläre Ausdrücke zu generieren.
Die Laufzeitvariante `regex` nutzt die gleiche Codebasis.

    auto ctr = ctRegex!(`^.*/([^/]+)/?$`);
    auto tr = regex(`^.*/([^/]+)/?$`);
    // ctr und tr liefern das gleiche Ergebnis,
    // nur dass ctr schneller ist!

Nicht alle Sprachfeatures stehen für CTFE zur Verfügung.
Allerdings wird die Menge derunterstützten Features ständig
erweitert.

### Weiterführende Quellen

- [Einführung in Reguläre Ausdrücke in D](https://dlang.org/regular-expression.html)
- [std.regex](https://dlang.org/phobos/std_regex.html)
- [Bedingte Kompilierung](https://dlang.org/spec/version.html)

## {SourceCode}

```d
import std.stdio : writeln;

/**
Berechnet die Quadratwurzel einer Zahl
mithilfe des Newton'schen Approximationsschemas.
Params:
    x = Wurzelargument

Returns: Quadratwurzel von x
*/
auto sqrt(T)(T x) {
    // Epsilon ist Abbruchgrenze der
    // Approximation, ab der eine
    // weitere Iteration unnötig ist.
    enum GoodEnough = 0.01;
    import std.math : abs;
    // Wähle einen guten Startwert!
    T z = x*x, old = 0;
    int iter;
    while (abs(z - old) > GoodEnough) {
        old = z;
        z -= ((z*z)-x) / (2*z);
    }

    return z;
}

void main() {
    double n = 4.0;
    writeln("Die Wurzel von 4 = ",
        sqrt(n));
    static cn = sqrt(4.0);
    writeln("Die Wurzel von Kompilierzeit 4 = ",
        cn);
}
```
