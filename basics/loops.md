# Schleifen

D stellt vier Schleifen-Konstrukte bereit:

### 1) `while`

Die `while`-Schleife führt den gegebenen Code-Block aus,
solange eine bestimmte Bedingung erfüllt ist:

    while (condition) {
        foo();
    }

### 2) `do ... while`

Die `do .. while`-Schleife führt den gegebenen Code-Block aus,
solange eine bestimmte Bedingung erfüllt ist. Aber im Gegensatz
zur `while`-Schleife wird der Code-Block ausgeführt, bevor 
die Bedingung das erste Mal geprüft wird.

    do {
        foo();
    } while (condition);

### 3) Klassische `for`-Schleife

Die klassische `for`-Schleife, bekannt aus C/C++ oder Java, 
mit _Initialisierung_ , _Test_ und _Fortsetzung_:

    for (int i = 0; i < arr.length; i++) {
        ...

### 4) `foreach`

Die [`foreach`-Schleife](basics/foreach), die in der nächsten Sektion 
im Detail eingeführt wird:

    foreach (el; arr) {
        ...
    }

#### Spezielle Schlüsselwörter und Labels

Das Schlüsselwort `break` bricht die aktuelle Scheife unverzüglich ab.
In verschachtelten Schleifen kann ein _Label_ zum Ausbrechen aus einer
äußeren Schleife genutzt werden:

    outer: for (int i = 0; i < 10; ++i) {
        for (int j = 0; j < 5; ++j) {
            ...
            break outer;

Das Schlüsselwort `continue` startet die nächste Schleifen-Iteration.

### In-depth

- `for`-Schleife in [_Programming in D_](http://ddili.org/ders/d.en/for.html), [specification](https://dlang.org/spec/statement.html#ForStatement)
- `while`-Schleife in [_Programming in D_](http://ddili.org/ders/d.en/while.html), [specification](https://dlang.org/spec/statement.html#WhileStatement)
- `do-while`-Schleife in [_Programming in D_](http://ddili.org/ders/d.en/do_while.html), [specification](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio;

/*
Berechnet den Mittelwert 
der Elemente eines Arrays.
*/
double average(int[] array) {
    // Die Eigenschaft .empty für Arrays ist in
    // D nicht nativ und wird durch Import aus 
    // std.array bereitgestellt.
    import std.array: empty, front;

    double accumulator = 0.0;
    auto length = array.length;
    while (!array.empty) {
        // Geht auch mit .front
        // durch Import von std.array: front;
        accumulator += array[0];
        array = array[1 .. $];
    }

    return accumulator / length;
}

void main()
{
    auto testers = [ [5, 15], // 20
          [2, 3, 2, 3], // 10
          [3, 6, 2, 9] ]; // 20

    for (auto i = 0; i < testers.length; ++i) {
      writeln("Mittelwert von ", testers[i],
        " = ", average(testers[i]));
    }
}
```
