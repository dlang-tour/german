# Arrays

In D gibt es zwei verschiedene Arten von Arrays: **statische** und **dynamische**
Arrays. Bei Zugriffen auf die Elemente eines Arrays wird immer überprüft, ob der Index
innerhalb des Arrays liegt, außer wenn der Compiler sicherstellen kann, dass diese
Überprüfung nicht nötig ist.
Wenn der Index außerhalb des Arrays liegt, wird eine `RangeError`-Exception geworfen,
die die Anwendung standardmäßig stoppt.
Dieses Feature kann durch die Compiler-Option `-boundscheck=off` ausgeschaltet
werden, wodurch Geschwindigkeitsverbesserungen auf Kosten von reduzierter Sicherheit
erreicht werden können.

#### Statische Arrays

Statische Arrays werden auf dem Stack gespeichert, wenn sie innerhalb einer
Funktion definiert sind. Andernfalls werden sie im statischen Speicher gesetzt.
Sie haben eine feste Länge, welche zur Compile-Zeit bekannt ist. Der Typ
beinhaltet hierbei die Länge:

    int[8] arr;

Der Typ von `arr` ist `int[8]`. Die Länge gehört zum Typ und wird nicht, wie in
C/C++, nach dem Variablennamen geschrieben.

#### Dynamische Arrays

Dynamische Arrays können zur Laufzeit erzeugt bzw. in ihrer Größe geändert werden.
Dies geschieht durch Nutzung des Heaps als Speicherort. Ein dynamisches Array wird
mithilfe des `new`-Ausdrucks und der Länge erstellt:

    int size = 8; // run-time variable
    int[] arr = new int[size];

Der Typ von `arr` ist `int[]`, welches ein **Slice** ist. Slices
werden im [nächsten Kapitel](basics/slices) genauer erklärt. Mehrdimensionale Arrays können
mit der `auto arr = new int[3][3]` Syntax erstellt werden.

#### Array-Operationen und Eigenschaften

Arrays können mit dem `~` Operator zu einem neuen dynamischen Array verkettet
werden.

Mathematische Operationen können auf das gesamte Array mit der Syntax
`c[] = a[] + b[]` angewendet werden.
Dieser Code summiert jedes Element in `a` mit `b`, sodass
`c[0] = a[0] + b[0]`, `c[1] = a[1] + b[1]`, etc. Es ist auch möglich
Operationen nur mit einem Wert auf ein gesamtes Array auszuführen:

    a[] *= 2; // multipliziert alle Elemente mit 2
    a[] %= 26; // speichert in jedem Element den Rest beim Teilen mit 26

Diese Operationen können vom Compiler durch spezielle Prozessor-Instruktionen
optimiert werden.

Statische und dynamische Arrays beinhalten eine `.length`-Eigenschaft.
Diese Eigenschaft ist nicht veränderbar für statische Arrays. Bei dynamischen
Arrays kann durch das Setzen dieser Eigenschaft das Array vergrößert oder
verkleinert werden. Außerdem haben Arrays eine `.dup` Eigenschaft, welche
eine Kopie (Duplikat) des Arrays erstellt.

Innerhalb der Arrayklammern kann auf die Länge des Arrays durch Verwendung
des `$`-Symbols zugegriffen werden.
Zum Beispiel referenziert `arr[$ - 1]` das letzte Element eines Arrays
und ist äquivalent zu `arr[arr.length - 1]`.

### Übung

Implementiere die Funktion `encrypt` zur Verschlüsselung einer geheimen
Nachricht. Der Text sollte mit einer *Caesar Verschlüsselung*
verschlüsselt werden, welche die Buchstaben im Alphabet mit einer
bestimmten Anzahl an Zeichen verschiebt. Der verschlüsselte Text
wird nur Zeichen innerhalb `a-z` beinhalten (dies vereinfacht die Implementierung).

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
und das nächste Zeichen nach z is a.

Params:
    input = Array zur Verschlüsselung
    shift = Verschiebung für jedes Zeichen
Returns:
    Verschlüsseltes char-Array
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
    / Hier verschlüsseln/verschieben wir
    // den Text um 16 Zeichen.
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("After: ", encrypted);

    // Testen der Korrektheit des Algorithmus
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```