# Assoziative Arrays

D besitzt integrierte *Assoziative Arrays* (auch als Hashtabelle 
oder Hashmap bekannt). Ein Assoziatives Array mit ein Schlüssel-Typ
`string` und einem Wert-Typ `int` wird folgendermaßen erzeugt:

    int[string] arr;

Der Wert kann über den Schlüssel erreicht und gesetzt werden:

    arr["key1"] = 10;

Um zu testen, ob ein Schlüssel in dem Assoziativen Array enthalten
ist, kann er `in`-Ausdruck genutzt werden:

    if ("key1" in arr)
        writeln("Yes");

Der `in`-Ausdruck gibt einen Zeiger auf den Wert zurück, fall er 
gefunden wurde, anderenfalls einen `null`-Zeiger. Also können der 
Existenz-Test und das Schreiben komfortabel komibiert werden:

    if (auto val = "key1" in arr)
        *val = 20;

Access to a key which doesn't exist yields a `RangeError`
that immediately aborts the application. For a safe access
with a default value, `get(key, defaultValue)` can be used.

AA's have the `.length` property like arrays and provide
a `.remove(val)` member to remove entries by their key.
It is left as an exercise to the reader to explore
the special `.byKey` and `.byValue` ranges.

### In-depth

- [Associative arrays in _Programming in D_](http://ddili.org/ders/d.en/aa.html)
- [Associative arrays specification](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.stdio : writeln;

/**
Splits the given text into words and returns
an associative array that maps words to their
respective word counts.

Params:
    text = text to be splitted
*/
int[string] wordCount(string text)
{
    // The function splitter lazily splits the
    // input into a range
    import std.algorithm.iteration : splitter;
    import std.string : toLower;

    // Indexed by words and returning the count
    int[string] words;

    foreach(word; splitter(text.toLower(), " "))
    {
        // Increment word count if word
        // has been found.
        // Integers are by default 0.
        words[word]++;
    }

    return words;
}

void main()
{
    string text = "D is a lot of fun";

    auto wc = wordCount(text);
    writeln("Word counts: ", wc);

    // possible iterations:
    // byKey, byValue, byKeyValue
    foreach (word; wc.byValue)
        writeln(word);
}
```
