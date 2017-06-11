# Bitmanipulation

Ein sehr gutes Beispiel für die Erzeugung von Code zur 
Kompilierzeit in D mit Mixins ist Bitmanipulation.

### Simple bit manipulation

D bietet die folgenden Operatoren für Bitmanipulation:

- `&` bitweises Und
- `|` bitweises Oder
- `~` bitweise Negation
- `<<`  bitweise vorzeichenbewahrende Links-Verschiebung
- `>>`  bitweise vorzeichenbewahrende Rechts-Verschiebung
- `>>>` bitweise vorzeichenlose Rechts-Verschiebung

### Ein praktisches Beispiel 

Ein gängiges Beispiel für Bitmanipulation ist das Einlesen
eines Bitwertes. D bietet `core.bitop.bt` für die meisten 
allgemeinen Aufgaben. Nichtsdestotrotz werden wir zum besseren
Verständnis die ausführliche Implementierung eines Bit-Tests
vornehmen:

```d
enum posA = 1;
enum maskA = (1 << posA);
bool getFieldA()
{
    return _data & maskA;
}
```

Eine Verallgemeinerung ist der Test von Bitblöcken, 
die aus mehreren aneinandergereihten Bits bestehen. 
Dazu wird eine spezielle Maske der Länge des Blocks 
benötigt. Der Datenblock wird entsprechend verschoben, 
bevor die Maske angewendet wird:

```d
enum posA = 1;
enum lenA = 3;
enum maskA = (1 << lenA) - 1; // ...0111
uint getFieldA()
{
    return (_data >> posA) & maskA;
}
```

Das Setzen solch eines Blocks kann genauso durch 
Negation der Maske erreicht werden, wodurch das Schreiben
nur innerhalb des spezifizierten Blocks möglich ist:

```d
void setFieldA(bool b);
{
    return (_data & ~maskAWrite) | ((b << aPos) & maskAWrite);
}
```

## Les- und Wartbarkeit mit `std.bitmanip` 

D bietet einen kompletten Werkzeugkoffer zur Erzeugung 
benutzerdefinierter Bitmanipulationen. Allerdings ist
bitmanipulierender Code oft fehlerträchtig und schwer 
zu warten.
`std.bitmanip` schafft hier Abhilfe, indem es (unter 
Zuhilfenahme von Mixins) erlaubt, wartbare, leicht 
lesbare Bitmanipulationen zu schreiben - ohne dabei 
Performance zu opfern.

Das Beispiel zeigt eine `BitVector`-Definition, die, 
obwohl sie nur X Bits benutzt, kaum von regulären Strukturen 
unterscheidbar ist.

`std.bitmanip` und `core.bitop` beinhalten weitere Helfer,
die sehr hilfreich für Anwendungen sind, die die Anforderung
eines geringen Speicherverbrauchs stellen.

### Auffüllen und Ausrichtung

Der Compiler wird Variablen auffüllen (engl.: Padding), die 
eine Größe kleiner als das Speicherlayout des Betriebssystems 
(`size_t.sizeof`) besitzen, wie z.B. `bool`, `byte` oder `char`.
Daher wird die Ausrichtung (engl.: Allignment) der Felder 
beginnend bei Bit 0 empfohlen.

## Weiterführende Quellen

- [std.bitmanip](http://dlang.org/phobos/std_bitmanip.html)
- [_Bit Packing like a Madman_](http://dconf.org/2016/talks/sechet.html)

## {SourceCode}

```d
struct BitVector
{
    import std.bitmanip : bitfields;
    // erzeugt ein private Feld mit 
    // folgenden Proxies
    mixin(bitfields!(
        uint, "x",    2,
        int,  "y",    3,
        uint, "z",    2,
        bool, "flag", 1));
}

void main()
{
    import std.stdio : writefln, writeln;

    BitVector vec;
    vec.x = 2;
    vec.z = vec.x - 1;
    writefln("x: %d, y: %d, z: %d",
              vec.x, vec.y, vec.z);

    // nur 8 bit - 1 Byte werden benutzt
    writeln(BitVector.sizeof);

    struct Vector { int x, y, z; }
    // 4 Byte (int) pro Variable
    writeln(Vector.sizeof);

	struct BadVector
	{
		bool a;
		int x, y, z;
		bool b;
	}
	// wegen des Auffüllens (padding)
	// werden 4 Byte pro Feld verwendet
	writeln(BadVector.sizeof);
}
```
