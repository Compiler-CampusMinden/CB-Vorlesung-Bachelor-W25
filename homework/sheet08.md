# Blatt 08: Mini-Projekt C++-Interpreter

## Aufgabe

Sie entwickeln in Ihrem 3er-Team eine kleine, C++‑ähnliche Sprache und
implementieren Lexer, Parser, semantische Analyse, Interpreter und eine
einfache REPL.

Der Fokus liegt auf Variablen und C++-Referenzen, auf Funktionen, auf
Klassen, Einfach‑Vererbung und dem Unterschied zwischen statischem und
dynamischem Dispatch (nicht‑virtual vs. virtual) - ohne Pointer/Heap und
ohne Casts. Die Sprache ist eine echte Teilmenge von C++ und soll
(abgesehen von den REPL‑Erweiterungen) mit einem C++‑Compiler
kompilierbar sein. Präprozessor‑Zeilen (`#include ...`) dürfen in
Dateien vorkommen; Ihr Interpreter soll sie ignorieren bzw. wie
Kommentare behandeln.

Sie *können* ANTLR zur Erstellung Ihres Lexers und Parsers einsetzen,
Sie können aber auch gern einen selbst implementierten LL-Parser
einsetzen.

Definieren Sie zunächst eine passende Grammatik und den AST, bevor Sie
Lexer und Parser umsetzen. Achten Sie auf eine angemessene semantische
Analyse, achten Sie dabei auf die in der Vorlesung besprochene sowie auf
diesem Blatt skizzierte und über die Tests gegebene C++-Semantik. Der
Interpreter selbst soll ein einfacher Tree-walking Interpreter wie in
der Vorlesung besprochen sein.

**Wichtig**: Wir haben die meisten benötigten Techniken besprochen und
geübt. An einigen wenigen Stellen müssen Sie selbst kreativ werden,
etwas beim Handling von überladenen Funktionen bzw. Vererbung und
überschriebenen Methoden. Recherchieren Sie eigenständig zu den Themen.

### Betrachtete Sprache: Sub-Dialekt von C++

#### Sprachkern

Unterstützen Sie mindestens folgende C++-Konzepte:

- Typen:
  - `bool` (Werte `true`, `false`), `int` (Ziffernfolgen ohne
    Dezimalpunkt, etwa `42`), `char` (Character in einfachen
    Anführungsstrichen, etwa `'a'`), `string` (Zeichenkette in doppelten
    Anführungsstrichen, etwa `"foo"`)
  - `void` für Funktionen ohne Rückgabewert
  - Zusätzlich Klassentypen (s.u.)
  - Escapes in Literalen mit `\`, beispielsweise `'\0'` als einzelnes
    `char`
- Variablen und Zuweisungen:
  - Deklaration `T x;` und Initialisierung `T x = expr;`
  - Einfache Zuweisung `=`
  - Nur lokale Variablen (keine globalen Variablen)
- Ausdrücke:
  - Arithmetik (nur `int`): `+`, `-`, `*`, `/`, `%` (binäre Ausdrücke);
    `+`, `-` (unäre Ausdrücke)
  - Zuweisung: `=`
  - Vergleich: `==`, `!=`, `<`, `<=`, `>`, `>=` (`int` und `char`;
    `bool` und `string` nur `==` und `!=`)
  - Logik: `&&` und `||` (beide mit *Short‑Circuit*) und `!` (nur
    `bool`)
  - Klammern zur Gruppierung von Ausdrücken `(...)`
  - Funktionsaufrufe `f(args)`
  - Feld-/Methodenzugriff: `obj.f`, `obj.m(args)`
- Kontrollfluss:
  - `if`-`then`-`else` mit optionalem `else`-Teil, `while`, Block
    `{ ... }`
  - `return`
- Funktionen/Methoden:
  - Definition und Aufruf
  - Überladung (Overloading) nur per exakt passender Signatur (Name +
    Anzahl + exakte Typen inkl. `&`‑Markierung)
- C++‑Referenzen “light”:
  - Deklaration Variable: `T& x = expr;` bzw. Deklaration Parameter:
    `T& p`
  - Initialisierung für Referenz-Variablen obligatorisch
- Klassen, Einfach-Vererbung (genau eine optionale Basisklasse),
  Polymorphie:
  - `class A { public: /* Felder + Methoden */ }` mit Feldern und
    Methoden (alles “public” sichtbar)
  - Felder können vom Typ her Basistypen oder Klassen sein
  - Parameterloser Konstruktor und weitere Konstruktoren (jeweils ohne
    Initialisierungslisten), Verwendung nur als `T x;` (ruft `T()` auf)
    oder `T x = T(args);` (kein direkter Aufruf `T x(args);`!)
  - Methoden können als `virtual` deklariert werden
  - `class D : public B { public: /* Felder + Methoden */ }`: Vererbung
    mit genau einer Basisklasse, keine Zyklen
- Eingebaute Funktionen (Runtime/Standardbibliothek): `print_bool`,
  `print_int`, `print_char`, `print_string` (Ausgabe eines Werts des
  jeweiligen Typs)
- Kommentare:
  - Zeilenweise Kommentare: `//` bis zum Zeilenende
  - Block-Kommentare: `/*` bis zum `*/` (kann über mehrere Zeilen gehen)
  - Präprozessor-Direktiven: `#` bis zum Zeilenende (soll als Kommentar
    behandelt werden)
