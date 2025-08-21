# C++: Pointer und Referenzen

> [!IMPORTANT]
>
> <details open>
>
> <summary><strong>🎯 TL;DR</strong></summary>
>
> Es gibt viele Arten Speicher, die sich vor allem in der Größe und
> Geschwindigkeit unterscheiden (Cache, RAM, SSD, Festplatte, …). Der
> Kernel stellt jedem Prozess einen linearen Adressraum bereit und
> abstrahiert dabei von den darunter liegenden physikalischen
> Speichermedien (es gibt eine Abbildung auf die jeweiligen
> Speichermedien durch die MMU, dies ist aber nicht Bestandteil dieses
> Kurses).
>
> Den virtuellen Speicher kann man grob in drei Segmente aufteilen: Text
> (hier befindet sich der Programmcode des Prozesses), Stack
> (automatische Verwaltung, für Funktionsaufrufe und lokale Variablen)
> und Heap (Verwaltung durch den Programmierer, dynamische
> Bereitstellung von Speicher während der Laufzeit des Programms).
>
> Pointer sind Variablen, deren **Wert als Adresse** (im virtuellen
> Speicher) interpretiert wird. Pointer können auf andere Objekte bzw.
> Variablen zeigen: Der Adressoperator “`&`” liefert die Adresse eines
> Objekts im virtuellen Speicher, diese kann einem Pointer zugewiesen
> werden (der Wert des Pointers ist dann die zugewiesene Adresse).
> Pointer können mit “`*`” dereferenziert werden, d.h. es wird an der
> Speicherstelle im virtuellen Speicher nachgeschaut, deren Adresse im
> Pointer gespeichert ist. Dadurch erfolgt der Zugriff auf das
> verwiesene Objekt. (Dies hat noch nichts mit *dynamischer
> Speicherverwaltung* zu tun!) Die Deklaration eines Pointers erfolgt
> mit einem `*` zwischen Typ und Pointername: `int *p;`. Da Pointer
> normale Variablen sind, unterliegen Pointer-Variablen den üblichen
> Gültigkeitsbedingungen (Scopes).
>
> In C++ gibt es zusätzlich **Referenzen**. Diese stellen Alias-Namen
> für ein Objekt (oder eine Variable) dar, d.h. ein Zugriff auf eine
> Referenz bewirkt den direkten Zugriff auf das verbundene Objekt.
> Referenzen müssen bei der Deklaration initialisiert werden
> (`Typ &ref = obj;`) und sind dann fest mit diesem Objekt verbunden.
>
> In C und C++ werden Funktionsparameter immer per Call-by-Value
> übergeben: Der Wert des Arguments wird in die lokale Variable des
> Funktionsparameters kopiert. Wenn ein Pointer übergeben wird, wird
> entsprechend der Wert des Pointers kopiert, also die gespeicherte
> Adresse. Mit der Adresse eines Objekts kann man aber auch in der
> Funktion direkt auf dieses Objekt zugreifen und dieses auslesen und
> verändern, d.h. durch die Übergabe eines Pointers hat man zwar immer
> noch Call-by-Value (die Adresse wird kopiert), die Wirkung ist aber
> wie bei Call-by-Reference (also als ob eine Referenz auf das Objekt
> übergeben wurde). Bei der Verwendung von C++-Referenzen hat man
> dagegen echtes Call-by-Reference.
>
> Zur Laufzeit kann man Speicher auf dem Heap reservieren
> (*allozieren*). Im Gegensatz zu Speicher auf dem Stack ist man selbst
> auch für die Freigabe des reservierten Speichers zuständig - wenn man
> dies nicht beachtet, läuft irgendwann der Heap voll. Allokation und
> Freigabe kann entweder mit den C-Funktionen `malloc` und `free`
> erfolgen oder mit den C++-Operatoren `new` und `delete`. Mischen Sie
> niemals nie `malloc()`/`free()` mit `new`/`delete`!
>
> Zwischen Pointern und Arrays gibt es eine enge Verwandschaft. Die
> einzelnen Elemente eines Arrays werden vom Compiler direkt
> aufeinanderfolgend im Speicher angeordnet, der Array-Name ist wie ein
> (konstanter) Pointer auf das erste Element. Tatsächlich übersetzt der
> Compiler Indexzugriffe für ein Array in die passende
> Pointerdereferenzierung: `a[i]` wird zu `*(a+i)`. Ein Pointer kann
> wiederum auch auf das erste Element eines zusammenhängenden
> Speicherbereichs zeigen, etwa wenn man über `malloc` Speicherplatz für
> mehrere Elemente anfordert. Da der Compiler aus einem Indexzugriff
> ohnehin die Pointerdereferenzierung macht, könnte man so einen Pointer
> auch per Indexzugriff abfragen. Dies ist aber gefährlich: Es
> funktioniert auch, wenn der Pointer nur auf *ein* anderes Objekt zeigt
> und nicht auf einen Speicherbereich … Ein Arrayname wird vom Compiler
> fest der ersten Speicheradresse des Arrays zugeordnet und kann nicht
> verändert werden, der Inhalt eines (nicht-konstanten) Pointer dagegen
> schon (der Pointer selbst wird auch fest im Speicher angelegt).
>
> Pointer haben einen Typ: Die Pointerarithmetik berücksichtigt die
> Speicherbreite des Typs! Damit springt man mit `ptr+1` automatisch zum
> nächsten Objekt und nicht notwendigerweise zum nächsten Byte.
>
> </details>

