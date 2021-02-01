# Wie du dir die Basis für dein eigenes Smart-Home-System baust

## Einführung

In diesem Tutorial wirst du FHEM auf einem Raspberry Pi aufsetzen. Du wirst außerdem Deinen Pi mit weiteren Hardware-Erweiterungen ausstatten, um später zahlreiche Projekte umsetzen zu können. Dies ist ein Must-Do und die Basis für alles weitere was du umsetzen wirst!

## Voraussetzungen
Keine vorangehenden Schritte. Folgende Hardware musst du bereithalten:
- Raspberry Pi (Version beliebig, wir empfehlen die Variante 3B+)
- Micro-SD-Karte (min. 16 GB!)
- TECKIN SP22 WLAN Steckdose
- Beliebige Funk-Steckdose


### Schritt 1 - Raspberry Pi OS auf deinem Pi

Um FHEM auf deinem Raspberry Pi zu installieren, musst du zunächst ein Betriebssystem installieren. Um dies zu bewerkstelligen, gehe exakt gemäß folgender Anleitung vor:
https://wiki.fhem.de/wiki/Raspberry_Pi#Installation_.2F_Setup
Du solltest die Abschnitte Vorbemerkung, SD-Karte vorbereiten, Anmeldung und Grundkonfiguration durchführen. Statt dich mit einem WLAN zu verbinden kannst du natürlich auch deinen Pi direkt via Ethernet-Kabel mit deinem Router verbinden.

### Schritt 2 - Die Installation von FHEM

Fhem ist nicht schwer zu installieren: Du verbindest dich wie schon in Schritt 1 mithilfe der Software Putty mit deinem Raspberry Pi. Nach Login kopierst du folgenden Befehl ins Putty-Fenster und führst ihn mit der Return-Taste aus: 

```
wget -qO - http://debian.fhem.de/archive.key | apt-key add -
echo "deb http://debian.fhem.de/nightly/ /" >> /etc/apt/sources.list
apt-get update
apt-get install fhem
```
Anschließend solltest du den Raspberry mit dem Kommando "reboot" (wieder über das Putty-Fenster durchgeführt) einmalig neustarten.

### Schritt 3 - Baue und verbinde einen sogenannten Selbstbau-CUL

Der Selbstbau-CUL ist ein Stück Hardware, welches du benötigst um ein 433 MHz-Signal mit deiner Smarthome-Installation zu verschicken. Du benötigst diesen, um mit deiner Funksteckdose zu kommunizieren. Das Praktische am sogenannten CUL ist, dass du diesen auch ganz einfach selbst bauen kannst. Hierfür brauchst du:
- Arduino Nano (oder Clon)
- CC1101 Funkmodul mit 868 MHz oder 433 MHz (von uns verwendet) inkl. Antenne
- Jumper Kabel weiblich-weiblich
Der Zusammenbau des CUL ist relativ einfach:

|Arduino Nano|Funkmodul                  |
|---                |---                 |
|Pin 17 - VCC 3,3V  |PIN 1 - VDD         |
|Pin 14 - D11       |PIN 3 - SI (MOSI)   |
|Pin 16 - D13       |PIN 4 - SCK         |
|Pin 15 - D12       |PIN 5 - SO (MISO)   |
|PIN 5 - D02        |PIN 6 - GDO2        |
|Pin 13 - D10       |PIN 7 - CSn (SS)    |
|Pin 6 - D03        |PIN 8 - GDO0        |
|PIN 4 - GND        |PIN 9 GND           |
|nicht belegt       |PIN 2 & 10          |

Als nächstes musst Du eine Firmware, also einen Treiber/ein Mini-Betriebssystem für den CUL aufspielen. 
Hierzu meldest du dich über das Programm PuTTy per SSH auf deinem Rapsberry Pi an und führst nacheinander die folgenden Befehle aus:

```
sudo apt-get install make gcc-avr avrdude avr-libc subversion
svn checkout http://svn.code.sf.net/p/culfw/code/trunk culfw-code
cd culfw-code/culfw/Devices/nanoCUL
nano board.h
```
Je nach dem, ob du einen 868 MHz oder einen 433 MHz-Chip besitzt, musst du eine Zeile im sich geöffneten Editor ausklammern. Die entsprechende Zeile ist dir im Editor-Code markiert. (If yo are using a ... module for XXX MHz). Lösche die jeweils nicht zu deinem Chip passende Zeile.

Speichere die Änderungen anschließend mit Strg + X, Y und Enter.
Anschließend kompilierst du die Firmware durch:
```
make -j 4
```

sowie anschließendem 
```
make program
```
Der Pi sollte nun erfolgreichen Abschluss melden: "avrdude done. Thank you."

### Schritt 4 - Binde eine Funksteckdose an FHEM an

Deine Funksteckdose verfügt auf der Rückseite über eine Klappe, die du öffnen kannst, um die Funksteckdose individuell einzustellen. Die sogenannten DIP-Schalter, die du hier findest, können entweder an oder ausgeschaltet sein. (Im Folgenden sprechen wir von 0 = ON, F = OFF). Stelle deine Funksteckdose mit einem eigenen individuellen Code ein, z.B. indem du die ersten 6 Schalter auf den Zustand OFF/AUS und die letzten vier Schalter auf den Zustand ON/AN stellst. Merke dir die Position der Schalter, um die Steckdose wie folgt in FHEM einzubinden:

```
define funksteckdose IT 000000FFFF 0F F0
```