- Programmorganisation:
  - Ein einzelnes Source‑File mit einer optionalen `main()`-Funktion
  - Präprozessor-Anweisungen sollen Sie wie Kommentare ignorieren
    (behandeln Sie `#include` wie einen zeilenweisen Kommentar, d.h. ab
    einem `#` wird der Input bis zum nächsten Zeilenumbruch ignoriert)
  - Main-Funktion: zulässig sind die Formen `int main()` und
    `void main()`

*Hinweis*: Polymorphie in dieser Sprache folgt C++‑Prinzipien (Slicing
bei Wertkopie, dynamischer Dispatch über Referenzen), nicht
Java‑Semantik.

#### Reservierte Schlüsselwörter

`int`, `bool`, `char`, `string`, `true`, `false`, `if`, `else`, `while`,
`return`, `class`, `void`, `public`, `virtual`

#### Semantik‑/Typregeln

- Keine Mehrfachdefinitionen in demselben Scope
  (Variablen/Funktionen/Klassen)
- Sichtbarkeit:
  - Verwendungen müssen im aktuellen Scope sichtbar (auflösbar) sein
  - Variablen: “define-before-use”, d.h. Variablen müssen bereits im
    ersten Pass sichtbar sein
  - Funktionen, Methoden, Klassen: “define-after-use” - mehrere Passes
    notwendig (nur beim Start des Interpreters)
- LValues: benannte Variablen `a` bzw. Feldzugriffe `obj.f`; alles
  andere sind RValues
- Variablen sind nicht aufrufbar; Funktionen sind nicht zuweisbar (keine
  Funktion als LValue)
- Keine impliziten Typkonversionen außer im booleschen Kontext (in
  `if`/`while`‑Bedingungen werden `int`/`char`/`string` wie in C++
  implizit in `bool` konvertiert, d.h. `0` bzw. der leere String werden
  als `false` und alles andere als `true` behandelt)
- Ausdrücke:
  - Bei binären Operatoren müssen beide Seiten den selben Typ haben
  - Zuweisung ist ein Ausdruck; die linke Seite des Zuweisungsoperators
    muss ein LValue sein
  - Das Ergebnis der Zuweisung ist Wert/Typ der rechten Seite
  - Operatorpräzedenz und Assoziativität: wie in C++ üblich
    (Reihenfolge: unär, multiplicative, additive, relational, equality,
    &&, \|\|, assignment; assignment rechtsassoziativ)
- Funktionen/Methoden:
  - Argumentanzahl muss zur Parameterliste passen
  - Overload‑Auflösung: exakter Match Name und Arität und identische
    Typen inkl. `&`‑Markierung; bei Mehrdeutigkeit Fehler
- Referenzen
  - Referenz‑Parameter und ‑Variablen können nur mit LValues
    initialisiert werden
  - Keine Neubindung: Zuweisung an eine Referenz‑Variable schreibt in
    das referenzierte Ziel
  - Keine Referenzen als Felder/globale Variablen
  - Keine `&`‑Rückgaben (Rückgabe nur als Kopie)
