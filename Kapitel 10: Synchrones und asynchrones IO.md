# Kapitel 10: Synchrones und asynchrones I/O

---

## Synchrones und asynchrones I/O und Eventloops

Asynchrone I/O-Operationen bedeuten, dass (potenziell langsame) I/O-Aufrufe wie z.B. das Einlesen einer Datei, das Abrufen eines Verzeichnisinhaltes nicht die Ausführung des Programms blockieren, sondern das Programm parallel zur Ausführung der Operation weiterläuft.

Ist die asynchrone I/O-Operation abgeschlossen, wird eine Calback-Funktion aufgerufen, die das Ergebnis der I/O-Operation als Parameter übergeben bekommt.

Diese Art des Programmierens ist eng mit eventbasierter Programmierung verknüpft. Hier wird der Hauptstrang der Programmausführung nicht vom Entwickler bestimmt (keine main-Methode), sondern besteht aus einer kontinuierlich laufenden Event-Loop. Hier werden auflaufende Events als Queue verwaltet und nacheinander abgearbeitet. Solche Events sind z.B. Nutzerinteraktionen (Mausklicks, Tastendrücke), vollendete asynchrone I/O-Operationen, Timer-Events usw.

Wichtig ist, dass ein einlaufendes Event niemals die Abarbeitung eines laufenden Eventhandlers unterbricht, sondern hinten an die Event-Queue angefügt wird. Deshalb müssen Anwendungen, um reaktiv zu bleiben, darauf achten nur kurze, schnell abzuarbeitende Event-Handler zu verwenden. Länger dauernde Berechnungen müssen ggf. aufgeteilt werden und per defer jeweils wieder hinten an die Queue angehängt werden.

---

## node.js und express.js

### node.js - Grundlagen

node.js ist eine Javascript-Implementation, die außerhalb einer Browser-Umgebung läuft. Damit eignet sich node.js für die Umsetzung beliebiger Programmieraufgaben, insbesondere auch Server-Aufgaben. Es lassen sich aber auch andere Anwendungen in node.js implementieren, so sind z.B. die Tools npm oder webpack selbst node.js-Anwendungen.

node.js setzt im Wesentlichen auf zwei grundlegende Bibliotheken:

* Die **Javascript-Engine v8**, die auch in Google Chrome verwendet wird. Damit entspricht der nutzbare Sprachumfang dem von Chrome.
* Die Bibliothek **libuv**, die eine Event-Loop bereitstellt und asynchrones I/O bietet.

Gegenüber einer Browserumgebung fehlen DOM-API und weitere Browser-APIs. Hinzu kommen aber eine Reihe von Standardbibliotheken, etwa für den **Dateisystemzugriff (fs)** oder grundlegende **HTTP-Server-Funktionen (http)**. Der Umfang dieser Bibliotheken erreicht aber nicht den von Python, so dass es bei node.js-Anwendungen üblich ist, viele (kleine) Pakete per npm zu installieren.

#### Einfaches node.js-Programm

Ein einfaches node.js-Programm (woche10/nodesjs/demo1.js) sieht so aus:

```javascript
console.log("Hello world!");
```

Ein node.js-Programm wird über die Kommandozeile gestartet:

```
> node demo1.js
Hello world!
>
```
#### Event-Loop
demo2.js gibt einmal pro Sekunde einen Text aus, bis das Programm abgebrochen wird:

```javascript
function schedule() {
    setTimeout(function () {
        console.log("Hello world again.");
        schedule();
    }, 1000);
};
schedule();
```

#### Asynchrone I/O-Funktionen
I/O-Funktionen sind asynchron, d.h. sie liefern kein Ergebnis per return zurück, sondern benötigen eine Callback-Funktion, die bei Erfolg (oder Fehler) aufgerufen wird.

```javascript
var fs = require('fs');

fs.readdir('.', function (err, files) {
    console.log(files);
});

console.log("Now I will read the file.");
```
Es gibt auch synchrone Varianten der I/O-Funktionen, die jeweils das Präfix Sync im Namen tragen.

