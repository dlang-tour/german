# Willkommen zu D

Herzlich willkommen zu der interaktiven Tour der Programmiersprache D.

{{#dmanmobile}}

Diese Tour wird dir einen Überblick über diese __mächtige__ und __ausdrucksstarke__
Sprache, welche direkt zu __effizientem__, __nativem__ Maschinencode kompiliert.

{{/dmanmobile}}

### Was ist D?

D ist die Akkumulation von Jahrzehnten an Erfahrung im Compilerbau
für viele verschiedene Sprachen und D hat eine Vielzahl an einzigartigen
[Features](http://dlang.org/overview.html):

{{#dmandesktop}}

- _Konstrukte auf hoher Ebene_ für bedeutende Modellierfähigkeiten
- eine _hoch performante_, kompilierte Sprache
- statische Typisierung
- direktes Interface zu den Betriebssystem API's und Hardware
- rasend schnelle Kompilierzeiten
- sicherer Speicherzugriff (SafeD)
- _wartbarer_, _einfach zu verstehender_ Code
- flache Lernkurve (sehr änhliche Syntax zu C, C++, Java u.a.)
- kompatibel mit der C Binärschnittstelle (ABI)
- verschiedene Paradigmen (imperative, strukturierte, objektorientierte, generische, funktionale Programmierung - und sogar Assembler)
- eingebaute Fehlerkorrektur (Verträge, Unittests)

... und [viele weitere Features](http://dlang.org/overview.html).

{{/dmandesktop}}

### Über diese Tour

Jede Lektion beinhaltet ein Beispiel mit Quellcode, welches bearbeitet werden kann
und so zum Experimentieren mit den Features von D verwendet werden kann.
Klicke auf den Run-Button (oder drücke `Umschalt-Enter`) um das jeweilige Programm zu kompilieren
und auszuführen.

### Mitmachen

Diese Tour ist [open source](https://github.com/dlang-tour)
und wir freuen uns über Pull Requests, die diese Tour noch besser machen.

## {SourceCode}

```d
import std.stdio;

// Lass uns anfangen
void main()
{
    writeln("Hallo Welt!");
    
    // Ein Beispiel für erfahrenere User:
    // Nimm drei Arrays, und sortiere  
    // ohne weitere Speicherallokation
    // über alle Arrays in-place
    int[] arr1 = [4, 9, 7];
    int[] arr2 = [5, 2, 1, 10];
    int[] arr3 = [6, 8, 3];
    sort(chain(arr1, arr2, arr3));
    writefln("%s\n%s\n%s\n", arr1, arr2, arr3);
    // Mehr über dieses Beispiel unter 
    // dem Stichwort "Ranges"
}
```
