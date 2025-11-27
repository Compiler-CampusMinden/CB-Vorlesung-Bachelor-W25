# Blatt 05: Semantische Analyse

## Zusammenfassung

Ziel dieses Aufgabenblattes ist die Erstellung einer Symboltabelle und
einer einfachen Typ-Prüfung für eine fiktive statisch typisierte Sprache
mit Expressions, Kontrollstrukturen und Funktionen.

## Methodik

Analysieren Sie die nachfolgend in der Aufgabe gegebene Grammatik.
Welche Sprache akzeptiert/generiert diese Grammatik? Erstellen Sie
verschiedene syntaktisch zulässige und unzulässige Programme. Erstellen
Sie weitere Programme, in denen Sie bewusst die semantischen Regeln
(siehe Aufgabe) verletzen.

Erstellen Sie mit dieser Grammatik und ANTLR einen Lexer und Parser.
Konvertieren Sie Ihren Parse-Tree in einen AST, Sie können dazu die
vorgegebenen Datenstrukturen und Konvertierungsregeln nutzen.

Die semantische Analyse soll zweiphasig realisiert werden:

1.  Aufbauen der Symboltabelle und Prüfen von z.B.
    Deklaration/Definition (Variablen, Funktionen), bei Referenzierung
    von Variablen auf vorherige Deklaration/Definition im
    Sichtbarkeitsbereich
2.  Prüfen von Funktionsaufrufen auf vorhandene/sichtbare
    Funktionsdefinitionen

## Sprachdefinition

``` antlr
grammar MiniC;

// Parser
program : stmt+ EOF ;

stmt
  : vardecl
  | assign
  | fndecl
  | expr ';'
  | block
  | while
  | cond
  | return
  ;

vardecl : type ID ('=' expr)? ';' ;
assign  : ID '=' expr ';' ;

fndecl  : type ID '(' params? ')' block ;
params  : type ID (',' type ID)* ;
return  : 'return' expr ';' ;

fncall  : ID '(' args? ')' ;
args    : expr (',' expr)* ;

block   : '{' stmt* '}' ;
while   : 'while' '(' expr ')' block ;
cond    : 'if' '(' expr ')' block ('else' block)? ;

expr
  : fncall
  | expr ('*' | '/') expr
  | expr ('+' | '-') expr
  | expr ('>' | '<') expr
  | expr ('==' | '!=') expr
  | ID
  | NUMBER
  | STRING
  | 'T'
  | 'F'
  | '(' expr ')'
  ;

type : 'int' | 'string' | 'bool' ;


// Lexer
ID      : [a-zA-Z] [a-zA-Z0-9]* ;
NUMBER  : [0-9]+ ;
STRING  : '"' (~[\n\r"])* '"' ;

COMMENT : '#' ~[\n\r]* -> skip ;
WS      : [ \t\n\r]+   -> skip ;
```

## Semantik

Statements ergeben bei der Ausführung keinen Wert. Expressions haben
immer einen Typ und ergeben bei der Evaluierung einen Wert.

Jeder Code-Block (beginnend und endend mit geschweiften Klammern) bildet
einen eigenen Scope - alle Deklarationen/Definition in diesem Scope sind
im äußeren Scope nicht sichtbar. In einem Scope kann auf die Symbole
(Namen) des/der umgebenden Scopes zugegriffen werden. Symbole in einem
Scope können gleichnamige Symbole im äußeren Scope verdecken. Symbole
dürfen in einem Scope nicht mehrfach definiert (`*decl`-Statements)
werden.

Bei allen Operatoren müssen die Operanden jeweils beide den selben Typ
haben. Bei Vergleichsoperatoren sind `string` und `int` zulässig, das
Ergebnis ist immer vom Typ `bool`. Bei arithmetischen Operatoren sind
für `+` sowohl `string` als auch `int` zulässig, für die anderen
Operatoren (`-`, `*`, `/`) nur `int`, das Ergebnis ist vom Typ der
Operanden.

Variablen müssen vor ihrer Benutzung (Zugriff, Zuweisung) definiert und
im aktuellen Scope sichtbar sein. Die Initialisierung kann zusammen mit
der Definition erfolgen. Variablen können in einem Scope nicht mehrfach
definiert werden. Variablen können nicht als Funktion benutzt werden
(aufgerufen werden).

Eine Funktionsdefinition macht dem Compiler die Implementierung einer
Funktion bekannt. Funktionen können in einem Scope nicht mehrfach
definiert werden. Eine aufgerufene Funktion muss im aktuellen Scope
sichtbar sein, der Funktionsaufruf muss zur Definition passen. Die
aufgerufene Funktion muss (im Gegensatz zum Zugriff auf Variablen) nicht
vor dem ersten Aufruf definiert sein. Sie kann also später im selben
Scope definiert werden. Einer Funktion kann nichts zugewiesen werden.

## Aufgaben

### A5.1: Grammatik und Sprache (1P)

Analysieren Sie die obige ANTLR-Grammatik. Welche Sprache wird durch
diese Grammatik akzeptiert/generiert?

Erstellen Sie einige unterschiedlich komplexe Beispiele in der Sprache.
Erstellen Sie dabei auch einige syntaktisch unkorrekte Beispiele.
Erstellen Sie zusätzlich semantisch unkorrekte Beispiele.

Generieren Sie mit ANTLR einen Lexer und Parser für die obige
ANTLR-Grammatik.

### A5.1: AST (2P)

