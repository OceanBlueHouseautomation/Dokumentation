# Modul Kamera

## Integration Überwachungskamera
Dieses Tutorial beinhaltet ...

* ein Konzept für die Realisierung
* die Ausarbeitung weiterer nützlicher Funktionalitäten

## Realisierungskonzept Überwachungskamera
Grundsätzlich lässt sich sagen, dass die Umsetzung einer Kamera zur Videoüberwachung mittels eines Raspberry Pi’s recht simpel vonstatten geht.

Zunächst einmal müssen die benötigten Komponenten besorgt werden, denn sonst ist eine Umsetzung unmöglich. Für die Videoüberwachung wird ein ausreichend dimensioniertes Netzteil benötigt. Des weiteren braucht man ein Kamera-Modul. Dabei gibt es zwei Ausführungen, einmal die Standardvariante und die NoIR Variante. Die Standardvariante dient dazu Aufnahmen bei Tageslicht zu machen und die NoIR Variante für Nachtaufnahmen, da dort eine Infrarotbeleuchtung möglich ist.

Es ist allerdings von enorm hoher Relevanz, dass das Netzteil ausreichend Leistung bietet, denn sonst kann es zu Performance-Problemen kommen. In unserem Fall hätten wir aber schon ein ausreichend gutes Netzteil.

## Inbetriebnahme des Kamera-Moduls
Nach der Entscheidung darüber, welches Modul gewählt werden soll geht es nun darum, dieses auch entsprechend zu implementieren. Dafür wird das Modul an die CSI-Schnittstelle des Raspberry Pi’s angeschlossen. Die Schnittstelle befindet sich zwischen dem HDMI Anschluss und dem Audioausgang.

Wichtig zu erwähnen ist, dass die Installation des Moduls immer gleich abläuft, unabhängig davon, ob es sich um die Standardvariante oder die NoIR-Variante handelt.

## Aktivierung der Kamera
Im nächsten Schritt soll es darum gehen, die Kamera zu aktivieren. Bevor irgendwelche Befehle eingegeben werden können muss erstmal der Kamera-Support in Raspbian aktiviert werden. Das funktioniert über das Konfigurationstool raspi-config.

Es sind verschiedene Punkte auswählbar. Man nimmt den Punkt 5 und setzt diesen auf Enable. Nun muss der Raspberry Pi noch einmal rebootet werden.

In der Konsole wird nun folgendes eingegeben, um das obige zu tun:
```bash
$ sudo raspi-config
```

Ob das alles geklappt hat, wird mit einem weiteren Befehl die Funktionalität des Moduls getestet:
```bash
$ raspistill -o test.jpg
```
Wenn diese beiden Schritte erfolgreich durchgeführt werden konnten, geht es jetzt mit der eigentlich Konfiguration weiter.

## V4L-Treiber für das Kameramodul installieren
Früher gab es immer wieder Schwierigkeiten, den richtigen Treiber zu installieren. Dieses Problem wurde allerdings beseitigt, so dass nun keine Schwierigkeiten diesbezüglich mehr auftreten.

Der Treiber befindet sich schon in Raspbian, somit müssen nur noch die entsprechenden Module geladen werden.

Die relevanten Module sind das v4l2_common und das bcm2835-v4l2. Über die folgenden Befehle werden diese Module installiert:
```bash
$ sudo modprobe v4l2_common
$ sudo modprobe bcm2835-v4l2
```
Nun geht es darum die beiden Module in der Datei /etc/modules einzutragen, damit Kamerafunktionalität auch nach einem Reboot des Pi’s noch gegeben ist.
```bash
$ echo “v4l2_common“
$ sudo tee -a /etc/modules
$ echo “ bcm2835-v4l2“
$ sudo tee -a /etc/modules
```
Wenn diese Befehle ohne Probleme funktionieren, sollte nun ein Video-Device verfügbar sein.