#### http-Server

Die mitgeliefert http-Bibliothek stellt grundlegende http-Server-Funktionalität bereit. Bibliotheken werden per CommonJS-Syntax eingebunden.

```javascript
var http = require("http");

http.createServer(function (req, res) {
  res.writeHeader(200, {"Content-Type": "text/plain"});
  res.write("Hello World\n");
  res.end();
}).listen(8080);

console.log("Server running at http://127.0.0.1:8080/");
```

### Server-Framework: express.js

**express.js** ist das meistgenutzte Framework für die serverseitige Entwicklung von Web-Anwendungen mit node.js. Es ist in vielerlei Hinsicht mit dem bislang in der Vorlesung entwickelten Python-Framework vergleichbar.

express.js ist eine node.js-Bibliothek, die zunächst über den npm-Package-Manager installiert werden muss.

Der grundlegende Aufbau einer express.js-Anwendung lässt sich an einem Minimalbeispiel verdeutlichen:

```javascript
var express = require('express');  // Bibliothek einbinden
var app = express();  // Express-Objekt erzeugen

app.get('/', function (req, res) {  // Route und Request-Handler definieren
    res.send("Hello world!");
});

app.listen(3000, function () {  // Server starten
   console.log("Example app listening on http://localhost:3000 ")
});
```

Einige Begriffe unterscheiden sich etwas zwischen Python-Framework und express.js, die meisten Konzepte sind aber identisch, wie die folgende Vergleichstabelle zeigt:

|Python-Framework|express.js|Anmerkungen|
|----------------|----------|-----------|
|Webserver|App|Sehr vergleichbar: Instanz erzeugen, konfigurieren, serve()/listen() aufrufen|
|App|Router|Sehr vergleichbar: „Mini-App“, die mit einem konfigurierbaren URL-Präfix versehen wird.|
|Middleware|Middleware|Express-Middlewares kommen nur in der Request-Phase zum Einsatz, nicht nochmal nach der Response-Erzeugung. Express verwendet viele Funktionen, die im Python-Framework eingebaut sind als Zusatz-Middlewares (z.B. cookie-parser)|
|Request-Objekt|Request-Objekt|Methoden vergleichbar|
|Response-Objekt|Response-Objekt|Methoden vergleichbar|
|Template-Engine|Template-Enging|Sehr vergleichbar: Template-Engine zentral registrieren und konfigurieren|

### Beispiel: Wiki in express.js

Als komplexere Express-Anwendung **übertragen wir das Wiki (inkl. Mustache-Templates) auf node.js mit express**. Dazu werden die Templates nur minimal verändert, die Datenhaltung unverändert übernommen und aller Python-Code in node.js/express umgesetzt. Die Authentifizierung bleibt zunächst außen vor und wird erst im nächsten Schritt ergänzt.

Der verwendete Code (s. woche10/expresswiki/server1.js):

