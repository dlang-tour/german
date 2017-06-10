# Contract Programming

Contract Programming (dt.: vertragsbasierte Programmierung)
in D umfasst einen Satz von Sprachkonstrukten, die die 
Quellcodequalität durch bestimmte Prüfroutinen (sog. Contracts)
erhöhen und korrektes Verhalten sicherstellen sollen.

Contracts stehen nur im **Debug-Modus** zur Verfügung und 
werden im Release-Modus nicht ausgeführt.
Deshalb sollten sie nicht für die Überprüfung von 
Benutzereingaben eingesetzt werden.

### `assert`

Der `assert(...)`-Ausdruck bietet die einfachste Form 
des Contract Programming und prüft ob eine bestimmte
Bedingung eingehalten wird, anderenfalls bricht das 
Programm ab.

    assert(sqrt(4) == 2);
    // optionale benutzerdefinierte Fehlernachricht
    assert(sqrt(16) == 4, "sqrt is broken!");

### Contracts bei Funktionen

`in` und `out` erlauben formalisierte Contracts für
Eingangsparameter und Rückgabewerte von Funktionen.

    long square_root(long x)
    in {
        assert(x >= 0);
    } out (result) {
        assert((result * result) <= x
            && (result+1) * (result+1) > x);
    } body {
        return cast(long)std.math.sqrt(cast(real)x);
    }

Der Inhalt des `in`-Blocks könnte auch im Rumpf der Funktion
stehen, auf diese Art wird aber die Intention viel klarer.
Im `out`-Block der Funktion kann der Rückgabewert mit 
`out(result)` erfasst und entsprechend geprüft werden.

### Überprüfung mit invariant

`invariant()` ist eine spezielle Memberfunktion von `struct`-
und `class`-Typen, der die Überprüfung des Objektzustands
während dessen gesamter Lebenszeit erlaubt. 

`invariant()` wird zu folgenden Zeitpunkten ausgeführt:
* nach Ausführung des Konstrukors
* vor Ausführung des Destruktor
* vor Eintritt in eine Memberfunktion
* nach Beendigung einer Memberfunktion

### Überprüfung von Benutzereingaben

Da alle Contracts im Release-Build entfernt werden, sollten
Benutzereingaben nicht mit Contracts überprüft werden.
Darüber hinaus können `assert`s weiterhin in `nothrow`-Funktionen
verwendet werden, da diese fatale `Error` werfen.


Die Laufzeitentsprechung zu `assert` ist [`std.exception.enforce`](https://dlang.org/phobos/std_exception.html#.enforce),
die auffangbare `Exceptions` wirft.

### Weiterführende Quellen

- [`assert` und `enforce` in _Programming in D_](http://ddili.org/ders/d.en/assert.html)
- [Contract programming in _Programming in D_](http://ddili.org/ders/d.en/contracts.html)
- [Contract Programming bei Strukturen und Klassen in _Programming in D_](http://ddili.org/ders/d.en/invariant.html)
- [Contract programming in der D-Spezifikation](https://dlang.org/spec/contracts.html)
- [`std.exception`](https://dlang.org/phobos/std_exception.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Vereinfachter Datentyp
Nutze stattdessen std.datetime
*/
struct Date {
    private {
        int year;
        int month;
        int day;
    }

    this(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    invariant() {
        assert(year >= 1900);
        assert(month >= 1 && month <= 12);
        assert(day >= 1 && day <= 31);
    }

    /**
    Serialisiere Datenobjekt aus einem
    YYYY-MM-DD String.
    
    Params:
        date = zu serialisiernder String
        
    Returns: Datnobjekt
    /*
    void fromString(string date)
    in {
        assert(date.length == 10);
    }
    body {
        import std.format : formattedRead;
        // formattedRead parst das gegebene
        // Format und schreibt das Ergebnis
        // in die gegebene Variable
        formattedRead(date, "%d-%d-%d",
            &this.year,
            &this.month,
            &this.day);
    }

    /**
    Serialisiert das Datenobjekt zu YYYY-MM-DD

    Returns: String-Darstellung des Datums
    /*
    string toString() const
    out (result) {
        import std.algorithm : all, count,
                              equal, map;
        import std.string : isNumeric;
        import std.array : split;

        // überprüfe, ob YYYY-MM-DD
        // zurückgegeben wird
        assert(result.count("-") == 2);
        auto parts = result.split("-");
        assert(parts.map!`a.length`
                    .equal([4, 2, 2]));
        assert(parts.all!isNumeric);
    }
    body {
        import std.format : format;
        return format("%.4d-%.2d-%.2d",
                      year, month, day);
    }
}

void main() {
    auto date = Date(2016, 2, 7);

    // Dies wird invariant fehlschlagen lassen.
    // Überprüfe Benutzereingaben nicht mit
    // Contracts! 
    // Stattdessen Exceptions werfen!
    date.fromString("2016-13-7");

    date.writeln;
}
```
