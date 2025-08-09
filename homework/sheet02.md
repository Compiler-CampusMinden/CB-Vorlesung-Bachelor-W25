# Blatt 02: CFG

## A2.1: PDA (2P)

Erstellen Sie einen deterministischen PDA, der die Sprache

``` math
L = \lbrace w \in \lbrace a, b, c \rbrace^* \; | \; w \; \text{hat doppelt so viele a's wie c's} \rbrace
```

akzeptiert.

Beschreiben Sie Schritt für Schritt, wie der PDA die Eingaben *bcaba*
und *bccac* abarbeitet.

## A2.2: Akzeptierte Sprache (1P)

Ist der folgenden PDA deterministisch? Warum (nicht)?

$`q_4`$ sei der akzeptierende Zustand.

``` math
\begin{eqnarray}
\delta(q_0,a, \perp) &=& (q_0, A\perp)           \nonumber \\
\delta(q_0,a, A) &=& (q_0, AA)                   \nonumber \\
\delta(q_0,b, A) &=& (q_1, BA)                   \nonumber \\
\delta(q_1,b, B) &=& (q_1, BB)                   \nonumber \\
\delta(q_1,c, B) &=& (q_2, \epsilon)             \nonumber \\
\delta(q_2,c, B) &=& (q_2, \epsilon)             \nonumber \\
\delta(q_2,d, A) &=& (q_3, \epsilon)             \nonumber \\
\delta(q_3,d, A) &=& (q_3, \epsilon)             \nonumber \\
\delta(q_3,d, A) &=& (q_3, AA)                   \nonumber \\
\delta(q_3,\epsilon, \perp) &=& (q_4, \epsilon)  \nonumber
\end{eqnarray}
```

Zeichnen Sie den Automaten. Geben Sie das 7-Tupel des PDa an. Welche
Sprache akzeptiert er?

## A2.3: Kontextfreie Sprache (1P)

Welche Sprache generiert die folgende kontextfreie (Teil-) Grammatik?

``` math
G = (\lbrace \text{Statement}, \text{Condition}, \ldots \rbrace, \lbrace \text{"if"}, \text{"else"}, \ldots \rbrace, P, \text{Statement})
```

mit

``` math
\begin{eqnarray}
P = \lbrace &&                                                                                                           \nonumber \\
&\text{Statement}& \rightarrow \text{"if" Condition Statement} \; | \; \text{"if" Condition Statement "else" Statement}  \nonumber \\
&\text{Condition}& \rightarrow \ldots                                                                                    \nonumber \\
\rbrace                                                                                                                  \nonumber
\end{eqnarray}
```

Ist die Grammatik mehrdeutig? Warum (nicht)?

## A2.4: Kontextfreie Grammatik (2P)

Entwickeln Sie eine kontextfreie Grammatik für die Sprache

``` math
L = \lbrace a^ib^jc^k \; | \; i = j \lor j = k \rbrace
```

Zeigen Sie, dass die Grammatik mehrdeutig ist. Entwickeln Sie einen PDA
für diese Sprache.

## A2.5: Kontextfreie Grammatik (4P)

Betrachten sie die folgende Grammatik:

``` math
G = (\lbrace S, A \rbrace, \lbrace 1, 2, 3 \rbrace, P, S)
```

mit

``` math
\begin{eqnarray}
P = \lbrace &&                        \nonumber \\
&S& \rightarrow 1AS \; | \; 3         \nonumber \\
&A& \rightarrow 2AS \; | \; \epsilon  \nonumber \\
\rbrace                               \nonumber
\end{eqnarray}
```

Berechnen die die *First-* und *Follow-Mengen* der Grammatik.

Zeigen Sie, dass die Grammatik LL(1) ist.

Konstruieren Sie die LL-Parsertabelle für die Grammatik und simulieren
Sie das Parsen des Wortes *1233*.

------------------------------------------------------------------------

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png" width="10%">

Unless otherwise noted, this work is licensed under CC BY-SA 4.0.

<blockquote><p><sup><sub><strong>Last modified:</strong> e188a08 (markdown: switch to leaner yaml header (#363), 2025-08-09)<br></sub></sup></p></blockquote>
