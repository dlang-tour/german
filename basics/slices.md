# Slices

Slices sind Objekte des Types `T[]` für einen beliebigen Typen `T`.
Slices sind eine Untermenge (Sicht) oder die gesamte Menge
eines Arrays.
**Dynamische Arrays sind Slices.**

Ein Slice beinhaltet zwei Eigenschaften: (1) einen Zeiger auf das erste Element und
(2) die Länge des Slices:

    T* ptr;
    size_t length;

`size_t` entspricht dem Datentypen der maximalen Arraygröße der Zielplatform,
z.B. `uint` auf 32-bit Architekturen und `ulong` auf 64-bit Architekturen
(für detailliertere Informationen gehe zurück in das [Datentypen-Kapitel](basics/basic-types)).

### Erstellung eines neuen Slices mit `new`

Bei der Erstellung eines dynamischen Arrays wird ein Slice zum
zugewiesenen Speicher zurückgegeben.

    auto arr = new int[5];
    assert(arr.length == 5); // Speicheraddresse in arr.ptr

Der eigentliche Speicher wird von der automatischen Speicherbereinigung
(Garbage Collection) verwaltet. Ein Slice stellt nur die Menge an Elementen dar.

### Erstellung eines Slices durch bestehenden Speicher

Der Slice-Operator kann auch benutzt werden, um auf bestehenden Speicher zu zeigen.
Der Slice-Operator kann auf einen anderen Slice, auf statische Arrays, auf
Strukturen und Klassen, welche `opSlice` implementieren, und ein paar andere Objekte
angewendet werden.

Beim Ausdruck `arr[Start .. Ende]` gibt der Slice-Operator eine Teilmenge
der Elemente von `arr` anfangend bei `Start` bis aus­schließ­lich `Ende` zurück.

    auto newArr = arr[1 .. 4]; // Index 4 wird NICHT mit einbezogen
    assert(newArr.length == 3);
    newArr[0] = 10; // ändert newArr[0] und somit arr[1]

Solche Slices erstellen keine Kopie vom Speicher, sondern geben nur eine
Untermenge (Sicht) des Arrays an. Wenn kein Slice mehr zum Speicher oder zu einer
Untermenge zeigt, wird der Speicher bei der Garbage Collection gelöscht.

Mit Slices kann speichereffizienter Code geschrieben werden, welcher mit nur einem
Block an Speicher arbeitet und Sichten auf Unterbereiche des Speicherblockes zur
Vermeidung neuer Speicherallokationen benutzt.

Im [vorherigen Kapitel](basics/arrays) wurde angesprochen, dass `arr[$]` eine kurze Form von
`arr[arr.length]` ist und damit versucht das Element nach dem Ende des Slices
zu bekommen. Das würde ein `RangeError` werfen, wenn `bounds-checking` nicht
deaktiviert wurde.

### In der Tiefe

- [Einführung zu Slices in D](http://dlang.org/d-array-article.html)
- [Slices in _Programming in D_](http://ddili.org/ders/d.en/slices.html)

## {SourceCode}

```d
import std.stdio : writefln;

/**
Berechnet rekursiv das Minimum von allen
Werten in einem Slice. Für jeden Aufruf
wird eine Untermenge mit einem Slice
erstellt und erstellt daher keine Kopie
und alloziert keinen Speicher.
*/
int minimum(int[] slice)
{
    assert(slice.length > 0);
    if (slice.length == 1)
        return slice[0];
    auto otherMin = minimum(slice[1 .. $]);
    return slice[0] < otherMin ?
        slice[0] : otherMin;
}

void main()
{
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    auto min = minimum(test);
    writefln("Das Minimum von %s ist %d",
        test, min);
    assert(min == 2);
}
```