```javascript
// builtin libraries
const fs = require('fs');  // file system access (node.js builtin)

// http framework
const express = require('express');  // express js framework (installed via npm)

const cookieParser = require('cookie-parser'); // middleware for parsing cookies
const bodyParser = require('body-parser');  // middleware for parsing request bodys
const accesslog = require('access-log'); // logging middleware

// templating engine
const mustacheExpress = require('mustache-express');  // mustache as template engine for express (installed via npm)

function markup(text) {
    // substitute < by &lt;
    text = text.replace(/</g, "&lt;");
    // substitute links: [[pagename]] -> <a href="/show/pagename">pagename</a>
    text = text.replace(/\[\[([a-zA-Z0-9]+)\]\]/g, "<a href='/show/$1'>$1</a>");
    // substitute headlines: !bang -> <h1>bang</h1>
    text = text.replace(/^!(.*)$/gm, "<h1>$1</h1>");
    // replace two ends of line with <p>
    text = text.replace(/\r?\n\r?\n/gm, "<p>");
    // replace one end of line with <br>
    text = text.replace(/\r?\n\r?\n/gm, "<br>");
    return text
}

function pagelist() {
    // use synchronous I/O to get page list - return list of strings
    return fs.readdirSync('./data');
}

// show wiki page
function show(req, res, next) {
    var page = req.params['page'] || 'main';
    var pages = pagelist();
    fs.readFile('data/'+page, 'utf8', function (err, data) {
        if (err) return next(err);  // pass to next handler which is the error handler
        res.render('show.mustache', { 'text': markup(data), 'pagename': page, 'pages': pages});
    });
}

// show wiki page for editing
function edit(req, res, next) {
    var page = req.params['page'] || 'main';
    var pages = pagelist();
    fs.readFile('data/' + page, 'utf8', function (err, data) {
        if (err) return next(err);
        res.render('edit.mustache', {'text': data, 'pagename': page, 'pages': pages});
    });
}

// receive post request, save data and redirect to show
function save(req, res, next) {
    var page = req.params['page'] || 'main';
    var data = req.body.wikitext;
    fs.writeFile('data/'+page, data, function (err, data) {
        if (err) return next(err);
        res.redirect('/show/'+page);
    });
}

// setup und start express engine
var app = express();

app.engine('mustache', mustacheExpress());
app.set('views', './templates');
app.set('view engine', 'mustache');

// bodyParser middleware parses request bodies
app.use(bodyParser.urlencoded({ extended: true })); // enables parsing application/x-www-form-urlencoded

// logging middleware
app.use(function (req,res, next) {
    accesslog(req,res);
    next();
});

// error handling
app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});

// serve static files from ./static as /static
app.use('/static', express.static('static'));

// configure routes
app.get('/', show);
app.get('/show', show);
app.get('/show/:page(\\w+)', show);
app.get('/edit', edit);
app.get('/edit/:page(\\w+)', edit);
app.post('/save/:page(\\w+)', save);

// run the wiki app
app.listen(8080, function () { console.log('Wiki app listening on http://localhost:8080/'); });
```

### Authentifizierung mit Passport

Für die Authentifizierung im Wiki wird die sehr verbreitete Authentifizierungs-Middleware passport.js verwendet. Die Arbeitsweise ist grundsätzlich mit der Authentifizierungsmiddleware in Python vergleichbar: Es werden serverseitig gespeicherte Sessions verwendet, die über einen Session-ID-Cookie identifiziert werden. In der Session wird nach erfolgreicher Formular-Authentifizierung ein User-Objekt (bzw. eine ID zur Identifikation eines Nutzers in einer Datenbank) abgelegt. Eine Besonderheit von passport ist die Möglichkeit, sehr viele verschiedene Authentifizierungsmethoden (z.B. per github, Facebook etc.) über ein einheitliches Interface ansprechen zu können.

#### Weitere Bibliotheken

Insgesamt werden vier weitere node.js-Bibliotheken benötigt:

```javascript
// authentication middleware (passport)
const expressSession = require('express-session');  // express session support
const passport = require('passport'); // authentication middleware
const Strategy = require('passport-local').Strategy;  // local authentication strategy for passport
const ensureLoggedIn = require('connect-ensure-login').ensureLoggedIn;  // redirect to login for actions that need auth
```
Das letzte Plugin wird für das "Lazy Login" benötigt, d.h. die Umleitung auf die Login-Seite, wenn eine authentifizierungspflichtige Aktion ausgeführt werden soll.

#### Konfiguration

Die Session-Middleware und die Passport-Middleware müssen konfiguriert werden. Anstatt eine Datenbank oder zumindest eine Datei mit Nutzerdaten anzubinden, wird der Benutzer 'admin' mit Passwort 'admin' hart codiert.

```javascript
app.use(require('express-session')({ secret: '0815secret',
        resave: false,
        saveUninitialized: false }));

passport.use(new Strategy(
  // quick hack: we only have admin/admin. Connect to database here!
  function(username, password, cb) {
    if (username=='admin' && password=='admin') {
        return cb(null, 'admin');
    } else {
        return cb(null, false);
    }
  }));

// map session representation to user object (we use the same string in both cases)
passport.serializeUser(function(user, cb) { cb(null, user); });
passport.deserializeUser(function(id, cb) { cb(null, id); });

// Initialize Passport and restore authentication state, if any, from the session.
app.use(passport.initialize());
app.use(passport.session());
```

