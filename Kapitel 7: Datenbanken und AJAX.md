# Woche 7: Datenbanken und AJAX

1. [SQL-Datenbanken](#sql-datenbanken)
2. [NoSQL-Datenbanken](#nosql-datenbanken)
3. [MVC in Webanwendungen](#mvc)
4. [AJAX](#ajax)

---

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

INSERT-Statements fügen neue Zeilen in eine einzelne Tabelle ein. Je nachdem, ob Spalten als verpflichtend definiert wurden, müssen alle oder nur einige der Spalten mit Werten belegt werden. Spaltennamen werden ohne Quotes oder ggf. mit Backquote `....` geschrieben, String-Werte mit einfachen Quote '...', Zahlenwerte ohne Anführungszeichen.

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

sql = "INSERT INTO person (firstname, lastname, email) VALUES ('Hans', 'Meier', 'hmeier@email.de')"
conn.execute(sql)
conn.commit()

fn = "Hans"
for row in conn.execute("SELECT * FROM person WHERE firstname=?;", [fn]):
    print(row)
```

---

[Zurück nach oben](#woche-7-datenbanken-und-ajax)
## NoSQL-Datenbanken

### SQL vs. NoSQL

In **relationalen Datenbanksystemen** sind **jederzeit beliebige Verknüpfungen** zwischen allen enthaltenen Daten möglich (dynamisch erzeugte Queries mit JOIN-Klauseln). Die Modellierung ist (wenn sie gewissen Anforderungen genügt) hinreichend allgemein, dass viele verschiedene Anwendungsfälle mit der Modellierung abbildbar sind.

Der damit erkaufte **Nachteil** ist: Es ist nicht möglich, die Datenbank zu partitionieren, d.h. die Datenbank auf viele Server zu verteilen, ohne die Möglichkeit beliebiger JOINs aufzugeben oder zumindest sehr ineffizient zu machen. In Webanwendungen (denken Sie an Datenmengen von Google oder Facebook) werden die Grenzen relationaler Datenbanken aber mit Leichtigkeit gesprengt.

**NoSQL-Datenbanken** hingegen sind für die Verarbeitung **großer und sehr großer Datenmengen** ausgelegt. Serverkapazität kann erweitert werden, ohne dass die Leistungsfähigkeit der Datenbank beeinträchtigt wird.

**Erkauft wird dieser Vorteil durch den Verzicht auf JOINs**. Es ist nicht möglich, die enthaltenen Daten auf effiziente Weise beliebig innerhalb des Datenbanksystems zu verknüpfen (man stelle sich vor, sie lägen ohnehin auf verschiedenen Servern). Bei der Modellierung muss daher in stärkerem Maße als bei SQL-Datenbanken **der spätere Anwendungszweck berücksichtigt werden**.

NoSQL-Datenbanken sind zudem **häufig schemafrei**, d.h. sie benötigen keine strenge Definition eines Datenbankaufbaus, sondern können jederzeit auch abweichende oder erweiterte Datensätze speichern. Dafür muss dann aber die verarbeitende Anwendung stärker darauf achten, ob die Daten dem erwarteten Format entsprechen.

### ACID vs. BASE

Relationale Datenbank sind auf Datenintegrität, korrekte Modellierung, Transaktionssicherheit und Konsistenz ausgelegt. Diese Eigenschaften werden häufig unter dem Acronym ACID zusammengefasst:

    - A - Atomicity: Zusammengehörige Aktionen gelingen entweder vollständig oder gar nicht.
    - C - Consistency: Verweise innerhalb der Datenbank sind jederzeit korrekt (referentielle Integrität)
    - I - Isolation: Transaktionen beeinflussen sich nicht gegenseitig, während sie ablaufen
    - D - Durability: Die Daten werden dauerhaft gespeichert.

Allerdings haben relationale Datenbanken auch einige Nachteile, die in den Folgeabschnitten beschrieben werden. Als Alternative haben sich vor allem für Web-Anwendungen so genannte NoSQL-Datenbanken etabliert, die in einigen wichtigen Punkten von relationalen Datenbanken abweichen. Sie werden häufig mit dem Acronym BASE beschrieben:

    - BA - Basically available: Die Daten sind immer verfügbar, aber ggf. nicht in der aktuellsten Version
    - S - Soft state: Die Daten sind nicht immer konsistent
    - E - Eventual consistency: Aber irgendwann werden sie wieder konsistent

Nicht alle NoSQL-Datenbanken erfüllen all diese Kriterien, sie machen aber eines deutlich: Für ein auf Sicherheit und Konsistenz ausgelegtes Datenbanksystem (z.B. für die Führung von Bankkonten) sind diese Eigenschaften absolut unerwünscht. In Web-Anwendungen sieht die Lage aber oft anders aus: Ob Sie wirklich das neueste Facebook-Posting sofort oder erst in einigen Minuten sehen, ist meist verschmerzbar, genauso ist die ganz korrekte Zahl der angezeigten Likes nicht entscheidend.

### MongoDB

MongoDB ist eine so genannte dokumentorientierte Datenbank. In einer solchen Datenbank sollen die von der Anwendung benötigten Daten so abgelegt werden, dass sie aus Anwendungssicht vollständige Dokumente ergeben.

MongoDB repräsentiert seine Daten im BSON-Format, einem binären JSON-Format. Die darstellbaren Typen entsprechen damit nahezu 1:1 JSON. Eine Peron könnte auf diese Weise wie folgt definiert werden:

```javascript
{ 'id': f38aad43,
  'firstname': 'Tobias',
  'lastname': 'Thelen',
  'hobbies': ['Singen', 'Springen', 'Fröhlich sein'],
  'teams': [{ 'name': 'Tigers' }, { 'name': 'Lions' }]
}
```

Alle Personen würden dann in einer Collection *persons* zusammengefasst werden.

Die Darstellung der Hobbies und Teams ist in relationalen Datenbanken auf diese Weise nicht möglich. Allerdings können auch hier nicht beliebige Strukturen abgebildet werden: In den Teams wiederum Personen-Dokumente einzubetten führt zu zirkulären Definitionen.

An dieser Stelle müssen also Referenzen in Form von IDs verwendet werden. Dazu könnten z.B. die Team-Subdokumente durch ihre IDs ersetzt werden. Dann müssen die Daten auf Anwendungsebene zusammengefügt werden, d.h. nach mehreren Einzelabfragen werden "Application Level Joins" vorgenommen.

```javascript
// a person:
{ 'id': f38a,
  'teams': [ea89, u9id]
}
// a team:
{ 'id': ea89,
  'name': 'tigers',
  'members': [f38a, ...]
}
```

### MongoDB-Abfragen

Als Abfragesprache dienen ebenfalls BSON-Dokumente. Es wird dann geprüft, zu welchen Dokumenten in der gewählten Collection das Musterdokument passt.

Beispiele:

```javascript
// Alle Dokumente mit Feld "firstname" und Wert "Tobias"
{ "firstname": "Tobias" }
// Alle Dokumente bei denen "Singen" in der Liste der Hobbies vertreten ist
{ "hobbies": "Singen" }
// Alle Dokumente, die Singen oder Brieftauben züchten als ein Hobby aufführen (oder-verknüpft)
{ "hobbies": { "$in" : [ "Singen", "Brieftauben züchten" ] } }
// Alle Personen, die ein age-Attribut haben und älter als 25 sind ($gt = "greater than", $lt = "lower than")
{"age": { "$gt": 25, "$lt": 44 } }
```

Es ist **nicht** möglich, auf Dokumente aus verschiedenen Collections zuzugreifen.

### MongoDB in Python

Mit der **pymongo**-Bibliothek (separat zu installieren) können Mongo-DB-Datenbanken auf einfache Weise von Python aus genutzt werden. BSON-Dokumente werden von der Bibliothek in Python-Dictionaries umgewandelt und umgekehrt.

Das Vorgehen entspricht immer folgendem Schema:

1. Verbindung zum Datenbankserver herstellen und Client initialisieren (Zeile 3)
2. Datenbank auswählen. Wenn diese Datenbank nicht existiert, wird sie angelegt (Zeile 4)
3. Collection innerhalb der Datenbank auswählen (wenn inexistent wird sie angelegt) und CRUD-Operationen durchführen (Zeile 6ff.)

Die typischen Operationen **CREATE**, **READ**, **UPDATE**, **DELETE** (CRUD) werden durch vier Methoden eines Collection-Objektes abgebildet:

**CREATE** = collection.*insert*(BSON-Dokument)

**READ** = collection.*find*(BSON-Such-Dokument)

**UPDATE** = collection.*update*(BSON-Such-Dokument, BSON-Dokument)

**DELETE** = collection.*delete*(BSON-Such-Dokument)

#### Bespiel-Code:

```python
import pymongo

client = pymongo.MongoClient('mongodb://localhost:27017/')
db = client['database-name']

db['staedte'].insert({'name': 'Osnabrück', 'inhabitants': 170000,
                      'quarters': ['Wüste', 'Hellern', 'Voxtrup']
                      'sights': [ {'name': 'Rathaus', 'age': 505},
                                  {'name': 'Schloss', 'age': 449} ]})

db['staedte'].insert({'name': 'Bramsche', 'inhabitants': 32000,
                      'quarters': ['Bramsche', 'Entger', 'Kalkriese']
                      'sights': [ {'name': 'Varus-Schlacht', 'age': 2007}]})

for item in db['staedte'].find({'sights.age' : {'$gt': 800}}):
    print(item['name'])

# will print 'Bramsche'
```

---

[Zurück nach oben](#woche-7-datenbanken-und-ajax)
## MVC in Webanwendungen

---

[Zurück nach oben](#woche-7-datenbanken-und-ajax)
## AJAX
