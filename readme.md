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

| Vorlesung (2 SWS)            | Praktikum (2 SWS)                       |
|:-----------------------------|:----------------------------------------|
| Do, 15:45 - 17:15 Uhr (Zoom) | G1: Mi, 09:45 - 11:15 Uhr (Zoom)        |
|                              | G2: Mi, 11:30 - 13:00 Uhr (Zoom)        |
|                              | G3: Mi, 09:45 - 11:15 Uhr (Präsenz B40) |
|                              | G4: Mi, 11:30 - 13:00 Uhr (Präsenz B40) |

Durchführung der Vorlesung als *Flipped Classroom* (Carsten) bzw. als
*reguläre Vorlesung* (BC). Zugangsdaten Zoom siehe
[ILIAS](https://www.hsbi.de/elearning/goto.php/crs/1555855).

## Fahrplan

Hier finden Sie einen abonnierbaren [Google
Kalender](https://calendar.google.com/calendar/ical/07021c87022fe3f132fa2e2e799b230b5ab9c7088c85d152f2beab8f1b5218af%40group.calendar.google.com/public/basic.ics)
mit allen Terminen der Veranstaltung zum Einbinden in Ihre Kalender-App.

Abgabe der Post Mortems jeweils **Mittwoch bis 09:00 Uhr** im
[ILIAS](https://www.hsbi.de/elearning/goto.php/exc/1582799). Vorstellung
der Lösung im jeweiligen Praktikum in der Abgabewoche.

| Woche | Vorlesung: Selbststudium (Vorbereitung) | Vorlesung: Gemeinsame Sitzung | Praktikum | Edmonton/Minden-Meetings |
|:---|:---|:---|:---|:---|
| W01 / KW41 | [Überblick](lecture/00-intro/overview.md) \| [Sprachen](lecture/00-intro/languages.md) \| [Anwendungen](lecture/00-intro/applications.md) | [Orga](https://github.com/Compiler-CampusMinden/CB-Vorlesung-Bachelor-W25) (*Zoom*) |  |  |
| W02 / KW42 |  | [Reguläre Sprachen 1](lecture/01-lexing/regular1.md) |  |  |
| W03 / KW43 |  | [Reguläre Sprachen 2](lecture/01-lexing/regular2.md) \|\| [CFG](lecture/02-parsing/cfg.md) | [B01](homework/sheet01.md) Reguläre Sprachen |  |
| W04 / KW44 | [Lexer mit ANTLR](lecture/01-lexing/antlr-lexing.md) | [Parser mit ANTLR](lecture/02-parsing/antlr-parsing.md) | [B02](homework/sheet02.md) CFG |  |
| W05 / KW45 |  | [LL-Parser](lecture/02-parsing/ll-parser.md) | [B03](homework/sheet03.md) ANTLR | **Mo, 03.11., 18:00 - 19:00 Uhr (online): Edmonton/Minden: ANTLR + Live-Coding** |
| W06 / KW46 | [Lexer (Implementierung)](lecture/01-lexing/recursive.md) | [LL-Parser (Implementierung)](lecture/02-parsing/ll-parser-impl.md) |  |  |
| W07 / KW47 | Symboltabellen: [Überblick](lecture/03-semantics/symbtab0-intro.md) \| [Scopes](lecture/03-semantics/symbtab1-scopes.md) | [Funktionen](lecture/03-semantics/symbtab2-functions.md) \| [Klassen](lecture/03-semantics/symbtab3-classes.md) | [B04](homework/sheet04.md) LL hand coded |  |
| W08 / KW48 | [AST-basierte Interpreter 1](lecture/06-interpretation/astdriven-part1.md) | [AST-basierte Interpreter 2](lecture/06-interpretation/astdriven-part2.md) | [B05](homework/sheet05.md) Semantische Analyse |  |
| W09 / KW49 | C++ I: [Basics](lecture/99-languages/cpp0-basics.md) | [Pointer & Referenzen](lecture/99-languages/cpp1-pointer.md) |  | **Mo, 01.12., 18:00 - 19:00 Uhr (online): Edmonton/Minden: Minden Presentations** |
| W10 / KW50 | C++ II: [Klassen](lecture/99-languages/cpp2-classes.md) \| [Big Three](lecture/99-languages/cpp3-big3.md) \| [Operatoren](lecture/99-languages/cpp4-operators.md) | [Vererbung & Polymorphie](lecture/99-languages/cpp5-inheritance.md) | [B06](homework/sheet06.md) Interpreter | **Mo, 08.12., 18:00 - 19:00 Uhr (online): Edmonton/Minden: Edmonton Presentations** |
| W11 / KW51 |  | *Sprechstunde* | [B07](homework/sheet07.md) C++ |  |
| *- / KW52* |  | *Weihnachtspause* |  |  |
| *- / KW01* |  | *Weihnachtspause* |  |  |
| W12 / KW02 |  | *Sprechstunde* | *Freies Arbeiten [B08](homework/sheet08.md)* |  |
| W13 / KW03 |  | *Sprechstunde* | *Freies Arbeiten [B08](homework/sheet08.md)* |  |
| W14 / KW04 | [Prüfungsvorbereitung](admin/exams.md) | Rückblick (*Zoom*) | [B08](homework/sheet08.md) Mini-Projekt |  |

## Prüfungsform, Note und Credits

**(Digitale) Klausur plus Studienleistung**, 5 ECTS

- **Studienleistung**: “Portfolio”

  Kriterien je Team (3 Personen):

  1.  Aktive Teilnahme an mind. zwei der drei Edmonton/Minden-Termine
  2.  Mind. vier der sieben Übungsblätter B01..B07 erfolgreich
      bearbeitet[^1]
  3.  Mini-Projekt (B08) erfolgreich bearbeitet
  4.  Vortrag über Mini-Projekt (B08) am Semesterende (W14), pro Team 15
      Minuten

  Je Kriterium: Abgabe eines Post Mortem[^2] im ILIAS (**jede Person
  individuell**)

- **Gesamtnote**: (Digitale) Klausur im B40 (90 Minuten)

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

<blockquote><p><sup><sub><strong>Last modified:</strong> d7278a8 (orga: fix link to ilias, 2025-09-10)<br></sub></sup></p></blockquote>

[^1]: “erfolgreich bearbeitet”: Bearbeitung in 3er Teams, alle
    Aufgaben/Punkte bearbeitet, fristgerechte Abgabe des ausreichenden
    Post Mortems im ILIAS

[^2]: **Post Mortem**: Jede Person beschreibt in der ILIAS-Abgabe
    individuell(!) die Bearbeitung des jeweiligen Kriteriums bzw. die
    Teilnahme am Edmonton/Minden-Meeting zurückblickend mit mind. 200
    bis max. 400 Wörtern (Nutzlast; Überschriften und Links zählen nicht
    mit). Gehen Sie dabei aussagekräftig und nachvollziehbar auf
    folgende Punkte ein:

    1.  Zusammenfassung: Was wurde gemacht bzw. was wurde auf dem
        Meeting besprochen?
    2.  Details: Kurze Beschreibung besonders interessanter Aspekte der
        Umsetzung bzw. des Meetings.
    3.  Reflexion: Was war der schwierigste Teil bei der Bearbeitung?
        Wie haben Sie dieses Problem gelöst?
    4.  Reflexion: Was haben Sie gelernt oder (besser) verstanden?
    5.  Team: Mit wem haben Sie zusammengearbeitet?
    6.  Link zu Ihrem Repo mit den relevanten Artefakten (Lösung, Slides
        für den Vortrag, …).

    Für die Edmonton/Minden-Meetings passen Sie bitte die Punkte (1) bis
    (4) entsprechend inhaltlich an, (5) und (6) entfallen.

    Siehe auch
    https://github.com/Compiler-CampusMinden/CB-Vorlesung-Bachelor-W25/discussions/3.
