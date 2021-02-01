# Raspberry Pi

## Verbindung mit dem Raspberry Pi
### Per SSH:
Empfohlen wird die Nutzung von PuTTY, Öffnen einer SSH-Session mit folgenden Daten:
Host Name (or IP address)	Port
87nr4b7s6c0r4wv9.myfritz.net	22
Die Erreichbarkeit ergibt sich durch DynDNS im Zusammenspiel mit Portweiterleitung.
 
Die erste Abfrage ("login as: ") muss mit dem gewünschten Nutzernamen beantwortet werden. Dieser ist "pi" (Achtung: Dieser Nutzer hat alle Rechte!)
Das zugehörige Passwort lautet: projekt5
 
### Per Telnet:
Nicht möglich (deaktiviert)
 
Interne IP: 192.168.178.133

## Einstellungen des Raspberry Pi 
* SWAP-Datei (Auslagerungsdatei für den Arbeitsspeicher) wurde deaktiviert, um die SD-Karte zu schonen
* Verzeichnisse /var/run und /var/log wurden in den Arbeitsspeicher verlegt und sind daher flüchtig
* UART (Serielle Schnittstelle) wurde zur Verbindung mit dem Selbstbau-CUL aktiviert

## Sicherungskonzept
FHEM sichert seine Konfiguration einmal täglich auf die SD-Karte des Raspberry Pi - zur Wiederherstellung eines anderen Tagesstands der Automatisierungsskripte bspw.. Der Pi sichert den kompletten SD-Karten-Inhalt einmal täglich als Image auf einen externen USB-Stick - für den Fall, dass die SD Karte kaputt gehen sollte.
 
### Automatisierte Sicherungen von FHEM immer um 23:59
```
define NTFY_BackupRun at *23:59:00 set SYS_Backup Ausführen
attr NTFY_BackupRun room Server
save
```
vgl. backup – FHEMWiki
https://wiki.fhem.de/wiki/Backup#Backup_automatisch_ausf.C3.BChren
 
Das Backup wird ebenfalls vor einem Update des FHEM durchgeführt.
 
### Automatisierte tägliche Backups des kompletten Raspberry Pi auf einen externen USB-Stick (32GB)
Der externe USB Stick wurde als /dev/sda1 in /mnt/usb eingebunden:
```
sudo mkdir /mnt/usb
 
mount -t ntfs-3g -o utf8,uid=pi,gid=pi /dev/sda1 /mnt/usb
``` 
Zur Erstellung der Backups kommt folgendes Script von Github-Nutzer Izkelley zum Einsatz: GitHub - lzkelley/bkup_rpimage: Script to backup a Raspberry Pi disk image
https://github.com/lzkelley/bkup_rpimage
```
sudo apt-get install git
git clone https://github.com/lzkelley/bkup_rpimage.git
```
### Initiales Backup
```
sudo sh ~/bkup_rpimage/bkup_rpimage.sh start -c /mnt/usb/rpi_backup.img
```
Erstellung eines Jobs für tägliche Backups, benannt nach dem jeweiligen Datum:
```
sudo crontab -e

0 0 * * * sudo sh ~/bkup_rpimage/bkup_rpimage.sh start -c /mnt/usb/rpi_$(date +%Y-%m-%d).img
``` 
siehe auch How to Backup your Raspberry Pi SD Card - Pi My Life Up
https://pimylifeup.com/backup-raspberry-pi/#:~:text=%20Backing%20up%20your%20Raspberry%20Pi%20to%20a,to%20look%20at%20the%20Mounted%20on...%20More%20

### Manuelle Backups des Sicherungssticks - einmal wöchentlich freitags
 
Ein Backup nimmt ca. 2 GB ein - eingestellt wurde ein täglich neu angelegtes Backup, um hiermit für den Ernstfall auch eine Versionierung zu ermöglichen. Es ist daher möglich, den Stand eines bestimmten Tages wiederherzustellen. Um nicht in Platzknappheit zu geraten, sollte der USB-Stick also spätestens nach 14 Tagen einmal geleert werden bzw. die Images auf ein anderes Speichermedium überspielt - oder nicht mehr benötigte Images gelöscht werden.
 
### Wiederherstellung eines Backups
 
Die Backups sind Image-Files und können beispielsweise mit balenaEtcher auf die SD-Karte geschrieben werden.

## Installation des FHEM
Gemäß Raspberry Pi – FHEMWiki und Hausautomation für wenig Geld: FHEM auf dem Raspberry Pi | reichelt.de
https://wiki.fhem.de/wiki/Raspberry_Pi#Installation_.2F_Setup
https://www.reichelt.de/magazin/how-to/hausautomation-mit-fhem-der-raspberry-als-starke-smart-home-zentrale/

### Anbindung des Selbstbau-CUL
hängt vrstl. an /dev/ttyUSB0 (Name ist " ch341-uart converter")
 
Funktionalität testen:
 
Kurz herausfinden, welcher tty Port für die USB Serial Verbindung zugewiesen wurde:
``` 
dmesg | grep tty
``` 
Ausgegeben wird der entsprechende Port:
``` 
[...] now attached to ttyUSBX
``` 
Die Kommunikation mit dem Board nutzt 38400 Baud und die Serial Konfiguration 8N1 - diese müssen im Programm "minicom" gesetzt werden:
``` 
sudo minicom -s
```
Anschließend minicom starten und eine Abfrage durch "V" (Shift + V, dann Enter) starten:
Die Antwort sollte den selbstgebauten CUL mit 433 MHz und der Softwareversion (V 1.67) enthalten.

### Daten auf den Raspberry übertragen
* Via SFTP unter Nutzung von Filezilla
* Nutzung der bekannten Credentials der SSH-Verbindung
 
#### Achtung: 
Es müssen mglw. kurzfristig Schreibrechte gewährt werden, um die Daten vom privaten Rechner auf den Pi zu schreiben,
 
Hierzu auf dem PI z.B.
cd fhem/www
sudo chmod 777 tablet
