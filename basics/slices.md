# Slices

Slices sind Objekte vom Typ `T[]` für jeden Typen `T`.
Slices stellen eine Untermenge oder die gesamte Menge
eines Arrays dar.
**Slices und dynamische Arrays sind das­sel­be.**

Ein Slice beinhaltet zwei Eigenschaften - einen Zeiger auf das erste Element und
die Länge des Slices:

    T* ptr;
    size_t length; // unsigned 32 bit auf 32bit, unsigned 64 bit auf 64bit

### Ein neues Slice mit new erstellen

Wenn ein dynamisches Array erstellt wird wird ein Slice zum
erstellten Speicher zurückgegeben.

    auto arr = new int[5];
    assert(arr.length == 5); // Speicheraddresse in arr.ptr

Der eigentliche Speicher wird vom Garbage Collector verwaltet, das Slice
stellt hier die Menge an Elementen dar.

### Ein Slice anhand vom bestenden Speicher erstellen

Der Slice Operator kann auch benutzt werden, um auf bestenden Speicher zu zeigen.
Der Slice Operator kann auf ein anderes Slice, auf statische Arrays, auf
Strukturen und Klassen welche `opSlice` implementieren und ein paar andere Objekte
angewendet werden.

Beim Ausdruck `arr[Start .. Ende]` gibt der Slice Operator die Elemente von `arr`
startend bei Start bis aus­schließ­lich Ende als Untermenge zurück.

    auto newArr = arr[1 .. 4]; // Index 4 wird NICHT mit einbezogen
    assert(newArr.length == 3);
    newArr[0] = 10; // ändert newArr[0] und somit arr[1]

Solche Slices erstellen keine Kopie vom Speicher sondern geben nur eine
Untermenge des Arrays an. Wenn kein Slice mehr zum Speicher oder zu einer
Untermenge zeigt wird der Speicher vom Garbage Collector gelöscht.

Mit Slices kann Speichereffizienter Code geschrieben werden, welcher mit nur einem
Block an Speicher arbeitet und Unterbereiche des Speichers benutzt um keinen neuen
Speicher generieren zu müssen.

Im vorherigen Kapitel wurde angesprochen, dass `arr[$]` eine kurze Form von
`arr[arr.length]` ist und damit versucht das Element nach dem Ende des Slices
zu bekommen. Das würde ein `RangeError` werfen wenn bounds-checking nicht
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
und allocated keinen Speicher.
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
    writefln("Das minimum von %s ist %d",
        test, min);
    assert(min == 2);
}
```