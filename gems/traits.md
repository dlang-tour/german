# Traits

Eine von D's Stärken ist CTFE (Funktionsauswertung zur 
Kompilierzeit), die, kombiniert mit Introspektion, das
Schreiben hoch optimierter generischer Programme möglich macht.

## Explizite Contracts

Traits (dt.: Merkmale / Eigenschaften) erlauben es, explizit
erlaubte Eingangstypen zu spezifizieren.
Z.B. kann `splitIntoWords` beliebige String-Typen
verarbeiten:

```d
S[] splitIntoWord(S)(S input)
if (isSomeString!S)
```
Dies gilt auch für Template-Parameter. Z.B. kann 
`myWrapper` sicherstellen, dass das hineingereichte 
Symbol eine aufrufbare Funktion ist:

```d
void myWrapper(alias f)
if (isCallable!f)
```
Als einfaches Beispiel wird die Funktion
[`commonPrefix`](https://dlang.org/phobos/std_algorithm_searching.html#.commonPrefix)
aus `std.algorithm.searching` analysiert, 
die das gemeinsame Präfix zweier Ranges ausgibt:

```d
auto commonPrefix(alias pred = "a == b", R1, R2)(R1 r1, R2 r2)
if (isForwardRange!R1
    isInputRange!R2 &&
    is(typeof(binaryFun!pred(r1.front, r2.front)))) &&
    !isNarrowString!R1)
```

Dies bedeutet, dass die Funktion nur aufrufbar ist und 
damit kompiliert, wenn:

- `r1` speicherbar ist (garantiert durch `isForwardRange`)
- `r2` iterierbar ist (garantiert durch `isInputRange`)
- `pred` mit den Elementtypen von `r1` und `r2` aufrufbar ist
- `r1` kein narrow string (`char[]`, `string`, `wchar` or `wstring`) 
 ist - der Einfachheit halber, sonst müsste eine Dekodierung stattfinden

### Spezialisierung

Viele APIs zielen auf Vielseitigkeit, allerdings soll 
die Verallgemeinerung nicht mit zusätzlicher Laufzeit
erkauft werden.
Mit Introspektion und CTFE ist es möglich, eine Funktion
zur Kompilierzeit zu spezialisieren, um so die beste 
Performance für gegebene Eingangstypen zu erreichen.

Ein gängiges Problem ist, dass die Länge eines Streams
oder einer Liste, im Gegensatz zu z.B. einem Array, vor
dem Durchschreiten nicht bekannt ist. 
Das folgende Beispiel stellt eine einfache Implementation 
der `std.range`-Methode `walkLength` dar, die das 
Durchschreiten für alle iterierbaren Typen verallgemeinert:

```d
static if (hasMember!(r, "length"))
    return r.length; // O(1)
else
    return r.walkLength; // O(n)
```

#### `commonPrefix`

Die Verwendung von Introspektion zur Kompilierzeit ist 
in Phobos allgegenwärtig. Z.B. differenziert `commonPrefix` 
zwischen `RandomAccessRange`s und linear iterierbaren Ranges,
da es in einer `RandomAccessRange` möglich ist zwischen 
Positionen zu springen und so den Algorithmus zu beschleunigen.

#### Weitere CTFE-Magie

[std.traits](https://dlang.org/phobos/std_traits.html) umfasst die 
meisten von D's [traits](https://dlang.org/spec/traits.html).

#### Spezielle Schlüsselwörter

Zusätzlich bietet D einige spezielle Schlüsselwörter für Debuggingzwecke:

```d
void test(string file = __FILE__, size_t line = __LINE__, string mod = __MODULE__,
          string func = __FUNCTION__, string pretty = __PRETTY_FUNCTION__)
{
    writefln("file: '%s', line: '%s', module: '%s',\nfunction: '%s', pretty function: '%s'",
             file, line, mod, func, pretty);
}
```

Mit D's CLI-Auswertung wird `time` nicht benötigt - CTFE kann benutzt werden!

```d
rdmd --force --eval='pragma(msg, __TIMESTAMP__);'
```

## Weiterführende Quellen

- [std.range.primitives](https://dlang.org/phobos/std_range_primitives.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [std.meta](https://dlang.org/phobos/std_meta.html)
- [Spezifikation von Traits in D](https://dlang.org/spec/traits.html)

## {SourceCode}

```d
import std.functional : binaryFun;
import std.range.primitives : empty, front,
    popFront,
    isInputRange,
    isForwardRange,
    isRandomAccessRange,
    hasSlicing,
    hasLength;
import std.stdio : writeln;
import std.traits : isNarrowString;

/**
Gibt den gemeinsamen Präfix zweier Ranges zurück
(ohne den Auto-Dekoding Spezialfall).

Params:
    pred = Predicate des Vergleichs
    r1 = Eine Forward Range aus Elementen.
    r2 = Eine Input Range aus Elementen.

Returns:
Ein Slice von r1, welches die Zeichen enthält,
mit denen beie Ranges beginnen.
 */
auto commonPrefix(alias pred = "a == b", R1, R2)
                 (R1 r1, R2 r2)
if (isForwardRange!R1 && isInputRange!R2 &&
    !isNarrowString!R1 &&
    is(typeof(binaryFun!pred(r1.front,
                             r2.front))))
{
    import std.algorithm.comparison : min;
    static if (isRandomAccessRange!R1 &&
               isRandomAccessRange!R2 &&
               hasLength!R1 && hasLength!R2 &&
               hasSlicing!R1)
    {
        immutable limit = min(r1.length,
                              r2.length);
        foreach (i; 0 .. limit)
        {
            if (!binaryFun!pred(r1[i], r2[i]))
            {
                return r1[0 .. i];
            }
        }
        return r1[0 .. limit];
    }
    else
    {
        import std.range : takeExactly;
        auto result = r1.save;
        size_t i = 0;
        for (;
             !r1.empty && !r2.empty &&
             binaryFun!pred(r1.front, r2.front);
             ++i, r1.popFront(), r2.popFront())
        {}
        return takeExactly(result, i);
    }
}

void main()
{
    // prints: "hello, "
    writeln(commonPrefix("hello, world"d,
                         "hello, there"d));
}
```