#### Zusätzliche Routen

Das explizite Login und Logout benötigen neue Routen. Die /login-Route wird sowohl per GET als auch per POST angesprochen, im ersten Fall für die Anzeige des Login-Fomulars (dafür wird ein Request-Handler loginview angelegt) und im zweiten für das Überprüfen der Authentifizierungsdaten inkl. Einrichten der Session. Dieser Schritt wird von einer Funktion des Passport-Frameworks übernommen. Diese Funkion erwartet Nutzername und Passwort im Request-Body unter den Schlüsseln username und password. (Abweichungen können konfiguriert werden)

```javascript
// show login page
function loginview(req, res, next) {
    var page = req.params['page'] || 'main';
    var pages = pagelist();
    res.render('login.mustache', { 'pagename': page, 'pages': pages });
}

// logging in an out
app.get('/login', loginview);
app.post('/login', passport.authenticate('local', { successReturnToOrRedirect: '/',
                                                    failureRedirect: '/login' }));
app.get('/logout',
  function(req, res){
    req.logout();
    res.redirect('/');
});
```

#### Absicherung der zu schützenden Routen
Die Edit- und Save-Route müssen nun abgesichert werden. Das ensure-login-Plugin bietet dafür eine Funktion ensureLoggedIn(redirectTarget), die als Request-Handler genutzt werden kann. In der Konfiguration einer Route können mehrere Request-Handler angegeben werden, die die Kontrolle per Aufruf von next() jeweils an den nächsten Handler weiterreichen können.

```javascript
function savewikitext(req, res, next) {
    req.session.wikitext = req.body.wikitext;
    next();
}

app.get('/edit', ensureLoggedIn('/login'), edit);
app.get('/edit/:page(\\w+)', ensureLoggedIn('/login'), edit);
app.post('/save/:page(\\w+)', savewikitext, ensureLoggedIn('/login'), save);
app.get('/save/:page(\\w+)', function (req, res) { res.redirect('/edit/'+req.params['page']); });
```

Leider gibt es ein Problem mit der save-Route: Nach erfolgreichem Login sendet Passport eine Redirect-Response, die per GET ausgeführt wird. Das Ziel des Redirects wird in der Session abgelegt und von dort abgerufen. POST-Parameter werden damit nicht mitgesichert.

Hier wird nun folgende Lösung verfolgt:
1. Bevor eine POST-Aktion auf /save geprüft wird, wird der POST-Parameter wikitext in die Session geschrieben (savewikitext)
2. Ist der Nutzer bereits authentifiziert, wird save ausgeführt und der Session-Eintrag wieder gelöscht.
3. Ist er noch nicht authentifiziert, bekommt er ein Login-Formular präsentiert und wird anschließend auf GET /edit weitergeleitet, damit der Text nochmal zum Editieren/Absenden angezeigt.
4. Dazu muss der Edit-Handler so modifiziert werden, dass der anzuzeigende Text - falls vorhanden - aus der Session rekonstruiert wird, ansonsten aus der Datei gelesen wird.

Damit sehen edit und save wie folgt aus:

```javascript
// show wiki page for editing
function edit(req, res, next) {
    var page = req.params['page'] || 'main';
    var pages = pagelist();
    if (req.session.wikitext) {  // perhaps we have a saved text in the session to survive login
        res.render('edit.mustache', { 'text': req.session.wikitext, 'pagename': page, 'pages': pages });
        req.session.wikitext = undefined;
    }
    fs.readFile('data/'+page, 'utf8', function (err, data) {
        if (err) return next(err);
        res.render('edit.mustache', { 'text': data, 'pagename': page, 'pages': pages });
    });
}

// receive post request, save data and redirect to show
function save(req, res, next) {
    var page = req.params['page'] || 'main';
    var data = req.body.wikitext;
    req.session.wikitext = undefined;
    fs.writeFile('data/'+page, data, function (err, data) {
        if (err) return next(err);
        res.redirect('/show/'+page);
    });
}
```

