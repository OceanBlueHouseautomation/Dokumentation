# Styleguide-Dokumentation

# Stil
Der Stil gibt einen Überblick, wie die Tutorials geschrieben werden soll.
### Umfassend
* Jedes Command
* Erklärungen und Hintergründe
* Konzepte sollen vermittelt werden
* Keine Annahmen über Leser*innen
* Keine Worte wie „einfach“, „nur“, „offensichtlich“


### Technisch detailliert und korrekt
* Nicht nur copy&paste Code/Einstellungen
* Erst erklären was und wieso
* Optionen aufzeigen
* Zum Testen exakt nach Tutorial durcharbeiten


### Abgeschlossen
* Jedes Tutorial erklärt einen Abschnitt von Anfang bis Ende
* Links zu vorangehenden und (möglicherweise) nachfolgenden Tutorials
* Links zu externen Quellen wo nötig
* Links zu externen Quellen, um weiterführende Informationen bereitzustellen


### Freundlich, aber formal
* Keine Witze, Emojis, unnötiger Fachjargon
* In der zweiten Person Singular geschrieben („Du wirst…“)
* Motivierende und auf Ziele ausgerichtete Sprache („In diesem Tutorial wirst du FHEM auf einem Raspberry Pi aufsetzen“)
* Inklusive Sprache, * zur Bildung geschlechtsneutraler Begriffe bzw. User*innen

## Struktur
Die Tutorials werden hier im Wiki geschrieben und veröffentlicht. Sie sollen alle eine einheitliche Struktur besitzen, die Leser*innen einen schnellen Überblick gibt.

* Titel (eigene Seite im Wiki)
* Einführung (Überschrift 2, Schriftgrad mittel)
* Voraussetzungen (Überschrift 2, Schriftgrad mittel)
* Schritt 1 — Die erste Sache machen (neuer Abschnitt)
* Schritt 2 — Die zweite Sache machen (neuer Abschnitt)
* ...
* Schritt n — Die letzte Sache machen (neuer Abschnitt)
* Abschluss (neuer Abschnitt)
 
### Titel
Der Titel soll auf die Ziele ausgerichtet sein, welche Ziel Leser*innen mit Hilfe des Tutorials erreichen werden.

* „Wie du Rollläden mittels Temperatursensoren in FHEM steuerst“

 

### Einführung
1-3 Absätze

Leser*innen motivieren, Erwartungen festlegen und Zusammenfassung

Diese Fragen sollten in der Einführung beantwortet werden:

* Worum geht es in diesem Tutorial? Welche Hard- und Software wird verwendet? Was machen die einzelnen Komponenten (kurz)?
* Was machen Leser*innen in diesem Tutorial? Setzen sie einen Server auf? Richten sie eine Anwendung ein? Spezifische Angaben motivieren!
* Was haben Leser*innen nach dem Abschluss erreicht? Was können sie jetzt, was sie davor nicht konnten?

Das Beantworten dieser Fragen hilft außerdem das Tutorial klar und auf die Leser*innen ausgerichtet zu schreiben.

Die Leser*innen und was sie erreichen werden sollte im Mittelpunkt stehen. Statt „Wir werden lernen, wie man XYZ konfiguriert…“, „Du wirst diese und jene Konfigurationen vornehmen…“ Durch die direkte Ansprache werden Leser*innen motiviert und interessieren sich mehr für das Thema. Außerdem eher auf das Projekt beziehen, als auf die eingesetzte Technologie.

### Voraussetzungen
Die Voraussetzungen haben den Sinn, Leser*innen exakt zu erklären, was erledigt werden muss, bevor mit dem Tutorial begonnen wird. Sie werden in Form einer Liste von Stichpunkten angegeben. Jeder Stichpunkt sollte auf ein vorausgehendes Tutorial verweisen.

Zu den Voraussetzungen zählen u.a.:

* Welche Hardware benötigt wird
* Welches Betriebssystem vorhanden sein muss
* Welche Software vorhanden sein muss
* Ob ein Account vorhanden sein muss (zB bei GitHub)

Jeweils mit Links zu Tutorial soweit vorhanden. Ansonsten zu externen Quellen, die den nötigen Kontext vermitteln. Leser*innen sollen nicht mitten im Tutorial mit neuer Vorarbeit überrascht werden.

 
### Schritte
In den Schritten steht die Schritt-für-Schritt-Anleitung, was Leser*innen in diesem Tutorial tun werden. Ein Schritt enthält Commands, Code, Einstellungen und Dateien und eine Erklärung was zu tun ist und auch warum es so gemacht wird.

Jeder Schritt beginnt mit einem neuen Absatz und wird im Präsenz geschrieben.

Am Anfang des Absatzes steht ein Satz, der erklärt was Leser*innen in diesem Schritt machen und welche Rolle dies für das Ziel des Tutorials spielt.