> [!TIP]
>
> <details>
>
> <summary><strong>🎦 Videos</strong></summary>
>
> - [C++: Pointer und Referenzen](https://youtu.be/1Tzkp1SzVk0)
>
> </details>

## Virtueller Speicher

                +-----------------------------------------+
                |          Text                           | 0x0000
                |                                         |    |
                |-----------------------------------------|    |
                |          Heap (Data)                    |    |
                |                                         |    |
                |--------------------+--------------------|    |
                |                    |                    |    |
                |                    v                    |    |
                |                                         |    |
                |                                         |    v
                |                    ^                    |
                |                    |                    |
                |--------------------+--------------------|
                |                                         |
                |          Stack                          |
                +-----------------------------------------+

- Kernel weist jedem Prozess seinen eigenen virtuellen Speicher zu
- Linearer Adressbereich, beginnend mit Adresse 0 bis zu einer maximalen
  Adresse
- Verwaltung durch MMU (*Memory Management Unit*)
  - MMU bildet logische Adressen aus virtuellem Speicher auf den
    physikalischen Speicher ab
  - Transparent für den Prozess

### Segmente des virtuellen Speichers: Text (read-only)

- Programm Code
- Konstanten, String Literale

zusätzlich (nicht in Abbildung dargestellt):

- Bereich initialisierter Daten (globale und static Variablen (explizit
  initialisiert))
- Bereich uninitialisierter Daten (globale und static Variablen
  (uninitialisiert) =\> Wert 0)

### Segmente des virtuellen Speichers: Stack

- Dynamisch wachsend und schrumpfend
- Stackframe je Funktionsaufruf:
  - Lokale Variablen (“automatische” Variablen)
  - Argumente und Return-Werte
- **Automatische Pflege**
  - Nach Funktionsrückkehr wird der Stackpointer (“Top of Stack”) weiter
    gesetzt
  - Dadurch “Bereinigung”: Speicher der lokalen Variablen wird
    freigegeben

### Segmente des virtuellen Speichers: Data (Heap)

- Dynamisch wachsend und schrumpfend
- Bereich für dynamischen Speicher (Allokation während der Laufzeit)
- Zugriff und Verwaltung aus laufendem Programm =\> **Pointer**
  - `malloc()`/`calloc()`/`free()` (C)
  - `new`/`delete` (C++)
  - typischerweise **Pointer**
- **KEINE automatische Pflege - Programmierer ist selbst
  verantwortlich!**

## Konzept eines Pointers

``` c
int i = 99;
int *iptr;

iptr = &i;  /* Wert von iptr ist gleich Adresse von i */
*iptr = 2;  /* Deferenzierung von iptr => Veränderung von i */
```

            Variable    Speicheraddresse    Inhalt

                                            |          |
                                            +----------+
            i           10125               | 99       |  <--+
                                            +----------+     |
                                            |          |     |
                        ....                 ....            |
                                            |          |     |
                                            +----------+     |
            iptr        27890               | 10125    |  ---+
                                            +----------+
                                            |          |

### Pointer sind Variablen

- haben Namen und Wert
- können mit Operatoren verändert werden
- sind einer Speicheradresse im virtuellen Speicher zugeordnet

Im Beispiel:

- Variable `i`:
  - Name: “i”
  - Wert: 99
  - Speicherzelle (Adresse): 10125
- Variable `iptr`:
  - Name: “iptr”
  - Wert: 10125
  - Speicherzelle (Adresse): 27890

### Pointer sind besondere Variablen

<div align="center">

Der Wert eines Pointers wird als **Adresse** im Speicher behandelt

</div>

Der Wert von `iptr` ist nicht ein beliebiger Integer, sondern eine
Adresse. In diesem Fall handelt es sich um die Adresse im virtuellen
Speicher, wo die Variable `i` abgelegt ist.

Wirkung/Interpretation: Variable `iptr` “zeigt” auf die Adresse von
Variable `i`.

### Pointer und Adressen (Syntax)

- Deklaration

  ``` c
  Typ * Name;
  ```

- Zuweisung einer Adresse über den `&`-Operator:

  ``` c
  int i = 99;
  int *iptr;

  iptr = &i;  /* Wert von iptr ist gleich Adresse von i */
  ```

- `iptr` ist ein Pointer auf eine (beliebige) Speicherzelle mit Inhalt
  vom Typ `int`

- Nach Zuweisung: `iptr` ist ein Pointer auf die Speicherzelle der
  Variablen `i`

### Dereferenzierung: Zugriff auf Ziel

- Dereferenzierung mit `*`:

  ``` c
  int i = 99;
  int *iptr;

  iptr = &i;

  *iptr = 2;  // Zugriff auf verwiesene Speicherzelle i
  ```

### Pointer: Schreibweisen

- Position des `*` zwischen Typ und Name beliebig

  ``` c
  /* aequivalente Schreibweisen */
  int* iptr;
  int * iptr;
  int *iptr;
  ```

  ``` c
  /* Vorsicht Mehrfachdeklaration */
  int* iptr, ptr2;      /* ptr2 ist nur ein int! */
  ```

- Dereferenzierung von Pointern auf Klassen/Structs: Operator `->`

  ``` c
  /* aequivalente Schreibweisen */
  (*iptr).attribut;
  iptr->attribut;
  ```

### Pointer: Zuweisungen an andere Pointer

``` c
int i=99, *iptr, *ptr2;

iptr = &i;

ptr2 = iptr;

*ptr2 = 2;
```

Jetzt zeigen zwei Pointer auf die Speicherzelle von Variable `i`: `iptr`
(wegen `iptr = &i`), und weil der Wert von `iptr` in `ptr2` kopiert
wurde (`ptr2 = iptr`), zeigt nun auch `ptr2` auf `i`.

Der Wert von `iptr` ist die Adresse von `i`. Wenn dieser Wert kopiert
oder zugewiesen wird, ändert sich an dieser Adresse nichts. `ptr2`
bekommt diesen Wert zugewiesen, d.h. bei einer Dereferenzierung von
`ptr2` würde auf die Adresse von `i` zugriffen werden und dort
gelesen/geschrieben werden.

### Pointer und Scopes

<div align="center">

**Nicht auf Variablen außerhalb ihres Scopes zugreifen!**

</div>

``` c
int i=9;
int *ip = &i;

*ip = 8;
{  /* neuer Block */
    int j=7;
    ip = &j;
}
*ip = 5;  /* AUTSCH!!! */
```

``` c
int* murks() {
    int i=99;
    return &i;  /* AUTSCH!!! */
}
```

### Hotelzimmer-Analogie

- Wenn Sie in ein Hotel einchecken, bekommen Sie den Schlüssel zu
  **Ihrem** Zimmer
  - *Pointer* == Schlüssel
  - *Variable auf die Pointer zeigt* == Zimmer
- Wenn Sie auschecken, geben Sie normalerweise Ihr Zimmer auf und den
  Schlüssel ab
  - Pointer wird ungültig
  - Variable wird ungültig
- Wenn Sie beim Auschecken den Schlüssel nicht abgeben, gehört das
  Zimmer dennoch nicht mehr Ihnen
  - Sie haben noch den Pointer
  - Die Variable, auf die der Pointer zeigt, ist ungültig
- Wenn Sie jetzt auf das Zimmer gehen, kommen Sie (evtl.) noch rein
  - Evtl. ist das Zimmer noch nicht wieder belegt, und Sie finden Ihr
    vergessenes Handy
  - Bei Dereferenzierung erhalten Sie noch den alten Wert der Variablen
    - Evtl. wurde das Zimmer bereits wieder vergeben =\> Sie “brechen”
      bei einem Fremden ein!
    - Bei Dereferenzierung greifen Sie auf “fremde” Variablen
      (Speicherbereiche) zu!

### Pointer und Initialisierung

<div align="center">

Pointer werden vom Compiler **nicht** initialisiert!

</div>

- Zeigen ohne explizite Initialisierung auf **zufällige** Adresse
- Dereferenzierung uninitialisierter Pointer problematisch

**Explizite Null-Pointer**:

- Wert 0 zuweisen
- Besser: Symbolische Konstante `NULL` aus `stdio.h` bzw. `cstdio` bzw.
  in C++ `nullptr`

## Speicherverwaltung

- C: **Funktionen** zur Verwaltung dynamischen Speichers: `malloc()`,
  `free()`, … (in `<stdlib.h>`)

  ``` c
  void* malloc(size_t size)
  ```

  - Alloziert `size` Bytes auf dem Heap und liefert Adresse zurück
  - Pointer auf `void`, da Typ unbekannt - vor Nutzung auf korrekten Typ
    umcasten
  - Im Fehlerfall wird ein Null-Pointer zurückgeliefert: `NULL`
  - Achtung: Speicher ist nicht initialisiert!

  ``` c
  int *p = (int*) malloc(sizeof(int));
  int *pa = (int*) malloc(4*sizeof(int));

  free(p);
  free(pa);
  ```

- C++: **Operatoren**: `new` und `delete`

  - Direkte Angabe des Zieltyps
  - Rückgabe eines Pointers auf diesen Typ
  - Exception, wenn kein Speicher verfügbar
  - Form mit `[]`-Operator für Arrays
  - Mit `new` allozierter Speicher muss mit `delete` freigegeben werden
  - Mit `new []` allozierter Speicher muss mit `delete []` freigegeben
    werden

  ``` cpp
  int *p = new int;
  int *pa = new int[4];

  delete p;
  delete [] pa;
  ```

### Speicher allozieren: Standardidiom

In C müssen Sie die Rückgabe von `malloc` prüfen:

``` c
int *i, *x;

i = (int *) malloc(sizeof(int));
x = (int *) malloc(sizeof(*x));   /* Stern wichtig */

if (!i) {
    /* Fehlerbehandlung */
} else {
    /* mach was */
}
```

In C++ bekommen Sie eine Exception, falls `new` nicht erfolgreich war:

``` cpp
int *i;

try {
    i = new int;
    /* mach was */
} catch (...) { /* Fehlerbehandlung */ }
```

*Hinweis*: Pointer-Variablen `i` und `x` liegen auf Stack, angeforderter
Speicher im Heap!

### Pointer und Typen

- Typ eines Zeigers relevant, wird vom Compiler geprüft
- Zuweisung ohne expliziten Cast nur an allgemeinere Typen/Oberklassen
  - Jeder Zeiger auf Typ `T` kann automatisch zum `void`-Pointer
    konvertiert werden

  - Für Zuweisung von `void`-Pointern an Pointer auf Typ `T` expliziter
    Cast nach `T*` nötig (siehe auch nachfolgenden Hinweis zu C11)

    ``` c
    char *cp;
    void *vp;

    vp = cp;          /* OK */
    cp = vp;          /* problematisch */
    cp = (char *) vp; /* OK */
    ```

### Fallstricke dynamischer Speicherverwaltung

#### Nur new und delete kombinieren bzw. malloc und free

- `delete` darf nur auf mit `new` erzeugte Objekte angewendet werden
  - Vorsicht bei Pointern auf Stack-Variablen!
  - **NIE** mischen mit `malloc()`/`calloc()`/`free()`!

  ``` cpp
  int *p = (int *) malloc(sizeof(int));
  delete p;  // FEHLER! Absturzgefahr
  ```

#### delete\[\] genau nur bei new\[\]

- `delete[]` darf nur auf mit `new[]` erzeugte Objekte angewendet werden
  (und **muss** dort auch angewendet werden)

  `delete` auf mit `new[]` erzeugtes Array würde nur erstes Element
  freigeben!

#### Vorsicht mit Pointern auf lokale Variablen

- Funktioniert technisch, ist aber gefährlich:

  ``` c
  int* murks() {
      int i=99;
      return &i;  /* SO NICHT: Pointer auf lokale Variable! */
  }
  ```

- Etwas besser:

  ``` c
  int* wenigerMurks() {
      int *p = (int *) malloc(sizeof(int)); /* neuer Speicher */
      *p=99;
      return p; /* das geht */
  }
  ```

<p align="right">Warum nur “etwas besser”?</p>

Jetzt haben Sie aber ein neues Problem: Der Aufrufer der Funktion muss
wissen, dass diese Speicher alloziert und muss sich selbst um die
Freigabe kümmern. Dies ist unschön, da die Allokation und Freigabe in
unterschiedlicher Verantwortung liegen! Dadurch können sehr schnell
Fehler passieren.

Besser wäre, wenn der Aufrufer einen Pointer übergibt, mit dem dann in
der Funktion gearbeitet wird. Dann liegt die Verantwortung für die
Erstellung und Freigabe des Pointers komplett in der Hand des Aufrufers.

#### Memory Leaks

- Pointer-Variablen unterliegen den Gültigkeitsregeln für Variablen

- Mit `malloc()` reservierter Speicher existiert bis Programmende

  ``` c
  {
      int *i;
      i = (int *) malloc(sizeof(*i));
      *i = 99;
  }
  /* hier existiert die Variable i nicht mehr */
  /* aber der Speicher auf dem Heap bleibt belegt */
  /* ist aber nicht mehr zugreifbar -> SPEICHERLOCH! */
  ```

#### Double Free und Stale Pointer

- `free()` darf nur einmal pro Objekt aufgerufen werden
  - Hintergrund: Intern wird eine Freispeicherliste verwaltet
- Nach `free()` ist der Zeiger undefiniert:
  - Zeigt immer noch in den Heap (alte Adresse!)
  - Ist nicht gleich `NULL` oder 0
  - Zugriff ist möglich, aber gefährlich: Speicher kann wieder vergeben
    und überschrieben werden (*Hotelzimmer-Analogie*)
- Mehrere Pointer auf ein Objekt: Einmal `free()` reicht!
  - Die anderen Pointer dürfen anschließend aber auch nicht mehr
    dereferenziert werden (stale/dangling pointer)

#### Beispiel Stale Pointer

``` c
    int *i, *k; i = (int *) malloc(sizeof(*i)); k = i;

    free(i);
    free(i); /* EINMAL reicht! */
    *k = 42; /* Speicher ist bereits frei - stale pointer */
    free(k); /* Speicher ist bereits frei - double free */
    *i = 99; /* Speicher ist bereits frei */
```

*Anmerkung*: Anwendung auf `NULL`-Pointer bewirkt nichts und ist
unschädlich

#### Dereferenzieren von “Bad Pointern”

Der klassische Scanf-Bug :)

