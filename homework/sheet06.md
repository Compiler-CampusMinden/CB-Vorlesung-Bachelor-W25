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

Es ist empfehlenswert, den Interpreter dreistufig zu realisieren:

1.  Einlesen aus einer Datei mit Lisp-Code und Parsen der Inhalte
2.  Aufbauen der Symboltabelle und Durchführung der semantischen Analyse
3.  Ablaufen des Parse-Tree/AST und Auswerten der Ausdrücke
    (Interpretation)

## Sprachdefinition

Siehe [Blatt 04](sheet04.md).

## Aufgaben

### A6.1: Semantische Analyse (2P)

1.  Erstellen Sie zunächst einige Programme in der Zielsprache. Diese
    sollten von einfachsten Ausdrücken bis hin zu komplexeren Programmen
    reichen. Definieren Sie beispielsweise eine Funktion, die rekursiv
    die Länge einer Liste berechnet.

    Definieren Sie neben gültigen Programmen auch solche, die in der
    semantischen Analyse zurückgewiesen werden sollten. Welche
    Fehlerkategorien könnte es hier geben?

2.  Führen Sie die semantische Analyse durch: Sind alle Symbole bekannt,
    passen die Scopes?

### A6.2: Tree-Walking-Interpreter (3P)

Bauen Sie einen Tree-Walking-Interpreter in Ihr Projekt ein.

Realisieren Sie die eingebauten Funktionen `print` und `str` dabei als
*native* Funktionen. Realisieren Sie `list`, `nth`, `head` und `tail`
sowie `def`, `let`, `defn`, `do` und die Operatoren und die
Kontrollstrukturen geeignet.

Lösen Sie die als “*syntactic sugar*” bezeichneten Ausdrücke auf und
transformieren Sie den AST entsprechend: `(+ 1 2 3 4)` soll zu
`(+ (+ (+ 1 2) 3) 4)` umgeformt werden. Analog für die anderen
Operatoren der Sprache (Vergleiche, Arithmetik).

Achten Sie auf die Datentypen. Die Typen von Variablen etc. sind erst
zur Laufzeit bekannt und müssen dann passen.

Lesen Sie den zu interpretierenden Code aus einer Datei ein.

Testen Sie Ihren Interpreter mit Ihren Beispielprogrammen.

### A6.3: Interaktiver Interpreter (3P)

Bauen Sie eine *REPL* ein, d.h. geben Sie nach dem Start des
Interpreters einen Prompt aus und verarbeiten Sie die Eingaben
interaktiv. Wie müssen Sie hier mit der Symboltabelle umgehen?

### A6.4: Auswirkungen der Grammatik auf den Interpreter (2P)

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

<blockquote><p><sup><sub><strong>Last modified:</strong> 04c7c82 (homework: embed lisp grammars (B06), 2025-11-19)<br></sub></sup></p></blockquote>
