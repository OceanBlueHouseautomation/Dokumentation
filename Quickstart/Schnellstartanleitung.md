# Wie du sofort eine komplette FHEM-Installation bekommst, ohne Lebenszeit zu verschwenden!

## Einführung

In diesem Tutorial wirst du lernen, wie du das Ocean Blue Houseautomation-System in wenigen Schritten funktionsbereit konfigurierst und installierst.
So erhältst du unsere Abschlusslösung, ohne die Gefahr, dass in einzelnen Tutorials etwas schief läuft!

## Voraussetzungen
* Benötigte Hardware griffbereit
* Image heruntergeladen, siehe Schritt 1

### Schritt 1 - Beiliegendes Image-File auf die MicroSD-Karte schreiben

Du hast von uns ein Image-File erhalten, welches bereits die komplette Konfiguration des Raspberry Pis inklusive FHEM-Installation und aller gelieferten Module enthält.
Dieses kannst du einfach mit BalenaEtcher oder einer vergleichbaren Software auf die Karte schreiben.
Das Image findest du hier: https://1drv.ms/u/s!AgnLQTrcdpQIgqsAM64PKjwyvJlgcQ?e=f2W5kb (Der Download ist  nur bis Mai 2021 möglich!)
BalenaEtcher findest du hier: https://www.balena.io/etcher/


### Schritt 2 - Anschließen der Hardware

Zum Anschließen der Hardware empfehlen wir dir die Lektüre des folgenden Tutorials:
https://github.com/OceanBlueHouseautomation/Dokumentation/blob/main/Tutorials/Modul%20Hardware/Tutorial%20Hardware.md

Tipp: Bei der Einrichtung des Shellys solltest du diesen direkt mit der IP-Adresse 192.168.178.99 anbinden - dann findet unsser Basis-Image diesen direkt und du sparst noch mehr Zeit bei der Einrichtung!

### Schritt 3 - Starten deiner Installation

Deine Installation ist mit einem Passwort gesichert. Du erhältst Zugriff über den Nutzername "pi" und das Passwort "projekt5". In den Einstellungen der FTUI kannst du nun
alle relevanten Daten wie zum Beispiel deine Postleitzahl hinterlegen.


### Schritt 4 - "Feintuning"

Manche Module benötigen nun noch etwas Feintuning von dir: Z.B. musst du bei der Verkehrsroutenüberwachung und beim Dienst PushNotifier einen eigenen API-Key hinterlegen. Wie du diese generierst und richtig integrierst, lernst du in den detaillierten Tutorials.
Außerdem solltest du über FTUI deine Einstellungen anpassen. So kommst du mit unserem Grundgerüst Schritt für Schritt zu deinem eigenen FHEM-System! Wenn einzelne Module zu Fehlern führen, ist immer noch eine Kleinigkeit anzupassen. Komm bei Fragen hierzu natürlich auch gerne auf uns zu!


## Abschluss
Schon fertig?
Dann schau doch mal in unsere Einzeltutorials. Wir sind uns sicher, dass du dann selbst nachvollziehen kannst, wie und warum alles funktioniert - und du bekommst Inspiration
für weitere Projekte. Die Quellen, auf denen unsere Arbeit basiert, werden darüber hinaus ebenfalls dort genannt.

Viel Spaß!
