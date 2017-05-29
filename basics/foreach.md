# Foreach

{{#img-right}}dman-teacher-foreach.jpg{{/img-right}}

D besitzt eine `foreach`-Schleife, die weniger fehlerträchtige
und besser lesbare Iterationen erlaubt.

### Element-Iteration

Ein gegebenes Array `arr` vom Typ `int[]` kann mithilfe der 
`foreach`-Schleife elementweise iteriert werden:

    foreach (int e; arr) {
        writeln(e);
    }

Das erste Feld der `foreach`-Definition ist der Name der in der 
Schleifen-Iteration benutzte Variable. Ihr Typ wird automatisch
abgeleitet:

    foreach (e; arr) {
        // typeof(e) == int
        writeln(e);
    }

Das zweite Feld muss ein Array sein - oder ein spezielles 
iterierbares Objekt, das **Range** genannt wird. Es wird im 
nächsten Abschnitt behandelt. 

### Zugriff über Referenz

Elemente des Arrays oder der Range werden während des Iterierens
kopiert. Für Basistypen ist die akzeptabel, für große Typen mag
das aber ein Problem darstellen. Um das Kopieren zu verhindern,
oder um die Elemente *in-place* zu ändern, kann `ref` verwendet 
werden:

    foreach (ref e; arr) {
        e = 10; // Wert in-place überschreiben
    }

### `n`-malige Iteration

Mit der `..`-Syntax erlaubt D eine prägnante Schreibweise
von Iterationen, die 'n'-mal ausgeführt werden sollen:

    foreach (i; 0..3) {
        writeln(i);
    }
    // 0 1 2

Die letze Zahl in `a..b` wird von der Range ausgeschlossen 
(Math.: rechtsoffenes Intervall [a,b)), sodass die Schleife 
`3`-mal ausgeführt wird.

### Iteration mit Index-Zähler

Die `foreach`-Definition kann um eine Index-Variable ergänzt 
werden:

    foreach (i, e; [4, 5, 6]) {
        writeln(i, ":", e);
    }
    // 0:4 1:5 2:6

### Umgekehrte Iteration mit `foreach_reverse`

Eine umgekehrte Iteration erfolgt mit
`foreach_reverse`:

    foreach_reverse (e; [1, 2, 3]) {
        writeln(e);
    }
    // 3 2 1

### Weiterführende Quellen

- [`foreach` in _Programming in D_](http://ddili.org/ders/d.en/foreach.html)
- [`foreach` with Structs and Classes  _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [`foreach` specification](https://dlang.org/spec/statement.html#ForeachStatement)

## {SourceCode}

```d
import std.stdio;

void main() {
    auto arr = [ [5, 15], // 20
          [2, 3, 2, 3], // 10
          [3, 6, 2, 9] ]; // 20
1234567890123456789012345678901234567890
    // Iteriere über das Array 
    // in umgekehrter Reihenfolge
    import std.range: retro;
    foreach (row; retro(arr))
    {
        double accumulator = 0.0;
        foreach (c; row)
            accumulator += c;

        writeln("Mittelwert von ", row,
            " = ", accumulator / row.length);
    }
}
```
