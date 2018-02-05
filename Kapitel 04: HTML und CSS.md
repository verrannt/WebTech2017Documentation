# Kapitel 04: HTML und CSS

* [Hypertext](#hypertext)
  * [historisch](#historisch)
* [HTML](#html)
  * [HTML-Tags](#html-tags)
  * [HTML-Attribute](#html-attribute)
  * [Ersatzdarstellungen: HTML-Entities](#ersatzdarstellung-html-entities)
  * [HTML5 Grundgerüst](#html5-grundgerüst)
---

## Hxpertext

### historisch

"Tim Berners-Lee , R. Cailliau. 12 November 1990, CERN" hat geschrieben:
> HyperText is a way to link and access information of various kinds as a web of nodes in which the user can browse at will. 
> It provides a single user-interface to large classes of information (reports, notes, data-bases, computer documentation and on-line help). 
> We propose a simple scheme incorporating servers already available at CERN... A program which provides access to the hypertext world we call a browser...

HTML ist neben http und URIs der dritte Grundpfeiler der "Erfindung WWW". 
Der wesentliche Aspekt des WWW, der durch die _Hypertext Markup Language_ (HTML) ermöglicht wird, ist die Vernetzung von Dokumenten untereinander, ohne dass diese Vernetzung hierarchisch vorgegeben und zentral gesteuert würde. 
Auf diese Weise entsteht ein _Hypertext_, d.h. ein über einen Einzeltext hinauswachsender verwebter Text.

Die Idee des Hypertextes existierte schon vor der Erfindung von HTML. Wichtige Stationen bzw. Vorläufer waren:

* 1941: Jorge Luis Borges: El jardín de senderos que se bifurcan (Erzählung), englisch: The Garden of Forking Paths
* 1945: Die Vision einer Text-Vernetzungs-Maschine "Memex" (Memory Index) von Vanemar Bush
* 1965: Hypertext Editing System von Ted Nelson (Begriffe Hypertext und Hypermedia erstmal verwendt)
* 1980: ENQUIRE: Wiki-artiges Hypertext-System von Tim Berners-Lee
* 1987: HyperCard von Apple - Autoren- und Präsentationssystem für Hypermedia-Anwendungen (Demo-Video)
* 1991: Gopher - Menü-/Hierarchie-orientiertes Hypertext-Netz (TCP/IP), etwas eher als das WWW verbreitet, inkl. Suchmaschinen
* 1989: WWW-Proposal, Tim Berners-Lee


[Zurück nach oben](#kapitel-04-html-und-css)

---
### Markup

Texte sind halbstrukturierte Daten, d.h. sie können nicht ohne weiteres in Tabellen (wie in relationalen Datenbanksystemen) gespeichert werden und trotzdem in ihrer Struktur zugänglich bleiben. Textstrukturen sind hiearchisch, z.B.: Buch -
Kapitel - Text.

Für die Repräsentation halbstrukturierter Daten, vor allem Textdaten für die geisteswissenschaftliche Forschung, wurde SGML (Standard Generalized Markup Language) erfunden und 1986 standardisiert.

SGML ist streng hierarchisch aufgebaut, d.h. Textteile können durch im Text enthaltene Auszeichnungen (Markup) gruppiert und miteinander in Beziehung gesetzt werden. Auf diese Weise ausgezeichnete Textteile können sich nicht überlappen, d.h.
z.B. dass zwei Kapitel keinen gemeinsamen Text enthalten können. Die Auszeichnungen werden mithilfe von Tags vorgenommen, die die gleiche Form wie HTML-Tags haben.

SGML ist selbst keine Auszeichnungssprache, sondern Grundlage für konkrete Auszeichnungssprachen. Sein Nachfolger ist in vielerlei Hinsicht XML. Bekannte SGML-Anwendungen, d.h. Markup-Sprachen nach SGML-Regeln, sind:

* TEI (Text Encoding Initiative - Codierung und Annotation von Texten für die geisteswissenschaftliche Forschung)
* DocBook (für technische Dokumentation)
* HTML (bis HTML 4)

[Zurück nach oben](#kapitel-04-html-und-css)

---

## HTML

### HTML 1

HTML 1 (1990)  funktioniert auch heute noch weitestgehend und definierte viele noch heute gebräuchlich Tags:
 
* `<TITLE>` - Dokumenttitel
* `<H1> ... <H6>` - Überschriften
* `<P>` - Absatz
* `<OL>` - Ordered List
* `<UL>` - Unordered List
* `<DL>` - Definition-List
* `<XMP>` - Vorformatiertes Beispiel
* `<A HREF="">` - Ausgehender Link
* `<A NAME="">` - Linkziel (über #-Fragment angesprochen)

[Zurück nach oben](#kapitel-04-html-und-css)

---

### HTML-Tags

#### HTML-Elementtypen


|Typ|Kennzeichen|Tags|
|---|---|---|
| Void | Kein Endtag, kein Content | `area`, `base`, `br`, `col`, `embed`, `hr`, `img`, `input`, `keygen`, `link`, `meta`, `param`, `source`, `track`, `wbr` |
| Raw Text Elements | Kann keine anderen Elemente enthalten, Darf kein `</element>` enthalten, Darf keine & enthalten | `script`, `style` |
| Escapable Raw Text Elements | Kann keine anderen Elemente enthalten, Darf kein `</element>` enthalten, Darf & enthalten | `textarea`, `title` |
| Foreign Elements | Kann andere Elemente aus dem Namespace enthalten, Darf `<tag/>` enthalten | (MathML- und SVG-Namespaces) |
| Normale Elemente | Keine Beschränkungen, kann andere Elemente enthalten | Alle übrigen |

[Zurück nach oben](#kapitel-04-html-und-css)

---

#### HTML-Attribute

Jedes Tag kann folgende Globalattribute haben:
 

Attribute|Description ([Quelle](http://www.w3schools.com/tags/ref_standardattributes.asp))
---|---
[`accesskey`](https://www.w3schools.com/tags/att_global_accesskey.asp)|Specifies a shortcut key to activate/focus an element
[`class`](https://www.w3schools.com/tags/att_global_class.asp)|Specifies one or more classnames for an element (refers to a class in a style sheet)
[`contenteditable`](https://www.w3schools.com/tags/att_global_contenteditable.asp)|Specifies whether the content of an element is editable or not`
[`data-*`](https://www.w3schools.com/tags/att_global_data.asp)|Used to store custom data private to the pageA or application
[`dir`](https://www.w3schools.com/tags/att_global_dir.asp)|Specifies the text direction for the content in an element
[`draggable`](https://www.w3schools.com/tags/att_global_draggable.asp)|Specifies whether an element is draggable or not
[`hidden`](https://www.w3schools.com/tags/att_global_hidden.asp)|Specifies that an element is not yet, or is no longer, relevant
[`id`](https://www.w3schools.com/tags/att_global_id.asp)|Specifies a unique id for an element
[`lang`](https://www.w3schools.com/tags/att_global_lang.asp)|Specifies the language of the element's content
[`spellcheck`](https://www.w3schools.com/tags/att_global_spellcheck.asp)|Specifies whether the element is to have its spelling and grammar checked or not (textarea und contenteditable)
[`style`](https://www.w3schools.com/tags/att_global_style.asp)|Specifies an inline CSS style for an element
[`tabindex`](https://www.w3schools.com/tags/att_global_tabindex.asp)|Specifies the tabbing order of an element
[`title`](https://www.w3schools.com/tags/att_global_title.asp)|Specifies extra information about an element
[`translate`](https://www.w3schools.com/tags/att_global_translate.asp)|Specifies whether the content of an element should be translated or not

Zusätzlich gibt es noch Data-Attribute der Form `data-*`, die verwendet werden, um Informationen im HTML-Dokument abzulegen, auf die Javascript-Programme zugreifen sollen.

[Zurück nach oben](#kapitel-04-html-und-css)

---

### Ersatzdarstellungen: HTML-Entities

Es gibt eine Reihe von Zeichen, die in HTML eine Sonderbedeutung haben, und deshalb mit einer Ersatzdarstellung ausgedrückt werden müssen, wenn sie im Text verwendet werden sollen.

Für Ersatzdarstellungen in HTML werden so genannte "HTML Entities" benutzt, die die Form `&name;` haben. 
`name` steht dabei für einen definierten Namen des betreffenden Zeichens.

Zeichen, die eine Ersatzdarstellung benötigen, sind:

* `<` `&lt;` (Tag-öffnendes Zeichen)
* `>` `&gt;` (Tag-schließendes Zeichen, muss nicht unbedingt codiert werden)
* `&` `&amp;` (als Teil von Entities)
* `"` `&quot;` (in Attributen)

HTML 1-4 verwendete als Default-Codierung _CP1252_ (eine Latin-1-Variante), sicher waren in der Praxis aber nur ASCII-Zeichen, andere Zeichen haben ebenfalls Entities:
 
* `ä` `&auml` ("a Umlaut")
* `Ä` `&Auml;` ("A Umlaut")
* `ö` `&ouml;` ....
* `ß` `&szlig;` (SZ-Ligatur)
* &phone; `&phone;` (Telefonsymbol)

Unicode-Zeichen können auch direkt über ihren Unicode-Codepoint adressiert werden (nicht UTF-8-Code!)
 
* `&#x40;` &#x40; (Hexadezimal 40 = @)
* `&#2620;` &#x2620; (Schneemann)
* `&#9822;` &#9822; (schwarzer Springer)

[Zurück nach oben](#kapitel-04-html-und-css)

---

### HTML5 Grundgerüst

```html
<!DOCTYPE html>
<html lang="de">
    <head>
        <title>Beispieldokument</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>

    </body>
</html>
```

[Zurück nach oben](#kapitel-04-html-und-css)

---


