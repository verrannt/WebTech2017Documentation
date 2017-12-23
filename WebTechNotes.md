## HTTP Nachrichten
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

## HTTP Request
    Request      = Request-Line
                   (header CRLF)
                   CRLF
                   [ message-body ]

    Request-Line = Method SP Request-URI SP HTTP-Version CRLF

Die __Request-Line__ hat z.B. die Form:

    GET / HTTP/1.1

### HTTP Methoden
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

### Request-Header

Der HTTP-Standard definiert für Anfragen und Antworten insgesamt 47 Header-Felder. Eine Header-Zeile hat die Form:

    Feldname: Wert CRLF

Im Folgenden werden nur wenige Headerfelder aufgeführt. Die vollständige Liste ist in RFC2616, 14 aufgeführt.

#### Host
HTTP-Requests müssen das Headerfeld __Host__ enthalten; dieses muss den host-Teil, der zur Verbindung verwendet wurde, enthalten.

Beispiel:

    telnet www.inf.uos.de 80
    GET / HTTP/1.1
    Host: www.inf.uos.de
    ... Ausgabe ...

Das Feld muss enthalten sein, damit Server, die unter einer IP-Adresse mehrere Domänen hosten (Stichwort: virtuelle Server) entscheiden können, welcher virtuelle Host angesprochen wurde.

#### Accept
Ein Client kann angeben, welche entity-Typen und -Codierungen er entgegennehmen kann und welche er bevorzugt.

Beispiel:

    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,\*/\*;q=0.8

Der Wert für Accept ist eine komma-getrennte Liste von media-type-Angaben:

    media-type = type "/" subtype \*( ";" parameter )

Der Parameter hat die Form q=Wert, wobei Wert eine Präferenz zwischen 0 (nicht akzeptabel) und 1 (vollkommen akzeptabel) angibt.

#### Media-Types
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

## HTTP Response
Auf eine http-Anfrage muss der Server mit einer http-Antwort antworten. Die allgemeiner Form einer solchen Antwort ist:

    Response      = Status-Line           
                    (response-header | entity-header CRLF)
                    CRLF
                    [ message-body ]

    Status-Line   = HTTP-Version SP Status-Code SP Reason-Phrase CRLF

Eine Statuszeile kann beispielhaft so aussehen:

    HTTP/1.1 200 OK

### Status-Codes
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

### Response-Header
Der http-Standard definiert für Anfragen und Antworten insgesamt 47 Header-Felder, die z.T. für beides, z.T. nur für Anfragen oder Antworten geeignet sind. Eine Header-Zeile hat die Form:

    Feldname: Wert CRLF

Im Folgenden werden nur wenige Headerfelder aufgeführt. Die vollständige Liste ist in RFC2616, 14 aufgeführt.

#### Date
Ein Webserver muss - sofern er über eine Uhr verfügt - einen Zeitstempel mitliefern, der den Zeitpunkt angibt, zu dem die Nachricht generiert wurde. Dadurch werden Clients in die Lage versetzt, sinnvolles Caching zu betreiben bzw. Anfragen so zu formulieren, dass Caching ermöglicht wird. Das Datumsformat ist in RFC1123 festgelegt (s. [RFC2626](http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1), 3.3.1).

Beispiel:

    Date: Fri, 16 Apr 2010 08:37:03 GMT

#### Content-Type
Content-type ist ein Entity-Header, der angibt, von welchem Medien-Typ die angehängte Entity ist. Erlaubte Medientypen entsprechen denen von __Accept__, erlauben aber keine Wildcards.

----

### NOTE: Several chapters are missing here

----

# Woche 7: Datenbanken und AJAX

1. [SQL-Datenbanken](#sql-datenbanken)
2. [NoSQL-Datenbanken](#nosql-datenbanken)
3. [MVC in Webanwendungen]
4. [AJAX]

<div id='sql-datenbanken'/>

## SQL-Datenbanken

914 translation by H. Rackham

"But I must explain to you how all this mistaken idea of denouncing pleasure and praising pain was born and I will give you a complete account of the system, and expound the actual teachings of the great explorer of the truth, the master-builder of human happiness. No one rejects, dislikes, or avoids pleasure itself, because it is pleasure, but because those who do not know how to pursue pleasure rationally encounter consequences that are extremely painful. Nor again is there anyone who loves or pursues or desires to obtain pain of itself, because it is pain, but because occasionally circumstances occur in which toil and pain can procure him some great pleasure. To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it? But who has any right to find fault with a man who chooses to enjoy a pleasure that has no annoying consequences, or one who avoids a pain that produces no resultant pleasure?"
Section 1.10.33 of "de Finibus Bonorum et Malorum", written by Cicero in 45 BC

"At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga. Et harum quidem rerum facilis est et expedita distinctio. Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus id quod maxime placeat facere possimus, omnis voluptas assumenda est, omnis dolor repellendus. Temporibus autem quibusdam et aut officiis debitis aut rerum necessitatibus saepe eveniet ut et voluptates repudiandae sint et molestiae non recusandae. Itaque earum rerum hic tenetur a sapiente delectus, ut aut reiciendis voluptatibus maiores alias consequatur aut perferendis doloribus asperiores repellat."

<div id='nosql-datenbanken'/>

## NoSQL-Datenbanken

914 translation by H. Rackham

"But I must explain to you how all this mistaken idea of denouncing pleasure and praising pain was born and I will give you a complete account of the system, and expound the actual teachings of the great explorer of the truth, the master-builder of human happiness. No one rejects, dislikes, or avoids pleasure itself, because it is pleasure, but because those who do not know how to pursue pleasure rationally encounter consequences that are extremely painful. Nor again is there anyone who loves or pursues or desires to obtain pain of itself, because it is pain, but because occasionally circumstances occur in which toil and pain can procure him some great pleasure. To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it? But who has any right to find fault with a man who chooses to enjoy a pleasure that has no annoying consequences, or one who avoids a pain that produces no resultant pleasure?"
Section 1.10.33 of "de Finibus Bonorum et Malorum", written by Cicero in 45 BC

"At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga. Et harum quidem rerum facilis est et expedita distinctio. Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus id quod maxime placeat facere possimus, omnis voluptas assumenda est, omnis dolor repellendus. Temporibus autem quibusdam et aut officiis debitis aut rerum necessitatibus saepe eveniet ut et voluptates repudiandae sint et molestiae non recusandae. Itaque earum rerum hic tenetur a sapiente delectus, ut aut reiciendis voluptatibus maiores alias consequatur aut perferendis doloribus asperiores repellat."

## MVC in Webanwendungen

## AJAX
