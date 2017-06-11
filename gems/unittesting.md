# Unittests

Tests sind ein exzellenter Weg der Gewährleistung von Stabilität 
und Fehlerfreiheit in der Anwendungsentwicklung. Sie dienen als 
interaktive Dokumentation und erlauben Codeveränderungen ohne 
Angst davor, bereits bestehende Funktionalität zu zerstören. 
D bietet mit `unittest`-Blöcken eine bequeme, native Syntax, die 
überall in einem D-Modul eingesetzt werden kann, um 
Quellcode-Funktionalität zu prüfen.

    // Unittest der Funktion myAbs
    unittest
    {
        assert(myAbs(-1) == 1);
        assert(myAbs(1)  == 1);
    }

Dies erlaubt unkomplizierte [testgetriebene Entwicklung](https://de.wikipedia.org/wiki/Testgetriebene_Entwicklung)
auf Abruf.

### Ausführung von `unittest`-Blöcken

`unittest`-Blöcke können beliebigen Code enthalten, der 
nur kompiliert und ausgeführt wird, wenn das DMD-Compiler-Flag
`-unittest` gesetzt ist. Auch DUB bietet die Kompilierung und
Ausführung von Unittests mit dem Befehl `dub test` an.

### Funktionsnachweis mittels `assert`

Typischerweise enthalten `unittest`s `assert`-Ausdrücke, die
die Funktionalität einer gegebenen Funktion sicherstellen.
`unittest`-Blöcke befinden sich in der Regel in der Nähe der 
einer Funktionsdefinition oder sogar in Klassen und Strukturen.

### Erhöhung der Code-Abdeckung

Unittests sind eine mächtige Waffe für die Entwickung robuster
Anwendungen. Ein Indikator für den Grad der Überprüfung des Codes
durch Tests ist die _Code-Abdeckung_ (engl.: code coverage -
Verhältnis ausgeführter zu  existierender Codezeilen).
Der DMD-Compiler erlaubt eine einfache Berichterstellung der
Code-Abdeckung durch Hinzufügen von `-cov`. Damit wird für jedes
Modul eine `.lst`-Datei mit detailierten Statistiken erzeugt.

Da der Compiler Attribute von Template-Quellcode automatisch 
ableiten kann, ist es ein gängiges Muster, Unittests mit 
Annotationen zu versehen, um die Verwendung gewünschter Attribute 
sicherzustellen:

    unittest @safe @nogc nothrow pure
    {
        assert(myAbs() == 1);
    }

### Weiterführende Quellen

- [Unittests in _Programming in D_](http://ddili.org/ders/d.en/unit_testing.html)
- [Unittests in D](https://dlang.org/spec/unittest.html)

## {SourceCode}

```d
import std.stdio : writeln;

struct Vector3 {
    double x;
    double y;
    double z;

    double dot(Vector3 rhs) const {
        return x*rhs.x + y*rhs.y + z*rhs.z;
    }

    // Das ist ok!
    unittest {
        assert(Vector3(1,0,0).dot(
          Vector3(0,1,0) == 0);
    }

    string toString() const {
        import std.string : format;
        return format("x:%.1f y:%.1f z:%.1f",
          x, y, z);
    }

    // .. und das auch!
    unittest {
        assert(Vector3(1,0,0).toString() ==
          "x:1.0 y:0.0 z:0.0");
    }
}

void main()
{
    Vector3 vec = Vector3(0,1,0);
    writeln(`Dieser Vektor wurde getestet: `,
      vec);
}

/*
Im Normal-Modus werden Unittests ignoriert.
Rufe 'dub test' auf oder kompiliere
mit dmd -unittest, um Unittests tatsächlich
auszuführen"
*/
unittest {
    Vector3 vec;
    // .init ist eine spezielles 
    // Property in D, die den
    // Initialwert des Typs angibt.
    assert(vec.x == double.init);
}
```
