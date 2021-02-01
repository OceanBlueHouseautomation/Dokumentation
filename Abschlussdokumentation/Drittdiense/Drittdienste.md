# Drittdienste

## Merkmale der Dienste
* PushoverApp, Prowl App, Gammu	
  * Kostenpflichtig
* Pushalot, Pushbullet App, Andnotify App
  * Nicht für alle Betriebssysteme
* yowsup modul (Whatsapp)
  * Keine offizielle API, ist eine Community Lösung
* PushNotifier
  * Nicht mit Handynummer konfigurierbar
* TelegramBot
  *Nutzer muss den Bot erst anschreiben 

## Entscheidung
Wir haben uns nach ausführlicher Betrachtung für PushNotifier entschieden, da die vorhandenen Features am ehesten mit den Wünschen des Auftraggebers zusammenpassen.

## Einrichtung
Wird über folgenden Define in Fhem eingebunden:
```
define pushAll PushNotifier 8E7D8B2DDE7DDE7DDE7DBV466C3VETBTTTTBFKFETB com.DHBWProjekt.app projekt5dhbw Projekt5dhbw! .*
```
Achtung: Das FHEM-Objekt muss neu angelegt werden, sobald man PushNotifier ein neues Gerät hinzufügt.
