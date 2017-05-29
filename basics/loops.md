# Schleifen

D stellt vier Schleifen-Konstrukte bereit:

### 1) `while`

`while`-Schleifen führen den gegebenen Code-Block aus,
solange eine bestimmte Bedingung erfüllt ist:

    while (condition) {
        foo();
    }

### 2) `do ... while`

`do .. while`-Schleifen führen den gegebenen Code-Block aus,
solange eine bestimmte Bedingung erfüllt ist. Aber im Gegensatz
zur `while`-Schleife wird der Code-Block ausgeführt, bevor 
die Bedingung das erste Mal abgeprüft wird.

    do {
        foo();
    } while (condition);

### 3) Klassische `for`-Schleife

Die klassische `for`-Schleife, bekannt aus C/C++ oder Java, 
mit _Initialisierung_ , _Test_ und _Fortsetzung_:

    for (int i = 0; i < arr.length; i++) {
        ...

### 4) `foreach`

The [`foreach` loop](basics/foreach) which will be introduced in more details
in the next section:

    foreach (el; arr) {
        ...
    }

#### Special keywords and labels

The special keyword `break` will immediately abort the current loop.
In a nested loop a _label_ can be used to break of any outer loop:

    outer: for (int i = 0; i < 10; ++i) {
        for (int j = 0; j < 5; ++j) {
            ...
            break outer;

The keyword `continue` starts with the next loop iteration.

### In-depth

- `for` loop in [_Programming in D_](http://ddili.org/ders/d.en/for.html), [specification](https://dlang.org/spec/statement.html#ForStatement)
- `while` loop in [_Programming in D_](http://ddili.org/ders/d.en/while.html), [specification](https://dlang.org/spec/statement.html#WhileStatement)
- `do-while` loop in [_Programming in D_](http://ddili.org/ders/d.en/do_while.html), [specification](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio;

/*
Computes the average of
the elements of an array.
*/
double average(int[] array) {
    // The property .empty for arrays isn't
    // native in D but has to be made accessible
    // by importing the function from std.array
    import std.array: empty, front;

    double accumulator = 0.0;
    auto length = array.length;
    while (!array.empty) {
        // this could be also done with .front
        // with import std.array: front;
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
      writeln("The average of ", testers[i],
        " = ", average(testers[i]));
    }
}
```
