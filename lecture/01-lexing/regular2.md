# Reguläre Sprachen, Ausdrucksstärke (Teil 2)

> [!NOTE]
>
> <details>
>
> <summary><strong>🖇 Weitere Unterlagen</strong></summary>
>
> - [Annotierte Folien: Reguläre Sprachen,
>   Ausdrucksstärke](https://github.com/Compiler-CampusMinden/AnnotatedSlides/blob/master/lexing_regular2.ann.ba.pdf)
>
> </details>

## Wiederholung

### Endliche Automaten, reguläre Ausdrücke, reguläre Grammatiken, reguläre Sprachen

- Wie sind DFAs und NFAs definiert?
- Was sind reguläre Ausdrücke?
- Was sind formale und reguläre Grammatiken?
- In welchem Zusammenhang stehen all diese Begriffe?

## Motivation

### Was haben reguläre Sprachen mit Compilern zu tun?

Hier entsteht ein Tafelbild.

### Themen für heute

- Reguläre Sprachen
- Lexer
- Grenzen regulärer Sprachen

### Wozu reguläre Sprachen im Compilerbau?

Reguläre Ausdrücke

- definieren Schlüsselwörter und alle weiteren Symbole einer
  Programmiersprache, z. B. den Aufbau von Gleitkommazahlen
- werden (oft von einem Generator) in DFAs umgewandelt
- sind die Basis des *Scanners* oder *Lexers*

## Lexer

### Ein Lexer ist mehr als ein DFA

Ein **Lexer**

- kann aus regulären Ausdrücken automatisch generiert werden

- wandelt mittels DFAs aus regulären Ausdrücken die Folge von Zeichen
  der Quelldatei in eine Folge von sog. Token um

- bekommt als Input eine Liste von Paaren aus regulären Ausdrücken und
  Tokennamen, z. B. (“while”, WHILE)

- Kommentare und Strings müssen richtig erkannt werden. (Schachtelungen)

- liefert Paare von Token und deren Werte, sofern benötigt, z. B.
  (WHILE, \_), oder (IDENTIFIER, “radius”) oder (INTEGERZAHL, “334”)

### Wofür reichen reguläre Sprachen nicht?

Für z. B. alle Sprachen, in deren Wörtern Zeichen über eine Konstante
hinaus gezählt werden müssen. Diese Sprachen lassen sich oft mit
Variablen im Exponenten beschreiben, die unendlich viele Werte annehmen
können.

- $`a^ib^{2*i}`$ ist nicht regulär
- $`a^ib^{2*i}`$ für $`0 \leq i \leq 3`$ ist regulär

<!-- -->

- Wo finden sich die oben genannten Variablen bei einem DFA wieder?
- Warum ist die erste Sprache oben nicht regulär, die zweite aber?

### Wie geht es weiter?

Ein **Parser**

- führt mit Hilfe des Tokenstreams vom Lexer die Syntaxanalyse durch

- basiert auf einer sog. kontextfreien Grammatik, deren Terminale die
  Token sind

- liefert die syntaktische Struktur in Form eines Ableitungsbaums
  (**syntax tree**, **parse tree**), bzw. einen **AST** (abstract syntax
  tree) ohne redundante Informationen im Ableitungsbaum (z. B.
  Semikolons)

- liefert evtl. Fehlermeldungen

## Wrap-Up

### Wrap-Up

- Definition und Aufgaben von Lexern
- Zusammenhänge zwischen diesen Mechanismen und Lexern, bzw.
  Lexergeneratoren
- Grenzen regulärer Sprachen

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
> - k1: Ich kenne die Aufgaben eines Lexers
> - k1: Ich kenne die Zusammenhänge zwischen DFAs, regulären Ausdrücken,
>   regulären Grammatiken und Lexern
> - k2: Ich kann für eine Beispielsprache begründen, warum sie nicht mit
>   einem der oben genannten Mechanismen beschrieben werden kann
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

<blockquote><p><sup><sub><strong>Last modified:</strong> 9697eda (lecture: fix indentation/formatting (Regular), 2025-10-15)<br></sub></sup></p></blockquote>
