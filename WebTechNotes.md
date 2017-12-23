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
3. [MVC in Webanwendungen](#mvc)
4. [AJAX](#ajax)

<div id='sql-datenbanken'/>

## SQL-Datenbanken

### Datenbanken in Web-Anwendungen

Web-Anwendungen benötigen in aller Regel eine separate Datenhaltung, um die Daten zwischen den verschiedenen Request-Response-Zyklen, aber auch über mehrere Webserver und eventuelle Server-Neustarts hinweg persistent zu halten.

Der Session-Mechanismus, den wir in der letzten Woche kennengelernt haben, ist auch ein Persistenz-Mechanismus. Für viele Anwendungen ist er  aber nicht ausreichend, weil die Session-Daten immer nutzerspezifisch abgelegt werden. Alle Anwendungen, die Daten für mehrere Nutzer zugänglich machen wollen, benötigen also einen anderen Mechanismus. Sessions müssen immer komplett eingelesen werden, man kann über sie nicht suchen und sie sind im Grunde so angelegt, dass sie nur temporär existieren - nämlich solange eine gültige Login-Session besteht.

Die typische Persistenz-Schicht für eine Web-Anwendung ist eine Datenbank, allerdings kann auch - wie in einigen Beispiele hier schon gesehen - das Dateisystem genutzt werden. Im Folgenden werden Dateisystem, klassische relationale SQL-Datenbanken und dokumentorientierte NoSQL-Datenbanken miteinander verglichen.

### Wichtige SQL-Statements

#### Schreiben von neuen Datenbankeinträge (**CREATE**):

```sql
INSERT INTO table (column1, column2, column3) VALUES (value1, value2, value3)
```

INSERT-Statements fügen neue Zeilen in eine einzelne Tabelle ein. Je nachdem, ob Spalten als verpflichtend definiert wurden, müssen alle oder nur einige der Spalten mit Werten belegt werden. Spaltennamen werden ohne Quotes oder ggf. mit Bakcquote `....` geschrieben, String-Werte mit einfachen Quote '...', Zahlenwerte ohne Anführungszeichen.

#### Auslesen von Datenbankeinträge (**READ**):

```sql
SELECT * FROM table WHERE condition1 AND condition2 OR condition3 ...
```

Dieses Statement liefert einzelne Zeilen (bei Angabe von \*, ansonsten nur Teile von Zeilen, nämlich die spezifizierten Spalten) aus einer Tabelle, für die die angegebenen Bedingungen (Tests auf Gleichheit, größer als, kleiner als, ...) erfüllt sind. Wenn die WHERE-Klausel entfällt, werden alle Zeilen einer Tabelle ausgewählt.

#### Ändern von Datenbankeinträgen (**UPDATE**):

```sql
UPDATE table SET column1=value1, column2=value2, column3=value3 WHERE condition1 AND condition2 ...
```

Das Update-Statement identifiziert per WHERE-Klausel einzelne einschlägige Zeilen und ändert dort die angegebenen Werte.

#### Löschen von Datenbankeinträge (**DELETE**):

```sql
DELETE FROM table WHERE condition1 AND condition2 ...
```

Löscht alle Zeilen aus einer Tabelle, die die angegebenen Bedingungen erfüllen.

### Komplexere SQL-Statements (Verbinden mehrerer Tabellen)

Die Statements und Queries im vorherigen Abschnitt haben immer nur auf eine Tabelle zugegriffen. Bei der Modellierung haben wir aber die Informationen auf mehrere Tabellen aufgeteilt. So kann ich z.B. nicht mit einem einzigen einfachen SELECT-Query sowohl Namen als auch Hobbies einer Person bekommen, weil diese Informationen auf zwei Tabellen aufgeteilt sind.

Die Lösung heißt **"JOIN"**. Anstelle nur einer Tabelle, aus der das SELECT-Query lesen soll, können auch mehrere miteinander verknüpfte Tabellen angegeben werden. Die JOIN-Klauseln geben an, über welche Spalten die Tabellen verknüpft werden. Typischerweise sind das Schlüssel und Fremdschlüssel. Es gibt verschiedene JOIN-Typen, wir benötigen hier nur LEFT JOIN.

Syntax:
```sql
SELECT * FROM table1 LEFT JOIN table2 ON table1.column1 = table2.column2 WHERE condition1 AND condition2 ...
```
Am einfachsten lassen sich JOIN-Konstruktionen verstehen, wenn man sich eine neue größere Tabelle vorstellt, die alle Spalten aller am JOIN beteiligten Tabellen vereint sind. Die Zeilen sind dann alle gültigen Kombinationen von Zeilen der ersten, zweiten, ... Tabelle, die über die ON-Bedingung verknüpft sind und die angegebenen Bedingungen erfüllen. Die maximale Anzahl an Zeilen in dieser virtuellen Tabelle ist das Produkt aus den Zeilenanzahlen aller beteiligten Tabellen.

### SQLite in Python

Python enthält eine vollständige SQL-Datenbank-Engine, die allerdings nicht auf große Datenmengen und Performance ausgelegt ist.

Der grundsätzliche Ablauf ist:

1. Verbindung zu einer Datenbank aufbauen (per Dateiname). Wenn sie noch nicht exisitiert, wird sie angelegt.
2. Ein Cursor-Objekt für die Verbindung erzeugen.
3. Ein SQL-Statement per execute-Methode an das Cursor-Objekt senden.
4. Vor allem bei lesenden Zugriffen: Ergebnisse abfragen/abholen.
5. Bei ändernden Zugriffen: Datenbanktransaktion (mehrere zusammenhängende Einzelstatements) per commit-Methode des Verbindungs-Objektes abschließen.

Die SQLite-Schnittstelle ist dokumentiert unter https://docs.python.org/3/library/sqlite3.html

Beispiel-Code:

```python
import sqlite3

sql = """
CREATE TABLE IF NOT EXISTS person (
  id integer PRIMARY KEY,
  firstname text NOT NULL,
  lastname text NOT NULL,
  email text NOT NULL
);"""

conn = sqlite3.connect('sqlite_demo.db')
curs = conn.cursor()
curs.execute(sql)
conn.commit()

sql = "INSERT INTO person (firstname, lastname, email) VALUES ('Tobias', 'Thelen', 'tthelen@uos.de')"
conn.execute(sql)
conn.commit()

fn = "Tobias"
for row in conn.execute("SELECT * FROM person WHERE firstname=?;", [fn]):
    print(row)
```

<div id='nosql-datenbanken'/>

## NoSQL-Datenbanken

<div id='mvc'/>

## MVC in Webanwendungen

<div id="ajax"/>

## AJAX
