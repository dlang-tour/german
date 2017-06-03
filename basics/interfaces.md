# Interfaces

D erlaubt die Definition von Interfaces (`interface`),
die technisch Klassentypen (`class`) entsprechen, aber deren
Member von jeder erbenden Klasse impelementiert werden müssen.

    interface Animal {
        void makeNoise();
    }

Die `makeNoise`-Memberfunktion muss von `Dog` impelementiert 
werden, weil es von dem  `Animal`-Interface erbt.
Letztlich verhält sich `makeNoise` wie eine `abstract`-Memberfunktion
einer Basisklasse.

    class Dog: Animal {
        override makeNoise() {
            ...
        }
    }

    auto dog = new Dog;
    Animal animal = dog; // implicit cast to interface
    dog.makeNoise();

Die Anzahl der Interfaces einer Klasse ist nicht limitiert,
es kann allerdings nur von einer Basisklasse geerbt werden.

### NVI (Nonvirtual Interface) Muster

Das [NVI-Muster](https://en.wikipedia.org/wiki/Non-virtual_interface_pattern)
erlaut _non virtual_-Methoden in einem Interface, z.B. um den Aufruf
überschriebener Methoden zu steuern.

D ermöglicht das NVI-Muster, indem es die Definition von `final`-Methoden in 
einem Interface erlaubt, welche nicht überschrieben werden können. Dies erzwingt 
ein bestimmtes Verhalten, welche durch Überschreiben anderer Interface-
Memberfunktionen angepasst werden kann.

    interface Animal {
        void makeNoise();
        final doubleNoise() // NVI pattern
        {
            makeNoise();
            makeNoise();
        }
    }

### Weiterführende Quellen

- [Interfaces in _Programming in D_](http://ddili.org/ders/d.en/interface.html)
- [Interfaces in D](https://dlang.org/spec/interface.html)

## {SourceCode}

```d
import std.stdio : writeln;

interface Animal {
    /*
    Virtuelle Function
    die überschrieben werden muss!
    */
    void makeNoise();

    /*
    NVI-Muster. Nutzt makeNoise intern
    zur Anpassung des Verhaltens erbender
    Klassen.
    
    Params: 
        n =  Anzahl der Wiederholungen
    */
    final void multipleNoise(int n) {
        for(int i = 0; i < n; ++i) {
            makeNoise();
        }
    }
}

class Dog: Animal {
    override void makeNoise() {
        writeln("Woof!");
    }
}

class Cat: Animal {
    override void makeNoise() {
        writeln("Meeoauw!");
    }
}

void main() {
    Animal dog = new Dog;
    Animal cat = new Cat;
    Animal[] animals = [dog, cat];
    foreach(animal; animals) {
        animal.multipleNoise(5);
    }
}
```
