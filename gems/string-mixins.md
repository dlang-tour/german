# String Mixins

Der `mixin`-Ausdruck nimmt einen beliebigen String,
kompiliert diesen und generiert dementsprechende
Befehle. Dies ist ein reiner **Kompilierzeit**-Mechanismus,
der nur mit Strings arbeitet, die während der Kompilierung
zur Verfügung stehen.

    mixin("int b = 5");
    assert(b == 5); // wird einwandfrei kompiliert

`mixin` arbeitet auch mit Strings, die dynamisch erzeugt werden,
sofern die benötigten Informationen nicht auf Laufzeit-Werten
beruhen.

Die Kombination von `mixin` und **CTFE** erlauben beeindruckende
Bibliotheken wie [Pegged](https://github.com/PhilippeSigaud/Pegged),
die einen Grammatik-Parser aus einer als String definierten
Grammatik im Quellcode erzeugt.

### Weiterführende Quellen

- [Mixins in D](https://dlang.org/spec/template-mixin.html)

## {SourceCode}

```d
import std.stdio : writeln;

auto calculate(string op, T)(T lhs, T rhs)
{
    return mixin("lhs " ~ op ~ " rhs");
}

void main()
{
    // 'Hello World' mal anders!
    mixin(`writeln("Hello World");`);

    // Reiche den gewünschten Operator
    // als Template-Parameter weiter.
    writeln("5 + 12 = ", calculate!"+"(5,12));
    writeln("10 - 8 = ", calculate!"-"(10,8));
    writeln("8 * 8 = ", calculate!"*"(8,8));
    writeln("100 / 5 = ", calculate!"/"(100,5));
}
```
