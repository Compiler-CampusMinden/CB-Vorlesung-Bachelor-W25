# Blatt 06: Interpreter

## Zusammenfassung

Ziel dieses Aufgabenblattes ist die Erstellung eines
Tree-Walking-Interpreters für eine Lisp-artige Sprache.

## Methodik

Sie haben in [Blatt 04](sheet04.md) eine Grammatik für die Lisp-artige
Sprache definiert und darauf aufbauend einen Lexer und Parser
implementiert, d.h. Sie können für eine Eingabe eines Programms mit
S-Expressions den entsprechenden AST erzeugen.

Korrigieren Sie die Codebasis von Blatt 04, falls notwendig. Ergänzen
Sie sodann einen Tree-Walking-Interpreter und eine REPL für die
Lisp-artige Sprache.

Es ist empfehlenswert, den Interpreter mehrstufig zu realisieren:

1.  Einlesen von Lisp-Code, Parsen der Inhalte und Erzeugen des AST
2.  Ablaufen des AST und dabei
    - Durchführung der semantischen Analyse
    - Auswerten der Ausdrücke (Interpretation)

*Hinweis*: Wir wollen eine *REPL* (“Read-Eval-Print”-Loop) realisieren
und in einer Schleife Lisp-Code von der Konsole einlesen, parsen und
interpretieren und das Ergebnis wieder ausgeben. Sie erzeugen also für
jede Eingabe immer wieder neu einen AST. Wenn wir die semantische
Analyse als eigenen Pass zwischen Parsing und Interpretation realisieren
würden, müssten wir immer sämtlichen bisher verarbeiteten Code mitführen
(allerdings nur die gültigen, d.h. nicht verworfenen Eingaben) und
zusammen mit der aktuellen Eingabe der semantischen Analyse unterwerfen.
Da dies recht frickelig werden kann, realisieren wir auf diesem Blatt
eine vereinfachte semantische Analyse “*on-the-fly*” auf den
Environments im Interpreter.

**Hinweis**: Wenn Sie Blatt 04 nicht oder nicht erfolgreich bearbeitet
haben sollten, können Sie dieses Blatt trotzdem bearbeiten. Erstellen
Sie sich zunächst eine Grammatik für die beschriebene Sprache (oder
nehmen eine der beiden unten angegebenen Grammatiken). Treten Sie dabei
mental einen Schritt zurück und betrachten Sie das ganze Bild: Was wird
*syntaktisch* wirklich an Strukturen benötigt? Danach lassen Sie sich
mit ANTLR einen Lexer und Parser generieren und steigen ganz normal in
die Bearbeitung dieses Blattes ein.

## Sprachdefinition

Siehe [Blatt 04](sheet04.md).

## Aufgaben

### A6.1: AST und Syntactic Sugar (1P)

Lösen Sie die als “*syntactic sugar*” bezeichneten Ausdrücke auf und
transformieren Sie den AST entsprechend: `(+ 1 2 3 4)` soll zu
`(+ (+ (+ 1 2) 3) 4)` umgeformt werden. Analog für die anderen
Operatoren der Sprache (Vergleiche, Arithmetik).

### A6.2: Tree-Walking-Interpreter (4P)

Bauen Sie einen Tree-Walking-Interpreter in Ihr Projekt ein.

Realisieren Sie die eingebauten Funktionen `print` und `str` dabei als
*native* Funktionen.

Realisieren Sie `list`, `nth`, `head` und `tail` sowie `def`, `let`,
`defn`, `do` und die Operatoren und die Kontrollstrukturen geeignet.

Lesen Sie den zu interpretierenden Code aus einer Text-Datei ein.

Testen Sie Ihren Interpreter mit Ihren Beispielprogrammen.

### A6.3: Semantische Analyse (3P)

Führen Sie eine vereinfachte semantische Analyse während der
Interpretation durch:

- Symbole dürfen im selben Scope nicht mehrfach definiert werden
- Referenzierte Symbole müssen sich über den aktuellen Scope (oder
  dessen Eltern-Scopes) auflösen lassen
- Bei Funktionsaufrufen muss das als Funktionsname verwendete Symbol
  tatsächlich eine Funktion sein
- Achten Sie auf die Datentypen der Operanden beim Ausführen der
  Operatoren

### A6.4: Interaktiver Interpreter (1P)

Bauen Sie eine *REPL* ein, d.h. geben Sie nach dem Start des
Interpreters einen Prompt aus und verarbeiten Sie die Eingaben
interaktiv.

Erlauben Sie das Einlesen eines Lisp-Programms aus einer Text-Datei beim
Start des Interpreters. Dabei soll zunächst der eingelesene Code
interpretiert werden und damit die Environments vorbelegt werden. Danach
soll der Interpreter in die REPL gehen, d.h. der in der Konsole
eingegebene Code wird dann im Kontext des vorher eingelesenen Programms
interpretiert.

### A6.5: Auswirkungen der Grammatik auf den Interpreter (1P)

Vergleichen Sie ihre eigene Grammatik mit den folgenden beiden
Grammatiken:

<details>

<summary><strong>Grammatik A</strong></summary>

``` antlr
grammar MiniLispA;


// Parser
program :  expr+ EOF ;

expr    :  NUMBER
        |  STRING
        |  TRUE
        |  FALSE
        |  ID
        |  '(' (ID | OP)* expr* ')'
        ;


// Lexer
TRUE    :  'true' ;
FALSE   :  'false' ;
ID      :  [a-z][a-zA-Z0-9]* ;
NUMBER  :  [0-9]+ ;
OP      :  '+' | '-' | '*' | '/' | '=' | '>' | '<' ;
STRING  :  '"' (~[\n\r"])* '"' ;

COMMENT :  ';;' ~[\n\r]* -> skip ;
WS      :  [ ,\t\n\r]+ -> skip ;
```

</details>

<details>

<summary><strong>Grammatik B</strong></summary>

``` antlr
grammar MiniLispB;


// Parser
program :  expr+ EOF ;

expr    :  literal
        |  symbol
        |  list
        |  def
        |  fn
        |  fcall
        |  let
        ;

literal :  NUMBER
        |  STRING
        |  TRUE
        |  FALSE
        ;

symbol  :  ID ;

list    :  '(' 'list' expr* ')' ;
def     :  '(' 'def' symbol expr ')' ;

fn      :  '(' 'defn' symbol '(' symbol* ')' expr* ')' ;
fcall   :  '(' (ID | OP) expr* ')' ;

let     :  '(' 'let' '(' binding* ')' expr ')' ;
binding :  symbol expr ;


// Lexer
TRUE    :  'true' ;
FALSE   :  'false' ;
ID      :  [a-z][a-zA-Z0-9]* ;
NUMBER  :  [0-9]+ ;
OP      :  '+' | '-' | '*' | '/' | '=' | '>' | '<' ;
STRING  :  '"' (~[\n\r"])* '"' ;

COMMENT :  ';;' ~[\n\r]* -> skip ;
WS      :  [ ,\t\n\r]+ -> skip ;
```

</details>

Welche Auswirkungen hat die Grammatik auf den Interpreter? Machen Sie
ein kleines **Gedankenexperiment**: Überlegen Sie, was Sie alles in
Ihrer Implementierung ändern müssten, wenn Sie die jeweils andere
Grammatik-Variante (A bzw. B) nutzen würden.

------------------------------------------------------------------------

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png" width="10%">

Unless otherwise noted, this work is licensed under CC BY-SA 4.0.

<blockquote><p><sup><sub><strong>Last modified:</strong> 806a400 (homework: add remarks on how to start from scratch (B06), 2025-12-08)<br></sub></sup></p></blockquote>
