# Scope Guards

Scope Guards (dt. etwa: Bereichswächter) erlauben die Ausführung
von Anweisungen unter bestimmten Bedingungen, wenn der aktuelle
Codeblock verlassen wird:

* `scope(exit)` wird die Anweisungen immer ausführen
* `scope(success)` Anweisungen werden ausgeführt, wenn keine 
  Exception geworfen wurde
* `scope(failure)` Anweisungen werden ausgeführt, wenn vor
  dem Blockende eine Exception geworfen wurde

Die Verwendung von Scope Guards erhöht die Klarheit des Quellcodes, weil 
so Ressourcenallokation und Aufräum-Code nebeneinander stehen können.
Auch kann sichergestellt werden, dass bestimmter Code *immer*
ausgeführt wird, unabhängig von dem zur Laufzeit ausgeführten Pfad.

D's `scope` bietet einen effektiven Ersatz für das in C++ 
verwendete RAII-Idiom, dass oft zur Implementierung spezieller Scope 
Guard-Objekte für spezielle Ressourcen führt.

Scope Guards werden in umgekehrter Reihenfolge ausgeführt, wie sie
definiert wurden.

### Weiterführende Quellen

- [`scope` in _Programming in D_](http://ddili.org/ders/d.en/scope.html)

## {SourceCode}

```d
import std.stdio : writefln, writeln;

void main()
{
    writeln("<html>");
    scope(exit) writeln("</html>");

    {
        writeln("\t<head>");
        scope(exit) writeln("\t</head>");
        "\t<title>%s</title>".writefln("Hallo");
    } // scope(exit) der vorigen Zeile
      // wird hier ausgeführt

    writeln("\t<body>");
    scope(exit) writeln("\t</body>");

    writeln("\t\t<h1>Hallo Welt!</h1>");

    // Scope Guards erlauben es, Allokationen
    // und zugehöriges Aufräumen nebeneinander
    // zu schreiben
    import core.stdc.stdlib : free, malloc;
    int* p = cast(int*) malloc(int.sizeof);
    scope(exit) free(p);
}
```
