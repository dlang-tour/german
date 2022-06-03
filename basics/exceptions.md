# Exceptions

Dieser Abschnitt befasst sich nur mit Benutzer-`Exceptions` - System-`Error` sind
normalerweise fatal und sollten __nie__ auffangen werden.

### Exceptions auffangen

Ein Anwendungsfall für Exceptions ist die Validierung von potentiell invaliden
Benutzereingaben. Sobald eine Exception geworfen wurde, wird der Stack abgewickelt
(engl: to unwind), bis der erste passende Exception-Handler gefunden wurde.

```d
try
{
    readText("dummyFile");
}
catch (FileException e)
{
    // ...
}
```

Die Verwendung mehrerer `catch`-Blocks ist möglich. Ein abschließender
`finally`-Block wird unabhängig von einem Fehlerauftreten ausgeführt.
Exceptions werden mit `throw` geworfen.

```d
try
{
    throw new StringException("You shall not pass.");
}
catch (FileException e)
{
    // ...
}
catch (StringException e)
{
    // ...
}
finally
{
    // ...
}
```

Beachte: Die Verwendung eines [scope guards](gems/scope-guards) ist
in der Regel die bessere Lösung verglichen mit dem `try-finally`-Muster.

### Benutzerdefinierte Exceptions

Eine benutzerdefinierte Exception kann durch Erben von der Klasse
`Exception` erzeugt werden:

```d
class UserNotFoundException : Exception
{
    this(string msg, string file = __FILE__, size_t line = __LINE__) {
        super(msg, file, line);
    }
}
throw new UserNotFoundException("D-Man is on vacation");
```

### Sicherheit durch `nothrow`

Der D-Compiler kann sicherstellen, dass eine Funktion keine katastrophischen
Seiteneffekte provozieren kann.
Solche Funktionen werden mit dem `nothrow`-Schlüsselwort annotiert. Der D-Compiler
verbietet statisch das Werfen von Exceptions in `nothrow`-Funktionen.

```d
bool lessThan(int a, int b) nothrow
{
    writeln("unsafe world"); // output can throw exceptions, thus this is forbidden
    return a < b;
}
```

Anmerkung: Der Compiler ist in der Lage Attribute für Template-Code automatisch
abzuleiten.

### std.exception

Es ist wichtig, Contract-Programmierung für Benutzereingaben zu vermeiden, da
Contracts im Falle einer Kompilierung im Release-Mode entfernt werden.
Komfortablerweise bietet `std.exception` `enforce`, welches wie `assert`
genutzt werden kann, aber `Exceptions` anstatt von `AssertError` wirft.

```d
import std.exception : enforce;
float magic = 1_000_000_000;
enforce(magic + 42 - magic == 42, "Floatingpoint-Arithmetik macht Spaß!");

// throw custom exceptions
enforce!StringException('a' != 'A', "Groß- / Kleinschreibung beachten!");
```

`std.exception` bietet darüberhinaus die Möglichkeit, die Behandlung
nicht-fataler Fehler mit `collect` zu verkürzen.

```d
import std.exception : collectException;
auto e = collectException(aDangerousOperation());
if (e)
    writeln("The dangerous operation failed with ", e);
```

Der Test, ob eine Exception geworfen wurde, kann mit `assertThrown`
erfolgen.

### Weiterführende Quellen

- [Exception Safety in D](https://dlang.org/exception-safe.html)
- [std.exception](https://dlang.org/phobos/std_exception.html)
- system-level [core.exception](https://dlang.org/phobos/core_exception.html)
- [object.Exception](https://dlang.org/library/object/exception.html) - base class of all exceptions that are safe to catch and handle.

## {SourceCode}

```d
import std.file : FileException, readText;
import std.stdio : writeln;

void main()
{
    try
    {
        readText("dummyFile");
    }
    catch (FileException e)
    {
        writeln("Message:\n", e.msg);
        writeln("File: ", e.file);
        writeln("Line: ", e.line);
        writeln("Stack trace:\n", e.info);

        // Standard-Formatierug
        // auch möglich!
        // writeln(e);
    }
}
```
