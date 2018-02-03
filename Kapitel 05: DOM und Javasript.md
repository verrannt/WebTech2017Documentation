# Kapitel 5: DOM und Javascript

* [Variablen und Typen](#variablen-und-typen)
  * [Number](#number)
  * [String](#string)
  * [Boolean](#boolean)
  * [Variablen](#variablen)
  * [Arrays](#arrays)
  * [Reguläre Ausdrücke](#reguläre-ausdrücke)
* [Statements und Operatoren](#statements-und-operatoren)
  * [Operatoren](#operatoren)
  * [Statements](#statements)
* [Objektorientierung](#objektorientierung)
  * [Objekte](#objekte)
* [Funktionen, Scope und Closures](#funktionen,-scope-und-closures)
  * [Funktionen](#funktionen)
  * [Scope](#scope)
  * [Closures](#closures)
---

## Variablen und Typen

### Number

JavaScript besitzt nur einen Zahlen-Datentyp. Es gibt lediglich 64-Bit-Gleitkommazahlen (`double`) gemäß _IEEE 754_, aber keinen Integer-Datentyp.
Damit werden zahlreiche Probleme wie Unter- und Überläufe vermieden und der Umgang mit Zahlen vereinfacht.

Selbstverständlich ergeben sich dadurch auch Probleme. Zwei typische Beispiele:

```javascript
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

```javascript
// Beispiel
var rounded = Math.round(1.5);
```
[Zurück nach oben](#kapitel-5-dom-und-javascript)

----
### String

Zeichenketten sind vom Datentyp String und können einfach als String-Literale angegeben werden:
 
```javascript
var my_string = "Hello World";
```

Strings sind unveränderbar, können mit einfachen oder doppelten Anführungszeichen umschloßen und mit den Operatoren `==` und `===` verglichen werden.
 
```javascript
"foo" === 'foo'
```

Es gibt keinen gesonderten Datentyp für einzelne Buchstaben.

Um eine Zeichenkette in eine Zahl zu verwandeln, verwendet man parseInt. Der zweite Parameter gibt optional die Basis an. Früher führten Werte wie "010" zu einer Behandlung als Oktal-Zahl - das ist inzwischen in aktuellen Browsern aber nicht mehr der Fal.

```javascript
// convert string to number
parseInt("17")
> 17
parseInt("010")
> 10
```

Zahlen können mit Hilfe der Methode toString in Strings umgewandelt werden:

```javascript
var num = 23;
num.toString() === "23"
```
Zeichenketten werden intern als _UCS-2_ verarbeitet. Das entspricht nicht ganz _UTF-16_, da es keine 4-Byte-Codes 
(Beispiel Notenschlüssel: `D834 DD1E`) erkennt.

Die Länge einer Zeichenkette kann mit der Objekteigenschaft `length` ausgelesen werden:
```javascript
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

Die folgenden Werte gelten als _false_:

```javascript
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

```javascript
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

```javascript
var cities = ["Adrilankha",
              "Northport",
              "Candletown"];
cities[1] === "Northport"
```
Arrays sind Objekte und funktionieren nicht wie übliche Arrays in Java oder C. Beim Auslesen und Setzen werden die Indizes in Strings
verwandelt und als Objekteigenschaft behandelt. Für spärlich besetzte Arrays funktioniert das gut. In allen anderen Fällen ist die
Performance nicht besonders gut.

Arrays haben eine besondere Objekteigenschaft length. Diese entspricht immer dem höchsten Index + 1:

```javascript
var anArray = ["foo"];
anArray[100] = "bar";
anArray.length === 101
```

Um Werte an ein Array anzuhängen kann man das gut verwenden:

```javascript
cities[cities.length] = "Fenario";
```

Zum Iterieren über ein Array verwendet man die übliche for-Schleife:

```javascript
for (var i = 0; i < cities.length; i += 1) {
    doIt(a[i]);
}
```
Man kann Elemente aus einem Array löschen. Je nach Art und Weise bleiben dabei Löcher zurück oder aber die nachfolgenden Elemente werden neu
nummeriert:

```javascript
// w/ holes
delete array[number]
// w/o holes
array.splice(number, 1)
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Reguläre Ausdrücke

Reguläre Ausdrücke können mit entsprechenden Literalen hergestellt werden:

```javascript
var number = /^-?\d+$/i;
```

Sie verhalten sich so, wie man es aus Python schon kennt. Ein paar Beispiele:

```javascript
var header = "Content-Type: text/html";
result = /^(.*): (.*)$/.exec(header)
result === [ "Content-Type: text/html", "Content-Type", "text/html" ]

var header = "Content-Type: text/html";
result = /^(.*): (.*)$/.test(header);
result === true

var header = "Content-Type: text/html";
result = header.match(/^(.*): (.*)$/);
result === [ "Content-Type: text/html", "Content-Type", "text/html" ]

var header = "Content-Type: text/html";
result = header.replace(/^(.*): (.*)$/, "$1=$2");
result === "Content-Type=text/html"

var header = "Content-Type: text/html";
result = header.search(/: /);
result === 12

var header = "Content-Type: text/html";
result = header.split(/: /);
result === [ "Content-Type", "text/html" ]
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

## Statements und Operatoren

### Operatoren

Die arithmetischen Operatoren entsprechen den üblicherweise verwendeten:

```javascript
+ - * / %
```
Es gibt zwei Sätze von Vergleichoperatoren:

```javascript
=== !===
== !=
< <= > >=
```
Zuweisung geschieht über den bekannten Zuweisungsoperator `=`. Zusätzlich gibt es noch kombinierte Zuweisungsoperatoren:

```javascript
+= -= *= /= %=
```
Auch die logischen Operatoren (und, oder, nicht) sollten bekannt sein:

```javascript
&& || !
```
Zur bitweisen Verknüpfung von Werten existieren:

```javascript
& | ^ >> >>> <<
```
Und der ternäre Operator darf ebenfalls nicht fehlen:

```javascript
true ? "hoge" : "fuga"
```
Zum Inkrementieren und Dekrementieren gibt es diese Operatoren (jeweils als Prä- und Postfixoperator):

```javascript
++fuga --fuga
hoge++ hoge--
```
Zeichenketten können mit `+` konkateniert werden:

```javascript
"foo" + "bar"
```
Leider wird dieser Operator auch schon für die Addition von Zahlen verwendet. Handelt es sich bei beiden Operanden um Zahlen, wird addiert.
Ansonsten werden die Operanden implizit in Strings umgewandelt und konkateniert.

```javascript
1 + 2 === 3
"¥" + 4 + 2 === "¥42"
```
Da es sich bei den Zahlen um Gleitkommazahlen handelt, verwundert es nicht, dass folgender Ausdruck wieder eine Gleitkommazahl liefert
(anders als etwa in Java).

```javascript
23 / 13 !== 1
```
Die Vergleichoperatoren `==` und `!=` verwenden implizite Typumwandlung (_type coercion_), deren Regeln weder einsichtig noch leicht erlernbar
wären:

```javascript
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

```javascript
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

---

### Statements

Die Statements von JavaScript sehen (nahezu) genauso aus, wie in Java (oder C). Erwartet Java aber beispielsweise einen boolschen Wert im Test
eines if-Statements, begnügt sich JavaScript mit beliebigen Ausdrücken, die auf Truthy-ness oder Falsy-ness überprüft werden:

```javascript 
if (name == "foo") {
    //
} else {
    //
}
```
 
```javascript 
switch(1 + 3) {
    case 2 + 2:
        doIt();
        break;
    default:
        neverHappens();
}
```
 
```javascript 
while (true) {
    neverEndingStory();
}
```
 
```javascript 
do {
     matchHere(text);
} while (text = text.substr(1));
```
 
```javascript 
for (i = 0; i < array.length; i += 1
    alert(array[i]);
}
```
Die bekannten Schlüsselworte break, continue und return verhalten sich in JavaScript genauso, wie man das erwarten würde.

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

## Objektorientierung

### Objekte

Objekte sind in JavaScript "collections" dynamischer Eigenschaften. In JavaScript gibt es keine Klassen. Stattdessen stammen Objekte von anderen Objekten ab.

#### Objekt-Literale

Die einfachste Form, zur Erzeugung eines Objekts ist die Verwendung von Objekt-Literalen:

```javascript
var empty = {};
var rascal = {
    "given-name": "Borrah",
    surname:      "Minnevitch"
};
```

#### Zugriff auf Objekt-Eigenschaften

Der Zugriff auf eine Eigenschaft eines Objekts kann mit Punkt- oder Klammernotation erfolgen:

```javascript
// get
rascal.surname
rascal["given-name"]
```
Beide sind gleichwertig, allerdings erlaubt die Klammernotation die Verwendung von Eigenschaftsnamen, die nicht den genannten Regeln für Bezeichner unterliegen. 
Insbesondere können in der Klammernotation beliebige Ausdrücke verwendet werden. 
Objekteigenschaften können natürlich verschachtelt sein, d.h. also wiederum Objekte enthalten.

Das Setzen einer Objekteigenschaft erfolgt analog:

```javascript
// set
rascal.surname = "Fields";
rascal["given-name"] = "George";
```

Objekteigenschaften können mit `delete` gänzlich entfernt werden:

```javascript
// delete
delete rascal.surname;
delete rascal["given-name"];
Object.create()
```
Jedes Objekt ist mit einem anderen Objekt, einem Prototyp-Objekt, verbunden, dessen Eigenschaften es erbt. 
Objekt-Literale erben grundsätzlich von `Object.prototype`. 
Erzeugt man Objekte auf andere Art und Weise, kann ein beliebiges anderes Objekt als Prototyp dienen.

Leider verwirrte die JavaScript-Syntax selbst ein wenig, so dass in _ES5_ eine neue Methode `Object.create` spezifiziert wurde, mit der man ein neues Objekt auf der Basis eines anderen Objekts erzeugen kann.

Probieren wir das mal aus. 
Zunächst ein Objekt:

```javascript
var rascal = {
    "given-name": "Borrah",
    surname: "Minnevitch"
};
```
Nun können wir uns mehr von diesen herstellen:
```javascript
var anotherRascal = Object.create(rascal);

anotherRascal.surname === "Minnevitch"
```
Der Prototyp-Mechanismus wird aber nur beim Auslesen angewendet. 
Beim Schreiben wird die Eigenschaft lediglich im gegebenen Objekts gesetzt (also nicht im Prototyp-Objekt).

```javascript
// updates do not touch prototypes
// prototypes only used for retrieval
var anotherRascal = Object.create(rascal);

anotherRascal["given-name"] = "George";

rascal["given-name"] === "Borrah"
anotherRascal["given-name"] === "George"
```
Beim Auslesen einer Eigenschaft wird beim gegebenen Objekt begonnen. 
Wird die Eigenschaft nicht im Objekt gefunden, wird sie im Prototyp-Objekt gesucht. 
Dieser Schritt wird solange wiederholt, bis man letztlich bei `Object.prototype` endet. 
Wurde die Eigenschaft dann nicht gefunden, ist das Ergebnis `undefined`.

Dieses Konzept heißt _delegating inheritance based on prototypes_.

#### Konstruktoren-Syntax

In der Konstruktorenform werden Objekte ausgehend von einer Konstruktor-Funktion definiert.
Diese Funktion wird mit dem Statement `new` aufgerufen, wodurch ein Objekt erzeugt wird.
Anschließend werden an das Prototyp-Objekt Funktionen angehängt. Das riecht ein wenig nach [Monkey Patching](https://en.wikipedia.org/wiki/Monkey_patch), ist aber in Javascript völlig üblich. 
Auf die gleiche Weise können auch eingebaute Objekte, wie z.B. `Math` oder `Array` um neue Methoden erweitert werden.

```javascript
var Rascal = function (given_name, surname) {
    this["given-name"] = given_name;
    this.surname = surname;
};
Rascal.prototype.fullName = function () {
    return this["given-name"] + " " + this.surname;
};

var rascal = new Rascal("Borrah", "Minnevitch");

rascal.fullName() === "Borrah Minnevitch"
```
JavaScript ist zwar eine prototypbasierte Sprache. 
Die Syntax stammt aber aus einer klassenbasierten Sprache. 
Das macht die Sache etwas verwirrend.

Ruft man eine Funktion mit `new` auf, wird ein neues Objekt angelegt und der Prototyp des Objekt auf die Objekteigenschaft `prototype` der aufgerufenen Funktion gesetzt. 
`this` wird an das neue Objekt gebunden.

#### for-Schleifen über Objekt-Properties

Als eine Besonderheit von JavaScript gibt es noch das `for in`-Statement, dass die Eigenschaften eines Objekts iteriert.
Dabei werden allerdings auch (fast) alle geerbten Eigenschaften durchlaufen.

```javascript
for (name in object) {
    if (object.hasOwnProperty(name))
        doSomething(object[name]);
    }
}
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

## Funktionen, Scope und Closures


### Funktionen
Funktionsausdrücke erstellen Funktionsobjekte:

```javascript
var add = function (x, y) {
    var sum = x + y;
    return sum;
};
```
Funktionen sind `first-class`-Objekte und können daher:

* als Argument beim Funktionsaufruf verwendet werden.
* als Funktionsrückgabewert benutzt werden.
* einer Variablen oder einer Objekteigenschaft zugewiesen werden.
* können Methoden und Eigenschaften haben.
* können (natürlich) aufgerufen werden.

Das `var`-Statement wurde bereits oben verwendet, ohne genau erklärt zu werden. 
Mit dem `var`-Statement werden lokale Variablen deklariert und optional initialisiert. 
Variablen, die in einer Funktion deklariert werden, sind überall in dieser Funktion sichtbar.

```javascript
var foo = function () {

  var a = 3, b = 5;

  var bar = function () {

    var b = 7, c = 11;

    a = a + b + c;

  };

  bar();
};
```

__Wenn man das `var` vergisst, erhält man eine globale Variable. Globale Variablen sind böse.__

Zusätzlich zu den den Funktionsausdrücken gibt es noch Funktions-Statements. 
Diese entsprechen aber weitgehend den Funktionsausdrücken.

```javascript 
function add(x, y) {...}
// (almost) the same as
var add = function (x, y) {...};
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Scope

JavaScript hat keinen Block-Scope wie _Java_ oder _C_, sondern Function-Scope. 
Wie schon gesagt, sind alle lokalen Variablen überall in ihrer Funktion sichtbar (und außerhalb natürlich nicht).

```javascript 
function foo() {
  var bar = 23;
}

foo();
```
```javascript
try {
  result = bar;
} catch (e) {
  result = e.name + ": " + e.message;
}
```
Eine Funktion kann mit Hilfe des `return`-Statements einen Wert zurückgeben:

```javascript
// returns expression
return expression;
```

```javascript
// returns undefined
return;
```
Gibt eine Funktion nichts explizit zurück, ist das Ergebnis eines Funktionsaufrufs `undefined`.

Die Argumente einer Funktion sind nicht bindend, sondern eher _Richtlinien_. 
Fehlende Parameter werden auf `undefined` gesetzt und man kann mehr Argumente als die angegebenen übergeben. 
Diese werden einfach ignoriert.

```javascript
var add = function (x, y) { return x + y; }
add(2, 3, 4) === 5
```
Innerhalb einer Funktion gibt es einen magischen Pseudoparameter `arguments`, der sich ähnlich wie ein Array verhält.

```javascript
var add = function () {
    var sum = 0, i;
    for (i = 0; i < arguments.length; i += 1) {
        sum += arguments[i];
    }
    return sum;
}
add(2, 3, 4, 5) === 14
```

Außerdem ist in jeder Funktion automatisch der Pseudoparameter `this` definiert. 
Dieses `this` enthält eine Referenz zum Objekt des Aufrufs und wird durch die Art des Aufrufs festgelegt. 
Es gibt in JavaScript mehrere Arten, eine Funktion aufzurufen:

In der Funktionsform ist `this` eine Referenz auf das globale Objekt:
```javascript
var func = function () { return this; }
func() === window
```

In der Methodenform ist `this` eine Referenz auf das Objekt, an das die Nachricht gesendet wurde:

```javascript
rascal.func = function () { return this; }
rascal.func() === rascal
```

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

### Closures

Closures sind Funktionen, die beim Aufruf ihren ursprünglichen Kontext reproduzieren können, selbst wenn dieser schon nicht mehr existiert.

In _JavaScript_ können Funktionen in Funktionen erzeugt werden. 
Die _innere_ Funktion kann über ihre gesamte Lebensdauer hinweg auf die Variablen und Parameter der äußeren Funktion zugreifen. 
Selbst wenn diese Funktion schon nicht mehr existiert.

```javascript
var quo = function (status) {
  return {
    get_status: function () {
      return status;
    }
  };
};
var myQuo = quo("amazed");
myQuo.get_status() === "amazed"
```
(aus "_JavaScript: The Good Parts_")

[Zurück nach oben](#kapitel-5-dom-und-javascript)

---

## JSON

_JSON_ (**J**ava**S**cript **O**bject **N**otation) ist ein Datenformat, dass syntaktisch wie Javascript-Datenstrukturen aufgebaut ist. 

JSON kann enthalten:
 
* Zahlen, z.B. `5`, `4.3`
* Strings, z.B. `"Ein String!"`, `'und noch einer'`
* Boolean-Werte, `true` oder `false`
* Arrays, z.B. `[1,2,"toll",true]`, `[1,2,[3,4,[5,6]]]`
* Objekt-Literale, z.B. `{ name: "Tobias", "matrikel-nummer": 988854}`

Grundsätzlich können JSON-Daten als _Javascript_-Code z.B. Variablen zugewiesen werden. 
Häufig stammen die Daten aber aus einer anderen Quelle, z.B. einem _http_-Request (dazu später mehr) und müssen von Strings in Javascript-Datenstrukturen umgewandelt werden. 
Theoretisch wäre das per `eval()` möglich, davon ist aber abzuraten, da ggf. unsichere und aktive Code-Teile eingeschmuggelt werden können. 
Zur Verarbeitung von JSON-Daten eignet sich das [JSON-Objekt](#https://wiki.selfhtml.org/wiki/JSON).

[Zurück nach oben](#kapitel-5-dom-und-javascript)