``` c
int i;
scanf("%d", i);
```

<details>

Tipp: `i` ist **kein** Pointer :)

</details>

#### Auslesen von nicht-initialisiertem Speicher

Wenn Programmierer denken, dass irgendwer den Heap zwischendurch immer
mal wieder auf 0 setzt …

``` c
/* return y = Ax */
int *matvec(int **A, int *x, int N) {
    int *y = malloc(N*sizeof(int));
    for (int i=0; i<N; i++) {
        for (int j=0; j<N; j++) {
            y[i] += A[i][j] * x[j];
        }
    }
    return y;
}
```

<details>

Tipp: `y[i] += ...` setzt sinnvolle Werte in `y[i]` voraus …

</details>

#### Überschreiben von Speicher I

Allokation von falschen Größen

``` c
int *p;

p = malloc(N*sizeof(int));

for (int i=0; i<N; i++) {
    p[i] = malloc(M*sizeof(int));
}
```

<details>

Tipp: Jedes `p[i]` kann einen `int` speichern, bekommt aber einen
Pointer zugewiesen (könnte deutlich breiter im Speicher sein als ein
`int`) …

</details>

#### Überschreiben von Speicher II

Indexberechnung kaputt, sogenannte “*off-by-one-errors*”

``` c
int **p;

p = malloc(N*sizeof(int));

for (int i=0; i<=N; i++) {
    p[i] = malloc(M*sizeof(int));
}
```

