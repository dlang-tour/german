# Ranges

Wenn der Compiler auf ein `foreach`-Konstrukt trifft...

```
foreach (element; range)
{
    // Loop body...
}
```

wird es intern etwa wie folgt umgeschrieben:

```
for (auto __rangeCopy = range;
     !__rangeCopy.empty;
     __rangeCopy.popFront())
 {
    auto element = __rangeCopy.front;
    // Loop body...
}
```

Falls das Range-Objekt ein Referenztyp ist (z.B. `class`), wird es 
verbraucht ist für weitere Iterationen nicht mehr verfügbar (es sei
denn, der Schleifenrumpf bricht vor der letzten iteration ab).
Falls das Range-Objekt ein Werttyp ist, wird eine Kopie der Range 
erzeugt und - abhängig von der Definition - wird die ursprüngliche
Range verbraucht.
Die meisten Ranges der Standard-Bibliothek sind Strukturen (`struct`),
sodass eine Iteration normalerweise nicht zerstörend wirkt - allerdings
nicht garantiert. Sollte die Garantie wichtig sein, erfordert dies
**forward**-Ranges.

Jedes Objekt mit folgendem Interface wird **Range** genannt und kann 
somit iteriert werden:

```
    struct Range
    {
        T front() const @property;
        bool empty() const @property;
        void popFront();
    }
 ```
Note that while it is customary for `empty` and `front` to be defined as `const`
functions (implying that calling them won't modify the range), this is not
required.

The functions in `std.range` and `std.algorithm` provide
building blocks that make use of this interface. Ranges allow us
to compose complex algorithms behind an object that
can be iterated with ease. Furthermore, ranges allow us to create **lazy**
objects that only perform a calculation when it's really needed
in an iteration e.g. when the next range's element is accessed.
Special range algorithms will be presented later in the
[D's Gems](gems/range-algorithms) section.

### Exercise

Complete the source code to create the `FibonacciRange` range
that returns numbers of the
[Fibonacci sequence](https://en.wikipedia.org/wiki/Fibonacci_number).
Don't fool yourself into deleting the `assert`ions!

### In-depth

- [`std.algorithm`](http://dlang.org/phobos/std_algorithm.html)
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

struct FibonacciRange
{
    bool empty() const @property
    {
        // So when does the Fibonacci sequence
        // end?!
    }

    void popFront()
    {
    }

    int front() const @property
    {
    }
}

void main()
{
    import std.range : take;
    import std.array : array;

    FibonacciRange fib;

    // `take` creates another range which
    // will return N elements at maximum.
    // This range is _lazy_ and just
    // touches the original range
    // if actually needed
    auto fib10 = take(fib, 10);

    // But we do want to touch all elements and
    // convert the range to array of integers.
    int[] the10Fibs = array(fib10);

    writeln("The 10 first Fibonacci numbers: ",
        the10Fibs);
    assert(the10Fibs ==
        [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]);
}
```