- Klassen/Methoden:
  - Parameterloser Default‑Konstruktor wird synthetisiert, wenn keiner
    definiert wurde
  - Initialisierung von Feldern über impliziten (generierten)
    parameterlosen Default-Konstruktor: `bool`: `false`, `int`: `0`,
    `char`: `'\0'`, `string`: `""`, Felder von Klassentyp werden per
    Default‑Konstruktor initialisiert
  - Bei Vererbung wird von Konstruktoren bei abgeleiteten Klassen
    jeweils der parameterlose Basiskonstruktor implizit vor dem Body
    aufgerufen
  - Kein `this`. Unqualifizierte Namen in Methoden binden an lokale
    Variablen/Parameter vor eigene Members vor geerbte Members vor
    globale Scope.
  - Variablen vom Klassentyp sind Werte (feldweise Kopie bei
    Rückgabe/Zuweisung)
  - Bei abgeleiteten Klassen wird beim Konstruktoraufruf implizit
    zunächst der parameterlose Default-Konstruktor der Basisklasse
    aufgerufen
  - Abgeleitete Klassen erben alle Felder und Methoden der Basisklasse
  - Zuweisung `Base b; b = d;` (mit `class D : public B { ... }` und
    `D d;`) führt zum Slicing (einzige Ausnahme der Regel für selbe
    Typen auf beiden Seiten der Zuweisung)
  - Polymorphe Nutzung erfolgt ausschließlich über Referenzen:
    `B& b = d; b.m();` ruft die überschriebene Methode in `D` auf (wenn
    `B::m()` zusätzlich als `virtual` deklariert ist)
  - Virtuelle Bindung wie in C++: Ist die Methode im statischen Typ
    `virtual`, erfolgt dynamischer Dispatch bei Aufruf über Referenzen;
    Overrides in abgeleiteten Klassen sind auch ohne erneutes `virtual`
    virtuell
- Fehlerbehandlung:
  - Lexer-, Parser-, Typ-Fehler beenden die Analyse mit klarer Meldung
  - Laufzeitfehler (z.B. Division durch 0) sind sauber zu melden
- REPL (**abweichend** von C++): Neue Variablen/Funktionen/Klassen
  dürfen in der REPL definiert werden; sie werden in den Sitzungs-Scope
  aufgenommen und stehen danach zur Verfügung. Für Funktionen und
  Klassen ist in der REPL kein “define-after-use” mehr möglich.

#### Nicht Teil des Umfangs

Weitere, bisher nicht benannte mit C++ verbundene Konzepte wie
beispielsweise Präprozessor, Header-Files, Pointer, Arrays, Templates,
Sichtbarkeiten in Klassen, Trennung Deklaration/Implementierung bei
Klassen (Trennung .h und .cpp) brauchen Sie nicht umsetzen.

Darunter fallen auch (Aufzählung nicht vollständig):

- Pointer/Adressen: `&` (Adressoperator), `*` (Dereferenzierung),
  `->`/`new`/`delete`, Speicherverwaltung
- Casts, Inkrement/Decrement, Compound‑Assignments, Mehrfachvererbung,
  Templates, `static`, `const`, `friend`, Namespaces
- `sizeof`, `typedef`, `using`
- Unterscheidung signed und unsigned Datentypen
- Sichtbarkeiten (außer `public`)
- `this`, dadurch auch kein Shadowing von Member-Namen durch lokale
  Variablen bzw. Parameter
- Arrays (eindimensional, mehrdimensional)
- Kein `break/continue` in Schleifen
- Globale Variablen
- Referenzen als Rückgabetyp, Referenzen als Feld in Klassen
- Reine Funktionsdeklarationen (auch mit namenlosen Parametern)
- Initialisierungslisten, delegierende Konstruktoren, Destruktoren
- Shadowing: In Methoden sollen Parameter und lokale Variablen nicht den
  selben Namen haben wie die Felder der Klasse

#### Hinweis “*most vexing parse*”