Jeder Schritt endet mit einem Überleitungssatz, in dem beschrieben wird, was Leser*innen erreicht haben und was sie als nächstes tun werden.

 
### Commands in den Schritten
Alle Commands die durch Leser*innen ausgeführt werden sollen, stehen in eigenen Codeblöcken. Vor jedem Command steht eine Erklärung, was das Kommando macht. Nach dem Command stehen zusätzliche Details, wie Argumente und warum diese benutzt werden. 

Als erstes legst du einen Schalter an. Dieser soll einen Lichtschalter simulieren. Gib dazu folgenden Befehl in das Kommandofeld ein:

```bash
define mySwitch1 dummy
```

"define" ist der FHEM-Befehl, "mySwitch1" der zukünftige Name des devices und "dummy" bezeichnet den Typ. Die Worte define und dummy gehören zur Befehlssyntax von FHEM und können nicht verändert werden, mySwitch1 ist (mehr oder weniger) frei wählbar. Nach drücken der Enter-Taste wird die Detail-Ansicht des neuen FHEM-Device mySwitch1 angezeigt.

 
### Abschluss
Der Abschluss fasst zusammen, was Leser*innen mit dem Tutorial erreicht haben. zB "Du hast FHEM auf einem Raspberry Pi eingerichtet und...".
Außerdem sollten Leser*innen erfahren, wie sie weitermachen können. Dabei können weitere Anwendungen aufgezeigt werden und weitere Tutorials verlinkt werden.


## Formatierung
Die Formatierung ist an die Möglichkeiten des Teams Wiki angepasst.
 
### Überschriften
* Jedes Tutorial steht in einer eigenen Wiki-Seite. 
* Die Einführung steht am Anfang der Seite ohne eigenen Abschnitt. Sie wird mit "Einführung" betitelt und in Schriftgrad mittel und Überschrift 1 geschrieben.
* Die Voraussetzungen kommen nach der Einführung. Sie haben ebenfalls keinen eigenen Abschnitt. Sie werden mit "Voraussetzungen" betitelt und in Schriftgrad mittel und Überschrift 1 geschrieben.
* Jeder Schritt des Tutorials ist eine eigener Absatz.
* Der Abschluss des Tutorials ist eine eigener Absatz.
 
### Formatierung innerhalb von Fließtext
 
**Fett** sollte verwendet werden für:
* Angezeigter Text in der GUI, zB Auf **Start Local Terminal klicken**
* Host- und Usernamen, zB **root** oder **pi**
 
Texthervorhebung rosa (in MS-Teams) sollte verwendet werden für:
* Commands, zB unzip
* Packagenamen, zB mysql-server
* Dateinamen und Pfade, zB ~/ .ssh/authorized_keys
* Ports, zB :3000
* Tastaturbefehle warden zusätzlich in GROSSBUCHSTABEN geschrieben und mit einem Pluszeichen verbunden, zB ENTER oder STRG+C

Da durch GitHub nicht unterstützt wird hierbei durch **Fett** ausgeglichen

### Monospace
 
```bash
Monospace wird verwendet für:
```
* Commands, die Leser*innen ausführen müssen
* Dateien und Skripte
* Terminal Ausgabe
 
Auszüge oder Auslassungen in Code werden durch runde Klammern (...) gekennzeichnet.
 
#### Variablen
Alles, was Leser*innen ändern müssen, wie Beispiel-URLs oder Änderungen in der Konfiguration werden durch Texthervorhebung <Variable>.

Auf Variablen soll auch im Text hingewiesen werden, um zu vermittel was und wieso geändert werden muss.

## Namenskonventionen

### User, Hostnamen und Domains
Als Standard-/Beispiel-Username wird pi verwendet.
dein_server ist der Standard-Hostname. 
deine_domain ist die Standard-Domain. beispiel.de ist für die Dokumentation nützlich, aber deine_domain macht klar, dass Leser*innen den Namen bei sich entsprechend anpassen sollte.
 
Diese werden durch Texthervorhebung gelb in Konfigurationen, Codeblöcken und Outputs gekennzeichnet:
```bash
ip: deine_server_ip
domain: test.deine_domain
```
Dadurch wird Leser*innen aufgezeigt, dass sie etwas ändern sollten.
 
### IP-Adressen und URLs
**deine_server_ip** ist der Standard für IP-Adressen.
Beispiel-URLs die Variablen enthalten, die Leser*innen ändern sollen, werden durch Texthervorhebung rosa gekennzeichnet, wobei die Variable mit Texthervorhebung gelb gekennzeichnet wird.
**https://deine_domain:3000/beispiel/**
 
### Software
Für Software wird die offizielle Schreibweise des Unternehmens verwendet (Groß- und Kleinschreibung). 
Die Software wird bei der ersten Erwähnung im Text verlinkt.



