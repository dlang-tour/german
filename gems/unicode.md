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

Für D-Strings gilt folgende Zuordnung:

| Stringtyp | Zeichentyp | Kodierung |
|-----------|------------|-----------|
| `string`  | `char`     | UTF-8     |
| `wstring` | `wchar`    | UTF-16    |
| `dstring` | `dchar`    | UTF-32    |


Der Spezifikation nach führt das Speichern von Nicht-Unicode-Daten
in D-Stringtypen zu einem Fehler. Programme scheitern auf
verschiedene Arten, wenn ein String nicht korrekt kodiert ist.

Um andere Stringkodierungen nutzen, oder C/C++-Verhalten zu 
erhalten, können `ubyte[]` oder `char*` genutzt werden.

## Strings in Range-Algorithmen

*Die Lektüre der Sektion [Range Algorithmen](gems/range-algorithms) 
wird für diesen Abschnitt empfohlen.*

Ein paar wichtige Hinweise sind im Umgang mit Unicode in D zu beachten.

Wenn mit den Range-Funktionen über einen String iteriert wird,
wird Phobos bequemerweise alle `string`s und `wstrings` in UTF-32
Codepoints wandeln. Dies wird auch als **Autodecoding** bezeichnet:

```
static assert(is(typeof(utf8.front) == dchar));
```

Dieses Verhalten hat einige Implikationen. Viele Leute verwirrt,
dass `std.traits.hasLength!(string)` zu `False` ausgewertet wird.
Hinsichtlich der Range-API liegt dies in der Tatsache begründet,
dass die `length`-Methode von `string` die **Anzahl der Elemente in
dem String** und nicht die Anzahl der Elemente, *über die die 
Range-Funktion iteriert*, zurückgibt.

Anhand dieses Beispiels wird deutlich, warum diese beiden Dinge
nicht immer gleich sein müssen. Daher verhalten sich dei Range-Algorithmen
aus Phobos so, als ob `string`s keine `length`-Methode hätten.

### Weiterführende Quellen

- [Unicode auf Wikipedia](https://de.wikipedia.org/wiki/Unicode)
- [Grundlegende Unicode-Funktionen in Phobos](https://dlang.org/phobos/std_uni.html)
- [Tools für UTF-Dekodierung und -Kodierung in Phobos](https://dlang.org/phobos/std_utf.html)
- [Ein vertiefender Blick in Autodecoding](https://jackstouffer.com/blog/d_auto_decoding_and_you.html)
- [Eine vertiefende Abhandlung der Vorteile von UTF-8](http://utf8everywhere.org/)

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

    // Da die Elemente von Typ dchar sind,
    // wird look-ahead zur Kodierung des
    // Strings in UTF-32 Codepoints genutzt.
    // FÜr Nicht-Strings wird einfach gecasted
    foreach (dchar item; utf16)
    {
        auto c = cast(ushort) item;
        write(c, " ");
    }
    writeln();

    // ein Autodecoding-Ergebnis
    static assert(
        is(typeof(utf8[0]) == immutable(char))
    );
    static assert(
        is(typeof(utf8.front) == dchar)
    );
}
```
