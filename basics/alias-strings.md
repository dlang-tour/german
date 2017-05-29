# Alias & Strings

Nun da uns Arrays bekannt sind und wir mit den Basis-Typen 
und dem Schlüsselwort`immutable` beschäftigt haben, wird es
Zeit zwei neue Konstrukte in einer Zeile einzuführen:

    alias string = immutable(char)[];
    
Der Begriff `string` wrid durch das Schlüsselwort `alias` definiert, 
und zwar als Slice bestehend aus `immutable(char)`s.
Das bedeutet, sobald ein `string` konstruiert wurde, wird sich sein 
Inhalt nie mehr ändern. Und damit sind wir bei dem zweiten Konstrukt:
Willkommen UTF-8 `string`!

Aufgrund ihrer Unveränderlichkeit (engl.: immutablility) können 
`string`s über verschiedene Threads hinweg geteilt werden. Da `string` 
ein Slice ist, können Teile ohne Speicher-Allokation entnommnen werden.
Die Standard-Funktion `std.algorithm.splitter` z.B. teilt einen String 
anhand von Zeilensprüngen (newline-Zeichen) ohne jede Speicher-Allokation.

Neben dem  UTF-8 `string` gibt es zwei weitere Typen:

    alias wstring = immutable(wchar)[]; // UTF-16
    alias dstring = immutable(dchar)[]; // UTF-32

Diese Varianten können durch Nutzung der `to` Methode aus `std.conv` 
einfach ineinander konvertiert werden:

    dstring myDstring = to!dstring(myString);
    string myString   = to!string(myDstring);

### Unicode strings

This means that a plain `string` is defined as an array of 8-bit Unicode [code
units](http://unicode.org/glossary/#code_unit). All array operations can be
used on strings, but they will work on code unit level, and not character level. At
the same time, standard library algorithms will interpret `string`s as sequences
of [code points](http://unicode.org/glossary/#code_point), and there is also an
option to treat them as sequence of
[graphemes](http://unicode.org/glossary/#grapheme) by explicit usage of
[`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html).

This small example illustrates the difference in interpretation:

    string s = "\u0041\u0308"; // Ä

    writeln(s.length); // 3

    import std.range : walkLength;
    writeln(s.walkLength); // 2

    import std.uni : byGrapheme;
    writeln(s.byGrapheme.walkLength); // 1

Here the actual array length of `s` is 3, because it contains 3 code units:
`0x41`, `0x03` and `0x08`. Of those latter two define single code point
(combining diacritics character) and
[`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html)
(standard library function to calculate arbitrary range length) counts two code
points total. Finally, `byGrapheme` performs rather expensive calculations
to recognize that these two code points combine into a single displayed
character.

Correct processing of Unicode can be very complicated, but most of the time, D
developers can simply consider `string` variables as magical byte arrays and
rely on standard library algorithms to do the right job. Most Unicode
functionality is provided by the
[`std.uni`](https://dlang.org/library/std/uni.html) module, with some more basic
primitives available in [`std.utf`](https://dlang.org/library/std/utf.html).

### Multi-line strings

To create multi-line strings, use the `string str = q{ ... }` syntax.

    string multiline = q{ This
        may be a
        long document
    };

### Raw strings

It is also possible to use raw strings to minimize laborious escaping
of reserved symbols. Raw strings can be declared using either backticks (`` `
... ` ``) or the r(aw)-prefix (`r" ... "`).

    string raw  =  `raw "string"`; // raw "string"
    string raw2 = r"raw "string""; // raw "string"

### In-depth

- [Unicode gem](gems/unicode)
- [Characters in _Programming in D_](http://ddili.org/ders/d.en/characters.html)
- [Strings in _Programming in D_](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - UTF en-/decoding algorithms
- [std.uni](http://dlang.org/phobos/std_uni.html) - Unicode algorithms

## {SourceCode}

```d
import std.stdio;
import std.range: walkLength;
import std.uni : byGrapheme;
import std.string: format;

void main() {
    // format generates a string using a printf
    // like syntax. D allows native UTF string
    // handling!
    string str = format("%s %s", "Hellö",
        "Wörld");
    writeln("My string: ", str);
    writeln("Array length (code unit count)"
        ~ " of string: ", str.length);
    writeln("Range length (code point count)"
        ~ " of string: ", str.walkLength);
    writeln("Character length (grapheme count)"
        ~ " of string: ",
        str.byGrapheme.walkLength);

    // Strings are just normal arrays, so any
    // operation that works on arrays works here
    // too!
    import std.array: replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm: endsWith;
    writefln("Does %s end with 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv: to;
    // Convert to UTF-32
    dstring dstr = to!dstring(str);
    // .. which of course looks the same!
    writeln("My dstring: ", dstr);
}
```