---

## Web-Sockets

### Web-Sockets

#### In welchen Situationen ist asynchrone Server-Programmierung besonders hilfreich?

Die bisher betrachteten node.js-Beispiele haben im Wesentlichen die gleichen Funktionalitäten wie die Python-Lösung umgesetzt. Es gibt aber Situationen, in denen asynchrone Server-Programmierung einen erheblichen qualitativen Mehrwert gegenüber synchronen liefert.

Dies sind vor allem Situationen, in denen es viele, lang laufende aber geringe Last erzeugende Requests gibt, d.h. der Server viel Zeit mit Warten bei offenen Verbindungen verbraucht. Bei synchronem, blockierenden I/O blockiert jeder wartende Request einen Thread. Ein typischer Webserver kann mehrere hundert solcher Threads parallel verwalten, aber nicht deutlich mehr. Ein node.js-Server kommt aber problemlos mit mehr als 10.000 offenen Verbindungen zurecht, wenn diese keine nennenswerte Last erzeugen.

Typische Anwendungen, die von dieser Möglichkeit profitieren, sind:

* Chat-Systeme
* Benachrichtigungen (z.B. in Facebook) über neue Nachrichten
* in Echtzeit zu aktualisierende Informationen (Börsenkurse, eBay-Preise)

#### AJAX-Long-Polling
Die http-Standard-Technologie, die für Echtzeitbenachrichtigungen eingesetzt wird, ist AJAX Long Polling. Dabei wird ein Request an den Server geschickt, aber erst dann beantwortet, wenn neue Informationen vorliegen, z.B. ein Preis sich ändern, eine Nachricht eingeht oder jemand Neues den Chat-Raum betritt. In diesem Fall nimmt der Client die Antwort entgegen und stellt sofort einen neuen Request. Auf diese Weise kann der Server bestimmen, wann Informationen übermittelt werden, auch wenn er die Kommunikation nicht selbst etablieren kann. Diese Technologie verbraucht aber pro Nutzer dauerhaft eine Verbindung und ist für synchrones I/O in der Regel schlecht geeignet. Aber auch mit asnychronem Server-I/O bleibt sie eine Notlösung.

### Bidirektional nutzbare stehende TCP-Verbindung
Ideal für die oben skizzierten Zwecke wäre eine stehende, beliebig nutzbare bidirektionale TCP-Verbindung. Grundsätzlich gibt es eine solche Verbindung zwischen Client und Server, nämlich die für den Austausch von http-Messages genutzte Verbindung. Allerdings erwarten beide Seiten wohlgeformte HTTP-Nachrichten in der richtigen Reihenfolge.

Das Websocket-Protokoll ist ein Protokoll, das wie http eine Anwendungsschicht auf TCP-Basis definiert. Websocket-Nachrichten haben aber nur einen sehr kleinen Protokoll-Teil und sind in beide Richtungen identisch. Mit ihnen lassen sich von beiden Seiten aus jederzeit beliebige Daten an den anderen Kommunikationspartner übertragen.

Es gibt eine Möglichkeit, eine bereits etablierte http-Verbindung "umzuwidmen" und als Websocket-Verbindung weiterzunutzen. Dazu sendet der Client einen Request mit dem Header-Feldern "Connection: Upgrade" und "Upgrade: Websocket" sowie einigen Websocket-Parametern. Der Server antwortet mit dem Status-Code "101 Switchin Protocols" und einer Wiederholung der beiden Headerfelder. Nach dieser http-Nachricht wird die bestehende Verbindung für Websocket-Kommunikation genutzt.

#### Socket.IO
Wir verwenden im Folgenden eine Bibliothek, die beidseitig initiierbare Real-Time-Kommunikation zwischen Client und Server ermöglicht. Dazu definiert Socket.IO ein eigenes Protokoll, dass die jeweils bestverfügbare Technologie (Web-Sockets oder AJAX Long Polling) einsetzt.

