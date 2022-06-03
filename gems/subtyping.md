# Subtypen in D

D bietet Vererbung für Klassen an, nicht aber für
`struct`s. Zur Erweiterung der Funktionalität von
`struct`s gibt es allerdings ein weiteres großartiges
Mittel: Die Verwendung von Subtypen.

Einer der Member eines `struct`s kann als
`alias this` definiert werden:

    struct SafeInt {
        private int theInt;
        alias theInt this;
    }

Jede Funktion oder Operation an `SafeInt`s,
die nicht vom Typ selbst behandelt werden kann,
wird an den `alias this`-Member weitergeleitet.
Von außen betrachtet ist `SafeInt` also ein
normaler Integer.

Dies erlaubt die Erweiterung anderer Typen
mit neuer Funktionalität, aber ohne Mehraufwand
(engl.: zero overhead) in Bezug auf Speicher oder
Laufzeit.

`alias this` funktioniert auch mit Klassen!

## {SourceCode}

```d
import std.stdio : writeln;

struct Vector3 {
    private double[3] vec;
    alias vec this;

    double dot(Vector3 rhs) {
        return vec[0]*rhs.vec[0] +
          vec[1]*rhs.vec[1] + vec[2]*rhs.vec[2];
    }
}

void main()
{
    Vector3 vec;
    // Wir interagieren hier im
    // Wesentlichen mit double[].
    vec = [ 0.0, 1.0, 0.0 ];
    assert(vec.length == 3);
    assert(vec[$ - 1] == 0.0);

    auto vec2 = Vector3([1.0,0.0,0.0]);
    // Allerdings wurde die Funktionaliät
    // erweitert!
    writeln("vec dot vec2 = ", vec.dot(vec2));
}
```
