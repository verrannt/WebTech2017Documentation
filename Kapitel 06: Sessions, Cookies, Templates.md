# Kapitel 06: Sessions, Cookies, Templates

* [Templates](#templates)
  * [Typische Elemente von Webseiten](#typische-elemente-von-webseiten)
  * [Was benötigt eine Template-Engine?](#was-benötigt-eine-template-engine)
  * [Jinja2-Templates](#jinja2-templates)
  * [Logiklose Templates - Mustache](#logiklose-templates---mustache)
* [Zustände & Cookies](#zustände-cookies)
  * [Zustände](#zustände)
  * [Cookies](#cookies)
    * [Was ist in einem Cookie?]('#was-ist-in-einem-cookie)
    * [Cookie setzen](#cookie-setzen)
    * [Cookie löschen](#cookie-löschen)
    * [Same-Origin-Policy und Third-Party-Cookies](#same-origin-policy-und-third-party-cookies)
* [Sessions](#sessions)
  * [Motivation](#motivation)
  * [Middleware - Konzept](#middleware---konzept)

---

## Templates

Ziel der Nutzung von _HTML-Templates_ ist die Trennung von Logik und Darstellung. 
Diese Trennung ist aus mehreren Gründen sinnvoll:
* Beide Teile können (in gewissen Grenzen) unabhängig voneinander überarbeitet und geändert werden
* Trennung und Kapselung erleichtert den Austausch oder die Einrichtung weiterer Darstellungsvarianten
* Logik und Darstellung werden relativ unabhängig voneinander konzipiert und umgesetzt

Web-Anwendungen erzeugen _dynamisch_ HTML-Code. Dazu gibt es fünf verbreitete Varianten:

1. Konstruktion und Ausgabe von HTML-Code im Programm-Code mit Print-Aufrufen und String-Operationen. 
   Diese Variante _widerspricht_ der Trennung von Logik und Darstellung und sollte daher vermieden werden. 
   Es gibt allerdings auch Ausnahmen wie z.B. die Erzeugung von _HTML_ aus einfacherem Markup wie z.B. _Markdown_, die im Programmcode passieren muss.

2. Einbettung von Programm-Code in HTML-Code. Diesen Weg ermöglicht immer noch _PHP_. 
   Dort könnte die Ausgabe einer Teilnehmerliste z.B. so aussehen:
   ```html
   <h1>Die Teilnehmer:</h1>
   <ul>
   <? foreach ($teilnehmer as $t) { ?>
     <li class="teiln"> <?= $t.name ?> </li>
   <? } ?>
   ```
   Vorteil dieser Lösung ist, dass die gleiche Sprache wie für den Programmcode verwendet wird und Daten sehr einfach übergeben und verarbeitet werden können. 
   Es wird keine zusätzliche Template-Engine benötigt (Performance, zu wartender Code bzw. zu beachtende Abhängigkeit). 
   Der _Nachteil_ ist, dass die Trennung von  Logik und Darstellung erschwert wird. 
   _Stud.IP_-Templates basieren genau auf diesem Prinzip, dort gibt es Konventionen, wieviel bzw. welche Logik in Templates erwünscht ist. 
   Erzwungen werden diese Konventionen technisch nicht.

3. Verwendung einer relativ ausdrucksstarken Template-Sprache

   Die Varianten 1. und 2. ermöglichen turing-vollständige Verarbeitung von Daten im Template. 
   Variante 3 enthält viele, aber nicht alle Elemente von Programmiersprachen, so dass mächtige Konstruktionen möglich sind, aber keine beliebigen Berechnungen.
   So kann es z.B. möglich sein, in Templates Variablen zu definieren und zu belegen, Werte umzuwandeln und zu filtern, 
   Macros zu definieren und vom Template aus Funktionen und Methoden aus dem Hauptprogramm aufzurufen. 
   Die Syntax und Semantik der Template-Sprache ist nicht mit der Programmiersprache identisch, 
   d.h. es wird ein zusätzlicher Formalismus eingeführt (Lernaufwand, Performance, zu wartender Code bzw. zu beachtende Abhängigkeit).

4. Verwendung einer sehr wenig ausdrucksstarken Template-Sprache

   Diese Klasse von Template-Mechanismen versucht, nur das zwingend für eine Template-Engine nötige (s. nächster Abschnitt) umzusetzen und eine Logik in Templates zu ermöglichen. 
   Vorteil dieses Ansatzes ist eine sehr gute Trennung von Logik und Darstellung, der Nachteil sind zum Teil umständliche Konstruktionen, 
   da erleichternde Verabeitungsmöglichkeiten wie in Variante 3 nicht zur Verfügung stehen.

5. Verwendung eines reinen Platzhalter-Template-Mechanismus

   Diese Möglichkeit wurde bislang im hier vorgestellten Server verwendet. 
   So war es möglich, in Strings einzelne Platzhalter durch Strings zu erstzen. 
   An vielen Stellen stößt dieser Ansatz an Grenzen wie im übernächsten Abschnitt deutlich wird. 
   Vorteile sind die sehr leichte Erlernbarkeit, die effiziente Verarbeitung und im Idealfall vollständige Trennung von Logik und Darstellung. 
   Allerdings verführen die fehlenden Verarbeitungsmöglichkeiten auf Template-Ebene dazu, Teile mit Darstellungsangaben im Programmcode vorzukonstruieren, also die Trennung wieder aufzuheben.

Alle fünf Mechanismen werden verwendet, am häufigsten allerdings die relativ mächtigen dedizierten Template-Sprachen (Variante 3), 
die einen Kompromiss zwischen rigoroser Trennung und arbeitserleichternden Verabeitungsmöglichkeiten auf Template-Ebene darstellen.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

### Typische Elemente von Webseiten

Einige typische Elemente, die sich auf sehr vielen Webseiten wiederfinden, sind:
 
1. Identischer Rahmen für alle Einzelseiten einschließlich _Navigation_, _Header_, _Footer_.
2. Ausgabe von einzelnen Werten, die im Programmcode berechnet werden (in _Stud.IP_ auf dieser Seite z.B. der Name der Veranstaltung).
3. Ausgabe von Listen von Werten, z.B. der Liste der vorhandenen Wiki-Seiten aus der Wiki-Aufgabe
4. Bedingungen um Elemente oder Attribute nur bei Vorliegen definierter Bedingungen auszugeben, z.B. die Zuweisung der `CSS`-Klasse `active` an das jeweils aktive Navigationselement
5. Komplexe einheitliche Gestaltung wiederkehrender Elemente wie z.B. Nutzernamen mit Profillink und Miniatur-Bild

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

### Was benötigt eine Template-Engine?

Um die Trennung von Logik und Darstellung mit den minimal notwendigen Mechanismen zu unterstützen, benötigt eine Template-Engine folgende Eigenschaften bzw. Fähigkeiten:

1. Die Möglichkeit, einfache Werte (Zahlen, Strings) aus dem Programmcode an ein Template zu übergeben und dort auszugeben.
2. Die Möglichkeit, Bedingungen im Template zu verwenden, um Teile der Ausgabe abhängig vom Vorliegen definierter Bedingungen ausgeben oder nicht ausgeben zu können.
3. Die Möglichkeiten, iterierbare Wertemengen im Template mit Schleifen-Konstruktionen zu durchlaufen und alle Elemente einzeln auszugeben.
4. Die Möglichkeit, mehrfach verwendete Teile zentral zu definieren und an vielen Stellen flexibel einsetzen zu können. Dabei gibt es zwei unterschiedliche Anwendungsfälle:
    * Die Verwendung eines Grundgerüstes oder Layouts, das die Grundstruktur einer HTML-Seite definiert und an einzelnen Stellen von abgeleiteten Templates zu füllende Lücken ausweist.
    * Die Verwendung von Macros, um einzelne wiederkehrende Elemente gleichartig strukturiert darstellen zu können.
5. Die Unterstützung von Escaping-Routinen, d.h. der Möglichkeit, übergebene Strings ungeschützt auszugeben, so dass darin enthaltener HTML-Code aktiv wird, oder aber zu escapen, so dass enthaltene
   HTML-Sonderzeichen nicht aktiv werden, sondern per Ersatzdarstellung ausgegeben werden.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

### Jinja2-Templates

Jinja2 ist eine beliebte Template-Engine für Python, die in einer von Python unabhängigen Syntax mächtige Möglichkeiten bietet, Templates unter Python zu nutzen. 
Die Dokumentation befindet sich unter http://jinja.pocoo.org/.

#### Syntax

Jinja2-Tags werden in die Template-Datei, die im Wesentlichen HTML-Code enthält, eingestreut. 
Zu unterscheiden sind dabei folgende Formen:

| Jinja2-Tags | Bedeutung |
|----------------------------|-------------------------------------------------|
|`{{ expression }}`          | Ausdrücke, Einsetzen eines Variablenwertes      |
|`{% ... statement ... %}`   | Kontrollstrukturen, Jinja2-Statements           |
|`{# .... #}`                | Kommentare, die nicht in der Ausgabe erscheinen |
 
#### Template-Vererbung

Ein zentraler Mechanismus in Jinja2 ist die Vererbung von Templates. 
Ein konkretes Template für eine Einzelseite/-ansicht wird in der Regel immer mit einem `{% extends ... %}`-Statement beginnen. 
Auf diese Weise lässt sich ein Grundlayout oder Basistemplate für alle Seiten einer Anwendung erstellen, das nur an einzelnen Stellen ansichtsspezifisch zu füllen ist. 
Diese einzelnen Stellen heißen Blöcke und werden in einem erbenden Template gefüllt.

#### Expressions

Ausdrücke sind grundsätzlich die _Python_-Datentypen Strings, Zahlen, Listen, Tupel und Dictionaries, wobei nur die einfachen Werte sich für die direkte Ausgabe eignen. 
Mit Ausdrücken lässt sich in Jinja2 rechnen, Ausdrücke können verglichen und logisch miteinander verknüpft werden, so wie Sie es auch aus vollständigen Programmiersprachen 
z.B. im Bedingungsteil von if-Statements kennen.
Darüber hinaus gibt es die Möglichkeit, Ausdrücke zu filtern, d.h. zu formatieren, String-Operationen darauf anzuwenden, zu sortieren, auszuwählen usw. Jinja2 bietet eine API, mit der von Python aus eigene
Filter hinzugefügt werden können. 
Neben Filtern gibt es Tests, mit denen z.B. geprüft werden kann, ob eine Zahl gerade ist, eine Variable definiert oder ein String ist etc. 
Auch Tests können erweitert werden.

#### Statements

Die Statements umfassen die klassischen Kontrollstrukturen Bedingung und Schleife, außerdem Macros, Variablenzuweisung und Einfügen anderer Templates. 
In den Statements können alle oben dargestellten Möglichkeiten der Ausdrücke verwendet werden. 
Auf diese Weise können auch komplexe Listenansichten, fallabhängige Darstellungsvarianten usw. in relativ kompaktem Code realisiert werden.

#### Zusammenfassung

Jinja2 bietet eine sehr mächtige Templatesprache. 
Bei genauerer Betrachtung der durch Ausdrücke, Filter, Tests, Statements, Variablenzuweisung und Macros entstehenden Möglichkeit wird klar, 
dass es sich dabei um eine vollwertige, Turing-vollständige Programmiersprache handelt, mit der grundsätzlich beliebige Probleme gelöst werden könnten. 
Insbesondere ist es aber möglich, Programmlogik in die Templates zu verlagern.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

### Logiklose Templates - Mustache

Die Grundphilosophie von Mustache-Templates ist ein minimalistischer Ansatz: Die Engine soll alles leisten, was von einer Template-Engine benötigt wird, aber gleichzeitig soll es unmöglich sein, Programmlogik
in die Templates zu verlagern.

Mustache-Templates gibt es in Implementationen für sehr viele Programmiersprachen, darunter auch Python und Javascript. Die Mustache-Syntax ist hier dokumentiert: http://mustache.github.io/mustache.5.html, die
Python-Umsetzung pystache hier: https://github.com/defunkt/pystache

Insgesamt bietet Mustache nur sehr weniger Konstrukte:

| Mustache-Tag | Bedeutung |
|---|---|
|`{{ variable }}`           | HTML-escapte Ausgabe einer Variablen    |
|`{{{ variable }}}`         | Nicht-escapte Ausgabe einer Variablen  |
|`{{# variable }} ... {{/ variable }}` | Section |
|`{{^ variable }} ... {{/ variable }}`  | Inverted Section |
|`{{> template }}`                     | Template einfügen. Aktueller Kontext wird an Template übergeben.   |
|`{{! comment }}`           | Kommentar                      |

#### Section

Bilden eines Kontextes. 
Konsequenzen sind abhängig vom Wert der Variablen. 
Wenn der Wert zu False evaluiert, wird der enthaltene Teil übersprungen (Bedingung). 
Wenn der Wert ein iterierbares Objekt (z.B. Liste) ist, wird die Liste durchlaufen und der Inhalt für jedes Element ausgegeben. 
Ist der Wert ein aufrufbares Objekt (z.B. Funktion oder Methode), wird die Funktion mit dem Inhalt aufgerufen und ihr Rückgabewert eingesetzt.

#### Inverted Section
 
Inhalt wird nur berücksichtigt, wenn Variable zu `False` evaluiert.

#### Bewertung

Mit Mustache-Templates lässt sich in der Regel eine sehr saubere Trennung von Programmlogik und Darstellung erreichen. 
Der sehr hilfreiche Mechanismus der Template-Vererbung aus Jinja2 fehlt, die Umsetzung von Basis-Layouts ist komplizierter.

Einige Dinge, wie z.B. das Auslassen eines trennenden Kommas beim letzten Durchlauf einer Schleife sind nicht ohne weiteres zu erreichen.
In solchen Fällen müssen die Datenstrukturen serverseitig erweitert werden, im Komma-Beispiel könnte man aus den Listenelementen dictionaries machen, von denen das letzte das Attribut `last` bekommt.
Die Ausgabe des Kommas kann dann davon abhängig gemacht werden, dass `last` nicht `true` ist.

Für die meisten Anwendungsfälle reicht eine wenig ausdrucksmächtige Engine wie Mustache aus und ist aus systematischen Gründen einer mächtigeren Lösung vorzuziehen.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

## Zustände & Cookies

### Zustände

Unter Zustand wird in interaktiven Anwendungen die Frage verstanden, ob es zwischen verschiedenen Aktions-Reaktions-Paaren durchgängig verfügbare, d.h. persistente Daten geben muss, oder nicht.

Anwendungen, die in einem durchgängig laufenden Prozess ausgeführt und mit genau einem Ein-/Ausgabemedien bedient werden, sind selbstverständlich zustandsbehaftet, 
denn es gibt nur einen Nutzer, der das Programm fortwährend nutzt und für Veränderungen der Daten sorgt. 
Beispiele für solche Programme sind GUI-Anwendungen in Desktop- oder Mobil-Betriebssystemen wie z.B. ein Web-Browser, ein E-Mail-Client, ein Textverarbeitungsprogramm, ein Spiel oder eine IDE.

Beispiele für Software, die nicht in diesem Sinne zustandsbehaftet ist, sind nicht-interaktive Kommandozeilen-Programme. 
Sie übernehmen Argumente, lesen Daten aus dem Dateisystem und produzieren eine Ausgabe, 
bei mehrfacher Verwendung eines solchen Programms ergibt sich aber kein automatischer Zusammenhang zwischen diesen beiden Aufrufen.

Client-Server-Protokolle gibt es in beiden Varianten: 
_zustandslos_ und _zustandbehaftet_. 
Ein Beispiel für ein _zustandsbehaftetes_ Client-Server-Prokoll ist `ftp` (file transfer protocol), bei dem eine TCP-Verbindung zu einem File-Server aufgebaut wird, die bis zu ihrer Trennung bestehen bleibt. 
Der Nutzer kann auf dem entfernten Server im Verzeichnisbaum navigieren, die aktuelle Position im Verzeichnisbaum ist z.B. eine Zustandsvariable.

#### http ist zustandslos

In der Einführung zu _HTTP_ ist deutlich geworden: _HTTP_ ist ein zustandsloses Protokoll.
Der Webserver nimmt einzelne Requests entgegen und erzeugt jeweils eine Response. 
Ein Zusammenhang zwischen aufeinanderfolgenden Requests des gleichen Nutzers kann auf Protokollebene _nicht_ hergestellt werden. 
(Die HTTP/1.1-Option, die Verbindung aufrecht zu erhalten ist nur dafür gedacht, mit einer Request zusammenhängende Requests wie z.B. das Abrufen von CSS-Styledateien, Javascript-Quellen, Bildern etc. effizienter zu gestalten)

Soll eine Webanwendung dennoch zustandsbehaftet operieren, sind auf höherer Ebene Zustandsmechanismen einzuführen. 
Für diese Mechanismen gibt es mehrere verbreitete Lösungen, die je nach Anwendungsszenario auszuwählen sind.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

## Cookies

### Definition

Cookies sind ein Mechanismus, mit dem eine Webanwendung Informationen im Browser des Anwenders speichern kann.

Ein Browser unterhält eine Liste von Cookies (_CookieJar_), die er aus entgegengenommenen HTTP-Responses extrahiert und mit ausgesandten HTTP-Requests ausliefert. 
Damit wird es für Wenanwendungen möglich, Zustände zu speichern und aus nachfolgenden Requests wieder zu rekonstruieren.

Cookies wurden zunächst als proprietäre Erweiterung des HTTP-Protokolls von Netscape eingeführt und später standardisiert 
(s. [RFC2109](https://tools.ietf.org/html/rfc2109) - die spätere Überarbeitung in [RFC2965](https://tools.ietf.org/html/rfc2965) wird wenig verwendet). 
Sie sind sowohl nützlich als auch kontrovers diskutiert. 
Cookies eröffnen Möglichkeiten datenschutzrechtlich fragwürdiger Praktiken. 
Sie stellen allerdings, wie häufig falsch und verkürzend gemutmaßt, per se kein Sicherheitsrisiko für den Anwender dar. 
Die Verunsicherungen im Umgang mit Cookies versucht [RFC2964](https://tools.ietf.org/html/rfc2964) erklärend und empfehlend zu beseitigen.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

#### Was ist in einem Cookie?

Ein Cookie besteht aus folgenden Informationen:

* Name `name` - Bezeichner des Cookies, vergleichbar mit Schlüssel in Hashmap
* Wert `value` - Zum Schlüssel gehöriger Wert des Cookies
* Domäne `domain` - Domäne(nteil) für Request-Ziele, an die der Cookie ausgeliefert werden darf
* Pfad `path` - Pfad(teil) für Request-Ziele, an die der Cookie ausgeliefert werden darf
* Gültigkeitszeitraum `max-age`/`expires` -
  Zeitpunkt, zu dem der Cookie ungültig wird (zu senden als [RFC1123](https://tools.ietf.org/html/rfc1123)-konformer Datumsstring)
* `httpOnly` - Boolean-Angabe, ob der Cookie nur über http-Requests (statt auch per Javascript) verwendet werden darf
* `secure` - Boolean-Angabe, ob der Cookie nur mit https-Requests ausgeliefert wird (statt auch mit http-Requests)

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

#### Cookies setzen

Mit jedem Response darf der Server Cookies an den Browser schicken. Cookies sind Teil des Response-Headers und haben die Form:

`Set-Cookie: #name=#value;#attr=#attr-value`

Atrtibute sind (u.a.) die oben aufgeführten `domain`, `path` und `expires` (der im [RFC2109](https://tools.ietf.org/html/rfc2109) genannte Parameter `max-age` funktioniert nicht mit allen Browsern zuverlässig, stattdessen sollte `expires` verwendet werden.
Sollen mehrere Cookies gesetzt werden, sind mehrere solcher Response-Zeilen einzufügen. 
(Der im RFC-beschriebene Weg, mehrere Cookies kommagetrennt zu senden funktioniert mit aktuellen Browsern nicht.)

Werden Attribute nicht gesetzt, gelten folgende Defaultwerte:

* Domäne (domain): Der vollständige Domain-Name des Servers
* Pfad (path): Der vollständige Pfad des Requests
* Gültigkeitszeitraum (max-age / expires): Cookies ohne expliziten Gültigkeitszeitraum gelten, solange der Browser geöffnet ist (Browsersession) und werden bei Schließen des Browsers vernichtet

Für Cookies gilt eine Größenbeschränkung: Mehr als 4096 Zeichen müssen für einen Wert nicht gespeichert werden.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

#### Cookies löschen

Cookies werden vom Browser gelöscht, wenn der Platz für den Cookie-Jar erschöpft ist, die Lebenszeit des Cookies erschöpft ist oder der Nutzer die Cookies manuell löscht.

Um vom Server aus den Browser zum Löschen zu bewegen, gibt es kein explizites Kommando. Stattdessen wird ein Cookies mit einem gleichnamigen Cookie überschrieben, dessen Expire-Datum in der Vergangenheit liegt.

#### Cookies entgegennehmen

Der Browser überprüft für jeden abzusenden Request, ob in seinem Cookie-Jar Cookies vorhanden sind, die bei diesem Request mitgeliefert werden dürfen. Die Entscheidung wird aufgrund mehrerer Kriterien gefällt:

1. Passt die Domäne zu dem im Cookie gespeicherten Domänen-Teil?
2. Passt der Pfad zu dem im Cookie gespeicherten Pfad-Teil?
3. Ist der Cookie noch gültig? Falls nicht, wird er gelöscht.
Die auf diese Weise ausgewählten Cookies werden als Teil des HTTP-Request-Headers mit ausgeliefert und haben die Form:

Cookie: `#name=#value;...`
Mehrere zu liefernde Cookies werden mit Semikolon (oder Komma) getrennt in einer Zeile geliefert.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

#### Same-Origin-Policy und Third-Party-Cookies

Zwei Seiten haben die gleiche Origin (_Herkunft_), wenn Protokoll (_http_ oder _https_), Domain und Port identisch sind. 
Cookies können für die gleiche Origin gesetzt werden, aber auch für einiges darüber hinaus. 
Die Abweichungen sind:

Cookies sind nicht _port-spezifisch_. Sie gelten für alle Ports aller matchenden Domains.
Cookies sind zunächst nicht _protokoll-spezifisch_. 
Das `secure`-Flag kann benutzt werden, um die verwendbaren Protokolle einzuschränken.
Ein Cookie kann für eine von der Origin-Domäne abwecheichende Domäne gesetzt werden, wenn sie der Origin-Domäne übergeordnet ist, keine _Top-Level_-Domäne oder ein öffentliche Suffix ist (z.B. `.co.uk`) und _Second-Level_-Domäne gleich sind.
(Eine Response, die als Antwort auf einen Request an `studip.uni-osnabrueck.de` ausgeliefert wird, kann also `studip.uni-osnabrueck.de` oder nur `.uni-osnabrueck.de` als gültige Domain eintragen. 
Bitte beachten Sie, dass z.B. `studip.uos.de` nicht möglich ist, obwohl es auf den gleichen Server verweist.)
Cookies können für beliebige Pfade gesetzt werden.

Viel diskutiert wird der Begriff **Third-Party-Cookies**. Damit sind Cookies gemeint, die von eingebetteten Ressourcen gesetzt werden, die von einem anderen Server als der Haupt-Dokument-Origin angefordert werden. 

_Beispiel_:

So kann eine von `google.com` angeforderte Javascript-Datei Cookies für Google-Server setzen, ein von `tollewerbung.de` angefordertes Bild Cookies für `tollewerbung.de`. 
Wird die Anfrage mit einer Kennzeichnung der aktuellen Seite verknüpft, kann seitenübergreifendes Nutzertracking realisiert werden. 
Das funktioniert wie folgt:

Ich rufe erstmalig die News-Seite `schickeschuhe.de` auf und schaue mir dort begeistert das Modell `turnschuh77b` an. 
Auf dieser Seite wird ein (ggf. auch unsichtbares) Bild mit der Quelle `http://tollewerbung.de/?quelle=schickeschuhe&modell=turnschuh77b` geladen.
Der `tollewerbung`-Server generiert einen neuen zufälligen Cookie-Wert und speichert unter diesem Schlüssel ab, dass zu diesem Zeitpunkt auf der Seite `schickeschuhe.de` das Modell `turnschuh77b` angeschaut wurde.
Als nächstes rufe ich die News-Seite `supernews.de` auf, die Werbeplatz an `tollewerbung` verkauft hat.
Es wird ein Banner von `tollewerbung` eingebunden, wobei das vorhin empfangene Cookie mitgesandt wird. 
`tollewerbung` kann anhand der im Cookie abgelegten ID erkennen, dass ich mir zuletzt `turnschub77b` von `schickeschuhe.de` angeschaut habe und blendet mir entsprechende Werbung ein.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

## Sessions

### Motivation

In den vorangegangenen Beispielen wurden die Zustandsdaten explizit zum Browser übertragen und von dort wieder entgegengenommen. 
Ob die Werte in der Zwischenzeit manipuliert wurden, war nicht feststellbar. 
Zudem können so übertragene Daten nicht beliebig umfangreich werden (Cookie-Größenbeschränkung, `GET`-Größenbeschränkung, unsinnig große Mengen zu übertragender Daten bei Verwendung von `POST`).

Daher ist es gängige Praxis, nicht die Werte selbst, sondern nur eine ID für serverseitig gespeicherte Werte zu übertragen und die Werte bei jedem Request aus einem persistenten Speicher (Datenbank, Dateisystem, evtl. auch Server-RAM) zu
rekonstruieren.

Diese Praxis ist unter dem Namen Session-Handling bekannt, wobei zu beachten ist, den Begriff nicht mit Browser-Sessions zu verwechseln.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

### Middleware - Konzept

Eine Middleware stellt eine anwendungsunabhängige Zusatzfunktionalität bereit, ohne fest in den Server-Code integriert werden zu müssen. 
Middlewares klingen sich zum einen nach dem Parsen des Requests, aber vor der Verarbeitung durch eine App-Route in die Verarbeitung ein und zum Anderen nach der App-Route aber vor Ausliefern der Response.

Die Verarbeitung von Sessions ist ein typischer Fall für eine Webserver-Middleware-Komponente. 
Sie wird nicht immer benötigt und kann auf sehr unterschiedliche Weise realisiert werden, z.B. durch unterschiedliche Persistenz-Speicher (Dateisystem, Datenbank). 
Daher ist es nicht möglich, sie wie die Cookie-Verarbeitung im Kern zu integrieren.

### Middleware-Implementation

Middleware-Objekte implementieren eine oder beide der Methoden `process_request` und `process_response`. 
Für die Verarbeitung müssen die Verarbeitung und das Routing in der Methode `serve` des Webserver-Objektes ergänzt werden:

```python
if self.request.parse(conn):
    self.request.origin = caddr[0]
    log(2, "Request: %s\n" % self.request)
    try:
        # preprocessing (middlewares)
        for m in self.middlewares:
            m.process_request(self.request, self.response)
            # Middlewares may raise AlreadyProcessed if they already produced a response

        # processing (check registered routes for actions)
        processed = False
        for route in self.routes:
            log(2, "Matche %s gegen %s" % (route[0], self.request.path))
            match = re.match(route[0], self.request.path)
            if match:
                log(2, "Route %s matcht Request %s" % (route[0], self.request.path))
                route[1](self.request, self.response, match)
                processed = True
                break
        if not processed:
            raise StopProcessing(404, "No matching route.")

    except StopProcessing as spe:
        self.response.send(code=spe.code, body=spe.reason)
    except AlreadyProcessed:
        pass

    # preprocessing (middlewares)  
    for m in self.middlewares: 
        m.process_response(self.response)  
```

Die zu verwendenden Middlewares werden in einer Liste im Server-Objekt gespeichert und in der Reihenfolge ihrer Registrierung aufgerufen. 
Middleware-Objekte haben Zugriff auf das Request- und das Response-Objekt und können somit beliebig in beides eingreifen.

[Zurück nach oben](#kapitel-06:-sessions,-cookies,-templates)

---

