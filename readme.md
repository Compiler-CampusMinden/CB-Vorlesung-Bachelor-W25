# IFM 3.1: Compilerbau (Winter 2025/26)

<img src="admin/images/architektur_cb.png" width="80%">

## Kursbeschreibung

Der Compiler ist das wichtigste Werkzeug in der Informatik. In der
Königsdisziplin der Informatik schließt sich der Kreis, hier kommen die
unterschiedlichen Algorithmen und Datenstrukturen und
Programmiersprachenkonzepte zur Anwendung.

In diesem Modul geht es um ein grundlegendes Verständnis für die
wichtigsten Konzepte im Compilerbau. Wir schauen uns dazu relevante
aktuelle Tools und Frameworks an und setzen diese bei der Erstellung
eines kleinen Compiler-Frontends für C++ ein.

## Überblick Modulinhalte

1.  Lexikalische Analyse: Scanner/Lexer
    - Reguläre Sprachen
    - Generierung mit ANTLR
2.  Syntaxanalyse: Parser
    - Kontextfreie Grammatiken (CFG)
    - LL-Parser (Top-Down-Parser)
    - Generierung mit ANTLR
3.  Semantische Analyse: Symboltabellen
    - Namen und Scopes
    - Typen, Klassen, Polymorphie
4.  Interpreter: AST-Traversierung
5.  C++ als zu verarbeitende Programmiersprache

## Team

