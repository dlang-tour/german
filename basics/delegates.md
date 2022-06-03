# Delegates

### Funktionen als Argumente

Eine Funktion kann auch Parameter einer Funktion sein:

    void doSomething(int function(int, int) doer) {
        // rufe durchgereichte Funktion auf
        doer(5,5);
    }

    doSomething(add); // use global function `add` here
                      // add must have 2 int parameters

`doer` kann wie jede andere normale Funktion aufgerufen
werden.

### Lokale Funktionen mit Kontext

Im obigen Beispiel ist `doSomething` vom Typ `function`, und damit
ein Zeiger auf eine globale Funktion. Sobald auf eine Memberfunktion
oder eine lokale Funktion verwiesen wird, muss ein `delegate`
verwendet werden. Das ist ein Funktionszeiger mit zusätzlichen
Informationen zu seinem Kontext (in anderen Programmiersprachen
auch **Closure** genannt). Ein auf eine Memberfunktion einer
Klasse zeigendes `delegate` enthält z.B. einen Zeiger auf das
Klassenobjekt. Ein `delegate`, erzeugt in einer verschachtelten
Funktion, enthält stattdessen einen Link zu seinem umgebenden
Kontext. Allerdings darf der D-Compiler automatisch eine Kopie
des Kontextes auf dem Heap erstellen, falls dies der Speicher-
sicherheit dient. Das Delegate enthält dann einen Link zu diesem
Heap-Bereich

    void foo() {
        void local() {
            writeln("local");
        }
        auto f = &local; // f ist vom Typ delegate
    }

Die obige Funktion `doSomething` mit einem `delegate`-Parameter
würde so aussehen:

    void doSomething(int delegate(int,int) doer);

`delegate`- und `function`-Objekte können nicht gemischt werden.
Daher konvertiert die Standardfunktion
[`std.functional.toDelegate`](https://dlang.org/phobos/std_functional.html#.toDelegate)
eine `function` in ein `delegate`.

### Anonyme Funktionen und Lambdafunktionen

Da Funktionen als Variablen gespeichert und an andere Funktionen
übergeben werden können, ist es mühsam, sie zu definieren und ihnen
einen Namen zu geben. Daher erlaubt D namenlose Funktionen und
einzeilige _Lambdafunktionen_.

    auto f = (int lhs, int rhs) {
        return lhs + rhs;
    };
    // Lambdafunktion, gleich mit obiger Funktion
    auto f = (int lhs, int rhs) => lhs + rhs;

Auch besteht die Möglichkeit, Strings als Template-Argumente an
funktionale Teile der D-Standardbibliothek zu nutzen. Dies erlaubt
z.B. eine komfortable Art der Definition einer Reduce-Funktion:

    [1, 2, 3].reduce!`a + b`; // 6

String-Funktionen sind nur möglich für _ein oder zwei_ Argumente,
wobei `a` als erstes und `b` als zweites Argument dient.

### Weiterführende Quellen

- [Spezifikation: Delegates](https://dlang.org/spec/function.html#closures)

## {SourceCode}

```d
import std.stdio : writeln;

enum IntOps {
    add = 0,
    sub = 1,
    mul = 2,
    div = 3
}

/**
Stellt eine math. Berechnung bereit
Params:
    op = gewählte math. Operation
Returns: delegate, welches die math.
         Operation ausführt
*/
auto getMathOperation(IntOps op)
{
    // Definiere 4 Lambdafunktionen für
    // 4 verschiedene math. Operationen
    auto add = (int lhs, int rhs) => lhs + rhs;
    auto sub = (int lhs, int rhs) => lhs - rhs;
    auto mul = (int lhs, int rhs) => lhs * rhs;
    auto div = (int lhs, int rhs) => lhs / rhs;

    // switch deckt alle 4 Fälle ab
    final switch (op) {
        case IntOps.add:
            return add;
        case IntOps.sub:
            return sub;
        case IntOps.mul:
            return mul;
        case IntOps.div:
            return div;
    }
}

void main()
{
    int a = 10;
    int b = 5;

    auto func = getMathOperation(IntOps.add);
    writeln("Der Typ von func ist ",
        typeof(func).stringof, "!");

    // delegate 'func' aufrufen
    writeln("Ergebnis: ", func(a, b));
}
```