Socket.IO-Nachrichten (oder -Events) bestehen aus zwei Teilen:
1. Event-Typ (string)
2. Dateninhalt (Javascript-Datenstruktur)

Auf diese Weise ist es möglich, eine eventbasierte bidirektionale Client-Server-Kommunikation umzusetzen, die auch mit älteren Browsern und eingeschränkten Umgebungen zurechtkommt.

### Chat-Server mit Socket.IO

In diesem Beispiel wird ein einfacher Chat-Server in node.js und clientseitigem Javascript implementiert, der Chat-Nachrichten sehr schnell an alle Teilnehmer verteilt und auch in genau dieser Form für tausende von Teilnehmern nutzbar ist. Dazu wird die Bibliothek socket.io verwendet, die ein kleines eventbasiertes Kommunikationsprotokoll implementiert und dazu Websockets (oder, falls nicht verfügbar, Ajax Long Polling) verwendet.

#### Deployment

Die Anwendung nutzt node.js für den Server und webpack für die Zusammenführung des Client-Codes. npm wird zum Installieren von Paketen und zum Starten des Build-Prozesses und der Anwendung genutzt.

```
npm install (einmalig)
npm run build (nach Änderungen am Client-Code)
npm run (Neustarten nach Änderungen am Server-Code)
```

#### Client-Code (clientsrc/client.js)

```javascript
import $ from 'jquery';
import io from 'socket.io-client';  // the socket.io client
import {sprintf} from 'sprintf-js';  // library with sprintf-string formatting function
import './style.css';

$(document).ready(function(){
  // connect to webSocket
  var socket = io.connect();

  // incoming message
  // register event handler for 'chat' events
  socket.on('chat', function (data) {
    var now = new Date(data.time);
    // construct html string for chat message and add to #content list
    $('#content').append(
          sprintf("<li>[%02d:%02d:%02d] <b>%s: </b> <span>%s</span></li>",
              now.getHours(), now.getMinutes(), now.getSeconds(),
              data.name  || '', data.text));
    // scroll down (broken)
    $('body').scrollTop($('body')[0].scrollHeight);
  });

  // send a message (submit handler for html form)
  $('#sendform').submit(function (event) {
     // send (emit) a 'chat' event to server
    socket.emit('chat', { name: $('#name').val(), text: $('#text').val() });
    $('#text').val('');
     event.preventDefault();
  });

  // refresh number of chatters every second via ajax
  // this could of course be done via websockets / counting connects and disconnects as well,
  // but this is to demonstrate how to use websockets and ajax together
  window.setInterval(function () { $('#numclients').load('/numclients'); }, 5000);
});
```
#### Server-Code (server.js)
```javascript
// express.js uses the builtin http library
// so a http.Server object handles all request at first
// we need to register socket.io with that server
// because it sits on top of http, not on top of express
var express = require('express');
var app = express();
var server = require('http').createServer(app)
var io = require('socket.io').listen(server)
var conf = require('./config.json');

server.listen(conf.port);

// serve files from /public (output dir for webpack!)
app.use(express.static(__dirname + '/public'));

// send /public/index.html for / request
app.get('/', function (req, res) {
   res.sendfile(__dirname + '/public/index.html');
});

// Ajax route: Send number of currently active clients (just the number)
app.get('/numclients', function (req, res) {
   res.send(200, io.engine.clientsCount);
});

// Websocket handling

// someone connects
io.sockets.on('connection', function (socket) {
  socket.emit('chat', { time: new Date(), text: 'Connected to server!' });  // send him a welcome message
  socket.on('chat', function (data) {  // react to future chat messages from that client
    // send the text to all clients
    io.sockets.emit('chat', { time: new Date(), name: data.name || 'anonymous', text: data.text });
  });
  socket.on('disconnect', function () {
    io.sockets.emit('chat', { time: new Date(), name: 'someone', text: 'left the chat'});
  });
});

// tell console that server is up and running
console.log('Server running on http://127.0.0.1:' + conf.port + '/');
```
