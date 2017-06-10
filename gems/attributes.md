# Attribute

Funktionen können in D auf verschiedene Arten mit 
Attributen versehen werden. Im folgenden werden
zwei spracheigene und die benutzerdefinierten 
Attribute näher beleuchtet. Darüber hinaus gibt
es `@safe`, `@system` und `@trusted`, die bereits
im ersten Kapitel erwähnt wurden.

### `@property`

Eine als `@property` markierte Funktion sieht für
die äußere Welt wie ein normaler Member aus.

    struct Foo {
        @property bar() { return 10; }
        @property bar(int x) { writeln(x); }
    }
    
    Foo foo;
    writeln(foo.bar); // ruft tatsächlich foo.bar() auf
    foo.bar = 10; // calls foo.bar(10);
    
### `@nogc`

Wenn der D-Compiler auf eine als `@nogc` markierte Funktion
trifft, wird sichergestellt, dass **keine** Speicherallokationen
im Kontext dieser Funktion vorgenommen werden. Eine 
`@nogc`-Funktion darf nur Funktionen aufrufen, die ihrerseits
als `@nogc` markiert sind.

    void foo() @nogc {
      // FEHLER:
        auto a = new A;
    }

### Benutzerdefinierte Attribute (UDAs)

Jede Funktion und jeder Typ in D kann mit benutzerdefinierten 
Attributen (engl: user-defined attributes, UDAs) versehen werden:

    struct Bar { this(int x) {} }
    
    struct Foo {
      @("Hello") {
          @Bar(10) void foo() {
            ...
          }
      }
    }

Jeder Typ, spracheigen oder benutzerdefiniert, kann
Funktionen als Attribut beigefügt werden.
Die Funktion `foo()` in diesem Beispiel besitzt die 
Attribute `"Hello"` vom Typ `string` und `Bar` von Typ 
`Bar` mit dem Wert `10`. Zugang zu den Attributen 
bieten die im Compiler integrierten *Traits* 
(dt.: Merkmale / Eigenschaften) mittels
`__traits(getAttributes, Foo)`, die einen 
[`TypeTuple`](https://dlang.org/phobos/std_typetuple.html)
zurückgeben.

Benutzerdefinierte Attribute können generischen Code
verbessern, indem sie benutzerdefinierten Typen eine
weitere Dimension hinzufügen, die Kompilierzeit-Generatoren
helfen, sich diesen spezifischen Typen anzupassen.

### Weiterführende Quellen

- [Benutzerdefinierte Attribute in _Programming in D_](http://ddili.org/ders/d.en/uda.html)
- [Attribute in D](https://dlang.org/spec/attribute.html)

