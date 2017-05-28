# Arrays

In D gibt es zwei verschiedene Arten von Arrays: **statische** und **dynamische**
Arrays. Bei Zugriffen auf Elemente eines Arrays wird immer überprüft ob der Index
innerhalb des Arrays liegt, außer wenn der Compiler sicherstellen kann, dass diese
Überprüfung nicht nötig ist.
Wenn der Index außerhalb des Arrays liegt wird ein `RangeError` geworfen, der die
Anwendung standardmäßig stoppt.
Dieses Feature kann durch die Compiler Flag `-boundscheck=off` ausgeschaltet
werden, wodurch Geschwindigkeitsverbesserungen auf Kosten von Sicherheit erreicht
werden können.

#### Statische Arrays

Statische Arrays werden auf dem Stack gespeichert wenn sie innerhalb einer
Funktion definiert sind, andernfalls werden sie im statischen Speicher gelegt.
Sie haben eine feste Länge, welche zur Compile-Time bekannt ist. Der Typ
beinhaltet hierbei die Länge:

    int[8] arr;

Der Typ von `arr` ist `int[8]`. Die Länge gehört zum Typen und wird nicht wie in
C/C++ nach dem Variablennamen geschrieben.

#### Dynamische Arrays

Dynamische Arrays werden auf dem Heap gespeichert und können während der
Laufzeit vergrößert und verkleinert werden. Ein dynamisches Array kann mit `new`
und der Länge erstellt werden:

    int size = 8; // run-time variable
    int[] arr = new int[size];

Der Typ von `arr` ist `int[]`, welches ein **slice** ist. Slices
werden im nächsten Kapitel genauer erklärt. Mehrdimensionale Arrays können
mit dem `auto arr = new int[3][3]` Syntax erstellt werden.

#### Array Operationen und Eigenschaften

Arrays können mit dem `~` operator verbunden werden um ein neues dynamisches
Array zu erstellen.

Mathematische Operationen können auf das gesamte Array mit dem Syntax
`c[] = a[] + b[]` angewendet werden.
Dieser Code summiert jedes Element in `a` mit `b` sodass
`c[0] = a[0] + b[0]`, `c[1] = a[1] + b[1]`, etc. Es ist auch möglich
solche Operationen auf ein gesamtes Array mit nur einem Wert auszuführen:

    a[] *= 2; // multipliziert alle Elemente mit 2
    a[] %= 26; // speichert den Rest beim Teilen mit 26 in jedem Element von a

Diese Operationen können vom Compiler optimiert werden um spezielle
Prozessor Instruktionen zu verwenden.

Statische und dynamische Arrays beinhalten eine `.length` Eigenschaft.
Diese Eigenschaft ist readonly für statische Arrays. Bei dynamischen
Arrays kann diese Eigenschaft geändert werden um das Array zu vergrößern
oder zu verkleinern. Außerdem haben Arrays eine `.dup` Eigenschaft welche
eine Kopie des Arrays erstellt.

Wenn man auf ein Array Element zugreift kann man mit dem speziellen `$`
Symbol innerhalb der Arrayklammern auf die Länge des Arrays zugreifen.
Zum Beispiel referenziert `arr[$ - 1]` das letzte Element des Arrays
und ist equivalent zu `arr[arr.length - 1]`.

### Übung

Implementier die Funktion `encrypt` um die geheime Nachricht zu
verschlüsseln. Der Text sollte mit einer *Caesar Verschlüsselung*
verschlüsselt werden, welche die Buchstaben im Alphabet mit einer
bestimmten Anzahl an Zeichen verschiebt. Der verschlüsselte Text
wird nur Zeichen innerhalb `a-z` beinhalten, was die Implementation
etwas einfacher machen sollte.

### In der Tiefe

- [Arrays in _Programming in D_](http://ddili.org/ders/d.en/arrays.html)
- [Array Spezifikation](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Verschiebt jedes Zeichen im Array `input` um
`shift` Zeichen.
Die Zeichen sind beschränkt auf `a-z`
und das Zeichen nach z is a.

Params:
    input = array zur Verschlüsselung
    shift = Verschiebung für jedes Zeichen
Returns:
    Verschlüsseltes char Array
*/
char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // ...
    return result;
}

void main()
{
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // Das letzte Komma nach einem Wert
			// wird ignoriert.
    ];
    writeln("Before: ", toBeEncrypted);
		// Hier verschlüsseln/verschieben wir
		// den Text um 16 Zeichen.
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("After: ", encrypted);

    // Sicherstellen, dass der Algorithmus
    // richtig funktioniert.
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```