- [BC
  George](https://www.hsbi.de/minden/ueber-uns/personenverzeichnis/birgit-christina-george)
- [Carsten
  Gips](https://www.hsbi.de/minden/ueber-uns/personenverzeichnis/carsten-gips)
  (Sprechstunde nach Vereinbarung)

## Kursformat

| Vorlesung (2 SWS)            | Praktikum (2 SWS)                        |
|:-----------------------------|:-----------------------------------------|
| Mo, 09:00 - 10:30 Uhr (Zoom) | G1: Fr, 09:45 - 11:15 Uhr (Zoom)         |
|                              | G2: Fr, 11:30 - 13:00 Uhr (Zoom)         |
|                              | G3: Fr, 09:45 - 11:15 Uhr (Präsenz J101) |
|                              | G4: Fr, 11:30 - 13:00 Uhr (Präsenz J101) |

Durchführung der Vorlesung als *Flipped Classroom* (Carsten) bzw. als
*reguläre Vorlesung* (BC). Zugangsdaten Zoom siehe
[ILIAS](https://www.hsbi.de/elearning/goto.php?target=crs_1400597&client_id=FH-Bielefeld).

## Fahrplan

Hier finden Sie einen abonnierbaren [Google
Kalender](https://calendar.google.com/calendar/ical/07021c87022fe3f132fa2e2e799b230b5ab9c7088c85d152f2beab8f1b5218af%40group.calendar.google.com/public/basic.ics)
mit allen Terminen der Veranstaltung zum Einbinden in Ihre Kalender-App.

Abgabe der Übungsblätter jeweils **Montag bis 09:00 Uhr** im
[ILIAS](https://www.hsbi.de/elearning/goto.php?target=exc_1420724&client_id=FH-Bielefeld).
Vorstellung der Lösung im jeweiligen Praktikum in der Abgabewoche.

| Woche | Vorlesung: Selbststudium (Vorbereitung) | Vorlesung: Gemeinsame Sitzung | Praktikum |  |
|:---|:---|:---|:---|:---|
| W01 / KW41 |  | [Orga](https://github.com/Compiler-CampusMinden/CB-Vorlesung-Bachelor-W25) (*Zoom*) \|\| [Überblick](lecture/00-intro/overview.md) \| [Sprachen](lecture/00-intro/languages.md) \| [Anwendungen](lecture/00-intro/applications.md) |  |  |
| W02 / KW42 |  | [Reguläre Sprachen I](lecture/01-lexing/regular1.md) |  |  |
| W03 / KW43 |  | [Reguläre Sprachen II](lecture/01-lexing/regular2.md) \|\| [CFG](lecture/02-parsing/cfg.md) | [B01](homework/sheet01.md) Reguläre Sprachen |  |
| W04 / KW44 | [Lexer mit ANTLR](lecture/01-lexing/antlr-lexing.md) | [Parser mit ANTLR](lecture/02-parsing/antlr-parsing.md) | [B02](homework/sheet02.md) CFG |  |
| W05 / KW45 |  |  | [B03](homework/sheet03.md) ANTLR | **18:00 - 19:30 Uhr (online): Edmonton I: ANTLR + Live-Coding** |
| W06 / KW46 |  | [LL-Parser](lecture/02-parsing/ll-parser.md) |  | **Parcours I** |
| W07 / KW47 | [Lexer (Impl)](lecture/01-lexing/recursive.md) | [LL-Parser (Impl)](lecture/02-parsing/ll-parser-impl.md) |  |  |
| W08 / KW48 | [Überblick Symboltabellen](lecture/03-semantics/symbtab0-intro.md) \| [Symboltabellen: Scopes](lecture/03-semantics/symbtab1-scopes.md) | [Symboltabellen: Funktionen](lecture/03-semantics/symbtab2-functions.md) \| [Symboltabellen: Klassen](lecture/03-semantics/symbtab3-classes.md) | [B04](homework/sheet04.md) LL hand coded |  |
| W09 / KW49 | [AST-basierte Interpreter 1](lecture/06-interpretation/astdriven-part1.md) | [AST-basierte Interpreter 2](lecture/06-interpretation/astdriven-part2.md) | [B05](homework/sheet05.md) Semantische Analyse | **18:00 - 19:30 Uhr (online): Edmonton II: Vorträge Mindener Projekte** |
| W10 / KW50 | C++ I: [Basics](lecture/99-languages/cpp0-basics.md) | [Pointer & Referenzen](lecture/99-languages/cpp1-pointer.md) |  | **Parcours II** \|\| **18:00 - 19:30 Uhr (online): Edmonton III: Vorträge Edmontoner Projekte** |
| W11 / KW51 | C++ II: [Klassen](lecture/99-languages/cpp2-classes.md) \| [Big 3](lecture/99-languages/cpp3-big3.md) \| [Operatoren](lecture/99-languages/cpp4-operators.md) | [Vererbung & Polymorphie](lecture/99-languages/cpp5-inheritance.md) | [B06](homework/sheet06.md) Interpreter |  |
| W12 / KW02 |  | *Sprechstunde/Freies Arbeiten* | [B07](homework/sheet07.md) C++ | **Parcours III** |
| W13 / KW03 |  | *Sprechstunde/Freies Arbeiten* |  |  |
| W14 / KW04 |  | Rückblick (*Zoom*) \| [Prüfungsvorbereitung](admin/exams.md) | [B08](homework/sheet08.md) Mini-Projekt |  |

## Prüfungsform, Note und Credits

**Parcoursprüfung plus Testat**, 5 ECTS

- **Testat**: Vergabe der Credit-Points
  1.  Mindestens 4 der Übungsblätter [B01](homework/sheet01.md),
      [B02](homework/sheet02.md), [B03](homework/sheet03.md),
      [B04](homework/sheet04.md), [B05](homework/sheet05.md) und
      [B06](homework/sheet06.md) erfolgreich bearbeitet, **und**
  2.  **aktive** Teilnahme an allen 3 Edmonton-Terminen.

  (“erfolgreich bearbeitet”: Bearbeitung in 3er Teams, je mindestens 60%
  bearbeitet, fristgerechte Abgabe der Lösungen im ILIAS, Vorstellung
  der Lösungen im Praktikum)

### Prüfung im ersten Zeitraum

- **Stationen**:

  1.  ILIAS-Test (einzeln, 20.11.)
  2.  Vorstellung Mini-Projekt [B07](homework/sheet07.md) (3er Teams,
      letzte VL-Woche)

  Note für das Modul: Beide Stationen ergeben zu **je 50%** *oder* in
  der Gewichtung **30 Punkte (Station I) und 50 Punkte (Station II)**
  die Gesamtnote (individuelle Günstigerprüfung).

  Für Station I werden 3 Punkte Überhang gewährt: Von den 33 maximal
  erreichbaren Punkten werden 30 Punkte als 100% gewertet, darüber
  hinausgehende Punkte bleiben als Bonuspunkte erhalten.

### Prüfung im zweiten Zeitraum

- **Stationen**:
  1.  Mündliche Prüfung (individuell, ca. 45 Minuten, zweiter
      Prüfungszeitraum)

  Die Note der mündlichen Prüfung ergibt die Gesamtnote.

## Materialien

1.  [“**Compilers: Principles, Techniques, and
    Tools**”](https://learning.oreilly.com/library/view/compilers-principles-techniques/9789357054881/).
    Aho, A. V. und Lam, M. S. und Sethi, R. und Ullman, J. D. and
    Bansal, S., Pearson India, 2023. ISBN
    [978-9-3570-5488-1](https://fhb-bielefeld.digibib.net/openurl?isbn=978-9-3570-5488-1).
    [Online](https://learning.oreilly.com/library/view/compilers-principles-techniques/9789357054881/)
    über die
    [O’Reilly-Lernplattform](https://www.oreilly.com/library-access/).
2.  [“**Crafting
    Interpreters**”](https://github.com/munificent/craftinginterpreters).
    Nystrom, R., Genever Benning, 2021. ISBN
    [978-0-9905829-3-9](https://fhb-bielefeld.digibib.net/openurl?isbn=978-0-9905829-3-9).
    [Online](https://www.craftinginterpreters.com/).
3.  [“**The Definitive ANTLR 4
    Reference**”](https://learning.oreilly.com/library/view/the-definitive-antlr/9781941222621/).
    Parr, T., Pragmatic Bookshelf, 2014. ISBN
    [978-1-9343-5699-9](https://fhb-bielefeld.digibib.net/openurl?isbn=978-1-9343-5699-9).
    [Online](https://learning.oreilly.com/library/view/the-definitive-antlr/9781941222621/)
    über die
    [O’Reilly-Lernplattform](https://www.oreilly.com/library-access/).
4.  [“Writing a C
    Compiler”](https://learning.oreilly.com/library/view/writing-a-c/9781098182229/).
    Sandler, N., No Starch Press, 2024. ISBN
    [978-1-0981-8222-9](https://fhb-bielefeld.digibib.net/openurl?isbn=978-1-0981-8222-9).
    [Online](https://learning.oreilly.com/library/view/writing-a-c/9781098182229/)
    über die
    [O’Reilly-Lernplattform](https://www.oreilly.com/library-access/).

## Förderungen und Kooperationen

### Kooperation mit University of Alberta, Edmonton (Kanada)

Über das Projekt [“We CAN
virtuOWL”](https://www.uni-bielefeld.de/international/profil/netzwerk/alberta-owl/we-can-virtuowl/)
der Fachhochschule Bielefeld ist im Frühjahr 2021 eine Kooperation mit
der [University of
Alberta](https://www.hsbi.de/en/international-office/alberta-owl-cooperation)
(Edmonton/Alberta, Kanada) im Modul “Compilerbau” gestartet.

Wir freuen uns, auch in diesem Semester wieder drei gemeinsame Sitzungen
für beide Hochschulen anbieten zu können. (Diese Termine werden in
englischer Sprache durchgeführt.)

------------------------------------------------------------------------

## LICENSE

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png">

Unless otherwise noted, [this
work](https://github.com/Compiler-CampusMinden/CB-Vorlesung-Bachelor) by
[BC George](https://github.com/bcg7), [Carsten
Gips](https://github.com/cagix) and
[contributors](https://github.com/Compiler-CampusMinden/CB-Vorlesung-Bachelor/graphs/contributors)
is licensed under [CC BY-SA
4.0](https://github.com/Compiler-CampusMinden/CB-Vorlesung-Bachelor/blob/master/LICENSE.md).
See the [credits](CREDITS.md) for a detailed list of contributing
projects.

<blockquote><p><sup><sub><strong>Last modified:</strong> c8af504 (orga: update plan, split self study and class session (#368), 2025-08-29)<br></sub></sup></p></blockquote>
