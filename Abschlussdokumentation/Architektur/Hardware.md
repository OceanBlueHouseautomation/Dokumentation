# Hardware
## Funksteckdose
Die Funksteckdose verfügt über DIP-Schalter, diese sind eingestellt als 000000FFFF
O = DIP-Schalter steht auf ON
F = DIP-Schalter steht auf OFF
 
Angelegt in FHEM wurde die Funksteckdose also mit:
```
define funksteckdose IT 000000FFFF 0F F0
```
Die Funktionalität der Funksteckdose wurde überprüft. An der Funksteckdose hängt derzeit eine Lichterkette.

## WLAN Steckdose (Teckin)
Account auf Smart Life
Mail: projekt5@byom.de
Password: *****
 
 
 
siehe im Folgenden https://wiki.fhem.de/wiki/MQTT2-Module_-_Praxisbeispiele
und (19) Tasmota flashen ohne Löten! So funktioniert's! - YouTube
 
Geflasht mit tuya convert auf Tasmota, direkt Update auf 8.5.0 über Webserver (Tasmota-Oberfläche)
IP-Adresse herausfinden: Hier 192.168.178.136
 
Configure Modul -> Teckin
 
In FHEM:
```
In FHEM:
defmod mqttBroker MQTT2_SERVER 1883 global
``` 
 
Configure MQTT -> IP des Pi als IP, Port 1883
 
In FHEM angelegt als WLAN Steckdose (Automatisiert geschehen!)
 
 
TASMOTA Device in FHEM einbinden - my GettoWEB.DE
https://gettoweb.de/haus/tasmota-device-in-fhem-einbinden/
 
## Shelly 2.5 mit zwei Glühlampen
Der Shelly 2.5 ist mit dem internen Netzwerk unter der IP-Adresse 192.168.178.99 fest verbunden.
 
Angelegt in FHEM wird dieser also mit:
define myShelly Shelly 192.168.178.99
 
Auf der Detailseite sind folgende Einstellungen zu setzen:
Attribut model = shelly2
attr myShelly model shelly2
 
Attribut mode auf gewünschten Betriebsmodus - roller (Aufgabe Pumpe) oder relay (Aufgabe Rolladen)
attr myShelly mode <roller/relay>
 
Da beim Betrieb als Roller zwei Glühlampen verbunden sind, muss noch das Attribut defchannel gesetzt werden,
welches je nach gewünschter Glühlampe 0 oder 1 ist.
```
attr myShelly defchannel <0/1>
``` 

Vorangelegt wurden folgende Konfigurationen:
 
* ShellyPumpe1	
  * mode = relay
  * defchannel = 0
* ShellyPumpe2	
  * mode = relay
  * defchannel = 1
* ShellyRollladen	
  * mode = roller
 
Die Bedienung der Pumpen ist selbsterklärend (On/Off).
Die Bedienung der Rollladenfunktion muss in der commandref nachvollzogen werden und entspricht vrstl.:
set ShellyRollladen <open/close/stop>
 
 
Bug: Zwischen ShellyPumpe1, ShellyPumpe2 und ShellyRollladen kann derzeit nicht gewechselt werden. Möglicherweise kann nicht mit drei Profilen parallel gearbeitet werden und stattdessen müssen in den durch FTUI ausgelösten Skripten automatisiert der mode und weitere Einstellungen des Shellys entsprechend gesetzt werden.

## Aufbau Selbstbau-CUL
Gemäß Hausautomatisierung mit FHEM Teil 1 - CUL Stick Selbstbau - 868MHz CUL am Raspberry Pi
Achtung: Wir haben einen 433 MHz-Chip!

## Anbindung USB-Stick
Der USB-Stick ist für die automatisierte Anlegung der Backups mit dem Pi verbunden.
 
### Automatisches Mounting des USB-Sticks
 
Durch Nutzung des unten stehenden Befehls müssen Partition (sdaX), UUID und TYPE ermittelt werden.
```
sudo blkid
``` 

Aufruf der Datei fstab mit Nano:
```
sudo nano /etc/fstab
``` 

Aufruf der Datei fstab mit Nano:
 
Die Ausgabe des ersten Befehls ergab die zu setzenden Parameter:
UUID=74EA618724897ACE   /media/usb   ntfs   auto,nofail,sync,users,rw   0   0
 
 
siehe USB-Stick und USB-Festplatten mit "fstab" automatisch mounten/einhängen (Raspberry Pi) (elektronik-kompendium.de)
https://www.elektronik-kompendium.de/sites/raspberry-pi/2012181.htm
USB-Stick mit "usbmount" automatisch mounten/einhängen (Raspberry Pi) (elektronik-kompendium.de)
https://www.elektronik-kompendium.de/sites/raspberry-pi/1911271.htm

## Architektur
siehe Architekturschaubild
 
 
## Ultraschallsensor
Raspberry Pi: Ultraschallsensor HC-SR04 ansteuern (Entfernung messen) (einplatinencomputer.com)
Ultraschall Entfernungsmessung mit Raspberry, FHEM und dem HC-SR04 Modul
 
In Fhem: UC1
 
telnet wurde benötigt: In Raspberry Pi via apt-get installiert + telnet – FHEMWiki
 
Es müssen Berechtigungen für FHEM in Rapsberry Pi OS gesetzt werden