<details>

Tipp: Hier läuft `i` um einen Platz zu weit …

</details>

#### Überschreiben von Speicher III

Einlesen von Strings, zu kleine Buffer

``` c
char s[8];
gets(s);
```

<details>

Tipp: Wenn hier mehr als 7 Zeichen eingegeben werden, gibt es Probleme
:)

</details>

#### Überschreiben von Speicher IV

Pointerarithmetik falsch verstanden

``` c
int *search(int *p, int val) {
    while (*p && *p != val)
        p += sizeof(int);
    return p;
}
```

<details>

Tipp: Jeder Pointer hat einen Typ, und der Ausdruck “Pointer + 1”
rutscht um so viele Bytes im Speicher weiter, wie der Typ breit ist.
D.h. mit einem “Pointer + 1” gelangt man zum nächsten Element, während
der obige Ausdruck `p += sizeof(int);` um `sizeof(int)` Elemente
weiterspringt!

</details>

## Pointer und Arrays

Ein Array-Name ist wie ein *konstanter* Pointer auf Array-Anfang:
`a[i] == *(a+i)`

Ein **Array-Name** ist nur ein Label, welches der **Adresse des ersten
Array-Elements** entspricht. Die Wirkung ist entsprechend die eines
konstanten Pointers auf den Array-Anfang.