Es sollte in dem Fall ein **/dev/videoO** aufgelistet sein, was nun durch folgenden Befehl überprüft wird:
```bash
$ ls /dev/video*
```

## Motion
Motion ist das Aufnahmeprogramm, mit dem Livevideo per Raspberry Pi möglich gemacht werden können. Damit ist eine 24/7 Überwachung eines vom Nutzer ausgewählten Bereichs möglich.

Motion ist in allen gängigen Linux-Distributionen schon existent und muss daher nur noch installiert werden:
```bash
$ sudo apt-get install motion
```
Und auch in dem Fall soll Motion in der Ordner-Struktur gespeichert werden, um nicht bei einem Reboot verloren zu gehen: 
```bash
$ sudo nano /etc/default/motion
```
Wenn es gewünscht ist, dass Motion immer nach Systemstart geöffnet werden soll, muss hinter dem Eintrag start_motion_daemon das no zu einem yes umgeschrieben werden.

Damit die Aufnahmen der Kamera gespeichert werden können, muss noch ein Ordner erstellt werden, der auch die benötigten Schreibrechte besitzt:
```bash
$ mkdir /home/pi/cam
$ sudo chgrp motion /home/pi/cam
$ chmod g+rwx /home/pi/cam
```

## Livestream einrichten
Wie sonst bei Linux üblich nutzt auch Motion keine grafische Steuerung sondern funktioniert über eine Konfigurationsdatei. Diese soll nun angepasst werden, um die Livefunktion herszustellen.

Mit folgendem Befehl kann nun die config-Datei bearbeitet werden:
```bash 
$ sudo nano /etc/motion/motion.conf
```
Um nun den Netzwerkstream zu aktivieren muss in der config noch einiges umgeschrieben werden:

* daemon off -> daemon on
* target_dir /temp/motion -> target_dir /home/pi/cam
* stream_localhost on -> stream_localhost off


Wenn dies nun alles erledigt ist, kann nun das Kameramodul genutzt werden. Motion bietet allerdings viele verschiedene Anpassungsmöglichkeiten, um das Modul an die Nutzung anzupassen.

## Livestream in FTUI einbinden

Um den Livestream nun in FTUI einzubinden, besteht die Möglichkeit der Nutzung von widget_video.js, einem inoffiziellen Erweiterungsmodul für die FTUI. Dieses kann im FHEM-Forum nach kostenloser Registrierung als Nutzer heruntergeladen werden:

[widget video.js](https://forum.fhem.de/index.php/topic,95374.0.html)

Die heruntergeladene Datei muss dabei einfach in /fhem/www/tablet/js kopiert werden. Anschließend kann eine Einbindung gemäß des FHEM-Wikis erfolgen, siehe [hier](https://wiki.fhem.de/wiki/FTUI_Widget_Video)

Anmerkung: widget_video.js stellt bislang keine offizielle Erweiterung von FHEM dar. Die Nutzung erfolgt auf eigene Gefahr. Die Einbindung im Rahmen des Projekts erfolgte ohne Probleme.

## Weitere nützliche Funktionalitäten
Die Kameramodule können vielseitig verwendet werden, deshalb sollen nun ein paar weitere sinnvolle Verwendungsmöglichkeiten genannt werden.

Im obigen Konzept wird ja die Tageslichtkamera angesprochen, aber genauso ist es möglich ein Kameramodul einzubauen, dass das Haus Nachts überwacht.

Eine weitere Möglichkeit die der Raspberry Pi bietet ist die Mehrfachnutzung von Kameras. Man kann wie bei der einfachen Nutzung auch mehrere Kameramodule installieren(was auf dem selben Weg funktioniert), um dann mehrere Bereiche gleichzeitig beobachten zu können.

Es ist natürlich auch möglich eine Kamera an den Raspberry Pi anzuschließen, die keine Überwachungsfunktion hat, sondern als Webcam agieren soll. Allerdings wäre dabei die Sicherheit ein Aspekt den man definitiv nicht unterschätzen sollte.


