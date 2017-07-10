# Template-Metaprogrammierung

Wenn du jemals mit *Template-Metaprogrammierung* in C++
in Berührung gekommen bist, wirst du mit Erleichterung
lesen, dass D einige Werkzeuge bereitstellt, um dir das Leben
leichter zu machen.
Metaprogrammierung ist eine Technik, die Entscheidungen
abhängig von Template-Typeigenschaften ermöglicht und so noch
flexiblere Gestaltungsmöglichkeiten für generische Typen bietet.

### `static if` & `is`

`static if` erlaubt die bedingte Kompilierung eines
Code-Blocks. Im Gegensatz zu `if` wird die Bedingung
zur Kompilierzeit ausgewertet:

    static if(is(T == int))
        writeln("T ist ein int");
    static if (is(typeof(x) :  int))
        writeln("Variable x konvertiert implizit zu int");

Der [`is`-Ausdruck](http://wiki.dlang.org/Is_expression) ist
ein generischer Helfer, der Bedingungen zur Kompilierzeit
auswertet:

    static if(is(T == int)) { // T ist Templateparameter
        int x = 10;
    }

Wenn die Bedingung erfüllt ist, wird der Inhalt des Blocks
kopiert. Die umschließenden Klammern werden weggelassen 
und kein weiterer Scope erstellt.
Ein neuer Block / Scope kann allerdings explizit mit `{ {` und `} }`
erzeugt werden.

`static if` kann überall im Code genutzt werden - in
Funktionen, im globalen Scope sowie in Typdefinitionen.

### `mixin template`

Überall, wo Textbausteine gefragt sind, können 
`mixin template`s genutzt werden:

    mixin template Foo(T) {
        T foo;
    }
    ...
    mixin Foo!int; // int foo ab hier verfügbar.

`mixin template` können eine beliebige Anzahl an komplexen
Ausdrücken enthalten und werden am Instanziierungspunkt
eingesetzt. 
Dies macht einen Präprozessor, wie ihn z.B. C und C++ 
besitzen, überfüssig.

### Template-Beschränkungen

Ein Template kann mit beliebig vielen Beschränkungen
(engl.: contraints) definiert werden, um so Eigenschaften
eines Typs erzwingen:

    void foo(T)(T value)
      if (is(T : int)) { // foo!T nur gültig wenn T
                         // zu int konvertiert
    }

Beschränkungen können in booleschen Ausdrücken kombiniert
werden und sogar Funktionsaufrufe enthalten, sofern diese
zur Kompilierzeit ausgwwertet werden können.
Z.B. prüft `std.range.primitives.isRandomAccessRange`,
ob ein Typ eine Range ist, die den `[]`-Operator unterst

### Weiterführende Quellen

### Grundlegende Verweise

- [Tutorial zum Thema D-Templates](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Bedingte Kompilierung](http://ddili.org/ders/d.en/cond_comp.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [Weiteres zu Templates in _Programming in D_](http://ddili.org/ders/d.en/templates_more.html)
- [Mixins in  _Programming in D_](http://ddili.org/ders/d.en/mixin.html)

### Fortgeschrittene Verweise

- [Bedingte Kompilierung](https://dlang.org/spec/version.html)
- [Traits](https://dlang.org/spec/traits.html)
- [Mixin Templates](https://dlang.org/spec/template-mixin.html)
- [Spezifikation: D-Templates](https://dlang.org/spec/template.html)

## {SourceCode}

```d
import std.traits : isFloatingPoint;
import std.uni : toUpper;
import std.string : format;
import std.stdio : writeln;

/*
A Vector, der nur mit Integer- oder
Floatingpoint-Typen arbeitet.
*/
struct Vector3(T)
  if (is(T: real))
{
private:
    T x,y,z;

    /*
    Generator für Getter und Setter zur
    Vermeidung sich wiederholender 
    Textbausteine (engl.:boiler plate)!
    
    var -> T getVAR() and void setVAR(T)
    */
    mixin template GetterSetter(string var) {
        // Nutze mixin zur Erzeugung von
        // Funktionsnamen
        mixin("T get%s() const { return %s; }"
          .format(var.toUpper, var));

        mixin("void set%s(T v) { %s = v; }"
          .format(var.toUpper, var));
    }

    /*
    Einfache Generierung der Funktionen
    getX, setX etc. mit einem Mixin-Template.
    */
    mixin GetterSetter!"x";
    mixin GetterSetter!"y";
    mixin GetterSetter!"z";

public:
    /*
    Die dot-Funktion ist nur für 
    Floatingpoint-Typen verfügbar.
    */
    static if (isFloatingPoint!T) {
        T dot(Vector3!T rhs) {
            return x * rhs.x + y * rhs.y +
                z * rhs.z;
        }
    }
}

void main()
{
    auto vec = Vector3!double(3,3,3);
    // Folgendes funktioniert aufgrund der 
    // Template-Beschränkung nicht!
    // Vector3!string illegal;

    auto vec2 = Vector3!double(4,4,4);
    writeln("vec dot vec2 = ", vec.dot(vec2));
    
    auto vecInt = Vector3!int(1,2,3);
    // besitzt die Function dot nicht, da diese
    // statisch nur für Floatingpoint-Typen    
    // definiert wurde
    // vecInt.dot(Vector3!int(0,0,0));

    // generierte Getter und Setters!
    vecInt.setX(3);
    vecInt.setZ(1);
    writeln(vecInt.getX, ",",
      vecInt.getY, ",", vecInt.getZ);
}
```
