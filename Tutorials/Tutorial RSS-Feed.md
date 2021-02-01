# Wie du mithilfe von FHEM immer weißt, was auf der Welt los ist

## Einführung

In diesem Tutorial erfährst du, wie du mithilfe von Google Maps RSS-Feeds deiner Wahl einliest und die Überschriften der Beiträge/Artikel in FTUI darstellst.

## Voraussetzungen
- Funktionierende FHEM-Installation

### Schritt 1 - Anlegen des rss-Feeds

Das Modul rssFeed bringt von Haus aus bereits alles mit, was du benötigst, um einen RSS-Feed in FHEM zu integrieren.
```
define NACHRICHTEN rssFeed <URL> 3600
```
Dabei sorgen wir mit dem Wert von 3600 Sekunden dafür, dass eine stündliche Aktualisierung deines Nachrichtenfeeds erfolgt. Als URL musst du den kompletten Link zum RSS-Feed
deines gewünschten Anbieters kopieren. Ein paar Beispiele, die interessant sein könnten:
www.heise.de/rss/heise.rdf
https://www.tagesschau.de/xml/rss2_https/

### Schritt 2 - Integration in FTUI

Für die Abgabe haben wir uns entschlossen, die Überschriften der Artikel für sich stehend und durch Trennlinien getrennt aufzulisten.
Sicher fallen dir hier kreativere Lösungen ein: Für einen ersten Nachrichten-Überblick reicht unsere Lösung jedoch bereits aus,
die du einfach für bspw. deine Startseite übernehmen kannst:

```html
		<li data-row="3" data-col="2" data-sizex="3" data-sizey="2" class="left-align">                <header><div data-type="label" data-device="nachrichten1" data-get="f_description"></div></header>
                <div class="cell left-align">
					<div data-type="label" data-device="nachrichten1" data-get="n00_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n01_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n02_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n03_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n04_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n05_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n06_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n07_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n08_description" class="left-align"></div><hr color="#6699FF">
					<div data-type="label" data-device="nachrichten1" data-get="n09_description" class="left-align"></div>
                </div> 
				</li>
```

### Tipp: RSS-Feed über FTUI konfigurierbar machen
Mit der nachfolgenden DOIF-Anweisung kannst du via defmod (Eine Funktion zum Ändern von define-Werten, die sich mit anderen Boardmitteln nicht einfach ändern lassen) eine flexiblere 
Variante implementieren, bei der du den gewünschten RSS-Feed nachträglich  in deiner FTUI ändern kannst:

```
define doif_rss1 DOIF ([$SELF:P_RSS])(defmod nachrichten1 rssFeed [$SELF:P_RSS] 3600)
attr doif_rss1 do always
attr doif_rss1 readingList P_RSS
attr doif_rss1 selftrigger all
attr doif_rss1 setList P_RSS
```

Anschließend musst du das Reading P_RSS nur noch via Input-Widget in deine FTUI integrieren. Das kriegst du hin ;-)

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://fhem.de/commandref.html#rssFeed (Letzter Zugriff: 01.02.21)
