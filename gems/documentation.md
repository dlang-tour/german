# Dokumentation

D möchte wichtige Bereiche moderner Softwareentwicklung 
in die Sprache integrieren. Neben *Contract Programming* 
und *Unittests* bietet D nativ die Erzeugung einer 
[Quellcode-Dokumentation](https://dlang.org/phobos/std_variant.html) an.

Für die Dokumentation von Typen und Funktionen wird
ein Standard-Schema angewendet. Der Befehl
`dmd -D` erstellt auf bequeme Art eine HTML-Dokumentation
anhand der in der Kommandozeile übergebenen Quelldateien.
Tatsächlich wird die komplette [Phobos Bibliotheks-Dokumentation](https://dlang.org/phobos)
mit *DDoc* generiert.

Die fogenden Kommentarstile werden von DDoc
für die Einbindung in die Quellcode-Dokumentation
berücksichtigt:

* `/// Drei Slashes vor Typ bzw. Funktion`
* `/++ Mehrzeiliger Kommentar mit zwei +  +/`
* `/** Mehrzeiliger Kommentar mit zwei *  */`

Der Beispielquellcode zeigt einige
standartisierte Dokumentationsabschnitte.

### Weiterführende Quellen

- [DDoc Design](https://dlang.org/spec/ddoc.html)
- [Phobos Bibliotheks-Dokumentation](https://dlang.org/phobos)

## {SourceCode:incomplete}

```d
/**
  Berechnet die Quadratwurzel einer Zahl.

  Hier könnte ein längerer Absatz mit
  einer detailierten Beschreibung der
  großartigen und für den Fortbestand
  der Menschheit entscheidenden 
  Implikationen, die die Berechnung der
  Quadratwurze zweifelsohne besitzt, 
  stehen.

  Beispiel:
  -------------------
  double sq = sqrt(4);
  -------------------
  Params:
    number = die Zahl, die quadriert 
             werden soll
  License: use freely for any purpose
  Throws:  es wird nicht geworfen
  Returns: die Quadratwurzel von number
*/
T sqrt(T)(T number) {
}
```
