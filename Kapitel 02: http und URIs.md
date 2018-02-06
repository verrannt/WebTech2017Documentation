# Kapitel 02: HTTP und URIs

* [Uniform Ressource Identifiers](#uniform-ressource-identifiers)
  * [Syntax](#syntax)
  * [Requests for Comments](#requests-for-comments)
  * [Character Encoding](#character-encoding)
  * [Schemata](#schemata)
  * [HTTP URIs](#http-uris)
* [HTTP](#http)
  * [HTTP-Anwendungen als Client-Server-Anwendungen](#http-anwendungen-als-client-server-anwendungen)
  * [HTTP zu Fuß](#http-zu-fuß)
  * [HTTP Nachrichten](#http-nachrichten)
  * [HTTP Request](#http-request)
    * [HTTP Methoden](#http-methoden)
    * [Request-Header](#request-header)
  * [HTTP Response](#http-response)
    * [Status-Codes](#status-codes)
    * [Response-Header](#response-header)

---

## Uniform Ressource Identifiers

> A URI is an identifier consisting of a sequence of characters matching the syntax rule named <URI> in Section 3.
> It enables uniform identification of resources via a separately defined extensible set of naming schemes (Section 3.1).
> How that identification is accomplished, assigned, or enabled is delegated to each scheme specification.

[RFC 3986](https://tools.ietf.org/html/rfc3986)

### Vorteile und Eigenschaften von URIs:

* __Uniformity__: URIs können mit einheitlichen Verarbeitungsmechanismen behandelt werden, unabhängig von Inhalt und Zielsetzung der URIs.
* __Ressource__: Ressourcen können beliebige Online- und Offline-Ressourcen sein: Dokumente, Services, Personen, Telefonnummern, abstrakte Konzepte, ...
* __Identifier__: Identifikation dient dazu, die Ressource eindeutig zu bezeichnen. Sie muss dadurch weder definiert, noch in einzelnen ihrer Eigenschaften beschrieben werden.

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### Syntax

#### Definition

__URI__ = `scheme` `:` `hier-part` [ `?` `query` ] [ `#` `fragment` ]

Dabei steht `hier-part` für eine optionale _authority_ und den _path_.
Ist eine _authority_ vorhanden, beginnt sie mit zwei Schrägstrichen, und der Pfad muss mit einem Schrägstrich beginnen.

[Beispiele](https://tools.ietf.org/html/rfc3986#section-1.1.2)

> The following are two example URIs and their component parts:
>
>
>         foo://example.com:8042/over/there?name=ferret#nose
>         \_/   \______________/\_________/ \_________/ \__/
>          |           |            |            |        |
>        scheme    authority       path        query   fragment
>          |   _____________________|__
>         / \ /                        \
>         urn:example:animal:ferret:nose

[RFC 3986](https://tools.ietf.org/html/rfc3986)


Konzeptionell wird zwischen **URNs** (Universal Resource Name) und **URLs** (Universal Resource Locator).
URNs bezeichnen Objekte, wie z.B. ein Buch per ISBN-Nummer (`urn:isbn:0-486-27557-4`), ohne notwendigerweise darüber Auskunft zu geben, wie und wo das Buch gefunden werden kann.
URLs bezeichnen den Ort, an dem sich ein Objekt befindet, ohne notwendigerweise ein bestimmtes Objekt zu benennen (`http://www.newbooks.com/latest-entry`, `file:///home/username/RomeoAndJuliet.pdf`).
URIs können aber sowohl URNs als auch URLs sein, so dass die Unterscheidung tatsächlich nur konzeptioneller Natur ist.

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### Requests for Comments

Requests for Comments (RFCs) spezifizieren die wichtigsten Standards und Technologien für das Internet, also insbesondere auch das Web.
RFCs sind Spezifikationen, d.h. sie sind exakt und vollständig, aber leider nicht immer einfach zu lesen.
Als kleine Übung für den Umgang mit RFCs schauen Sie bitte in den [RFC3986](https://www.ietf.org/rfc/rfc3986.txt), der die generische URI-Synatx spezifiziert und beantworten Sie bitte die unten stehenden Fragen.

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### Character Encoding

Zeichen mit Sonderbedeutung

Einige Zeichen haben in URIs eine besondere Bedeutung (s. http://tools.ietf.org/html/rfc3986#section-2.2). Es sind dies die Zeichen:
```
: / ? # [ ] @
! $ & ‚ ( ) * + , ; =
```
Keine besondere Bedeutung haben die anderen verwendbaren Zeichen: `A-Z a-z 0-9 - . _ ~`.


#### Percent-encoding

Zeichen können in URIs per Percent-Encoding ausgedrückt werden:
```
pct-encoded = "%" HEXDIG HEXDIG
```
Die beiden Hexadezimalziffern stehen dabei für den ASCII-Code des zu codierenden Zeichens bzw. die Byte-Codes codierter UTF-8-Zeichen. Für eine Tabelle s. http://en.wikipedia.org/wiki/Percent_encoding

Das Leerzeichen wird häufig auch codiert, obwohl es eigentlich keine Sonderbedeutung hat.
URIs mit Leerzeichen werden aber u.U. falsch geparst.
Der Percent-Code des Leerzeichens ist `%20`, an manchen Stellen wird dafür aus historischen Gründen auch `+` verwendet.

#### Nutzung in Python

In Python können zum Codieren und Decodieren von URIs eingebaute Funktionen benutzt werden:

```
>>>  from urllib.parse import quote, unquote

>>> quote("/strange!&/f[i]len@ame=$()")
'/strange%21%26/f%5Bi%5Dlen%40ame%3D%24%28%29'
>>> quote("/path/to/file","") # auch / konvertieren
'path%2Fto%2Ffile'
>>> quote('noch ein "Beispiel"')
'noch%20ein%20%22Beispiel%22'
>>> unquote(quote('noch ein "Beispiel"'))
'noch ein "Beispiel"'
```

Die Funktionen quote und unquote werden typischerweise verwendet, um den Path-Teil einer URI zu codieren.

#### Zeichencodierung

URIs bestehen aus ASCII-Zeichen.
Es gibt 128 ASCII-Zeichen (7 Bit).

Sollen nicht-ASCII-Zeichen in URIs aufgenommen werden, muss zweischrittig vorgegangen werden:

1. Konvertierung in UTF-8
2. Prozent-Codierung aller UTF-8-Bytes

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### Schemata

Liste der offiziellen Schemata: http://www.iana.org/assignments/uri-schemes.html

Wichtige/bekannte Schemata:


Schema |  Protokoll | Verwendung  | Beispiel
--- | --- | --- | ---
`http`  | http | WWW | http://www.uni-osnabrueck.de
`https` | https | WWW | (sichere Verbindungen)  https://studip.serv.uni-osnabrueck.de
`ftp` | ftp | Dateiübertragung | ftp://ftp.rz.uos.de/
`svn` | proprietär |  Versionskontrollsystem |  
`itms` |  proprietär |  Apple iTunes Store   |
`mailto` | - | Angabe von E-Mail-Adressen |  tobias.thelen@uni-osnabrueck.de
`file` | -  |Referenzierung des lokalen Dateisystems|
`about` | -  | Interne Informationen/Funktionen z.B. des Browsers |  about:blank about:config

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### HTTP URIs

URIs für die Verwendung mit HTTP gehorchen den oben dargestellten Regeln.
Ein weiteres Beispiel verdeutlicht vor allem einen komplexeren Aufbau der Komponente Authority, die auch Benutzername und Passwort enthalten kann.

```
http://max:secret@www.example.org:80/demo/example.cgi?q=searchfor#sec1
|      |   |      |               | |                 |           |
|      |   |      host            | path              query       fragment
|      |   password               port
|      user
schema
```

[Zurück nach oben](#kapitel-02-http-und-uris)

---

## HTTP

### HTTP-Anwendungen als Client-Server-Anwendungen

Im Folgenden wird die aktuelle _HTTP-Version 1.1_  vorausgesetzt.

Sie ist in RFC2616 definiert: http://www.w3.org/Protocols/rfc2616/rfc2616.html


> The Hypertext Transfer Protocol (HTTP) is an application-level protocol for distributed, collaborative, hypermedia information systems.

(RFC2626, 1.1)

Im OSI-Referenzmodell ist http auf der Anwendungsschicht angesiedelt:

TCP/IP-Schicht |  ≈ OSI-Schicht | Beispiel
--- | --- | ---
Anwendungsschicht | 5–7 |HTTP, FTP, SMTP, POP, Telnet
Transportschicht |  4 | TCP, UDP, SCTP
Internetschicht | 3 | IP (IPv4,IPv6)
Netzzugangsschicht |  1–2 |Ethernet, Token Bus, Token Ring, FDDI

http ist zustandslos, d.h., dass Nachrichten sich nicht aufeinander beziehen und voneinander unabhängig behandelt werden.

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### HTTP zu Fuß

#### telnet als Browser

http-Verbindungen sind in der Regel TCP-Verbindungen, für Anfrage- und Antwortheader wird ASCII verwendet.
Der Standardport für http-Verbindungen ist Port `80`, so dass jede beliebige Webseite auch mit einem generellen Netzwerktool wie `telnet`
abgerufen werden kann.

Beispiel:

```
> telnet www.inf.uos.de 80
GET / HTTP/1.1
Host: www.inf.uos.de
... Ausgabe ...
```

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### HTTP Nachrichten

HTTP-Nachrichten (messages) sind Anfragen (Request) oder Antworten (Response). Beide verwenden ein generisches Nachrichtenformat, sind also strukturell ähnlich aufgebaut.

    HTTP-message    = Request | Response     ; HTTP/1.1 messages

    generic-message = start-line
                    (message-header CRLF)
                    CRLF
                    [ message-body ]
    start-line      = Request-Line | Status-Line

Der message-body enthält eine __entity__. Eine entity besteht aus _entity-header_ und _entity-body_:

    entity = (entity-header CRLF)
             entity-body

[Zurück nach oben](#kapitel-02-http-und-uris)

---

### HTTP Request

Request      = Request-Line
               (header CRLF)
               CRLF
               [ message-body ]

Request-Line = Method SP Request-URI SP HTTP-Version CRLF

Die __Request-Line__ hat z.B. die Form:

> GET / HTTP/1.1

#### HTTP Methoden
Die Menge der zulässigen Methoden ist nicht abschließend definiert. Anwendungen können selbst definierte Methoden verwenden, es ist dann aber nicht durch den http-Standard sichergestellt, dass die Gegenseite die Methode in der intendierten Form versteht bzw. interpretiert. Im Standard festgelegte Methoden sind:

|Methode	|safe?	|idempotent?|Beschreibung|
|-------|-------|-----------|------------|
|OPTIONS	|ja	    |ja	        |Optionen des Servers abfragen|
|**GET**	    |ja	    |ja	        |Informationen abrufen|
|HEAD	|ja	    |ja	        |Wie GET, aber nur Header senden|
|POST	|nein	|nein	    |Übergebene Entity verarbeiten|
|**PUT**	    |nein	|ja	        |Objekt unter der angegebenen URL ablegen|
|DELETE	|nein	|ja	        |Objekt unter der angegebenen URL löschen|
|TRACE	|ja	    |inhärent	|Liefert die angekommene Nachricht zurück|
|CONNECT	|ja	    |inhärent	|reserviert für Proxy-Anwendungen|

"safe?" gibt an, ob eine Operation seiteneffektfrei ist, d.h. serverseitig keine Daten verändert. Obwohl GET als reine Datenabfrage konzipiert ist, wird es von Webanwendungen häufig in anderer Weise verwendet (z.B. Kommando zum Löschen von Daten). Idempotente Operationen können beliebig oft wiederholt werden, ohne das es zu anderen Seiteneffekten als bei einer einzelnen Anfrage kommt.

#### Request-Header

Der HTTP-Standard definiert für Anfragen und Antworten insgesamt 47 Header-Felder. Eine Header-Zeile hat die Form:

    Feldname: Wert CRLF

Im Folgenden werden nur wenige Headerfelder aufgeführt. Die vollständige Liste ist in RFC2616, 14 aufgeführt.

##### Host
HTTP-Requests müssen das Headerfeld __Host__ enthalten; dieses muss den host-Teil, der zur Verbindung verwendet wurde, enthalten.

Beispiel:

    telnet www.inf.uos.de 80
    GET / HTTP/1.1
    Host: www.inf.uos.de
    ... Ausgabe ...

Das Feld muss enthalten sein, damit Server, die unter einer IP-Adresse mehrere Domänen hosten (Stichwort: virtuelle Server) entscheiden können, welcher virtuelle Host angesprochen wurde.

##### Accept
Ein Client kann angeben, welche entity-Typen und -Codierungen er entgegennehmen kann und welche er bevorzugt.

Beispiel:

    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,\*/\*;q=0.8

Der Wert für Accept ist eine komma-getrennte Liste von media-type-Angaben:

    media-type = type "/" subtype \*( ";" parameter )

Der Parameter hat die Form q=Wert, wobei Wert eine Präferenz zwischen 0 (nicht akzeptabel) und 1 (vollkommen akzeptabel) angibt.

##### Media-Types
Die Liste der erlaubten media-types ist nicht abschließend definiert. Der media-type entspricht (mit geringen Abweichungen) den für E-Mails definierten MIMI-Types. Eine Liste offiziell registrierter Typen wird unter http://www.iana.org/assignments/media-types/ bereitgestellt.

Wichtige Typen sind z.B.:

|Bezeichnung	|typische Dateiendung	|Bedeutung
|---|---|---|
|text/html	|.html	|HTML-Dokumente
|image/jpeg	|.jpg	|JPG-Bilder
|text/plain	|.txt	|ASCII-Text
|application/octet-stream	|-	|Generischer Typ für Binärdaten

text/plain und application/octet-stream sind generische Typen, die häufig für ganz unterschiedliche Text- bzw. Binärdaten verwendet werden.

Der Wert für akzeptierte media types kann Wildcards enthalten:

text/\* akzeptiert beliebige Text-Typen

\*/\* akzeptiert beliebige Typen


[Zurück nach oben](#kapitel-02-http-und-uris)

---

### HTTP Response
Auf eine http-Anfrage muss der Server mit einer http-Antwort antworten. Die allgemeiner Form einer solchen Antwort ist:

    Response      = Status-Line           
                    (response-header | entity-header CRLF)
                    CRLF
                    [ message-body ]

    Status-Line   = HTTP-Version SP Status-Code SP Reason-Phrase CRLF

Eine Statuszeile kann beispielhaft so aussehen:

    HTTP/1.1 200 OK

#### Status-Codes
Die Liste der möglichen Status-Codes ist nicht abschließend definiert, allerdings ist durch den Standard nicht sichergestellt, dass neu eingeführte Codes mit gleicher Semantik interpretiert werden. Die im Standard festgelegte Liste an Status-Codes ist unter http://www.w3.org/Protocols/rfc2616/rfc2616-sec6.html#sec6.1.1 zu finden.

Die erste Stelle der dreistelligen numerischen Codes gibt die Status-Code-Klasse an:

|Code	|Klasse
|-------|--------------
|1xx	|Informational
|2xx	|Success
|3xx	|Redirection
|4xx	|Client Error
|5xx	|Server Error

Wichtige Codes sind:

|Code	|Reason-Phrase	|Anmerkung
|-------|---------------|---------
|200	|OK	|Bei jeder erfolgreichen Anfrage zu senden
|301	|Moved permanently	|Unter anderer Adresse zu finden
|400	|Bad Request	|Nicht wohlgeformte Anfrage
|401	|Authorization required	|Server benötigt Authentifizierung
|404	|Not found	|Gewünschte Ressource nicht gefunden
|500	|Internal Server Error	|Unerwarteter Fehler auf Serverseite
|501	|Not implemented	|Gewünschte Funktionalität nicht vorhanden
|503	|Service unavailable	|Die Anfrage kann derzeit nicht bearbeitet werden

Es ist wichtig, sinnvolle und korrekte Status-Codes zu senden, da sich Browser- und Robots darauf verlassen und ihr Verhalten entsprechend anpassen. Beispiel: Steht ein Dienst wegen Wartungsarbeiten nicht zu Verfügung, könnte die Fehlermeldung 404 den Crawler/Robot einer Suchmaschine veranlassen, früher unter dieser Adresse gefundene Inhalte aus dem Suchindex zu löschen. Für diesen Fall ist Code 503 besser geeignet.

#### Response-Header
Der http-Standard definiert für Anfragen und Antworten insgesamt 47 Header-Felder, die z.T. für beides, z.T. nur für Anfragen oder Antworten geeignet sind. Eine Header-Zeile hat die Form:

    Feldname: Wert CRLF

Im Folgenden werden nur wenige Headerfelder aufgeführt. Die vollständige Liste ist in RFC2616, 14 aufgeführt.

##### Date
Ein Webserver muss - sofern er über eine Uhr verfügt - einen Zeitstempel mitliefern, der den Zeitpunkt angibt, zu dem die Nachricht generiert wurde. Dadurch werden Clients in die Lage versetzt, sinnvolles Caching zu betreiben bzw. Anfragen so zu formulieren, dass Caching ermöglicht wird. Das Datumsformat ist in RFC1123 festgelegt (s. [RFC2626](http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1), 3.3.1).

Beispiel:

    Date: Fri, 16 Apr 2010 08:37:03 GMT

##### Content-Type
Content-type ist ein Entity-Header, der angibt, von welchem Medien-Typ die angehängte Entity ist. Erlaubte Medientypen entsprechen denen von __Accept__, erlauben aber keine Wildcards.

[Zurück nach oben](#kapitel-02-http-und-uris)
