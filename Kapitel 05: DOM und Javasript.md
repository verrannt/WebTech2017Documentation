# Kapitel 5: DOM und Javascript

1. [Variablen und Typen](#variablen-und-tpyen)
  1. [Number](#number)
  2. [String](#string)
  3. [Boolean](#boolean)
  4. [Variablen](#variablen)
  5. [Arrays](#arrays)
  6. [Reguläre Ausdrücke](#reguläre-ausdrücke)
2. [Statements und Operatoren](#statements-und-operatoren)

---

## Variablen und Typen

### Number

JavaScript besitzt nur einen Zahlen-Datentyp. Es gibt lediglich 64-Bit-Gleitkommazahlen (`double`) gemäß _IEEE 754_, aber keinen Integer-Datentyp.
Damit werden zahlreiche Probleme wie Unter- und Überläufe vermieden und der Umgang mit Zahlen vereinfacht.

Selbstverständlich ergeben sich dadurch auch Probleme. Zwei typische Beispiele:

``` 
0.1 + 0.2 == 0.30000000000000004
 
9007199254740992 + 1 == 9007199254740992
```
In den meisten Anwendungen spielt das allerdings keine Bedeutung oder kann einfach vermieden werden.

Auf die üblichen mathematischen Funktionen kann mit Hilfe des `Math`-Objekts zugegriffen werden:

[`abs()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/abs)
[`ceil()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/ceil)
[`floor()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/floor)
[`round()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/round)
[`exp()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/exp)
[`log()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/log)
[`sqrt()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/sqrt)
[`pow()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/pow)
[`acos()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/acos)
[`asin()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/asin)
[`atan()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/atan)
[`atan2()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/atan2)
[`cos()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/cos)
[`sin()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/sin)
[`tan()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/tan)
[`max()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max)
[`min()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min)
[`random()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random)
[`E`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/E) 
[`LN10`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/LN10)
[`LN2`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/LN2)
[`LOG10E`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/LOG10E) 
[`LOG2E`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/LOG2E)
[`PI`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/PI)
[`SQRT1_2`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/SQRT1_2)
[`SQRT2`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/SQRT2)

```
// Beispiel
var rounded = Math.round(1.5);
```
[Zurück nach oben](#kapitel-5-dom-und-javascript)
----
### String

Zeichenketten sind vom Datentyp String und können einfach als String-Literale angegeben werden:

 
`var my_string = "Hello World";`

Strings sind unveränderbar, können mit einfachen oder doppelten Anführungszeichen umschloßen und mit den Operatoren `==` und `===` verglichen werden.

 
`"foo" === 'foo'`

Es gibt keinen gesonderten Datentyp für einzelne Buchstaben.

Um eine Zeichenkette in eine Zahl zu verwandeln, verwendet man parseInt. Der zweite Parameter gibt optional die Basis an. Früher führten
Werte wie "010" zu einer Behandlung als Oktal-Zahl - das ist inzwischen in aktuellen Browsern aber nicht mehr der Fal.

``` 
// convert string to number
parseInt("17")
> 17
parseInt("010")
> 10
```

Zahlen können mit Hilfe der Methode toString in Strings umgewandelt werden:

``` 
var num = 23;
num.toString() === "23"
```
Zeichenketten werden intern als _UCS-2_ verarbeitet. Das entspricht nicht ganz _UTF-16_, da es keine 4-Byte-Codes 
(Beispiel Notenschlüssel: `D834 DD1E`) erkennt.

Die Länge einer Zeichenkette kann mit der Objekteigenschaft `length` ausgelesen werden:
```
// but: wrong w/ surrogate pairs
"23".length === 2
```
Weitere Methoden von Strings:

[`charAt()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/charAt) 
[`charCodeAt()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt)
[`concat()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/concat)
[`indexOf()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf)
[`lastIndexOf()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/lastIndexOf)
[`localeCompare()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)
[`match()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/match)
[`replace()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
[`search()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/search)
[`slice()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/slice)
[`split()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/split)
[`substr()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/substr)
[`substring()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/substring)
[`toLowerCase()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase)
[`toString()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/toString)
[`toUpperCase()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase)
[`valueOf()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/String/valueOf)
 
[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Boolean

Vom Datentyp Boolean gibt es nur zwei Objekte: die Literale `true` und `false`.

Die später beschriebenen Statements und Operatoren verwenden darüber hinaus ein Konzept, nach dem ein Ausdruck _truthy_ oder _falsy_ ist. So wird
beispielsweise der then-Block eines `if`-Statements nur von Ausdrücken ausgelöst, die _truthy_ sind.

Die folgenden Werte gelten als _falsy_:

``` 
false
null
undefined
""
0
NaN
```
Alle anderen Werte sind _truthy_ (also zum Beispiel der String `"0"`).

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Variablen

Neue Variablen werden mit dem `var`-Statement definiert und optional initialisiert. Uninitialisierte Variablen haben stets den Wert `undefined`.

``` 
var name = "hoge";
var a;

a === undefined
```

`undefined` ist der primitive Wert, der verwendet wird, wenn einer Variablen kein Wert zugewiesen wurde.

`null` ist der primitive Wert, der für das absichtliche Weglassen eines Objektwerts steht.

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Arrays

JavaScript hat natürlich auch Arrays, die besonders einfach mit Array-Literalen erzeugt werden können:

``` 
var cities = ["Adrilankha",
              "Northport",
              "Candletown"];
cities[1] === "Northport"
```
Arrays sind Objekte und funktionieren nicht wie übliche Arrays in Java oder C. Beim Auslesen und Setzen werden die Indizes in Strings
verwandelt und als Objekteigenschaft behandelt. Für spärlich besetzte Arrays funktioniert das gut. In allen anderen Fällen ist die
Performance nicht besonders gut.

Arrays haben eine besondere Objekteigenschaft length. Diese entspricht immer dem höchsten Index + 1:

 
`var anArray = ["foo"];`
`anArray[100] = "bar";`
`anArray.length === 101`

Um Werte an ein Array anzuhängen kann man das gut verwenden:

 
`cities[cities.length] = "Fenario";`

Zum Iterieren über ein Array verwendet man die übliche for-Schleife:

 
```
for (var i = 0; i < cities.length; i += 1) {
    doIt(a[i]);
}
```
Man kann Elemente aus einem Array löschen. Je nach Art und Weise bleiben dabei Löcher zurück oder aber die nachfolgenden Elemente werden neu
nummeriert:

 
```
// w/ holes
delete array[number]
// w/o holes
array.splice(number, 1)
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Reguläre Ausdrücke

Reguläre Ausdrücke können mit entsprechenden Literalen hergestellt werden:

 
`var number = /^-?\d+$/i;`

Sie verhalten sich so, wie man es aus Python schon kennt. Ein paar Beispiele:

 
```
var header = "Content-Type: text/html";
result = /^(.*): (.*)$/.exec(header)
result === [ "Content-Type: text/html", "Content-Type", "text/html" ]
```
 
```
var header = "Content-Type: text/html";
result = /^(.*): (.*)$/.test(header);
result === true
```
 
```
var header = "Content-Type: text/html";
result = header.match(/^(.*): (.*)$/);
result === [ "Content-Type: text/html", "Content-Type", "text/html" ]
```
 
```
var header = "Content-Type: text/html";
result = header.replace(/^(.*): (.*)$/, "$1=$2");
result === "Content-Type=text/html"
```
 
```
var header = "Content-Type: text/html";
result = header.search(/: /);
result === 12
```

```
var header = "Content-Type: text/html";
result = header.split(/: /);
result === [ "Content-Type", "text/html" ]
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

## Statements und Operatoren

### Operatoren

Die arithmetischen Operatoren entsprechen den üblicherweise verwendeten:

```
+ - * / %
```
Es gibt zwei Sätze von Vergleichoperatoren:

```
=== !===
== !=
< <= > >=
```
Zuweisung geschieht über den bekannten Zuweisungsoperator `=`. Zusätzlich gibt es noch kombinierte Zuweisungsoperatoren:

```
+= -= *= /= %=
```
Auch die logischen Operatoren (und, oder, nicht) sollten bekannt sein:

```
&& || !
```
Zur bitweisen Verknüpfung von Werten existieren:

```
& | ^ >> >>> <<
```
Und der ternäre Operator darf ebenfalls nicht fehlen:

```
true ? "hoge" : "fuga"
Zum Inkrementieren und Dekrementieren gibt es diese Operatoren (jeweils als Prä- und Postfixoperator):
```

```
++fuga --fuga
hoge++ hoge--
```
Zeichenketten können mit `+` konkateniert werden:

```
"foo" + "bar"
```
Leider wird dieser Operator auch schon für die Addition von Zahlen verwendet. Handelt es sich bei beiden Operanden um Zahlen, wird addiert.
Ansonsten werden die Operanden implizit in Strings umgewandelt und konkateniert.

```
1 + 2 === 3
"¥" + 4 + 2 === "¥42"
```
Da es sich bei den Zahlen um Gleitkommazahlen handelt, verwundert es nicht, dass folgender Ausdruck wieder eine Gleitkommazahl liefert
(anders als etwa in Java).

```
23 / 13 !== 1
```
Die Vergleichoperatoren `==` und `!=` verwenden implizite Typumwandlung (_type coercion_), deren Regeln weder einsichtig noch leicht erlernbar
wären:

```
   '' == '0'       // false
    0 == ''        // true
    0 == '0'       // true
false == 'false'   // false
false == '0'       // true
false == undefined // false
false == null      // false
 null == undefined // true
```
Verwendet man hingegen `===` und `!==` wird keine implizite Typumwandlung vorgenommen. So erspart man sich böse Überraschungen.

Zu guter letzt gibt es noch den `typeof`-Operator, der den Datentyp des Operanden zurückliefert. Auch hier gibt es wieder kleine
Überraschungen:

```
typeof aNumber     === 'number'
typeof aString     === 'string'
typeof aBoolean    === 'boolean'
typeof aFunction   === 'function'
typeof anObject    === 'object'
typeof anArray     === 'object'    // !!!
typeof aNull       === 'object'    // !!!
typeof anUndefined === 'undefined'
```
[Zurück nach oben](#kapitel-5-dom-und-javascript)