wobei du 000000FFFF durch die jeweilige Schalterstellung deiner Schalter ersetzt. Zur Erinnerung: 0 steht für OFF, F steht für ON.

Was für ein Gerät du genau an deiner Funksteckdose betreibst, ist für FHEM nicht relevant und muss daher nicht angegeben werden. Wir empfehlen dir für den Start zum Beispiel eine Lichterkette anzubinden.

### Schritt 5 - Binde eine WLAN-Steckdose (Modell Teckin SP22) an FHEM an

Nachdem du die Funksteckdose sicher problemlos anschließen konntest, wird es mit der WLAN-Steckdose ein Stück weit komplizierter. Hierfür musst du nämlich zuerst eine sogenannte alternative Firmware aufspielen, die die Steckdose über den Umfang des Herstellers hinaus mit Funktionen nachrüstet. Der Heise-Verlag bietet dir hier mit dem Programm "tuya convert" eine bequeme Lösung, um deine Steckdose für FHEM vorzubereiten.

ACHTUNG: Update nicht die Firmware deiner Steckdose über die Smart Life-App des Herstellers! Außerdem müssen wir dich leider darauf hinweisen, dass nur ältere Modelle mit tuya convert "umgerüstet" werden können - im Zweifel findest du ja vielleicht auf eBay Kleinanzeigen eine gebrauchte und kompatible Version der TECKIN-Steckdose.

Die Steckdose sendet ihre Statusinformationen über das Protokoll MQTT innerhalb deines Netzwerks. Damit Sie sich mit FHEM verbinden kann, musst du daher MQTT in FHEM aktivieren. Das geht ganz einfach:

```
defmod mqttBroker MQTT2_SERVER 1883 global
```
Du hast nun auf dem sogenannten Port 1883 deines Raspberry Pi MQTT für FHEM geöffnet. 
FHEM sollte deine WLAN Steckdose nun automatisch erkennen und bereits anzeigen. Natürlich kannst du sie nachträglich umbenennen.

### Schritt 6 - Verbinde einen Shelly 2.5 mit deiner FHEM-Installation

Gegenüber der WLAN-Steckdose gestaltet sich die Einrichtung eines Shellys etwas einfacher.
Diesen musst du lediglich in eine Steckdose einstecken und er öffnet ein eigenes WLAN-Netzwerk. Wenn du dich mit diesem verbindest, erreichst du die Konfigurationsseite des Shellys. Auf diesem kannst du deinen Shelly mit deinem eigenen Router-WLAN verbinden und bei Vorhandensein auch Updates des Shellys installieren.
Merke dir nun die IP-Adresse, die dein Shelly sich zuweist. Tipp: Gib deinem Shelly manuell eine eigene IP-Adresse, das geht in der Konfigurationsoberfläche des Shellys, in der du auch alle anderen Einstellungen setzt. Wir haben uns hier für die Adresse 192.168.178.99 entschieden.

Diese IP ist es auch, die FHEM nun zur Konfiguration von dir benötigt:
```
define myShelly Shelly 192.168.178.99
```
Anschließend teilst du FHEM noch die genaue Version deines Shellys mit. Verwendest du den Shelly im Rahmen des DHBW Stuttgart-Projekts, handelt es sich um einen Shelly 2:
```
attr myShelly model shelly2
```
Fürs erste musst du darüber hinaus nichts mit dem Shelly machen. Du wirst ihn nachher in verschiedenen anderen Tutorials genauer konfigurieren.

### Schritt 7 - Verbinde einen Ultraschallsensor zum Messen von Füllständen, Abständen und Co.
Jetzt wird es nochmal technisch, das ist aber nichts, was du nicht hinkriegst! 

## Abschluss
Schon fertig?
Dann bist du jetzt bereit, dein erstes Automationsprojekt umzusetzen. Deine Hardware ist angeschlossen und bereit für den Einsatz.
Lerne hier, wie du smarte Steckdosen einbindest, am Beispiel einer coolen Weihnachtsbeleuchtung:
https://github.com/OceanBlueHouseautomation/Dokumentation/blob/main/Tutorials/Modul%20Weihnachtsbeleuchtung/Tutorial.md

Vielleicht hast du dir aber auch eine Rollladensteuerung auf Basis eines Shelly 2.5 gekauft? So bindest du diese ein:
https://github.com/OceanBlueHouseautomation/Dokumentation/blob/main/Tutorials/Modul%20Rolladensteuerung/Rolladensteuerung.md

Viel Spaß!

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://wiki.fhem.de/wiki/Raspberry_Pi (Letzter Zugriff: 28.01.21)
https://www.reichelt.de/magazin/how-to/hausautomation-mit-fhem-der-raspberry-als-starke-smart-home-zentrale/ (Letzer Zugriff: 28.01.21)
https://raspberry.tips/raspberrypi-tutorials/hausautomatisierung-mit-fhem-teil-1-cul-stick-selbstbau-868mhz-cul-am-raspberry-pi (Letzter Zugriff: 01.02.21)
https://gettoweb.de/haus/tasmota-device-in-fhem-einbinden/ (Letzter Zugriff: 28.01.21)
https://www.einplatinencomputer.com/raspberry-pi-ultraschallsensor-hc-sr04-ansteuern-entfernung-messen/ (Letzter Zugriff: 28.01.21)
https://forum.fhem.de/index.php?topic=19812.0 (Letzter Zugriff: 28.01.21)
