# Blatt 08: Mini-Projekt C++-Interpreter

## Aufgabe

Sie entwickeln in Ihrem 3er-Team eine kleine, C++‑ähnliche Sprache und
implementieren Lexer, Parser, semantische Analyse, Interpreter und eine
einfache REPL.

Der Fokus liegt auf Variablen und C++-Referenzen, auf Funktionen, auf
Klassen, Einfach‑Vererbung und dem Unterschied zwischen statischem und
dynamischem Dispatch (nicht‑virtual vs. virtual) - ohne Pointer/Heap und
ohne Casts. Die Sprache ist eine Teilmenge von C++ und soll (abgesehen
von den REPL‑Erweiterungen) mit einem C++‑Compiler kompilierbar sein.
Präprozessor‑Zeilen (`#include ...`) dürfen in Dateien vorkommen; Ihr
Interpreter soll sie ignorieren bzw. als Kommentare behandeln.

Sie *können* ANTLR zur Erstellung Ihres Lexers und Parsers einsetzen,
Sie können aber auch gern einen selbst implementierten LL-Parser
einsetzen.

Definieren Sie zunächst eine passende Grammatik und den AST, bevor Sie
Lexer und Parser umsetzen. Achten Sie auf eine angemessene semantische
Analyse, achten Sie dabei auf die in der Vorlesung besprochene
C++-Semantik. Der Interpreter selbst soll ein einfacher Tree-walking
Interpreter wie in der Vorlesung besprochen sein.

### Betrachtete Sprache: Sub-Dialekt von C++

#### Sprachkern

Unterstützen Sie mindestens folgende C++-Konzepte:

- Basisdatentypen: `bool`, `int`, `char`; zusätzlich Klassentypen;
  `void` für Funktionen ohne Rückgabewert
- Variablen und Zuweisungen:
  - Deklaration `T x;` und Initialisierung `T x = expr;`
  - Einfache Zuweisung `=`
- Arrays (eindimensional, minimal):
  - Nur `T x[INT]` für primitive Typen und Klassen
  - Operationen: Deklaration, Index‑Zugriff/‑Zuweisung
- Ausdrücke:
  - Arithmetik: `+`, `-`, `*`, `/`, `%` (nur für `int`)
  - Vergleich: `==`, `!=`, `<`, `<=`, `>`, `>=` (`int`, `char`; `bool`
    nur `==` und `!=`)
  - Logik: `&&` und `||` (mit Short‑Circuit), `!`
  - Klammern `(...)`
  - Funktionsaufrufe `f(args)`
  - Feld-/Methodenzugriff: `obj.f`, `obj.m(args)`
- Kontrollfluss:
  - `if`-`then`-`else`, `while`, Block `{ ... }`
  - `return`
- Funktionen:
  - Definition, Deklaration und Aufruf
  - Überladung (Overloading) nur per exakt passender Signatur (Name +
    Anzahl + exakte Typen inkl. `ref`‑Markierung)
- C++‑Referenzen “light”:
  - Deklaration Variable: `T& x;` bzw. Parameter `T& p`
  - Initialisierung ist obligatorisch und nur mit LValues (Variablen,
    Feldzugriffen, Array‑Index) erlaubt
  - Zuweisung an eine `ref`‑Variable schreibt in das referenzierte Ziel
- Klassen, Einfach-Vererbung, Polymorphie:
  - `class A { public: /* Felder + Methoden */ }` mit Attributen und
    Methoden
  - Konstruktoren; aber keine Destruktoren und keine
    Initialisierungslisten
  - Methoden können als `virtual` deklariert werden
  - Variablen vom Klassentyp sind Werte (feldweise Kopie)
  - `class D : public B { public: /* Felder + Methoden */ }`: Vererbung
    mit nur einer Basisklasse
  - Zuweisung `Base b = d;` (mit `class D : public B { ... }` und
    `D d;`) führt zum Slicing
  - Polymorphe Nutzung erfolgt ausschließlich über Referenzen:
    `B& b = d; b.m();` ruft die überschriebene Methode in `D` auf (wenn
    `D::m()` als `virtual` deklariert ist)
  - `*this` in Methoden hat Typ `C&`; `*this` ist nur als RValue lesbar
    (z.B. `return *this;`); Zuweisung an `*this` gibt es nicht