=\> Der Compiler übersetzt Array-Zugriffe per Indexoperator in
Pointerarithmetik: `a[i]` wird zu `*(a+i)` …

Vgl. auch die Diskussion in
[eli.thegreenplace.net/2009/10/21/are-pointers-and-arrays-equivalent-in-c](https://eli.thegreenplace.net/2009/10/21/are-pointers-and-arrays-equivalent-in-c)

``` c
char a[6], c, *cp;

&a[0] == a;
cp = a;

c = a[5];
c = *(a+5);
c = *(cp+5);
c = cp[5];

a = cp;  /* FEHLER */
a = &c;  /* FEHLER */
```

### Iteration durch Arrays (Varianten)

``` c
int a[10], *pa=a;

for (int k=0; k<10; k++)    /* Iteration, Variante 1 */
    printf("%d ", a[k]);

for (int k=0; k<10; k++)    /* Iteration, Variante 2 */
    printf("%d ", *(a+k));

pa = a;
for (int k=0; k<10; k++)    /* Iteration, Variante 3 */
    printf("%d ", *pa++);


/* Iteration, KEINE Variante */
for (int k=0; k<10; k++)
    printf("%d ", *a++);    /* DAS GEHT NICHT */
```

`*pa++`: Operator `++` hat Vorrang vor `*`, ist aber die
Postfix-Variante. D.h. `++` wirkt auf `pa` (und nicht auf `*pa`), aber
zunächst wird für die Ausgabe `*pa` ausgewertet …

`*a++` ist nicht erlaubt, weil dadurch der Name des Arrays (== Adresse
des ersten Array-Elements == konstanter Zeiger auf den Anfang des
Arrays) verändert würde.

### Array-Namen sind wie konstante Pointer

<div align="center">

**Array-Namen können NICHT umgebogen werden!**

</div>

``` c
int a[], *pa=a, k;

/* erlaubt */
a + k;
pa++;

/* VERBOTEN */
a++;
```

### Selbsttest: Was bedeutet was, was ist erlaubt/nicht erlaubt, was kommt raus? Warum?

``` c
int a[10], *pa, *pb, x;
pa = a;    pb = (int*) malloc(sizeof(int));

x = a[1];
x = *(a+1);
x = *(a++);

x = pa[1];
x = *(pa+1);
x = *(pa++);

x = pb[1];
x = *(pb+1);
x = *(pb++);
```

=\> Arrays können wie konstante Pointer behandelt werden.

=\> Pointer dürfen **nicht immer wie Arrays** behandelt werden!
(Syntaktisch zulässig, semantisch normalerweise nicht!)

### Pointerarithmetik: Typen beachten

- Pointer zeigen auf Objekte mit einem bestimmten Typ
- Typen haben unterschiedliche Speicherbreite
- Inkrementierung/Dekrementierung: Pointer zeigt nicht auf nächste
  Speicheradresse, sondern auf die Adresse des nächsten Werts!

``` c
double d[10];
double *d1 = &d[2];
double *d2 = d1;

d2++;

printf("%ld\n", d2-d1);               // ergibt 1
printf("%ld\n", (long)d2 - (long)d1); // double -> zB. 8 Bytes

printf("%ld\n", sizeof(d1));  // Breite Pointervariable
printf("%ld\n", sizeof(*d1)); // Breite Pointerdatentyp
```

## Referenzen in C++

<div align="center">

`Typ & Name = Objekt;`

</div>

``` cpp
int i=2;
int j=9;

int &r=i;    // Referenz: neuer Name fuer i
r=10;        // aendert i: i==10
r=j;         // aendert i: i==9

int &s=r;    // aequivalent zu int &s = i;
```

### Referenzen bilden Alias-Namen

``` c
int i = 99;
int *iptr = &i;

int &iref = i;   // Referenz: neuer Name fuer i
```

            Variable    Speicheraddresse    Inhalt

                                            |          |
                                            +----------+
            i, iref     10125               | 99       |  <--+
                                            +----------+     |
                                            |          |     |
                        ....                 ....            |
                                            |          |     |
                                            +----------+     |
            iptr        27890               | 10125    |  ---+
                                            +----------+
                                            |          |

- Referenz bildet Alias-Namen für ein Objekt
- Objekt hat damit mehrere Namen, über die es ansprechbar ist
- Referenzen in C++ mit Hilfe des `&`-Operators deklarieren

### Eigenschaften von Referenzen in C++

- Referenzen müssen bei Deklaration initialisiert werden
- Referenzen können nicht um-assigned werden
- Referenzen brauchen keinen eigenen Speicherplatz

<!-- -->

- Vorsicht bei gleichzeitiger Deklaration mehrerer Referenzen:

  ``` cpp
  int i=2;
  int j=9;

  int& r=i, s=j;    // SO NICHT!!!
  int &r=i, &s=j;   // korrekt
  ```

### Referenzen als Funktionsparameter

- Signatur:

  ``` cpp
  void fkt(int&, char);
  void fkt(int &a, char b);  // a per Referenz
  ```

- Aufruf: ganz normal (ohne extra `&`) …

  ``` cpp
  int x=3;
  char y='a';
  fkt(x, y);  // x per Referenz
  ```

Im Beispiel werden die Variablen `x` und `y` an die Funktion `fkt`
übergeben. Der erste Parameter wird per Referenz (call-by-reference),
der zweite per Kopie (call-by-value) übergeben.

Der Funktionsparameter `a` bindet sich an `x`, ist eine Referenz auf/für
`x` - jeder Zugriff auf `a` ist wie ein Zugriff auf `x`. Änderungen von
`a` sind also Änderungen von `x`.

Der zweite Parameter bindet sich an den *Wert* von `y`, d.h. `b` hat den
Wert `'a'`. Zwar kann auch `b` verändert werden, das hat dann aber nur
Auswirkungen innerhalb der Funktion und nicht auf die Variable `y` im
äußeren Scope.

## Call-by-Reference Semantik in C++

### Variante A: Pointer (C und C++)

Mit Hilfe von Pointern lässt sich die Call-by-Reference Semantik in C
und in C++ simulieren.

Bei der Übergabe eines Pointers wird der Wert des Pointers *kopiert*
(call-by-value!). Im Inneren der Funktion kann diese Adresse
dereferenziert werden und so auf das außerhalb der Funktion “lebende”
Objekt zugegriffen werden. Damit bekommt man in der Wirkung
call-by-reference.

``` c
void add_5(int *x) {
    *x += 5;
}

int main() {
    int i=0, *ip=&i;

    add_5(ip);

    add_5(&i);
}
```

- Pointer wird nach wie vor per call-by-value übergeben:
  - Wert wird bei Übergabe kopiert (hier Adresse von `i`)
  - Kopierter Wert ist immer noch ein Pointer (hier Pointer auf `i`, da
    Adresse von `i`)
  - Dereferenzierung des kopierten Pointers: Zugriff auf das
    Original-Objekt (hier `i`)

### Variante B: Referenzen (nur C++)

Referenzen müssen bei der Deklaration initialisiert werden und binden
sich an das dabei genutzte Objekt. Sie stellen letztlich lediglich einen
neuen Namen für das Objekt dar.

Bei der Übergabe von Variablen an Referenz-Parameter einer Funktion
binden sich diese Parameter an die übergebenen Objekte. Jeder Zugriff
innerhalb der Funktion auf einen Referenz-Parameter bewirken einen
Zugriff auf das ursprüngliche Objekt.

``` cpp
int add_5(int &x) {
    x += 5;
    return x;
}

int main() {
    int i=0, erg;
    erg = add_5(i);
}
```

- Funktionsparameter `x` ist eine Referenz
- Bei Aufruf der Funktion wird dieser Parameter initialisiert - die
  Referenz `x` bindet sich im Beispiel an die Variable `i`
- Zugriffe auf `x` in der Funktion sind also Zugriffe auf das
  Original-Objekt `i` - `x += 5` ist nichts anderes als `i += 5`
- Bei weiteren Aufrufen wird `x` dann neu gebunden

### Call-by-Reference: const

- Nachteil bei Call-by-Reference:

  Übergebenes Objekt könnte durch die Funktion (unbeabsichtigt)
  verändert werden

- Abhilfe: Deklaration der Parameter als konstant (Schlüsselwort
  `const`):

  ``` cpp
  void fkt(const int&, char);
  void fkt(const int &a, char b);
  // a wird per Referenz uebergeben, darf aber in der Funktion nicht veraendert werden
  ```

=\> `const`-heit ist Bestandteil der Signatur!

<div align="center">

**Arbeiten Sie (wo möglich/sinnvoll) mit (konstanten) Referenzen!**

</div>

### Rückgabe von Werten per Referenz

- Normalerweise per call-by-value (Kopie)
- Mit Referenzen oder Pointern auch als call-by-reference

``` cpp
int &fkt1(const int &, const char *);
int *fkt2(const int &, const char *);
```

- Vorsicht mit lokalen Variablen (Gültigkeit)!

  ``` cpp
  int &fkt1(const int &i, const char *j) {
      int erg = i+1;
      return erg;   // Referenz auf lokale Variable!
  }
  int *fkt2(const int &i, const char *j) {
      int erg = i+2;
      return &erg;  // Pointer auf lokale Variable!
  }
  int main() {
      int &x = fkt1(2, "a");  // AUTSCH!!!
      int *y = fkt2(2, "b");  // AUTSCH!!!
      int  z = fkt1(2, "c");  // OK
  }
  ```

Die Zuweisung `int &x = fkt1(2, "a");` ist syntaktisch erlaubt.
Semantisch aber nicht: Die Referenz `x` bindet sich an das
zurückgelieferte lokale `erg` - dieses existiert aber nicht mehr, da der
Scope von erg beendet ist …

**=\> Nur Pointer auf Speicher zurückliefern, der nach Beendigung des
Funtionsaufrufes noch existiert!** (Dies könnte beispielsweise Speicher
aus `malloc` oder `new` oder ein Pointer auf das eigene Objekt (`*this`)
sein.)

Die Zuweisung `int *y = fkt2(2, "b");` ist syntaktisch erlaubt.
Semantisch aber nicht: Der Pointer `y` übernimmt die zurückgelieferte
Adresse des lokalen `erg` - dieses existiert aber nicht mehr, da der
Scope von erg beendet ist …

**=\> Nur Referenzen zurückliefern, die nach Beendigung des
Funtionsaufrufes noch gültig sind!** (Dies könnten beispielsweise
Referenz-Inputparameter oder eine Referenz auf das eigene Objekt
(`*this`) sein.)

Die Zuweisung `int  z = fkt1(2, "c");` ist unbedenklich, da `z` eine
normale Integervariable ist und hier das übliche Kopieren der Rückgabe
von `ftk1` in die Variable stattfindet.

### Diskussion

In C++ können Sie Call-by-Reference über Pointer und/oder über
Referenzen erreichen.

In den obigen Beispielen wurde dies für die Parameter einer Funktion
gezeigt - es sind aber auch Pointer und/oder Referenzen als
Rückgabetypen möglich. Beachten Sie dabei, ob das jeweils wirklich Sinn
ergibt! Eine Referenz oder ein Pointer auf eine lokale Variable ist eine
große Fehlerquelle.

In C++ werden Referenzen über Pointer bevorzugt. Wenn Sie die Wahl
zwischen den beiden Signaturen `bar foo(wuppie&,  bar)` und
`bar foo(wuppie*,  bar)` haben, sollten Sie sich für
`bar foo(wuppie&,  bar)` entscheiden.

## Vergleich Pointer mit Referenzen

| Referenzen | Pointer |
|:---|:---|
| Alias-Name für Objekte/Variablen, kein eigener Speicherplatz | “Echte” Variablen mit eigenem Speicherplatz (für den Wert des Pointers) |
| Können nicht auf andere Objekte “umgebogen” werden | Können auf andere Objekte zeigen (falls nicht const) |
| Operationen agieren direkt auf dem referenzierten Objekt | Operationen auf referenzierten Objekt als auch auf dem Pointer selbst |
| Nur in C++ | In C und in C++ |
|  | Mit Pointern ist dynamische Speicherverwaltung möglich: Manipulation von Speicherbereichen im Heap |

## Wrap-Up

- Virtueller Speicher: Kernel stellt Prozessen linearen Adressraum
  bereit, Segmente: Text, Stack, Heap

<!-- -->

- Pointer sind Variablen, deren **Wert als Adresse** interpretiert wird
  - Deklaration mit `*` zwischen Typ und Name
  - Adressoperator `&` liefert die Adresse eines Objekts
  - Dereferenzierung eines Pointers mit `*` vor dem Namen

<!-- -->

- Verwandtschaft zw. Arrays und Pointern: Array-Name ist konstanter
  Pointer auf Array-Anfang
- Pointer haben Typ: Pointerarithmetik berücksichtigt Speicherbreite des
  Typs

<!-- -->

- C++-Referenzen als Alias-Namen für ein Objekt
  - Deklaration: `Typ &ref = obj;`
  - Fest mit Objekt verbunden
  - Zugriff auf Referenz: Direkter Zugriff auf das Objekt

## 📖 Zum Nachlesen

- Breymann ([2011](#ref-Breymann2011))
- „C and C++ Reference“ ([o. J.](#ref-cppreference.com))
- Allain und Hoffer ([o. J.](#ref-cprogramming.com))

> [!NOTE]
>
> <details>
>
> <summary><strong>✅ Lernziele</strong></summary>
>
> - k1: Ich kenne das Konzept des virtuellen Speichers und die
>   Aufteilung in die Segmente: Text, Data, Stack
> - k2: Ich kann an einem Beispiel erklären, dass Pointer Variablen
>   sind, deren Wert wird als Adresse interpretiert wird
> - k2: Ich kann die Initialisierung und Scopes bei Pointern erklären
> - k3: Ich kann in meinem Programm eine Adresse an einen Pointer
>   zuweisen
> - k3: Ich kann in meinem Programm Pointer dereferenzieren und auf das
>   referenzierte Element zugreifen
> - k3: Ich kann Pointer als Funktionsparameter nutzen und so
>   Call-by-Reference realisieren
> - k2: Ich kann die Problematik der Memory Leaks und Stale Pointer
>   erklären und weiss, wie ich diese vermeide
> - k3: Ich kann die C++-Operatoren `new` und `delete` praktisch
>   einsetzen und ich kann den Unterschied zu `malloc()` und `free()`
>   erklären
> - k3: Ich kann Referenzen in C++ einsetzen (Deklaration,
>   Initialisierung, Nutzung)
> - k2: Ich kann den Zusammenhang und den Unterschied zwischen Pointer
>   und Arrays erklären
> - k3: Ich kann mit Pointern rechnen und dabei den Typ berücksichtigen
>
> </details>

> [!TIP]
>
> <details>
>
> <summary><strong>🏅 Challenges</strong></summary>
>
> **Pointer**
>
> - Erklären Sie das Problem bei folgender Deklaration:
>   `int* xptr, yptr;`
>
> - Seien `p1` und `p2` Pointer auf `int`. Was ist der Unterschied
>   zwischen den beiden Code-Zeilen?
>
>   ``` c
>   p2  = p1;
>   *p2 = *p1;
>   ```
>
> - Ist `*&x` immer identisch mit `x`?
>
> - Ist `&*x` immer identisch mit `x`?
>
> - Wann kann die Funktion `void f(int*)` so aufgerufen werden:
>   `f(&x);`?
>
> **Swap …**
>
> - Warum funktioniert die folgende `swap()`-Funktion nicht? Wie müsste
>   sie korrigiert werden?
>
>   ``` c
>   void swap(int x, int y) {
>       int tmp; tmp=x; x=y; y=tmp;
>   }
>   ```
>
> - Was ist mit dieser Version dieser `swap()`-Funktion?
>
>   ``` c
>   void swap(int *x, int *y) {
>       int *tmp;
>       tmp=x; x=y; y=tmp;
>   }
>   ```
>
> **C++: new und delete**
>
> Betrachten Sie folgende Code-Schnipsel. Erklären Sie die Wirkung der
> jeweiligen Anweisungen.
>
> ``` cpp
> void fkt() {
>     char *cp = new char[100];
>     cp[0] = 'a';
> }
> ```
>
> ``` cpp
> int i=10;
> int *p = &i;
> delete p;
> ```
>
> ``` cpp
> char *p;
> {
>     char *cp = new char[100];
>     p = cp;
>     free(cp);
> }
> delete p;
> ```
>
> **Referenzen vs. Pointer: Welche der Aufrufe sind zulässig?**
>
> ``` cpp
> void f1(int*);
> void f2(int&);
>
> int main() {
>     int i=0, *ip=&i, &ir=i;
>
>     f1(i);      f1(&i);     f1(*i);
>     f1(ip);     f1(&ip);    f1(*ip);
>     f1(ir);     f1(&ir);    f1(*ir);
>
>     f2(i);      f2(&i);     f2(*i);
>     f2(ip);     f2(&ip);    f2(*ip);
>     f2(ir);     f2(&ir);    f2(*ir);
> }
> ```
>
> **C++-Referenzen und Pointer**
>
> Betrachten Sie folgende Code-Schnipsel. Erklären Sie die Wirkung der
> jeweiligen Anweisungen.
>
> ``` cpp
> int x=5, &y=x;
> int *ptr1 = &x;
> int *ptr2 = &y;
>
> *ptr1 += 1;
> *ptr1++;
>
> ptr2  = ptr1;
> *ptr2 = *ptr1;
>
> ptr1  == ptr2;
> *ptr1 == *ptr2;
> ```
>
> **Fallstricke mit C++-Referenzen**
>
> Betrachten Sie folgende Code-Ausschnitte. Welchen Wert haben die
> Variablen nach der Ausführung? Begründen Sie Ihre Antwort.
>
> ``` cpp
> int i=2, j=9;
> int &r=i, &s=r;
> s=200;
> ```
>
> ``` cpp
> int &versuch(int i, int j) {
>     int erg = i+j;
>     return erg;
> }
> int main() {
>     int &z = versuch(2, 10);
>     return 0;
> }
> ```
>
> **Referenzen in C++**
>
> Betrachten Sie folgende Code-Ausschnitte (C++). Erklären Sie, ob sich
> dort Fehler verstecken und falls ja, wie diese zu beheben wären.
>
> 1.  Versuch
>
>     ``` cpp
>     int &versuch(int&, int&);
>
>     int main() {
>         int a=10, b=20;
>         int &z = versuch(a, b);
>         return 0;
>     }
>
>     int &versuch(int &i, int &j) {
>         int &erg = i+j;
>         return erg;
>     }
>     ```
>
> 2.  Versuch
>
>     ``` cpp
>     int &versuch(int&, int&);
>
>     int main() {
>         int a=10, b=20;
>         int &z = versuch(a, b);
>         return 0;
>     }
>
>     int &versuch(int &i, int &j) {
>         int erg = i+j;
>         return erg;
>     }
>     ```
>
> 3.  Versuch
>
>     ``` cpp
>     int &versuch(int&, int&);
>
>     int main() {
>         int a=10, b=20;
>         int &z = versuch(a, 10);
>         return 0;
>     }
>
>     int &versuch(int &i, int &j) {
>         j += i;
>         return j;
>     }
>     ```
>
> **Pointer und Arrays**
>
> - Erklären Sie die Unterschiede folgender Anweisungen. Welche sind
>   erlaubt, welche nicht? Welche führen möglicherweise zu Fehlern?
>
>   ``` cpp
>   int a[10], *pa, *pb, x;
>   pa = a;
>   pb = new int;
>
>   x = a[1];
>   x = *(a+1);
>   x = *(a++);
>
>   x = pa[1];
>   x = *(pa+1);
>   x = *(pa++);
>
>   x = pb[1];
>   x = *(pb+1);
>   x = *(pb++);
>   ```
>
> **Typ eines Pointers bei Adressarithmetik**
>
> - Was ist der Unterschied zwischen den beiden folgenden Statements?
>
>   ``` c
>   ((char *)ptr)+1
>   ((double *)ptr)+1
>   ```
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
> <div id="ref-cprogramming.com" class="csl-entry">
>
> Allain, A., und A. Hoffer. o. J. „C Programming and C++ Programming“.
> Zugegriffen 16. September 2024. <https://www.cprogramming.com/>.
>
> </div>
>
> <div id="ref-Breymann2011" class="csl-entry">
>
> Breymann, U. 2011. *Der C++ Programmierer*. 2. Auflage. Hanser.
>
> </div>
>
> <div id="ref-cppreference.com" class="csl-entry">
>
> „C and C++ Reference“. o. J. cppreference.com. Zugegriffen 16.
> September 2024. <https://en.cppreference.com/w/>.
>
> </div>
>
> </div>
>
> </details>

------------------------------------------------------------------------

<img src="https://licensebuttons.net/l/by-sa/4.0/88x31.png" width="10%">

Unless otherwise noted, this work is licensed under CC BY-SA 4.0.

<blockquote><p><sup><sub><strong>Last modified:</strong> ce939b8 (lecture: rework outcomes (09/CPP1), 2025-08-21)<br></sub></sup></p></blockquote>
