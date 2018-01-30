# Kapitel 11: Web-Services und REST

* [Web-Services](#web-services)
  * [Was sind Web-Services](#was-sind-web-services)
  * [SOAP und XML-RPC](#soap-und-xml-rpc)
  * [REST](#rest)
  * [REST Maturity Model](#rest-maturity-model)
* [File-Server](#file-server)
  * [Micro-Frameworks](#micro-frameworks)
  * [File-Server Implementation](#file-server-implementation)
  * [File-Client Implementation](#file-client-implementation)


---

## Web-Services

### Was sind Web-Services?
Unter Web-Services versteht man ganz allgemein alle Interfaces zu einer Web-Anwendung, die nicht für Menschen, die die Anwendung mit einem Browser bedienen und betrachten, gedacht sind, sondern sich an andere Software richten. Beispiele für solche Software, die Webanwendungen nutzt, können sein:

* Weitergabe von Daten an andere Dienste durch eine Anwendung. (Beispiel: Mini-Twitter-Beiträge sollen auch bei Twitter erscheinen, dazu muss Mini-Twitter sie bei Twitter einreichen)
* Abruf von Daten aus einer anderen Anwendung. (Beispiel: Mini-Twitter soll auch Tweets von verknüpften Twitter-Accounts mit anzeigen)
* Nutzung einer Anwendung über alternative Interfaces, z.B. eine native Smartphone-App ermöglichen.
* Weitestgehende Verlagerung des Anwendungs-Codes in den Client, z.B. um eine "Web-App" zu realisieren. Damit fallen auch die Anwendung von Templates und nahezu die gesamte Applikationslogik in den Javascript-Client, lediglich die persistente Datenhaltung muss weiter serverbasiert erfolgen.

Im engeren Sinne werden heutzutage drei wichtige Technologien unter "Web-Services" verstanden:
* SOAP
* XML-RPC
* REST

Diese Technologien unterscheiden sich im Wesentlichen in der Art und Weise, wie Informationen ausgedrückt und Daten transportiert werden. Die Daten selbst werden typischerweise in XML oder JSON dargestellt.

### SOAP und XML-RPC

SOAP (Simple Object Access Protocol) und XML-RPC haben folgende Eigenschaften:

* Sie geben ein nach außen hin einheitliches, XML-basiertes Nachrichtenformat vor
* Es gibt einen Endpunkt (URL), an den die Nachrichten typischerweise per POST gesendet werden
* Sie verpacken anwendungsspezifische Daten:
  * SOAP stellt Daten in beliebigen XML-Stukturen dar
  * XML-RPC bildet Prozedur/Funktionsaufrufe nach (Prozedurname und Argumente sowie Rückgabewerte)

### REST

REST-Interfaces sollen weniger Overhead erfordern als z.B. SOAP- und XML-RPC-Interfaces. Sie beziehen das Transportprotokoll in stärkerer Weise mit ein und benutzen seine Semantik, um Aussagen über Nachrichten zu treffen.

REST (Representational State Transfer) wurde von Roy Fielding im Jahre 2000 (Dissertation)  konzipiert und ist inzwischen im Web-Umfeld außerordentlich weit verbreitet. Es versteht sich auf ein über die reine Frage der programmatischen Nutzung von Web-Anwendungen hinausgehender allgemeiner Architektur-Ansatz für den Entwurf verteilter Hypermedia-Systeme. Hypermedia wird hierbei verstanden auf die internet-weite Verknüpfung von Daten, Informationen und Diensten. Das REST-Konzept basiert auf 6 Constraints:

* **Client-Server:** Die Trennung in Client und Server ermöglich eine "separation of concerns", z.B. der Datenhaltung und der Benutzerinteraktion. Wird die Client-Server-Aufteilung in guter Weise vorgenommen, sind Benutzeroberflächen portabler und Server skalierbarer zu realisieren.
* **Stateless:** In einem zustandslosen Protokoll muss serverseitig kein Verbindungs-Zustand gespeichert werden. Stattdessen enthält jede Nachricht alle notwendigen Informationen zur Repräsentation / Wiederherstellung eine Zustandes.
* **Cacheable:** Abhängig davon, ob Daten verändert werden und Nachrichten idempotent sind (d.h. bei mehr- und einfacher Anwendung zu dem gleichen Resultat führen) können Sie von zwischenliegenden Schichten gecachet werden, um Serverkomponenten zu entlasten und Dienste insgesamt skalierbarer zu gestalten.
* **Layered System:** Zwischen Client und Server können weitere Schichten wie Caches, Proxies und Load Balancer zwischengeschaltet sein.
* **Conde on Demand:** Optional sollte es möglich sein, Code, der z.B. für Verschlüsselungen oder Datentransformationen notwendig ist, an den Client zu übertragen.
* **Uniform Interface:** Die Nachrichten sollen eine einheitliche Form haben, damit sie anwendungsunabhängig verarbeitet werden können. Dabei spielen 4 Aspekte eine besondere Rolle:
* **Einheitliche Identifikation der Ressourcen:** im Falle von http z.B. durch URIs. Eine Ressource wird meist durch ihren Typ und eine ID bestimmt. Eine einheitliche Darstellung dieser Informationen für alle Arten von Ressourcen ermöglicht eine insgesamt einheitlichere Datenverarbeitung und -repräsentation.
* **Manipulation durch Repräsentationen:** Beim Lesen von Daten sendet der Server Daten in einer definierten Repräsentation (s. self-descriptive Messages). Diese Repräsentationen sollten auch für das Anlegen und Verändern von Daten verwendet werden können.
* **Self-Descriptive Messages:** Die Nachrichten sollten alle Informationen, die zu ihrer Verarbeitung notwendig sind, in einheitlicher Weise mitbringen, z.B. durch Content-Type-Angaben in http-Nachrichten.
* **Hypertext as the engine of application state (HATEOAS):** Zustandsübergänge der Anwendung sollen durch Hypertext abgebildet werden. Dadurch kann ein Client die möglichen Zustandsübergänge aus einer Datenrepräsentation einfach entnehmen und nutzen.

### REST Maturity Model

REST-Interfaces für Web-Anwendungen werden sehr unterschiedlich umgesetzt. Um auszudrücken, wie stark sie den Grundgedanken von REST entsprechen, wurde das REST-Maturity-Model für http-REST-Interfaches entworfen.

* **Level 0:** Wenig Übereinstimmung mit den REST-Gedanken - Nutzung nur eines Endpoints und einer Methode (POST). Ressourcen und Aktionen werden in diesem Fall über den Inhalt der Repräsentationen bestimmt (wie bei SOAP und XML-RPC).
* **Level 1:** Hier werden die Ressourcen über URIs identifiziert, aber weiterhin immer POST-Requests verwendet.
* **Level 2:** Auf diesem Level werden Aktionen über http-Verben abgebildet und Ressourcen über URIs repräsentiert.
* **Level 3:** Sind in den Repräsentationen konsequent Hyperlinks auf mögliche Zustandsübergänge enthalten, dann wird das HATEOAS-Prinzip umgesetzt und den REST-Ideen am meisten entsprochen.

**Achtung:** Auch, wenn es oft wertend verwendet wird, fällt das REST-Maturity-Model keine Qualitätsurteile über ein Interface. Es kann gute Gründe für eine Umsetzung auf jedem der 4 Level geben, allerdings sollten sich die Entwickler bewusst sein, was sie tun und wo die Unterschiede zwischen den verschiedenen Realisierungsoptionen liegen.

---

## File-Server

Die Beispielanwendung in diesem Kapitel ist ein einfaches Interface zu einem Teil des Dateisystems. Per REST wird ein Verzeichnis exponiert, aus dem Dateien gelesen, geschrieben, verändert und gelöscht werden können.

Die Implementation ist im Code zur Vorlesung unter woche09/file_server_rest.py verfügbar. Zum Testen reicht der normale HTML-Modus des Browsers nicht aus, weil POST-Requests nicht durch URL-Eingabe abgesetzt werden können und PUT- und DELETE-Requests vom Browser gar nicht ohne weiteres erzeugt werden. Es gibt sehr viele unterschiedliche spezielle REST-Clients, entweder als Browser-Plugin oder

Die Routen sind:
|Route 	|Ziel 	|CRUD-Operation 	|Bedeutung|
|-------|-------|-----------------|---------|
|GET |/files/ 	|Collection 	|READ 	|Listet alle Dateien auf|
|GET |/files/one 	|Einzeldatei 	|READ 	|Ruft Inhalt der Datei "one" ab.|
|POST |/files/ 	|Collection 	|CREATE 	|Neue Datei anlegen|
|PUT |/files/one 	|Einzeldatei 	|UPDATE 	|Inhalt der Datei "one" ersetzen|

### Micro-Frameworks

Das bislang im Laufe der Vorlesung entwickelte Server-Framework lehnt sich mit vielen seiner Ideen und Konzepte an "große" Frameworks wie z.B. Django an. Es bietet die Möglichkeit, Anwendungen zu modularisieren, Teile als Apps wiederzuverwenden und durch Trennung von anwendungsspezifischen Teilen und Middlewares auf mehreren Ebenen zu abstrahieren. Für komplexere Serverprojekte wird diese Art von Frameworks vielfältig eingesetzt.

Für die Umsetzung einfacherer Server wie z.B. einem bloßen REST-Interface zu Datenbanken-Inhalten erzeugen vollständige Frameworks jedoch einigen Overhead. Deshalb haben sich verschiedenste Micro-Frameworks etabliert, die mit besonders wenig Code zu läuffähigen Server-Anwendungen führen. Bekannte Beispiele sind Bottle und Flask für Python, Camping für Ruby und Express.js für node.js.

#### Einsatz von Dekoratoren für die Auszeichnung von Routen

In Microframeworks werden Methoden oder Funktionen häufig an Ort und Stelle als zuständig für bestimmte Routen markiert. Im hier implementierten Beispiel geschieht das mit Python-Funktions-Dekoratoren (s. z.B. hier). Funktions-Dekoratoren sind "syntactic sugar" für die Ergänzung von Funktionen mit umgebendem Code. Ihre Definition bietet einige Möglichkeiten zur Verwirrung, ist mit etwas Nachdenken und ohne Scheu vor den Umgang mit Funktionen als "first class"-Objekten aber recht simpel.

Das Beispiel aus dem Video zeigt, wie einfach damit kleine Handler umgesetzt werden können:

```python
from server.micro import MicroApp

app = MicroApp()

@app.get('')
def index(request, response, pathmatch):
    response.send(body="""<!DOCTYPE html><html><body>
           Ahoi!
           <form action='greet' method='POST'>
           <input type=text name=name>
           </form></body></html>""")

@app.post('greet')
def greet(request, response, pathmatch):
    response.send(body="""
      <!DOCTYPE html><html><body>
        Ahoi {}!
      </body></html>""".format(request.params['name']))

app.serve()
```

Die Zeilen @app.get('') und @app.post('greet') registrieren dabei die direkt nachfolgend definierten Funktionen als Routen in der App app. Der Parameter des Dekorator folgt der bekannten Syntax für Routen-Ausdrücke.

#### Abbildung auf das Server-Framework

Das Server-Framework musste noch etwas erweitert werden, um Routen mit Methoden verknüpfen zu können. Dazu haben Routen nun optional eine Liste von für sie geltenden Methoden. Ist die Liste leer (default), gilt die Route für alle http-Methoden.

Das Microframework beruht darauf, dass es für die benutzte Webserver-Instanz nur genau eine App gibt, die eine bekannte Menge an Middlewares und ggf. Hilfs-Apps initialisiert. Diese App (MicroApp) wird durch die dekorierten Funktionen erweitert. Der Rest der Verarbeitung läuft wie gewohnt ab.

### File-Server Implementation

Der File-Server bildet im Wesentlichen die REST-Operationen auf Datei-Aktionen ab. Zusätzlich gibt es für die Wurzel-Route eine Übersicht über die verfügbaren Collections / Ressourcentypen. In der vorliegenden Implementation ist das lediglich der Typ "files".

Das Ergebnisformat ist JSON, das unter Zuhilfenahme des Python-Moduls "json" erzeugt wird.

Soll eine neue Datei erzeugt werden, wird der Dateiinhalt als Parameter "content" an die Collection-URL gePOSTet. Dabei wird - nicht sehr realitätsnah - ein Dateiname zufällig erzeugt. Vorbild dabei ist der Umgang mit Datenbank-IDs, die auch von der Datenbank-Engine selbst erzeugt werden. Aufgrund der Implementation ist es aber auch möglich, eine neue Datei mit bekanntem Namen durch einen PUT-Request zu erzeugen.

Alle weiteren Implementationsdetails entsprechen den schon bekannten Funktionalitäten des Server-Frameworks.

### File-Client Implementation

Als Client für den File-Server wird hier eine Javascript-Anwendung vorgestellt. Im Gegensatz zu den AJAX-Anwendungen aus vorherigen Kapiteln wird hier kein HTML vom Server erzeugt und in Häppchen dem Client übergeben. Stattdessen bekommt die Anwendung die Daten als JSON-Strukturen und erzeugt daraus selbst, durch Einsatz einer clientseitigen Template-Engine den dynamischen HTML-Code.

Die Javascript-Anwendung stellt eine so genannte "Single Page App" dar. Das bedeutet:

* Die statischen Dateien der Anwendung werden einmalig vom Server geladen. Sie umfassen:
  * Ein HTML-Rahmendokument, das mindestens das Laden von Javascript-Code anstößt.
  * Alle notwendigen Javascript-Dateien
  * ggf. weitere statische Dateien wie Style-Sheets, Bilder etc.
* "Single Page" bedeutet, dass bei der Arbeit mit der Anwendung niemals eine neue HTML-Datei vom Browser als Haupt-Request angefordert wird, d.h. es wird niemals ein neues Document-Object erzeugt.
* "Single Page" bedeutet NICHT, dass die Anwendung aus nur einer Ansicht besteht. Der Javascript-Code kann die Ansicht beliebig verändern und alle Interaktionen abbilden, die auch mit einer rein serverseitigen Anwendung und vielen neuen Requests möglich sind.
