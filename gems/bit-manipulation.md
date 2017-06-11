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

## `std.bitmanip` to the rescue

It's a lot of fun to write ones' custom bit manipulation code and
D provides the full toolbox to do so. However in most cases one doesn't want to
copy&paste such bit manipulation code as this is very error-prone and hard to maintain.
Hence in D `std.bitmanip` helps you to write maintainable, easy-to-read bit manipulations
with `std.bitmanip` and the power of mixins - without sacrificing performance.

Have a look at the exercise section. A `BitVector` is defined, but it still uses
just X bits and is nearly indistinguishable from a regular struct.

`std.bitmanip` and `core.bitop` contain more helpers that are greatly helpful
for applications that require low-memory consumption.

### Padding and alignment

As the compiler will add padding for variables with a size lower than the current
OS memory layout (`size_t.sizeof`) e.g. `bool`, `byte`, `char`, it is recommended
to start with fields of high alignments.

## In-depth

- [std.bitmanip](http://dlang.org/phobos/std_bitmanip.html) - Bit-level manipulation facilities
- [_Bit Packing like a Madman_](http://dconf.org/2016/talks/sechet.html)

## {SourceCode}

```d
struct BitVector
{
    import std.bitmanip : bitfields;
    // creates a private field with the
    // following proxies
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

    // only 8 bit - 1 byte are used
    writeln(BitVector.sizeof);

    struct Vector { int x, y, z; }
    // 4 bytes (int) per variable
    writeln(Vector.sizeof);

	struct BadVector
	{
		bool a;
		int x, y, z;
		bool b;
	}
	// due to padding,
	// 4 bytes are used for each field
	writeln(BadVector.sizeof);
}
```