- Eingebaute Funktionen: `print_bool`, `print_int`, `print_char`
  (Ausgabe eines Werts des jeweiligen Typs)
- Programmorganisation: ein einzelnes Source‑File, keine
  Includes/Präprozessor

*Hinweis*: Polymorphie in dieser Sprache folgt C++‑Prinzipien (Slicing
bei Wertkopie, dynamischer Dispatch über Referenzen), nicht
Java‑Semantik.

#### Semantik‑/Typregeln

- Keine Mehrfachdefinitionen in demselben Scope
  (Variablen/Funktionen/Klassen)
- Sichtbarkeit: Verwendungen müssen im aktuellen Scope sichtbar
  (auflösbar) sein
- Variablen sind nicht aufrufbar; Funktionen sind nicht zuweisbar (keine
  Funktion als LValue)
- Rückgaberegel: In non‑`void`‑Funktionen existiert auf allen Pfaden
  mindestens ein `return`
- Keine impliziten Typkonversionen, keine Casts
- Overload‑Auflösung: exakter Match Name und Arität und identische Typen
  inkl. `ref`‑Markierung; bei Mehrdeutigkeit Fehler
- Funktionen/Methoden: Argumentanzahl muss zur Parameterliste passen
- LValues: benannte Variablen `a`, `obj.f` (Feldzugriff), `a[i]`; alles
  andere sind RValues
- Referenzen
  - Parameter und lokale Referenzvariablen müssen mit LValues
    initialisiert werden
  - Keine Neubindung
  - Keine Referenzen als Felder/Globals oder Arrays
  - Keine `ref`‑Rückgaben (Rückgabe nur als Kopie)
- Klassen/Methoden: Nicht‑virtuelle Methoden binden statisch; virtuelle
  dynamisch
- Fehlerbehandlung:
  - Lexer-, Parser-, Typ-Fehler beenden die Analyse mit klarer Meldung
  - Laufzeitfehler (z.B. Division durch 0, Zugriff auf nicht
    initialisierte Variable) sind sauber zu melden
- REPL (**abweichend** von C++): Neue Funktionen/Klassen dürfen in der
  REPL definiert werden; sie werden in den globalen Scope aufgenommen
  und stehen danach zur Verfügung.

#### Nicht Teil des Umfangs

Weitere mit C++ verbundene Konzepte wie beispielsweise Präprozessor,
Header-Files, Pointer, Templates, Sichtbarkeiten in Klassen, Trennung
Deklaration/Implementierung bei Klassen (Trennung .h und .cpp) brauchen
Sie nicht umsetzen.

Darunter fallen auch (nicht vollständig):

- Pointer/Adressen: `&` (Adressoperator), `*` (Dereferenzierung, außer
  für `this` bzw. `*this`), `->`/`new`/`delete`, Speicherverwaltung
- Casts, Inkrement/Decrement, Compound‑Assignments, Mehrfachvererbung,
  Templates, `static`, `const`, `friend`, Namespaces
- Sichtbarkeiten (außer `public`)
- Mehrdimensionale Arrays
- Kein `break/continue` in Schleifen

#### Hinweis “*most vexing parse*”

