# Wie du mit Hilfe von FHEM nie wieder zu spät kommst

## Einführung

In diesem Tutorial erfährst du, wie du mithilfe von Google Maps benutzerdefinierte Routen überwachen kannst, und Verzögerungen sowie die beste Route über FTUI darstellst.

## Voraussetzungen
- Funktionierende FHEM-Installation
- Google-Account
- Kreditkarte

### Schritt 1 - Installation benötigter Software-Komponenten auf dem Raspberry Pi

Das Modul TRAFFIC ist zwar in FHEM bereits vorhanden, benötigt jedoch einige zusätzliche Skripte/Module auf deinem Pi, die Funktionen des Skripts überhaupt erst möglich machen. Daher ist es obligatorisch, folgende Pakete mit nachfolgenden Befehlen zu installieren:
```
sudo cpan -i LWP::Simple
sudo cpan -i JSON
sudo cpan -i MIME::Base64
```


### Schritt 2 - Erstellen eines Google-API-Keys

Das Modul TRAFFIC verbindet sich über einen sog. API-Schlüssel direkt mit deinem persönlichen Google-Account bzw. dem Dienst Google Cloud. Um diesen zu aktivieren, ist eine Kreditkarte nötig, deren Zahlungsdaten du in deinem Google Cloud-Account hinterlegen musst. Keine Sorge - wir stellen TRAFFIC gemeinsam so ein, dass nur sehr selten eine Abfrage erfolgt. Ebenso aktivieren wir nicht die automatische Abrechnung von Google Cloud. Damit bist du erst einmal vor anfallenden Kosten geschützt und kannst eine Weile mit dem bereitgestellten Gratis-Kontingent von 300 US-Dollar zurecht kommen.

Wie du in Google Cloud ein neues Projekt anlegst und für diesen einen API-Schlüssel erstellst, erfährst du hier:
https://cloud.google.com/docs/authentication/api-keys

Du musst folgende Schnittstellen ("API"s) aktivieren: Google Maps Directions API, Google Maps Javascript API

### Schritt 3 - Erstellen eines TRAFFIC-Devices und initiale Einrichtung

Die Einrichtung eines TRAFFIC-Devices funktioniert über FHEM relativ einfach:
```
define TRAFFIC1 TRAFFIC <API-KEY>
```

Neben dem Gerät TRAFFIC1 kannst du natürlich für die gewünschte Anzahl der Routenüberwachungs-Systeme eigene Geräte z.B. als TRAFFIC2, TRAFFIC3, usw. erstellen.
Anstelle <API-KEY> kopierst du den kompletten in Schritt 2 generierten API-Key deines Google-Accounts in den Befehl.

Du bist noch nicht ganz am Ende: Das Gerät wird dir - sofern du eine Kreditkarte hinterlegt hast und auch der API-Key richtig konfiguriert ist - den Status "Incomplete Configuration" anzeigen. Wenn nicht: Wiederhole Schritt 2 oder wende dich mit einer konkreten Fragestellung an das FHEM-Forum.

Nun musst du manuell noch die Reading-Felder "start_address" und "end_address" angeben. Gebe diese vollständig an, z.B. "Am Hauptbahnhof 2, 70173 Stuttgart", falls du zur IT der Landesbank Baden-Württemberg möchtest. Nach Eingabe der Start- und Zieladdresse werden ab der nächsten Aktualisierung Daten angezeigt.
Manuell kannst du eine erneute Abfrage natürlich ebenfalls ausführen:
```
set TRAFFIC1 update
```

### Schritt 4 - Integration in FTUI

Für die Abgabe haben wir uns entschlossen, die wichtigsten Informationen, also die Start- sowie End-Addresse und die Fahrtdauer inkl. Verzögerung sowie bester Route möglichst kompakt anzuzeigen. Dafür wurde folgender Code-Schnipsel verwendet, den du z.B. direkt auf deine Startseite oder eine eigens eingerichtete Seite integrieren kannst:

```html
<li data-row="2" data-col="3" data-sizex="1" data-sizey="2" class="left-align"><header>Verkehrsroutenüberwachung</header>
    <div class="sheet">
		<div class="inline big cell" data-type="label">Fahrt von</div>
        <div class="cell" data-type="input" data-device="TRAFFIC1" data-get="start_address" data-set="start_address" data-cmd="setreading" class="w1x"></div>
		<div class="inline big cell" data-type="label">nach</div>
        <div class="cell" data-type="input" data-device="TRAFFIC1" data-get="end_address" data-set="end_address" data-cmd="setreading" class="w1x"></div><hr>
		<div class="inline big cell" data-type="label">Dauer bis zum Ziel</div>
		<div data-type="label" data-device="TRAFFIC1" data-get="duration"></div>
		<div class="inline big cell" data-type="label">Verzögerung</div>
		<div data-type="label" data-device="TRAFFIC1" data-get="delay"></div>
		<div class="inline big cell" data-type="label">Kürzeste Fahrtzeit über ...</div>
		<div data-type="label" data-device="TRAFFIC1" data-get="summary"></div>
    </div> 
</li>
```

### Schritt 5 - Fein-Tuning

Du möchtest deine Verkehrsroutenüberwachung weiter nach deinen Wünschen einstellen? Normalerweise aktualisiert die Routenüberwachung sich nur einmalig pro Stunde, um das kostenlose Budget nicht zu sehr zu belasten. Wenn du jedoch bereits weißt, dass du zu bestimmten Stoßzeiten (z.B. zum Feierabendverkehr) eher an aktuellen Ergebnissen interessiert bist, kannst du dahingehend einen sogenannten Burst-Mode integrieren, der zu Stoßzeiten für eine häufigere Aktualisierung führt, z.B. für unser erstes eingerichtetes Device:
```
define FeierabendCheck AT *17:00 set TRAFFIC1 update 12 300
```
Mit diesem Codeschnipsel wird das Gerät TRAFFIC1 ab 17 Uhr für eine Stunde alle 5 Minuten aktualisiert.

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://wiki.fhem.de/wiki/TRAFFIC (Letzter Zugriff: 01.02.21)
