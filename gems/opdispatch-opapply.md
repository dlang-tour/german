# opDispatch & opApply

D erlaubt es, Operatoren wie `+`, `-` oder den Aufrufoperator
`()` für [Klassen und Strukturen](https://dlang.org/spec/operatoroverloading.html)
zu überladen.
Im Folgenden werden die beiden speziellen Operatorüberladungen
`opDispatch` und `opApply` genauer beleuchtet.

### opDispatch

`opDispatch` kann als Memberfunktionen eines `struct`- 
oder `class`-Typs definiert werden. Jede unbekannte Memberfunktionsaufruf
für diesen Typ wird an `opDispatch` weitergeleitet,
wobei sowohl Name als auch Parameter der unbekannten
Funktion als `string` durchgereicht werden.
Damit ist `opDispatch` eine *alles auffangede* Memberfunktion
und erlaubt eine andere Ebene der Generischen Programmierung,
und das komplett zur **Kompilierzeit**!

    struct C {
        void callA(int i, int j) { ... }
        void callB(string s) { ... }
    }
    struct CallLogger(C) {
        C content;
        void opDispatch(string name, T...)(T vals) {
            writeln("called ", name);
            mixin("content." ~ name)(vals);
        }
    }
    CallLogger!C l;
    l.callA(1, 2);
    l.callB("ABC");

### opApply

Eine alternative Implementing eines `foreach`-Durchlaufs,
verglichen mit der Definition einer benuterdefinierten *Range*,
ist die Erstellung einer `opApply`-Memberfunktion.
Das Iterieren mit `foreach` oder ähnlichem wird ein
spezielles Delegate als Parameter aufrufen:

    class Tree {
        Tree lhs;
        Tree rhs;
        int opApply(int delegate(Tree) dg) {
            if (lhs && lhs.opApply(dg)) return 1;
            if (dg(this)) return 1;
            if (rhs && rhs.opApply(dg)) return 1;
            return 0;
        }
    }
    Tree tree = new Tree;
    foreach(node; tree) {
        ...
    }

Der Compiler wandelt den `foreach`-Rumpf in ein spezielles
Delegate um, dass an das Objekt übergeben wird. Sein einziger
Parameter ist jeweils der aktuelle Wert der Iteration.
Der magische `int`-Rückgabewert muss interpretiert werden und,
falls dieser nicht  `0` ist, die Iteration abgebrochen werden.

### Weiterführende Quellen

- [Operatorüberladung in _Programming in D_](http://ddili.org/ders/d.en/operator_overloading.html)
- [`opApply` in _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [Operatorüberladung in D](https://dlang.org/spec/operatoroverloading.html)

## {SourceCode}

```d
/*
Variant ist etwas, dass jeden anderen Typ
enthalten könnnte:
https://dlang.org/phobos/std_variant.html
*/

import std.variant : Variant;

/*
Typ, der mittels opDispatch mit jeder 
Anzahl an Membern gefüllt werden kann.
Wie JavaScript's var.
*/
struct var {
    private Variant[string] values;

    @property
    Variant opDispatch(string name)() const {
        return values[name];
    }

    @property
    void opDispatch(string name, T)(T val) {
        values[name] = val;
    }
}

void main() {
    import std.stdio : writeln;

    var test;
    test.foo = "test";
    test.bar = 50;
    writeln("test.foo = ", test.foo);
    writeln("test.bar = ", test.bar);
    test.foobar = 3.1415;
    writeln("test.foobar = ", test.foobar);
    // FEHLER, weil es noch nicht existert
    // writeln("test.notthere = ",
    //   test.notthere);
}
```
