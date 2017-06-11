# Unicode in D

Unicode ist ein globaler Standard für Textkodierung und 
-repräsentation in Computern. D unterstützt Unicode 
vollständig, sowohl sprachseitig als auch in der 
Standard-Bibliothek.

## Was und Warum

Computer haben auf der niedrigsten Ebene keinen Begriff 
davon, was ein Text ist, da sie nur mit Zahlen umgehen.
Daher braucht es eine Möglichkeit, Textdaten in eine
binäre Repräsentation zu wandeln und umgekehrt. Die Methode
der Umwandlung wird Kodierungsschema (engl.: encoding scheme)
genannt, Unicode ist ein solches Schema.

Der Beispielcode veranschaulicht unterliegende numerische
Darstellung von Strings.

Unicode ist einzigartig, weil es alle Sprachen der Welt in
einem Kodierungsschema repräsentiert. Vor Unicode war eine
Kommunikation zwischen Computern unterschiedlicher Hersteller
oder Herkunftsländer eine schwierige Angelegenheit, manchmal
wurden Kodierungsschemata gar nicht unterstützt, was die 
Darstellung des Textes auf diesem Computer unmöglich machte.

Für weitere Informationen und technische Details wird der
[Wikipedia-Artikel über Unicode](https://de.wikipedia.org/wiki/Unicode)
empfohlen.

## Wie

Unicode hat die meisten dieser Probleme gelöst und wird von
jeder modernen Maschine unterstützt. D hat aus den Fehlern
älterer Programmiersprachen gelernt. So sind **alle** Strings 
in D Unicode-Strings, während sie in Sprachen wie C und C++
als einfache Byte-Arrays implementiert sind.

In D gilt folgende Zuordnung:
| Stringtyp | Zeichentyp | Kodierung |
|-----------|------------|-----------|
| `string`  | `char`     | UTF-8     |
| `wstring` | `wchar`    | UTF-16    |
| `dstring` | `dchar`    | UTF-32    |


According to the spec, it is an error to store non-Unicode
data in the D string types; expect your program to fail in
different ways if your string is encoded improperly.

In order to store other string encodings, or to obtain C/C++
behavior, you can use `ubyte[]` or `char*`.

## Strings in Range Algorithms

*Reading the [gem on range algorithms](gems/range-algorithms) is
suggested for this section.*

There are some important caveats to keep in mind with Unicode
in D.

First, as a convenience feature, when iterating over a string
using the range functions, Phobos will encode the elements of
`string`s and `wstrings` into UTF-32 code-points as each item.
This practice, known as **auto decoding**, means that

```
static assert(is(typeof(utf8.front) == dchar));
```

This behavior has a lot of implications, the main one that
confuses most people is that `std.traits.hasLength!(string)`
equals `False`. Why? Because, in terms of the range API,
`string`'s `length` returns **the number of elements in the string**,
rather than the number of elements the *range function will iterate over*.

From the example, you can see why these two things might not always
be equal. As such, range algorithms in Phobos act as if `string`s
do not have length information.

For more information on the technical details of auto decoding,
and what it means for your program, check the links in the
"In-Depth" section.

### In-Depth

- [Unicode on Wikipedia](https://en.wikipedia.org/wiki/Unicode)
- [Basic Unicode Functions in Phobos](https://dlang.org/phobos/std_uni.html)
- [Tools for Decoding and Encoding UTF in Phobos](https://dlang.org/phobos/std_utf.html)
- [An in Depth Look at Auto Decoding](https://jackstouffer.com/blog/d_auto_decoding_and_you.html)
- [An in Depth Essay on Benefits of Using UTF-8](http://utf8everywhere.org/)

## {SourceCode}

```d
import std.range.primitives : empty,
    front, popFront;
import std.stdio : write, writeln;

void main()
{
    string utf8 = "å ø ∑ 😦";
    wstring utf16 = "å ø ∑ 😦";
    dstring utf32 = "å ø ∑ 😦";

    writeln("utf8 length: ", utf8.length);
    writeln("utf16 length: ", utf16.length);
    writeln("utf32 length: ", utf32.length);

    foreach (item; utf8)
    {
        auto c = cast(ubyte) item;
        write(c, " ");
    }
    writeln();

    // Because the specified the element type is
    // dchar, look-ahead is used to encode the
    // string to UTF-32 code points.
    // For non-strings, a simple cast is used
    foreach (dchar item; utf16)
    {
        auto c = cast(ushort) item;
        write(c, " ");
    }
    writeln();

    // a result of auto-decoding
    static assert(
        is(typeof(utf8[0]) == immutable(char))
    );
    static assert(
        is(typeof(utf8.front) == dchar)
    );
}
```