In C++ entsteht durch die üblichen Syntax-Regeln das [“*most vexing
parse*”-Problem](https://en.wikipedia.org/wiki/Most_vexing_parse), wo
nach dem Muster `T ID ( ... ) ;` sowohl ein Funktionsprototyp als auch
eine Variablendeklaration mit Konstruktor‑Syntax möglich wäre.

Da mit den oben definierten Regeln Funktionsprototypen in diesem Projekt
nicht erlaubt sind und für Funktionsdefinitionen immer ein
anschließender Block auftaucht (`T ID ( ... ) { ... }`), Variablen für
Basistypen entweder über die Form `T x;` oder mit Initialisierung als
`T x = expr;` deklariert werden, und Variablen für Klassentypen in der
Form `T x;` (Aufruf parameterloser Konstruktor) bzw. der Form
`T x = T(arg);` (Erzeugung eines temporären Objekts mit dem passenden
Konstruktor und elementweises Kopieren) deklariert werden, tritt das
klassische *most vexing parse* in diesem Projekt nicht auf.

### REPL-Modell

#### Initialisierung

Der Interpreter soll beim Start eine optional angegebene Datei mit
C++-Code einlesen und verarbeiten können. Für die semantische Prüfung
des eingelesenen Codes soll eine Mehrpass-Prüfung realisiert werden
(Funktionen und Klassen: “define-after-use” erlaubt). Eine optional
enthaltene `main()`-Funktion soll in einem “Sitzungs-Scope” ausgeführt
werden, welcher vom globalen Scope “abzweigt”. Der Sitzungs-Scope wird
offen gehalten.

#### REPL

Danach soll dem User eine REPL angeboten werden, die einen Prompt in der
Konsole ausgibt und in der weitere C++-Statements (inklusive Blöcke und
Expression-Statements) eingegeben werden können, die dann im
Sitzungs-Scope interpretiert werden. Dadurch wird in den neu
eingegebenen Statements der Zugriff auf Variablen in der
`main()`-Funktion und auf frühere Funktions- und Klassendefinitionen
möglich.

In der REPL können zusätzlich auch neue Klassen und Funktionen und
Variablen definiert und genutzt werden. Klassen und Funktionen erweitern
dabei den globalen Scope und haben keinen Zugriff auf Sitzungsvariablen;
neue Variablen landen dagegen im Sitzungs-Scope. Im Unterschied zur
Initialisierungsphase gilt in der REPL stets “define-before-use”, d.h.
alle benutzten Namen müssen vorher bereits definiert worden sein.

In der REPL wird nur das Ergebnis von Expression‑Statements automatisch
ausgegeben; alle anderen Statements erzeugen keine Ausgabe. Für
explizite Ausgaben nutzen Sie `print_*()`. Fehler (Lexer, Parser,
semantische Analyse, Interpreter/Laufzeit) sollen ebenfalls auf der
Konsole ausgegeben werden.

Implementieren Sie eine spezielle Eingabe, mit der die REPL beendet
werden kann.

*Hinweis*: Achten Sie darauf, dass Ihr Interpreter die Eingabe nicht zu
früh beendet und zu früh mit der Interpretation beginnt! Oft ist ein
Zeilenumbruch das korrekte Signal zum Start der Verarbeitung. Es gibt
aber auch Situationen (etwa Funktions- oder Klassendefinitionen o.ä.),
wo ein Zeilenumbruch noch nicht eine vollständige Eingabe anzeigt. Hier
soll nach einem Zeilenumbruch ein Hilfsprompt ausgegeben werden, damit
der User weiss, dass die aktuelle Eingabe noch läuft.

### Tests

Im
[Starter-Projekt](https://github.com/Compiler-CampusMinden/student-support-code-template/tree/master/src/main/resources/cpp)
finden Sie einige Positiv- und Negativ-Tests. Für die Positivtests ist
die erwartete Antwort des Interpreters angegeben, bei der Interpretation
der Negativtests sollte es eine entsprechende Fehlermeldung (Lexer,
Parser, semantische Analyse, Interpreter/Laufzeit) geben.

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

<blockquote><p><sup><sub><strong>Last modified:</strong> 08cc399 (homework: you need to read (B08), 2025-12-07)<br></sub></sup></p></blockquote>