In C++ entsteht durch die üblichen Syntax-Regeln das [“*most vexing
parse*”-Problem](https://en.wikipedia.org/wiki/Most_vexing_parse), wo
nach dem Muster `T ID ( ... ) ;` sowohl ein Funktionsprototyp als auch
eine Variablendeklaration mit Konstruktor‑Syntax möglich wäre.

Versuchen Sie, dieses Problem durch geschickte Definitionen in der
Grammatik einzuschränken. Beispielsweise wurde oben bereits
einschränkend definiert, dass eine Variablendeklaration entweder die
Form `T x;` haben soll oder mit Initialisierung `T x = expr;`. Die in
C++ ebenfalls übliche Form `T x(expr);` braucht nicht unterstützt
werden. Für Konstruktoren erlauben Sie am besten nur `T x;` und
`T x = T(arg);`.

### REPL-Modell

#### Initialisierung

Der Interpreter soll beim Start eine optional angegebene Datei mit
C++-Code einlesen und verarbeiten können. Dabei soll die `main()` im
eingelesenen Code bis vor die schließende Klammer bzw. das beendende
`return` ausgeführt werden und der Aktivierungsrahmen von `main()`
“offen” gehalten werden.

#### REPL

Danach soll dem User eine REPL angeboten werden, die einen Prompt in der
Konsole ausgibt und in der weitere C++-Statements eingegeben werden
können, die dann im Kontext des bisher verarbeiteten Codes interpretiert
werden.

Abweichend von C++ können in der REPL neue Klassen und Funktionen
definiert und genutzt werden. Diese landen im aktuellen Scope.

Nach der Interpretation soll es jeweils eine Ausgabe des letzten
Ergebnisses (oder Fehlers) auf der Konsole geben, und dann soll ein
neuer Prompt ausgegeben und auf die nächste User-Eingabe gewartet
werden.

Implementieren Sie eine spezielle Eingabe, mit der die laufende
`main()`-Funktion und damit die REPL beendet werden kann.

*Hinweis*: Achten Sie darauf, dass Ihr Interpreter die Eingabe nicht zu
früh beendet und zu früh mit der Interpretation beginnt! Oft ist ein
Zeilenumbruch das korrekte Signal zum Start der Verarbeitung. Es gibt
aber auch Situationen (z.B. Funktionsdefinition o.ä.), wo ein
Zeilenumbruch noch nicht eine vollständige Eingabe anzeigt. Hier soll
nach einem Zeilenumbruch ein Hilfsprompt ausgegeben werden, damit der
User weiss, dass die aktuelle Eingabe noch läuft.

### Tests

Im
[Starter-Projekt](https://github.com/Compiler-CampusMinden/student-support-code-template/tree/master/src/main/resources/cpp)
finden Sie einige Positiv- und Negativ-Tests. Für die Positivtests ist
die erwartete Antwort des Interpreters angegeben, bei der Interpretation
der Negativtests sollte es eine entsprechende Fehlermeldung (Lexer,
Parser, semantische Analyse, Interpreter) geben.

Betrachten Sie die Testfälle als ausführbare Ergänzung der obigen
Spezifikation.

Entwickeln Sie weitere aussagekräftige Tests, die die Vorgaben ergänzen.

## Projektvorstellung: Walk-Through statt Präsentation

Stellen Sie Ihr Projekt am Semesterende vor (Termine siehe Fahrplan und
Ankündigung).

Jedes Team hat dafür **15 Minuten** Zeit.

Gehen Sie dabei am Code durch Ihr Projekt und diskutieren Sie relevante
Teile, mindestens aber:

- Spezifikation: kurze Sprachbeschreibung mit allen getroffenen
  Designentscheidungen
- Implementierung: ANTLR‑Grammatik, AST, Scopes/Symboltabellen,
  semantische Checks, Interpreter mit dynamischem Dispatch, REPL
- Kurze Anleitung: Wie bauen/ausführen, wie REPL nutzen.

Demonstrieren Sie die Funktionsfähigkeit mit Ihren C++-Codebeispielen.

**Sie sollen keine Folien erstellen. Die Präsentation soll live in der
IDE erfolgen.**

## Abgabeformat

Geben Sie wie bei den anderen Blättern auch pro Person ein individuelles
Post Mortem im ILIAS ab. Geben Sie dabei im Post Mortem unbedingt die
URL zu Ihrem Team-Repo an. Denken Sie daran, dass dieses Repo öffentlich
einsehbar sein muss.

------------------------------------------------------------------------

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png" width="10%">

Unless otherwise noted, this work is licensed under CC BY-SA 4.0.

<blockquote><p><sup><sub><strong>Last modified:</strong> d037d43 (homework: clarify arrays (B08), 2025-11-29)<br></sub></sup></p></blockquote>
