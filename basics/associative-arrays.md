# Assoziative Arrays

D besitzt integrierte *Assoziative Arrays* (auch als Hashtabelle 
oder Hashmap bekannt). Ein Assoziatives Array mit ein Schlüssel 
des Typs `string` und einem Wert des Typs `int` wird folgendermaßen 
erzeugt:

    int[string] arr;

Der Wert kann über den Schlüssel erreicht und gesetzt werden:

    arr["key1"] = 10;

Um zu testen, ob ein Schlüssel in dem Assoziativen Array enthalten
ist, kann der `in`-Ausdruck genutzt werden:

    if ("key1" in arr)
        writeln("Yes");

Der `in`-Ausdruck gibt einen Zeiger auf den Wert zurück, falls er 
gefunden wurde, anderenfalls einen `null`-Zeiger. Also können
Existenz-Test und Schreiben komfortabel kombiniert werden:

    if (auto val = "key1" in arr)
        *val = 20;

Zugriff auf einen nicht existierenden Schlüssel führt zu einem 
`RangeError` und dem sofortigen Abbruch der Anwendung. Für 
sicheren Zugriff mit einen Standardwert (engl: default value)
gibt es `get(key, defaultValue)`.

Assoziativery Arrays bieten neben der `.length`-Eigenschaft
herkömmlicher Arrays auch `.remove(val)`, um Einträge über 
ihren Schlüssel zu entfernen. 
Dem Leser wird als Übung empfohlen, die speziellen `.byKey`- 
und `.byValue`-Ranges zu erforschen.

### Weiterführende Quellen

- [Associative arrays in _Programming in D_](http://ddili.org/ders/d.en/aa.html)
- [Associative arrays specification](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.stdio : writeln;

/**
Teilt den gegebenen Text in Wörter und gibt
ein Assoziatives Array aus Wörtern und deren
Anzahl zurück.

Params:
    text: Zu teilender Text
*/
int[string] wordCount(string text)
{
    // Die Funktion splitter teilt den
    // Text in eine Range (lazy)
    import std.algorithm.iteration : splitter;
    import std.string : toLower;

    // Durch Wörter indiziert und Anzahl 
    // zurückgebend
    int[string] words;

    foreach(word; splitter(text.toLower(), " "))
    {
        // Inkrementiere Wortzähler 
        // falls ein Wort gefunden wurde.
        // Integer-Standartwert ist 0.
        words[word]++;
    }

    return words;
}

void main()
{
    string text = "D is a lot of fun";

    auto wc = wordCount(text);
    writeln("Word counts: ", wc);

    // Mögliche Iteration:
    // byKey, byValue, byKeyValue
    foreach (word; wc.byValue)
        writeln(word);
}
```
