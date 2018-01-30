# Kapitel 9: Frontend Entwicklung

* [Javascript](#javascript)
  * 1.1 [Geschichte](#geschichte)
  * 1.2 [Lodash](#lodash)
  * 1.3 [jQuery](#jquery)
* [Code-Abhängigkeiten](#code-abhängigkeiten)
  * [Script-Tags](#script-tags)
  * [Das Module-Pattern](#das-module-pattern)
  * [CommonJS, require.js und AMD-Module](#commonjs-requirejs-und-amd-module)
3. [Build-Prozess (webpack)](#build-prozess-webpack)
  1. [Code-Management und Build-Prozess](#code-management-und-build-prozess)
  2. [Development-Server](#development-server)
  3. [Webpack: Kernkonzepte](#webpack-kernkonzepte)
  4. [Webpack: Entwicklungsworkflow](#webpack-entwicklungsworkflow)
  5. [3rd-Party-Bibliotheken mit Webpack nutzen](#3rd-party-bibliotheken-mit-webpack-nutzen)

---

<div class="table-of-contents">
  <ol>
    <li><a href="#heading">Heading</a>
      <ol>
        <li><a href="#sub-heading-1">Sub heading 1</a></li>
        <li><a href="#sub-heading-2">Sub heading 2</a></li>
      </ol>
    </li>
  </ol>
</div>

## Javascript

### Geschichte

Javascript hat eine über 20-jährige wechselvolle Geschichte hinter sich.
Die wichtigsten Stationen waren:

**1995**: Netscape baut Javascript in den Netscape-Browser (Netscape Navigator 2.0) ein.

**1996**: Microsoft integriert die Variante JScript in den Internet Explorer 3.

**1997**: Versuch der Standardisierung als ECMAScript 1 und 2

**1999**: EcmaScript 3 führt einige Erweiterungen ein (z.B. reguläre Ausdrücke)

**1999**: Browser-Hersteller agieren trotz Standardisierung sehr unterschiedlich und erfinden häufig eigene Sonderlösungen, z.B. Microsoft mit dem XMLHttpRequest-Objekt

**1999**: Die Entwicklung eines Folgestandards ECMAScript 4 (ES 4) beginnt und soll "Programming in the large" ermöglichen.Geplante Features waren z.B.: Klassen, Interfaces, Module, Type Checking.

**2003**: Die Entwicklung von ES 4 scheiterte und wurde 2003 eingestellt. Gerade mit Aufkommen von AJAX-Anwendungen ("Web 2.0") wuchs das Interesse an einer standardisierten Javascript-Erweiterung aber wieder sehr stark. Unter dem Schlagwort "Harmony" wurde nach einem Ausgleich der verschiedenen Interessen gesucht. Zunächst sollten aber als ES 3.1 pragmatische Verbesserungen ohne neue Syntax vorgenommen werden.

**2009**: ES 3.1 ist fertig und wird als ES 5 veröffentlicht. Wichtigste Neuerungen sind JSON-Unterstützung, Strict-Mode, verschiedene Object- und Array-Methoden und vor allem eine in den Folgejahren umgesetzte breite Unterstützung des Standards durch alle Browser.

**2011**: node.js bringt Javascript auf den Server und erlebt sehr schnell sehr großen Zulauf.

**2015**: ES 2015 (a.k.a. ES 6) greift viele der ES-4-Ideen wieder auf und enthält auch Syntax-Änderungen. Durch Transpiler, die ES-6-Code in ES-5-Code übersetzen ist der Standard nutzbar, obwohl er noch nicht von allen Browsern vollständig umgesetzt wird.

**Aktueller Stand**:

- ES 2017 wird mit async/await weitere Konstrukte einführen, mit denen Event-Basierte asynchrone Programmierung übersichtlicher wird.
- CoffeeScript wollte vor allem durch eine andere Syntax (angelehnt an Ruby) übersichtlichere Programme ermöglichen, hat aber mit ES 2015 an Bedeutung verloren.
- TypeScript erweitert ES 2015 um optionale Typ-Annotationen und Type-Checking zur Compile-(Transpile-)Zeit

### Lodash

Die Javascript-Standardbibliothek ist nicht besonders umfangreich. Der Umgang mit eingebauten Datentypen, sowie Funktionen und Objekten ist an vielen Stellen unnötig kompliziert. Die verbreitete Bibliothek underscore.js rüstet einige hilfreiche Methoden nach, die eine modernere und direktere Programmierung ermöglichen.

Alles weitere zu dieser Bibliothek findet sich unter https://lodash.com/.

**Beispiele:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>underscore.js - Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/lodash@4.17.4/lodash.min.js"></script>
</head>
<body>
Alle Ausgaben als Alerts.
<script>
    // Collections

    // Ist 4 in der Collection enthalten?
    alert("_.includes([1,2,3,4,5,6,7,8], 4) = "+_.includes([1,2,3,4,5,6,7,8], 4));
    // Rufe Funktion mit jedem einzelnen Element auf
    _.each([1,2,3,4], alert);
    var persons = [{name:'tobias', age:17}, {name:'hans',age:88}, {name:'woody',age:99}];
    // Extrahiere Eigenschaft name aus den Objekten
    alert(_.map(persons, 'name'));
    // Functions
    // Was ist this?
    var func = function (greeting) {return greeting+": "+this.name};
    // Bindet Funktion an Kontext/Objekt (= belegt this für die Funktion)
    func = _.bind(func, {name: 'Tobias'});
    alert(func("Hallo"));

    var substract = function (a,b) {return b - a; };
    // Bildet partielle Funktion, indem 1. Parameter von substract mit 5 belegt wird
    var sub5 = _.partial(substract, 5);
    alert(sub5(20)); /// sollte 15 sein
</script>
</body>
</html>
```

### jQuery

[jQuery](https://jquery.com) ist die bekannteste und am weisteten verbreitete Javascript-Bibliothek. Sie erleichtert vor allem den Umgang mit der **DOM-API**, bietet einfache Effekte und Animationen und nivelliert Browser-Unterschiede. Mitte der 2000er Jahre waren diese beiden Aspekte noch sehr viel wichtiger als heute, so dass es lange Zeit kaum vorstellbar war, ohne jQuery clientseitig nicht-triviale Programme zu schreiben. Im Wettbewerb mit mehreren anderen Bibliotheken mit vergleichbaren Zielen (z.B. Dojo, Prototype + Scriptaculous) hat sich jQuery wegen der konsistenten API und der hervorragenden Dokumentation durchgesetzt.

Verbreitete Bibliotheken wie jQuery können entweder heruntergeladen und dann vom lokalen Webserver ausgeliefert werden, oder über ein "Content Delivery Network" (CDN) eingebunden werden. Letzteres bietet vor allem den Vorteil, dass evtl. andere Webseiten die Bibliothek schon vorher eingebunden haben und sie daher aus dem Cache ausliefern können, aber ggf. auch datenschutzrechtliche Nachteile, da Cookies mitgeliefert werden seitenübergreifendes Tracking erleichtert wird.

Im Beispielcode wird eine Liste mit Personennamen dargestellt, aus der einzelne Personen gelöscht werden können und zu denen Personen hinzugefügt werden können:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>jQuery - Demo</title>
    <script src="https://code.jquery.com/jquery-3.0.0.min.js"></script>
    <script>
        $(function () {  // DOMContentLoaded
           $(document).on("click", ".del", function () {
               $(this).parent().fadeOut("slow", function () { $(this).remove()});
           });
           $("#newpersonform").on("submit", function (event) {
               $("#thelist").append("<li class='person'>" + $("#newperson").val() +
                                    " <span class='del'>X</span></li>");
               event.preventDefault();
           });
        });
    </script>
    <style>
        .del { color:red; }
    </style>
</head>
<body>
  <ul id="thelist">
      <li id="p1" class="person">Tobias <span class="del">X</span></li>
      <li id="p2" class="person">Hanna <span class="del">X</span></li>
      <li id="p3" class="person">Michael <span class="del">X</span></li>
      <li id="p4" class="person">Maria <span class="del">X</span></li>
  </ul>

  <form id="newpersonform">
      Neue Person: <input id="newperson" size="20">
  </form>
</body>
</html>
```

---

## Code-Abhängigkeiten

### Script-Tags

Die einzige Möglichkeit, Javascript-Code zu laden besteht darin, ein entsprechendes Script-Tag mit src-Attribut im HTML-Quelltext bzw. im DOM zu platzieren. Dieses System ist relativ unflexibel und unterstützt die Modularisierung von Quellcode und die Deklaration von Abhängigkeiten nur sehr schlecht. Insbesondere gibt es damit nämlich auch keine einfache Möglichkeit, aus Javascript-Code heraus Javascript-Code, z.B. eine Bibliothek, nachzuladen.

#### Variante 1: Korrekte Reihenfolge
Die Script-Dateien werden in der Reihenfolge ihres Auftretens im HTML-Quelltext ausgeführt. Bestehen dabei Abhängigkeiten zwischen den Dateien, ist es wichtig, sie in der richtigen Reihenfolge zu laden.

#### Variante 2: DOMContentLoaded-Event
Wenn der DOM-Tree fertig konstruiert ist, wird das DOMContentLoaded-Event ausgelöst. Zu diesem Zeitpunkt sind alle eingebundene Javascript-Dateien eingelesen und ausgeführt. Code, der von anderen JS-Dateien abhängig ist, sollte auch in einem DOMContentLoaded-Event-Handler ausgeführt werden.

#### Variante 3: Dynamisches Nachladen von Scripts
Die einzige Möglichkeit, eine Javascript-Datei dynamisch nachzuladen ist es, ein Script-Tag dynamisch in den DOM-Tree einzufügen. Dieses wird dann ausgewertet, d.h. die Datei eingelesen und ausgeführt. Dafür wird aber nicht die Ausführung des aktuellen Skriptes unterbrochen, sondern erst, wenn die Javascript-Event-Loop wieder die Kontrolle bekommt, kommt der Code zur Ausführung. Möchte man anschließend eine Aktion ausführen, steht dafür das load-Event des Script-Elementes zur Verfügung.

### Das Module-Pattern

Das Problem der "Namensraumverschmutzung" (Namespace Pollution) wird in Javascript typischerweise durch das Module Pattern umgangen. Dieses Programmier-Pattern verwendet eine sofort ausgeführte anonyme Funktion, die alle für das aktuelle Modul (d.h. die aktuelle Datei globalen Variablen als lokale Variablen verkapselt.

Hier die Datei util.js mit den meisten Features des Module Patterns:

```javascript
var util = (function () {
    var my_state="util";

    return {
        util_func: function () {
            console.log("I'm util_func and working. My state is " + my_state);
            var script = document.createElement("script");
            script.src = "util2.js";
            document.head.appendChild(script);
            script.onload = function () {
                util2.funky();
            };
        }
    }
}());
```

Die Datei stellt somit ein Objekt bereit, dass unter dem globalen Namen util angesprochen wird (hier bestehen natürlich dann weiterhin Konfliktmöglichkeiten), das über eine Methode util_func verfügt. Alle weiteren Variablen und andere Namen des Moduls werden durch die anonyme Funktion aus dem globalen Namensraum herausgehalten. Wichtig ist, dass diese Funktion sofort ausgeführt wird und zu exportierende Funktionalitäten als Objekt zurückliefert. (Das muss nicht zwingend ein Objekt sein, möglich wäre z.B. auch eine Funktion, Objekte sind aber üblich, weil sie mehrere Methoden aufweisen können und die Aufrufsyntax util.util_func durchaus an ein Modulsystem erinnert.)

Weitere Informationen gibt der großartige Blogbeitrag von Ben Cherry unter http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html

### CommonJS, require.js und AMD-Module

#### Modularisierungs-Lösungen

Das Module Pattern adressiert nur das Problem des Namensraums, nicht das Nachladen bzw. Definieren und Auflösen von Abhängigkeiten zwischen Modulen. Für dieses Problem haben sich drei unterschiedlich aktuelle Lösungen etabliert, die jeweils auf unterschiedlichen Voraussetzungen basieren:

1. CommonJS-Module: Das commonJS-Projekt will eine browserunabhängige Javascript-Umgebung schaffen und hat dazu auch ein Modulsystem konzipiert, das zunächst keine Rücksicht auf Browser-Umgebungen nimmt. Um im Browser nutzbar zu sein, müssen weitere Tools hinzukommen, die z.B. require-Statements in Ladeoperationen übersetzen. Die praktische Bedeutung von CommonJS ist durch den Erfolg des neueren node.js-Projektes stark geschmälert worden, allerdings verwendet node.js die CommonJS-Modulsyntax.
2. AMD-Module: Dieses Modulsystem ist stark auf den Browser ausgerichtet und benötigt nur wenige Zusatztools. Die meisten aktuellen Javascript-Bibliotheken unterstützen AMD-Module. Insbesondere über den Loader require.js waren AMD-Module lange Zeit die meistverbreitete Modularisierungslösung für clientseitiges Javascript.
3. ES-6-Module: Mit Ecma-Script 6 wird ein an die Common-JS-Module angelehntes Modulsystem eingeführt, das in Zukunft sowohl für den Server als auch den Client als Standard fungieren wird.


#### CommonJS-Module

Module werden über die require-Funktion importiert, die ein Objekt (bzw. eine Funktion oder Konstante) liefert. Daher wird der Rückgabewert des require-Aufrufs jeweils an einen Bezeichner gebunden, der im folgenden für die importierte Funktionalität verwendet wird.

Exportiert wird über Properties des export-Objektes, d.h. einer einfachen Zuweisung an eine (neue) Propery dieses Objektes. Die folgenden Beispiele sind der Spezifikation unter http://wiki.commonjs.org/wiki/Modules/1.1.1 entnommen.

```javascript
// math.js
exports.add = function() {
    var sum = 0, i = 0, args = arguments, l = args.length;
    while (i < l) {
        sum += args[i++];
    }
    return sum;
};

// increment.js
var add = require('math').add;
exports.increment = function(val) {
    return add(val, 1);
};

// program.js
var inc = require('increment').increment;
var a = 1;
inc(a); // => 2

module.id == "program";
```

#### AMD Module

Für Asynchronous Module Definitions (AMD) wird ein Programmierpattern verwendet, das nur einen definierten Einstiegspunkt über ein data-main-Attribut und zwei zusätzliche Funktionen benutzt, nämlich require und define mit folgenden Signaturen:


```javascript
// load require.js library and define entry point
<script data-main="main.js" src="require.js">

// define a module that depends on PREREQUISITES (strings without .js ending).
// FACTORY must return the module implementation.
define([PREREQUISITES], FACTORY);

// Require a list of PREREQUISITES (strings without .js ending).
// Call CALLBACK after PREREQUISITES are loaded.
require([PREREQUISITES], CALLBACK);
```
require wird also für den Code verwendet, der selbst keine Funktionalitäten exportiert, sondern lediglich andere Module nutzt.  define hingegen stellt Funktionalität bereit, indem es einen Factory-Ausdruck auswertet. Der Rückgabewert dieses Ausdrucks wird dann in nutzenden Kontexten an eine Variable gebunden. Insgesamt werden für die AMD-Module keinerlei globale Variablen erzeugt!

Die Datei main.js sieht damit wie folgt aus:

```javascript
require(["util"], function (util) {
    var my_state = 'main';
    util.util_func();  // call a function from util.js
    alert("My state is " + my_state);
});
```
Der Code in der anonymen Funktion wird erst ausgeführt, wenn alle Abhängigkeiten (angegeben als Strings ohne .js-Endung) asynchron geladen sind. Das passiert erst nachdem der DOM-Content aufgebaut ist, d.h. das DOMContentReady-Event muss hier nicht mehr genutzt werden (es darf auch nicht mehr genutzt werden, weil der Code erst nachher ausgeführt wird und daher nicht mehr auf das Event reagieren könnte).

Alle Abhängigkeiten werden als Parameter der auszuführenden Funktion übergeben, d.h. es sollte genauso viele Parameter wie Abhängigkeiten geben. Die Parameter können, müssen aber nicht, wie die zu ladenden Module benannt werden.

Die Datei util.js:

```javascript
define(["util2"], function (util2) {
    var my_state = "util";

    return {
        util_func: function () {
            console.log("I'm util_func and working. My state is " + my_state);
            util2.funky();
        }
    }
});
```
Die Datei hat eine Abhängigkeit, util2, und liefert - wie beim Module Pattern - ein Objekt zurück, dessen Methode util_func in der main.js-Funktion verwendet wird.
Die Datei util2.js:

```javascript
define(function () {
    var my_state = 'util2';

    return {
        funky: function () {
            document.body.style.backgroundColor = 'red';
        }
    }
});
```
Der define-Aufruf hier hat keine Abhängigkeiten und verfügt daher nur über einen Parameter. Auch hier wird wieder ein Objekt zurückgegeben.

---

## Build-Prozess (webpack)

### Code-Management und Build-Prozess

#### Build-Prozesse und Code-Management-Tools
In der Frontend-Entwicklung ist es inzwischen nicht mehr üblich, den vollständigen und fertigen Javascript-Code (gleiches gilt für CSS-Code) für den Browser in statischen Dateien abzulegen. Stattdessen werden Tools eingesetzt, die aus den Quelldateien die für den Browser nutzbaren Dateien zusammenstellen. Typische Schritte, die beim Zusammenstellen durchgeführt werden, sind:

1. Auflösen von Abhängigkeiten und Zusammenpacken des Codes (statt dynamischem Nachladen)
2. Übersetzen von verschiedenen Javascript-Dialekten und -Versionen (z.B. ES 6) oder anderen Sprachen (z.B. TypeScript, CoffeeScript) in browser-kompatibles Javascript.
3. Minimieren des Codes durch Entfernen von Kommentaren etc.

Die meisten der aktuellen Tools, die für diese Zwecke verwendet werden, sind selbst in Javascript geschrieben und werden auf dem Entwicklungsrechner über die node.js-Umgebung ausgeführt (also nicht im Browser, sondern über die Kommandozeile). Die Entwicklung der Frontend-Tool-Szene ist sehr dynamisch; schnell entstehen neue Werkzeuge und werden noch kürzlich gut etablierte Tools abgelöst.

Hier wird deshalb exemplarisch ein aktuelles Tool (webpack) unter Nutzung externer Ressourcen vorgestellt. In der Frontend-Entwicklung müssen Sie bereit sein, sich laufend aus verschiedenen Quellen zu informieren und sich häufiger in neue Tools einzuarbeiten.

Die hier eingebundene Webpack-Einführung ist ein Beispiel für einen solchen Einarbeitungsprozess.

#### Webpack-Videos

Die folgenden Videos von Academind beziehen sich auf Webpack 2, funktionieren aber auch mit dem aktuellen Webpack 3, das insgesamt keine wesentlichen Änderungen für die hier verwendeten Funktionalitäten bietet.

Den Code zu den Videos finden Sie unter https://github.com/mschwarzmueller/yt-webpack2-basics (auf richtigen Branch achten!)

**Video 1**
https://youtu.be/GU-2T7k9NfI

#### Was ist Webpack?

Webpack ist im Kern ein Tool zum Zusammenführen statischer Ressourcen (Assets) für Web-Anwendungen, das sich hauptsächlich um Javascript-Dateien kümmert, mithilfe von Plugins (Loader) aber auch mit anderen Formaten, z.B. CSS umgehen kann. Es ist in der Lage, Abhängigkeiten zwischen Javascript-Dateien / -Modulen anhand verschiedener Konventionen (CommonJS-Module, AMD-Module und ES6-Module) zu erkennen, und fügt dann alle benötigten Teile zu einer einzelnen Javascript-Datei zusammen. Vor dem Zusammenfügen können Dateien noch vorbehandelt werden, z.B. mithilfe eines ES6->ES5-Konverters.

#### Webpack installieren

1. node.js und den Node Package Manager npm installieren: Download über https://nodejs.org/en/download/ oder Ihren Paketmanager
2. Projekt-Ordner anlegen
3. npm init in diesem Ordner ausführen (Kommandozeile). Alle Fragen können mit Enter (default) beantwortet werden
4. npm install webpack --save-dev installiert webpack dann lokal für das Projekt. (--save-dev gibt an, dass das Tool nur für die Entwicklung benötigt wird, nicht für die spätere Anwendung)

#### package.json und webpack nutzen
npm legt eine Datei package.json an, die die Konfiguration für das Build- und Code-Management Ihres Projektes festlegt. package.json gehört zu npm, nicht zu webpack!

npm war ursprünglich nur ein Package Manager zum Laden und Installieren von node.js-Modulen, deckt inzwischen aber auch die Funktionalitäten spezieller Build-Tools wie gulp, grunt oder bower ab. Über die package.json-Datei definieren Sie Aufgaben (tasks, scripts) wie z.B. das Zusammenstellen (build) Ihrer Anwendung. Als (derzeit einzigen) Teil dieses Build-Prozesses wird dann webpack ausgeführt:

```json
"scripts": {
  "build": "webpack src/js/app.js dist/bundle.js"
},
```

Das Kommando webpack wird von npm aus dem Ordner ./node_modules/.bin ausgeführt, Sie müssen es also nicht selbst lokalisieren. webpack bekommt zwei Argumente: 1. Das Start-Script (das dann ggf. weitere Abhängigkeiten hat) und 2. die Ausgabedatei.

Mit dem Kommando

> npm run build

wird webpack nun alle zur Anwendung gehörenden Javascript-Dateien zusammenpacken und in eine einzige Datei dist/bundle.js schreiben.

Mit dem Schalter *-p* läuft webpack im Produktions-Modus und minimiert die Ausgabedatei zusätzlich.

#### ES6-Module

Webpack kann mit allen drei etablierten Modulsystemen (CommonJS, AMD und ES6-Module) umgehen, in neuen Projekten wird man typischerweise ES6-Module wählen, da sie Teil des aktuellen (und zukünftigen) Javascript-Standards sind.

#### Module importieren

Die Import-Syntax erinnert ein wenig an die Python-Syntax. Typischerweise werden nicht ganz Module, sondern nur einzelne Teile davon importiert. Es gibt folgende Syntax-Varianten:

```javascript
// Default exports and named exports
import theDefault, { named1, named2 } from 'src/mylib';
import theDefault from 'src/mylib';
import { named1, named2 } from 'src/mylib';

// Renaming: import named1 as myNamed1
import { named1 as myNamed1, named2 } from 'src/mylib';

// Importing the module as an object
// (with one property per named export)
import * as mylib from 'src/mylib';

// Only load the module, don’t import anything
import 'src/mylib';
```

Module exportieren einzelne Objekte, Funktionen oder Konstanten und können ein Element als default auszeichnen.

#### Elemente exportieren

Es gibt zwei Wege, Objekte, Funktionen oder Konstanten eines Moduls als Export zu kennzeichnen:


Durch das Schlüsselwort export vor der Deklaration, z.B.

> export function inc() { counter++; }

Durch ein zusammenfassendes export-Statement am Ende der Datei:

> export { MY_CONST, myFunc };

oder mit veränderten Namen:

> export { MY_CONST as THE_CONST, myFunc as theFunc };

oder als Re-Export eines anderes Moduls:

> export { foo as myFoo, bar } from 'src/other_module';

Details zu ES-6-Modulen finden sich unter: http://2ality.com/2014/09/es6-modules-final.html

### Development-Server
Server-Umgebungen für clientseitige Anwendungen

Für eine reine HTML/CSS/Javascript-Anwendung ist kein echter Server notwendig, da die erforderlichen Aktionen auch über das file:-Protokoll direkt von der Festplatte ausgeführt werden können. Um eine echte Umgebung zu testen und Features wie z.B. AJAX nutzen zu können, ist allerdings ein http-Server notwendig.

Dazu kann der schon häufiger verwendete Python-Server oder ein sehr simpler Python-Server verwendet werden. Möglich sind allerdings auch verschiedene npm-Pakete, die ebenfalls einfache Server für statische Inhalte bereitstellen.

Im Folgenden wird das Paket webpack-dev-server verwendet, der besonders gut mit webpack zusammenarbeit und live reloading bietet, d.h. die Anwendung wird im Browser automatisch neu geladen, wenn der Build-Prozess durchgeführt wird.
Installation

> npm install webpack-dev-server --save-dev

#### Start
webpack-dev-server kann anstelle des webpack-Kommandos in der package.json-Konfiguration für npm run verwendet werden, dabei muss allerdings der erste Parameter mit dem Schlüsselwort --entry versehen werden.

**Video 2**
https://youtu.be/HNRt0lODCQM

### Webpack: Kernkonzepte

**Video 3**
https://youtu.be/8DDVr6wjJzQ

#### Konfigurationsdatei

Anstelle der Kommandozeile wird man bei komplexeren Szenarien eine Konfigurationsdatei verwenden, die die webpack-Parameter enthält. Wie bei node.js-Tools üblich, sind die Konfigurationsdateien selbst Javascript-Dateien. Damit ist es auch möglich, Berechnungen in den Konfigurationsdateien vorzunehmen.

Der Name der Konfigurationsdatei kann per --config spezifiziert werden, per Konvention lautet er webpack.config.js. Liegt diese Datei im gleichen Verzeichnis wie die package.json, kann der Konfig-Parameter entfallen.

Ein Gerüst einer typischen Webpack-Konfigurationsdatei webpack.config.js sieht so aus:

```javascript
const path = require('path');  // node.js uses CommonJS modules

module.exports = {
  entry: './src/index.js',  // the entry point
  output: {
    filename: 'bundle.js',  // the output filename
    path: path.resolve(__dirname, 'dist')  // fully qualified path
  }
};
```

#### Entry
Mit entry werden die Einstiegspunkte bezeichnet, von denen aus webpack nach Abhängigkeiten sucht. Pro entry wird ein Ausgabebundle erstellt.

#### Loaders
x

#### Plugins
x

#### Output
x

### Webpack: Entwicklungsworkflow

**Video 4**
https://youtu.be/8vnkM8JgjpU

### 3rd-Party-Bibliotheken mit Webpack nutzen

**Video 5**
https://youtu.be/IYuh8hIyvfE

Bislang haben wir nur lokale Javascript-Abhängigkeiten mit webpack gebundelt. Selbstverständlich ist es möglich, externe Bibliotheken zusätzlich auf dem üblichen Wege per script-Tag einzubinden und z.B. von einem Content-Delivery-Network (CDN, Vorteil: eventuell bereits im Cache des Browsers) abzurufen.

Es ist aber auch möglich, die benötigten externen Bibliotheken mit in das Bundle aufzunehmen. Dazu sind zwei Schritte notwendig:

#### Externe Bibliothek als Abhängigkeit (production dependency) per npm installieren
Das Kommando

> npm install --save jquery

lädt jQuery als Node Package herunter und erzeugt einen Eintrag in der package.json-Datei. Beachten Sie, dass es sich dabei nun um eine Produktions-Abhängigkeit und nicht um eine Entwicklungs-Abhängigkeit handelt und der Eintrag unter 'depedencies' statt unter 'devDependencies' landet. Sollen später einmal nur die Produktionsabhängigkeiten installiert werden, verwendet man das Kommando npm install --production.

#### Bereitstellen der Bibliothek für den eigenen Code
Nun soll die jQuery-Bibliothek auch für den eigenen Code verfügbar gemacht werden. Dazu gibt es zwei Möglichkeiten, zum Einen als Modul-Import und zum anderen als automatisch bereitgestellter Code für alle Dateien.

##### 1. Modul-Import (diese Möglichkeit wird im Video nicht vollständig erwähnt)

Über die Javascript-Anweisung

> import $ from 'jquery';

wird das jQuery-Objekt unter dem Namen $ in der aktuellen Datei verfügbar gemacht. Der alternative Bezeichner jQuery steht damit nicht automatisch zu Verfügung, könnte aber z.B. über

> var jQuery = $;

bereitgestellt werden. Beide Bezeichner sind änderbar, die Verwendung von $ und jQuery erfolgt konventionsgemäß.

Hier wird wieder das ES-6-Modulsystem verwendet, das einen Defaultexport nutzt und ihn an den Namen $ bindet.

##### 2. Automatische Bereitstellung (diese Möglichkeit wird im Video erläutert)

Webpack kann Bibliotheken in allen Dateien automatisch bereitstellen. Dazu wird das ProvidePlugin genutzt, das eine Liste von Variablennamen an den Export jeweils einer Bibliothek bindet.

In der webpack.config.js-Datei schreiben Sie also in der Plugins-Liste:

```javascript
new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery'
})
```
Nun ist kein import mehr notwendig, jedes Vorkommen einer freien Variable $ oder jQuery führt dazu, dass das Modul importiert wird und unter dem angegebenen Namen bereitsteht. Sie können Ausdrücke wie $('theButton') also ohne weitere import-Statements oder Script-Tags im Javascript-Code nutzen.
