# Templates

**D** erlaubt die Defintion von Funktionstemplates wie C++ und
Java. Dies ist ein Mittel um **generische** Funktionen oder Objekte zu 
definieren, die für jeden Typ funktionieren, der mit den Anweisungen des
Funktionsrumpfs kompiliert:

    auto add(T)(T lhs, T rhs) {
        return lhs + rhs;
    }

Der Template-Parameter `T` wird in runden Klammern vor den eigentlichen
Funktionsparametern definiert. `T` ist ein Platzhalter, der vom Compiler 
ersetzt wird, wenn die Funktion tatsächlich mit dem `!`-Operator 
*instanziiert* wird:

    add!int(5, 10);
    add!float(5.0f, 10.0f);
    add!Animal(dog, cat); // kompiliert nicht! Animal implementiert + nicht

### Implizite Template-Parameter
 
Funktionstemplates haben zwei Parametersätze - den ersten für 
Kompilierzeit-Argumente und den zweiten für Laufzeit-Argumente.
(Normale Funktionen akzeptieren nur Laufzeit-Argumente).
Wenn ein oder mehrere Kompilierzeit-Argumente beim Aufruf ausgelassen 
werden, versucht der Compiler deren Typ aus der Liste der Laufzeit-
Argumente abzuleiten.

    int a = 5; int b = 10;
    add(a, b); // T wird abgeleitet zu `int`
    float c = 5.0;
    add(a, c); // T wird abgeleitet zu `float` 

### Template-Eigenschaften

Eine Funktion kann beliebig viele Template-Parameter besitzen, welche
während der Instanziierung mit der `func!(T1, T2 ..)`-Syntax spezifiziert 
werden. Template-Parameter können von jedem Basistyp sein (inkl. `string` 
und Fließkommatypen).

Anders als Generics in Java, beziehen sich Templates in D nur auf die 
Kompilierzeit. Dies führt zu hochoptimiertem Code maßgeschneidert für die 
beim Funktionsaufruf genutzten Typen.

Natürlich können auch die Typen `struct`, `class` und `interface` als Template
definiert werden.

    struct S(T) {
        // ...
    }

### Weiterführende Quellen

- [Tutorial to D Templates](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Templates in _Programming in D_](http://ddili.org/ders/d.en/templates.html)

#### Fortgeschritten

- [D Templates spec](https://dlang.org/spec/template.html)
- [Templates Revisited](http://dlang.org/templates-revisited.html):  Walter Bright writes about how D improves upon C++ templates.
- [Variadic templates](http://dlang.org/variadic-function-templates.html): Articles about the D idiom of implementing variadic functions with variadic templates

## {SourceCode}

```d
import std.stdio : writeln;

/**
Template-Klasse, die die generische
Implementierung von animals erlaubt.
Params:
    noise = zu schreibender string
*/
class Animal(string noise) {
    void makeNoise() {
        writeln(noise ~ "!");
    }
}

class Dog: Animal!("Woof") {
}

class Cat: Animal!("Meeoauw") {
}

/**
Template-Funktion, die jeden Typ T
annimmt, der eine Funktion makeNoise
implementiert.
Params:
    animal = Objekt, das 'noise' macht
    n = Anzahl der makeNoise-Aufrufe
*/
void multipleNoise(T)(T animal, int n) {
    for (int i = 0; i < n; ++i) {
        animal.makeNoise();
    }
}

void main() {
    auto dog = new Dog;
    auto cat = new Cat;
    multipleNoise(dog, 5);
    multipleNoise(cat, 5);
}
```
