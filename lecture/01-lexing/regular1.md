# Reguläre Sprachen, Ausdrucksstärke (Teil 1)

> [!NOTE]
>
> <details>
>
> <summary><strong>🖇 Weitere Unterlagen</strong></summary>
>
> - [Annotierte Folien: Reguläre Sprachen,
>   Ausdrucksstärke](https://github.com/Compiler-CampusMinden/AnnotatedSlides/blob/master/lexing_regular.ann.ba.pdf)
>
> </details>

## Motivation

### Was muss ein Compiler wohl als erstes tun?

Hier entsteht ein Tafelbild.

### Themen für heute

- Endliche Automaten
- Reguläre Sprachen
- Lexer

## Endliche Automaten

### Alphabete

**Def.:** Ein **Alphabet** $`\Sigma`$ ist eine endliche, nicht-leere
Menge von Symbolen. Die Symbole eines Alphabets heißen *Buchstaben*.

**Def.:** Ein **Wort** $`w`$ *über einem Alphabet* $`\Sigma`$ ist eine
endliche Folge von Symbolen aus $`\Sigma`$. $`\epsilon`$ ist das leere
Wort. Die *Länge* $`\vert w \vert`$ eines Wortes $`w`$ ist die Anzahl
von Buchstaben, die es enthält (Kardinalität).

**Def.:** Eine **Sprache** $`L`$ *über einem Alphabet* $`\Sigma`$ ist
eine Menge von Wörtern über diesem Alphabet. Sprachen können endlich
oder unendlich viele Wörter enthalten.

### Beispiel

Hier entsteht ein Tafelbild.

### Deterministische endliche Automaten

Hier entsteht ein Tafelbild.

### Def.: deterministischer endlicher Automat

**Def.:** Ein **deterministischer endlicher Automat** (DFA) ist ein
5-Tupel $`A = (Q, \Sigma, \delta, q_0, F)`$ mit

- $`Q`$ : endliche Menge von **Zuständen**

- $`\Sigma`$ : Alphabet von **Eingabesymbolen**

- $`\delta`$ : die (eventuell partielle) **Übergangsfunktion**
  $`(Q \times \Sigma) \rightarrow Q`$, $`\delta`$ kann partiell sein

- $`q_0 \in Q`$ : der **Startzustand**

- $`F \subseteq Q`$ : die Menge der **Endzustände**

### Die Übergangsfunktion

**Def.:** Wir definieren
$`\delta^{\ast}: (Q \times \Sigma^{\ast}) \rightarrow Q`$: induktiv wie
folgt:

- Basis: $`\delta^{\ast}(q, \epsilon) = q\ \forall q \in Q`$

- Induktion:
  $`\delta^{\ast}(q, a_1, \ldots, a_n) = \delta(\delta^{\ast}(q, a_1, \ldots , a_{n-1}), a_n)`$

**Def.:** Ein DFA akzeptiert ein Wort $`w \in \Sigma^{\ast}`$ genau
dann, wenn $`\delta^{\ast}(q_0, w) \in F.`$

### Beispiel

Hier entsteht ein Tafelbild.

### Nichtdeterministische endliche Automaten

**Def.:** Ein **nichtdeterministischer endlicher Automat** (NFA) ist ein
5-Tupel $`A = (Q, \Sigma, \delta, q_0, F)`$ mit

- $`Q`$ : endliche Menge von **Zuständen**

- $`\Sigma`$ : Alphabet von **Eingabesymbolen**

- $`\delta`$ : die (eventuell partielle) **Übergangsfunktion**
  $`(Q \times \Sigma) \rightarrow Q`$

- $`q_0 \in Q`$ : der **Startzustand**

- $`F \subseteq Q`$ : die Menge der **Endzustände**

### Akzeptierte Sprachen

**Def.:** Sei A ein DFA oder ein NFA. Dann ist **L(A)** die von A
akzeptierte Sprache, d. h.

$`L(A) = \lbrace \text{Wörter}\ w\ |\ \delta^*(q_0, w) \in F \rbrace`$

### Wozu NFAs im Compilerbau?

Pattern Matching (Erkennung von Schlüsselwörtern, Bezeichnern, …) geht
mit NFAs.

NFAs sind so nicht zu programmieren, aber:

**Satz:** Eine Sprache $`L`$ wird von einem NFA akzeptiert
$`\Leftrightarrow L`$ wird von einem DFA akzeptiert.

D. h. es existieren Algorithmen zur

- Umwandlung von NFAs in DFAS
- Minimierung von DFAs

## Reguläre Sprachen

### Reguläre Ausdrücke definieren Sprachen

**Def.:** Induktive Definition von **regulären Ausdrücken** (regex) und
der von ihnen repräsentierten Sprache **L**:

- Basis:

  - $`\epsilon`$ und $`\emptyset`$ sind reguläre Ausdrücke mit
    $`L(\epsilon) =
      \lbrace \epsilon\rbrace`$, $`L(\emptyset)=\emptyset`$
  - Sei $`a`$ ein Symbol $`\Rightarrow`$ $`a`$ ist ein regex mit
    $`L(a) = \lbrace a\rbrace`$

- Induktion: Seien $`E,\ F`$ reguläre Ausdrücke. Dann gilt:

  - $`E+F`$ ist ein regex und bezeichnet die Vereinigung
    $`L(E + F) = L(E)\cup L(F)`$
  - $`EF`$ ist ein regex und bezeichnet die Konkatenation
    $`L(EF) = L(E)L(F)`$
  - $`E^{\ast}`$ ist ein regex und bezeichnet die Kleene-Hülle
    $`L(E^{\ast})=(L(E))^{\ast}`$
  - $`(E)`$ ist ein regex mit $`L((E)) = L(E)`$

Vorrangregeln der Operatoren für reguläre Ausdrücke: \*, Konkatenation,
+

### Beispiel

Hier entsteht ein Tafelbild.

### Wichtige Identitäten

**Satz:** Sei $`A`$ ein DFA $`\Rightarrow \exists`$ regex $`R`$ mit
$`L(A) = L(R)`$.

**Satz:** Sei $`E`$ ein regex $`\Rightarrow \exists`$ DFA $`A`$ mit
$`L(E) = L(A)`$.

### Formale Grammatiken

Hier entsteht ein Tafelbild.

### Formale Definition formaler Grammatiken

**Def.:** Eine *formale Grammatik* ist ein 4-Tupel $`G=(N,T,P,S)`$ aus

- $`N`$: endliche Menge von **Nichtterminalen**

- $`T`$: endliche Menge von **Terminalen**, $`N \cap T = \emptyset`$

- $`S \in N`$: **Startsymbol**

- $`P`$: endliche Menge von **Produktionen** der Form

$`\qquad X \rightarrow Y`$ mit
$`X \in (N \cup T)^{\ast} N  (N \cup T)^{\ast}, Y \in (N \cup T)^{\ast}`$

### Ableitungen

**Def.:** Sei $`G = (N, T, P, S)`$ eine Grammatik, sei
$`\alpha A \beta`$ eine Zeichenkette über $`(N \cup T)^{\ast}`$ und sei
$`A`$ $`\rightarrow \gamma`$ eine Produktion von $`G`$.

Wir schreiben: $`\alpha A \beta \Rightarrow \alpha \gamma \beta`$
($`\alpha A \beta`$ leitet $`\alpha \gamma \beta`$ ab).

**Def.:** Wir definieren die Relation $`\overset{\ast}{\Rightarrow}`$
induktiv wie folgt:

- Basis:
  $`\forall \alpha \in (N \cup T)^{\ast} \alpha \overset{\ast}{\Rightarrow} \alpha`$
  (Jede Zeichenkette leitet sich selbst ab.)

- Induktion: Wenn $`\alpha \overset{\ast}{\Rightarrow} \beta`$ und
  $`\beta\Rightarrow \gamma`$ dann
  $`\alpha \overset{\ast}{\Rightarrow} \gamma`$

**Def.:** Sei $`G = (N, T ,P, S)`$ eine formale Grammatik. Dann ist
$`L(G) = \lbrace \text{Wörter}\ w\ \text{über}\ T \mid S \overset{\ast}{\Rightarrow} w\rbrace`$
die von $`G`$ erzeugte Sprache.

### Beispiel

Hier entsteht ein Tafelbild.

### Reguläre Grammatiken

**Def.:** Eine **reguläre (oder type-3-) Grammatik** ist eine formale
Grammatik mit den folgenden Einschränkungen:

- Alle Produktionen sind entweder von der Form

  - $`X \to aY`$ mit $`X \in N, a \in T, Y \in N`$ (*rechtsreguläre*
    Grammatik) oder
  - $`X \to Ya`$ mit $`X \in N, a \in T, Y \in N`$ (*linksreguläre*
    Grammatik)

- $`X\rightarrow\epsilon`$ ist erlaubt

### Beispiel

Hier entsteht ein Tafelbild.

### Reguläre Sprachen und ihre Grenzen

**Satz:** Die von endlichen Automaten akzeptiert Sprachklasse, die von
regulären Ausdrücken beschriebene Sprachklasse und die von regulären
Grammatiken erzeugte Sprachklasse sind identisch und heißen **reguläre
Sprachen**.

**Reguläre Sprachen**

- einfache Struktur
- Matchen von Symbolen (z. B. Klammern) nicht möglich, da die fixe
  Anzahl von Zuständen eines DFAs die Erkennung solcher Sprachen
  verhindert.

### Wozu reguläre Sprachen im Compilerbau?

- Reguläre Ausdrücke

  - definieren Schlüsselwörter und alle weiteren Symbole einer
    Programmiersprache, z. B. den Aufbau von Gleitkommazahlen
  - werden (oft von einem Generator) in DFAs umgewandelt
  - sind die Basis des *Scanners* oder *Lexers*

## Lexer

### Ein Lexer ist mehr als ein DFA

- Ein **Lexer**

  - wandelt mittels DFAs aus regulären Ausdrücken die Folge von Zeichen
    der Quelldatei in eine Folge von sog. Token um

  - bekommt als Input eine Liste von Paaren aus regulären Ausdrücken und
    Tokennamen, z. B. (“while”, WHILE)

  - Kommentare und Strings müssen richtig erkannt werden.
    (Schachtelungen)

  - liefert Paare von Token und deren Werte, sofern benötigt, z. B.
    (WHILE, \_), oder (IDENTIFIER, “radius”) oder (INTEGERZAHL, “334”)

### Wie geht es weiter?

- Ein **Parser**

  - führt mit Hilfe des Tokenstreams vom Lexer die Syntaxanalyse durch

  - basiert auf einer sog. kontextfreien Grammatik, deren Terminale die
    Token sind

  - liefert die syntaktische Struktur in Form eines Ableitungsbaums
    (**syntax tree**, **parse tree**), bzw. einen **AST** (abstract
    syntax tree) ohne redundante Informationen im Ableitungsbaum (z. B.
    Semikolons)

  - liefert evtl. Fehlermeldungen

## Wrap-Up

### Wrap-Up

- Definition und Aufgaben von Lexern
- DFAs und NFAs
- Reguläre Ausdrücke
- Reguläre Grammatiken
- Zusammenhänge zwischen diesen Mechanismen und Lexern, bzw.
  Lexergeneratoren

## 📖 Zum Nachlesen

- Aho u. a. ([2023](#ref-Aho2023)): Abschnitt 2.6 und Kapitel 3
- Torczon und Cooper ([2012](#ref-Torczon2012)): Kapitel 2
- Parr ([2014](#ref-Parr2014))

> [!NOTE]
>
> <details>
>
> <summary><strong>✅ Lernziele</strong></summary>
>
> - k1: Ich kenne DFAs
> - k1: Ich kenne reguläre Ausdrücke
> - k1: Ich kenne reguläre Grammatiken
> - k2: Ich kann die Zusammenhänge und Gesetzmäßigkeiten bzgl. der oben
>   genannten Konstrukte an einem Beispiel erklären
> - k3: Ich kann für eine Fragestellung DFAs, reguläre Ausdrücke,
>   reguläre Grammatiken entwickeln
> - k3: Ich kann einen DFA entwickeln, der alle Schlüsselwörter, Namen
>   und weitere Symbole einer Programmiersprache akzeptiert
>
> </details>

------------------------------------------------------------------------

> [!NOTE]
>
> <details>
>
> <summary><strong>👀 Quellen</strong></summary>
>
> <div id="refs" class="references csl-bib-body hanging-indent"
> entry-spacing="0">
>
> <div id="ref-Aho2023" class="csl-entry">
>
> Aho, A. V., M. S. Lam, R. Sethi, J. D. Ullman, und S. Bansal. 2023.
> *Compilers: Principles, Techniques, and Tools, Updated 2nd Edition by
> Pearson*. Pearson India.
> <https://learning.oreilly.com/library/view/compilers-principles-techniques/9789357054881/>.
>
> </div>
>
> <div id="ref-Parr2014" class="csl-entry">
>
> Parr, T. 2014. *The Definitive ANTLR 4 Reference*. Pragmatic
> Bookshelf.
> <https://learning.oreilly.com/library/view/the-definitive-antlr/9781941222621/>.
>
> </div>
>
> <div id="ref-Torczon2012" class="csl-entry">
>
> Torczon, L., und K. Cooper. 2012. *Engineering a Compiler*. Morgan
> Kaufmann.
> <https://learning.oreilly.com/library/view/engineering-a-compiler/9780080916613/>.
>
> </div>
>
> </div>
>
> </details>

------------------------------------------------------------------------

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png" width="10%">

Unless otherwise noted, this work is licensed under CC BY-SA 4.0.

<blockquote><p><sup><sub><strong>Last modified:</strong> 64341f7 (lecture: split regular expressions into two lessons, 2025-08-29)<br></sub></sup></p></blockquote>
