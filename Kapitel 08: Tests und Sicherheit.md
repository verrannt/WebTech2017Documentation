# Kapitel 8: Tests und Sicherheit

1. [Webanwendungen testen](#webanwendungen-testen)
  1. [Unit-Tests](#unit-tests)
2. [Websicherheit](#websicherheit)
  1. [Path-Traversal-Angriff](#path-traversal-angriff)
  2. [SQL-Injections](#sql-injections)
  3. [Special-Character-Injections](#special-character-injections)
  4. [Cross-Site-Scripting](#cross-site-scripting)
  5. [Session-Hijacking](#session-hijacking)
  6. [Session-Fixation](#session-fixation)
  7. [Cross-Site-Request-Forgery](#cross-site-request-forgery)

---

## Webanwendungen testen

Automatisierte Tests sind ein wesentliches Merkmal guten Software-Engineerings: Nur durch sie lässt sich die Komplexität nichttrivialer Softwareprojekte während des gesamten Entwicklungsprozesses beherrschen.

Web-Anwendungen stellen etwas andere Anforderungen an die Testprozeduren als "einfache" Programme wie Bibliotheken (kein User-Interface) oder Kommandozeilentools (sehr einfaches User-Interface). Die größte Besonderheit liegt natürlich im Zusammenspiel von Client und Server einschließlich der Aufteilung aktiven Codes in Server- und Client-Code und der Frage, wie die verschiedenen Komponenten sowohl isoliert als auch in ihrem Zusammenspiel getestet werden können.

### Unit-Tests

Unit-Tests gehen davon aus, dass das zu testende Programm aus mehreren unabhängigen "Units" besteht. Zwischen diesen Units gibt es klar definierte Schnittstellen, somit lässt sich eine Unit auch individuell, unabhängig von den anderen Units betrachten.

Die Idee ist, dass man eine solche Unit nimmt und in eine Testvorrichtung "einspannt". Der eigentliche Test wird dabei zwischen **SETUP** und **TEAR DOWN** eingespannt.

**Wichtig** sind Unit-Tests vorallem bei dynamischen Programmiersprachen, da hier Dinge wie Compilerfehler und Typenchecks entfallen.

Es geht darum, **vergleichbare Bedingungen** zu schaffen:
Echtdaten (realistischer, dafür aber kompliziertere Tests)
Testdaten (einfacher, dafür weniger realistisch)


[Zurück nach oben](#kapitel-8-tests-und-sicherheit)
---

## Websicherheit

### Path-Traversal-Angriffe
Bei einem Path-Traversal-Angriff versucht der Angreifer über einen manipulierten String, der als Request-Parameter oder Bestandteil eines URL-Pfades an den Server übergeben wird, ungewünschten Zugriff auf das Dateisystem zu erhalten. Dazu muss der Paramater als Teil eines Dateipfades bzw. -namens verwendet und unzureichend geprüft werden.

#### Angriffsziel
Dateisystem des Servers

#### Angriffsvektor
Manipulierter Request-Parameter oder URL-Pfad-Bestandteil

#### Gegenmaßnahmen
**Eingabevalidierung**: Sind in der Eingabe nur Zeichen enthalten, die auch erwartet werden?
**Konstruktvalidierung**: Ist der zusammengesetzte Pfad gültig und zeigt er an eine erlaubte Stelle im Dateisystem?


### SQL-Injections

#### Angriffsziel
Der Angriff zielt auf die Server-Datenbank, die ausgelesen, manipuliert und sabotiert werden kann.

#### Angriffsvektor
Manipulierte Eingabestrings in Request-Parametern und URL-Pfadbestandteilen

#### Gegenmaßnahmen
Eingabenvalidierung
Konstruktvalidierung, vor allem durch Verwendung von Escape-Mechanismen des Datenbank-Treibers.

SQL-Injections sind immer noch eine relativ häufige Sicherheitslücke mit schwerwiegenden Folgen. Angreifer können u.U. Zugriff auf große Mengen sensibler Nutzerdaten erhalten, die z.B. vollständige Namen, E-Mail-Adressen (interessant für Spam-Versender), Kreditkartendaten und Passwörter enthalten können. Sind die Passwörter nicht verschlüsselt, ist das Problem evident, sind sie nicht mit einem individuellen Salt versehen, können insbesondere häufig genutzte Passwörter sehr leicht geknackt werden.

Bei korrekter und konsequenter Verwendung der Escape-Funktionen der jeweiligen Datenbanktreiber kann das Risiko von SQL-Injections stark minimiert werden.

#### Beispiele
| SQL | Injection | Result|
|------|-------|-------|
|SELECT user_id FROM users WHERE name='tobias' AND password='{}' | 42' OR 1 ; -- | Login ohne Kenntnis des Passwortes|
|SELECT {} FROM users | cc_num | Ausgabe der Kreditkarten-Nummer statt der vorgesehenen Auswahl von Nickname oder vollständigem Namen|
|SELECT * FROM users ORDER BY {} | name; DELETE FROM users WHERE 1; -- | Löschen aller Nutzer|


### Special-Character-Injections

Die SQL-Injections stellen im Grunde nur einen Spezialfall eines allgemeineren Problems dar: Dem Einschleusen von Zeichen oder Zeichenketten, die in der weiteren Verarbeitung eine besondere Bedeutung haben. Die unter dem Begriff "Special Character Injections" bekannten Angriffe können sehr vielfältig sein, ihre konkrete Form hängt von der Art der weiteren Verarbeitung ab.

#### Beispiel 1: Durchsuchen von Log-Dateien
Eine Web-Anwendung soll es ermöglichen, auf dem Server abgelegte Log-Dateien zu durchsuchen und das Suchergebnis an den Browser zu senden. Für das Durchsuchen wird das UNIX-Kommando grep verwendet und in diesem Beispiel von Python aus mit dem Kommando popen ("pipe open") gestartet und ausgelesen:

```
with os.popen('grep "{}" log/access.log'.format(request.params['searchfor']) as f:
    matching_lins = f.readlines()
```

Hier soll der Request-Parameter _searchfor_ benutzt werden, um die Log-Datei _log/access.log_ zu durchsuchen. _os.popen_ liefert einen File-Descriptor, aus dem die aus einer normalen Datei gelesen werden kann.

Man erkennt schon ein ähnliches Muster wie bei der SQL-Injection: Wenn es nun gelingt, den Parameter searchfor so zu präparieren, dass sich ein weiterhin gültiger, aber nicht mehr der Intention entsprechender String ergibt, der dann ausgeführt wird, ist der Angriff gelungen. Ein solcher Angriff kann sich hier z.B. auf folgende Aktionen beziehen:

- Überwindung von Beschränkungen bei der Suche in der Datei (wenn z.B. weitere Paramater für grep erzeugt werden könnten oder weitere Dateien zum Durchsuchen angegeben werden können)
- Ausführen weiterer beliebiger Kommandos mit den Rechten des Webserver-Nutzers durch Verkettung mit ;

Mögliche Angriffsstrings sehen dann wie folgt aus:

1. grep "root" log/access.log etc/passwd # "/log access.log

2. grep "egal" log/access.log ; cat ../config_database_password.py # " log/access.log

3. grep "egal" log/access.log ; rm -rf data

Genau wie bei der SQL-Injection wird hier ein komplexer String an die Stelle des Kommandos gesetzt, die eigentlich nur für einen Suchbegriff vorgesehen war. Der Rest des Strings, den Python immer anhängt (hier: " log/access.log") kann man per Kommentar-Einleitung (hier: #) quasi "ausschalten".

#### Beispiel 2: Manipulation von in einer Datei gespeicherten Nutzerdaten

Eine Webanwendung verwaltet ihre Benutzer in einer Textdatei, die die Informationen: Benutzername, Passwort, E-Mail-Adresse, Rechtestufe enthält. Mit "Rechtestufe" ist eine Einstufung als normaler Nutzer (Wert "user") oder als Administrator mit erweiterten Rechten gemeint (Wert "admin"). Die Werte werden durch : getrennt.

##### Beispiel:

> tobias:a3ee7692bcf323a90b87:tobias.thelen@uni-osnabrueck.de:user

Die Anwendung enthält eine Funktion, mit der jeder Nutzer seine E-Mail-Adresse ändern kann. Ein Special-Character-Injection-Angriff könnte z.B. nun versuchen, die E-Mail-Adresse von Tobias auf folgenden Wert zu setzen:

> tobias.thelen@uni-osnabrueck.de:admin

Wenn der Angriff gelingt, steht nun folgender Eintrag in der Nutzerdatei:

> tobias:a3ee7692bcf323a90b87:tobias.thelen@uni-osnabrueck.de:admin:user

Ist es nun weiterhin so, dass die Anwendung beim Einlesen der Datei überflüssige Spalten ignoriert, ist es Tobias gelungen, sich selbst hochzustufen.

#### Angriffsziel
Der Angriff zielt auf den Server.

#### Angriffsvektor
Präparierte Eingaben (Request-Parameter, URL-Bestandteile)

#### Gegenmaßnahmen
* Eingabevalidierung: Bei der Entgegennahme der Eingabe per Request-Parameter oder URL-Bestandteil sollte so streng und genau wie möglich geprüft werden, ob die Eingabe der erwarteten Form entspricht. Im Fall des Suchbegriffes könnten z.B. nur alphanumerische Zeichen erlaubt werden, bei der E-Mail-Adresse nur alphanumerische Zeichen, @, - und . (bzw. ein regulärer Ausdruck, der gültige E-Mail-Adressen erkennt).
* Konstruktvalidierung: Nach oder bei der Konstruktion des auszuführenden Codes bzw. der zu schreibenden Zeile sollte geprüft werden, ob sie der erwarteten Form entspricht.


### Cross-Site-Scripting (XSS)

#### Angriffsziel
Per Cross-Site-Scripting wird der jeweils eine Seite betrachtende Benutzer angegriffen, da ihm eine manipulierte Ausgabe präsentiert wird.

#### Angriffsvektor
In die Datenbank des Servers müssen manipulierte Strings eingeschleust werden, die dann die schadhafte Ausgabe produzieren. Die Strings kommen typischerweise per Request-Parameter in die Anwendung, ihre Ausgabe muss dann mit unzureichendem Schutz erfolgen.
#### Gegenmaßnahmen
* Eingabevalidierung: Soweit möglich schon bei der Entgegennahme von Texten prüfen, ob sie unerwünschte Zeichen enthalten und ggf. zurückweisen oder entfernen.
* Ausgabevalidierung: Vor der Ausgabe werden Zeichen mit HTML-Sonderbedeutung escaped. Dazu sollte der Escaping-Mechanismus der Template-Engine verwendet werden.


### Session-Hijacking

#### Angriffsziel
Der Angreifer versucht, eine gültige Session-ID zu bekommen und diese zu verwenden, um die Session des Nutzers zu übernehmen ("Session riding").

#### Angriffsvektor
Ein typischer Angriffsvektor für einen clientseitigen Angriff nutzt eine bestehende XSS-Lücke aus, um aktives Javascript einzuschleusen. Der Javascript-Code liest dann die Session-ID aus und überträgt sie z.B. mit einem GET-Request an einen vom Angreifer kontrollierten Server, der die Session-ID dann an einen Client übergeben kann, der im Namen des angegriffenen Nutzers agiert.

Andere Angriffsvektoren können aber auch serverseitig aktiv werden und z.B. die Session-Dateien auslesen oder eine Session-Datenbank anzapfen.

#### Gegenmaßnahmen
* Session-Cookies sollten das httpOnly-Flag tragen, um nicht per Javascript ausgelesen werden zu können
* Sessions können zusätzlich gesichert werden (Bindung an eine IP-Adresse, Bindung an einen Referrer-String, ...)
* Besonders kritische Aktionen wie z.B. das Ändern eines Passwortes sollten zusätzlich abgesichert werden (durch erneutes Abfragen des Passwortes)


### Session-Fixation

#### Angriffsziel
Bei Session-Fixation-Angriffen versucht der Angreifer einem Nutzer eine gültige aber wertlose Session-ID unterzuschieben, die dieser dann durch Authentifizierung "aufwertet". Anschließend kann der Angreifer wie beim Session-Hijacking die Identität des angegriffenen Nutzers übernehmen.

#### Angriffsvektor
* Unterschieben einer gültigen Session-ID per Request-Parameter oder Cookie
* Den angegriffenen Nutzer dazu bewegen, sich auf der für den Angreifer interessanten Webseite einzuloggen

#### Gegenmaßnahmen
* Die wichtigste Gegenmaßnahme ist es, Sessions niemals aufzuwerten, sondern bei Kontextwechsel (Login, Rollenwechsel etc.) immer eine neue Session-ID zu vergeben.
* Session-IDs sollten nicht über Request-Parameter gesetzt werden können.


### Cross-Site-Request-Forgery (CSRF)

#### Angriffsziel
Der Browser des betrachtenden Nutzers soll in seinem Namen umbemerkt unerwünschte Aktionen ausführen.

#### Angriffsvektor
* Von beliebiger Quelle von GET-Requests zu beliebigen Zielen erzeugt werden (z.B. durch img-Tags), dabei sendet der Browser automatisch alle passenden Cookies (inkl. Session-Cookies) mit.
* POST-Requests können wegen der Same-Origin-Policy für HTTP-Requests von beliebiger Quelle nur unter aktiver Mitwirkung des Nutzers (Abschicken eines Formulars) erzeugt  werden. Aber wer prüft bei jedem Formular im HTML-Quellcode das Ziel und die Hidden-Parameter?
* Über eine XSS-Lücke können auch POST-Requests an den gleichen Server ohne Mitwirkung des Nutzers erzeugt werden.

#### Gegenmaßnahmen
Gegen die ersten beiden Angriffsvektoren (Angriffe von außen) hilft ein so genanntes CSRF-Token. Dieses für jede Session neu erzeugte zufällige Token muss bei jedem POST-Request mitgesandt werden, ansonsten wird der Request abgewiesen. GET-Requests sollten - wie für die Methode GET vorgesehen - keine unerwünschten Nebenwirkungen haben.

Gegen Angriffe von innen (XSS) helfen CSRF-Token nicht.

[Zurück nach oben](#kapitel-8-tests-und-sicherheit)
