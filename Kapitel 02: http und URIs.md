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


Konzeptionell wird zwischen **URN**s (Universal Resource Name) und **URL**s (Universal Resource Locator). 
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