Definieren Sie einen AST für die Zielsprache. Sie können folgende
Definition nutzen:

    Stmt
      = VarDecl(type: PrimType, name: string, initializer?: Expr)
      | Assign(name: string, value: Expr)
      | FnDecl(returnType: PrimType, name: string, params: Param*, body: Block)
      | ReturnStmt(value: Expr)
      | ExprStmt(expr: Expr)
      | Block(statements: Stmt*)
      | WhileStmt(condition: Expr, body: Block)
      | IfStmt(condition: Expr, thenBranch: Block, elseBranch: Block)

    Expr
      = IntLiteral(value: int)
      | StringLiteral(value: string)
      | BoolLiteral(value: bool)
      | Variable(name: string)
      | Binary(left: Expr, op: Operator, right: Expr)
      | Call(name: string, args: Expr*)

    Param = Param(type: PrimType, name: string)

    PrimType = INT | STRING | BOOL

    Operator = EQ | NEQ | PLUS | MINUS | MUL | DIV | LT | GT

Programmieren Sie eine Traversierung des Parse-Trees, die den AST
erzeugt. Sie können dafür folgende Transformationen nutzen:

<details>

<summary><strong>Transformationen</strong></summary>

    program: stmt+ EOF
      → Liste[Stmt] in Quellreihenfolge (kein eigener Program-Knoten)

    stmt
      • vardecl: type ID ('=' expr)? ';'
        → VarDecl(PrimType(type), ID.text, initializer?)
           initializer? = Some(exprAST) falls vorhanden, sonst None

      • assign: ID '=' expr ';'
        → Assign(ID.text, exprAST)

      • fndecl: type ID '(' params? ')' block
        → FnDecl(PrimType(type), ID.text, paramsList, blockAST)

      • expr ';'
        → ExprStmt(exprAST)

      • block: '{' stmt* '}'
        → Block([AST(stmt1), AST(stmt2), ...])

      • while: 'while' '(' expr ')' block
        → WhileStmt(exprAST, blockAST)

      • cond: 'if' '(' expr ')' block ('else' block)?
        → IfStmt(exprAST, thenBlockAST, elseBlockOrEmpty)
           elseBlockOrEmpty = elseBlockAST, falls vorhanden;
                              sonst Block([])

      • return: 'return' expr ';'
        → ReturnStmt(exprAST)

    expr
      • fncall: ID '(' args? ')'
        → Call(ID.text, argsList)

      • expr ('*' | '/') expr
        → Binary(leftAST, op=MUL|DIV, rightAST)

      • expr ('+' | '-') expr
        → Binary(leftAST, op=PLUS|MINUS, rightAST)

      • expr ('>' | '<') expr
        → Binary(leftAST, op=GT|LT, rightAST)

      • expr ('==' | '!=') expr
        → Binary(leftAST, op=EQ|NEQ, rightAST)

      • ID
        → Variable(ID.text)

      • NUMBER
        → IntLiteral(parseInt(NUMBER.text))

      • STRING
        → StringLiteral(unquote(STRING.text))

      • 'T' → BoolLiteral(true)
      • 'F' → BoolLiteral(false)

      • '(' expr ')'
        → exprAST (Klammern sind rein syntaktisch)

    type
      • 'int'    → PrimType.INT
      • 'string' → PrimType.STRING
      • 'bool'   → PrimType.BOOL

    params: type ID (',' type ID)*
      → paramsList = [ Param(PrimType(type_i), ID_i.text) ] in Quellreihenfolge

    args: expr (',' expr)*
      → argsList = [ exprAST_i ] in Quellreihenfolge

    Operator-Zuordnung
      '*' → MUL, '/' → DIV, '+' → PLUS, '-' → MINUS, '>' → GT, '<' → LT, '==' → EQ, '!=' → NEQ

    Allgemein
      • Tokens wie Klammern, Kommas, Semikolons, Schlüsselwörter erscheinen nicht im AST.
      • Präzedenz/Assoziativität kommt aus der Parse-Struktur; der AST ist normalisiert zu Binary(left, op, right).

</details>

Testen Sie Ihre Transformation mit Ihren in der vorigen Aufgabe
erstellten Beispielprogrammen.

### A5.2: Aufbau der Symboltabelle (5P)

Bauen Sie für den AST eine Symboltabelle auf. Achten Sie darauf, dass
Sie für den zweiten Lauf eine Querverbindung zwischen den
Symboltabelleneinträgen und den AST-Knoten benötigen.

Führen Sie die im ersten Lauf möglichen Prüfungen durch, beispielsweise
ob eine Variable oder Funktion in einem Scope mehrfach definiert wird
oder ob Variablen als Funktion genutzt (aufgerufen) werden. Für
referenzierte Variablen muss geprüft werden, ob sie tatsächlich bereits
definiert und im Scope sichtbar sind. Zuweisungen dürfen nur an
Variablen erfolgen.

Geben Sie erkannte Fehler auf der Konsole aus.

### A5.3: Symboltabelle: Referenzierungen und Funktionsaufrufe (2P)

Implementieren Sie einen zweiten Lauf. Hier soll für Funktionsaufrufe
geprüft werden, ob diese Funktionen bereits definiert und im Scope
sichtbar sind.

Damit wird so etwas möglich:[^1]

``` c
// einfacher Funktionsaufruf, ohne Vorwärtsdeklaration
bar();

void bar() {
    print_char('b');
}
```

Geben Sie erkannte Fehler auf der Konsole aus.

------------------------------------------------------------------------

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png" width="10%">

Unless otherwise noted, this work is licensed under CC BY-SA 4.0.

<blockquote><p><sup><sub><strong>Last modified:</strong> 1243317 (homework: collapse transformation rules (B05), 2025-11-27)<br></sub></sup></p></blockquote>

[^1]: Anmerkung: Das ist kein gültiges C, sondern dient nur dazu, das
    Vorgehen bei mehreren Passes über die Symboltabelle zu erkennen und
    zu üben.
