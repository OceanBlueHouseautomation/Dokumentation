# Wie FHEM dir Warnungen und Statusupdates direkt an Dein Handy schickt!

## Einführung

In diesem Tutorial erfährst du anhand eines konkreten Beispiels einer Sturmwarnung, wie du Push-Benachrichtigungen von FHEM über den Dienst PushNotifier an dein Android- oder iOS-Gerät schickst. Warum gerade über PushNotifier? PushNotifier ist als einziger etablierter Dienst sowohl für Android als auch iOS verfügbar, dabei jedoch nicht mit anfallenden Kosten verbunden.

## Voraussetzungen
- Funktionierende FHEM-Installation
- Smartphone mit Android oder iOS :-)

### Schritt 1 - Erstellen eines PushNotifier-Kontos und Anlegen der Geräte

Wichtig bei der Handhabung von PushNotifier in Verbindung mit deinem FHEM-System ist, dass du bereits im Vorfeld ein eigenes PushNotifier-Konto erstellst und diesem
deine Geräte hinzufügst, indem du die zugehörige App auf deinen Smartphones installierst und diese einloggst. Über die Seite API Settings musst du zudem eine Applikation
mit einem beliebigen Namen anlegen. Zudem benötigst du einen sog. API-Key. Um mögliche Fehler auszuschließen, erstelle bitte unbedingt eine Applikation mit einem individuellen
Namen, den voraussichtlich noch kein anderer Nutzer verwendet.

### Schritt 2 - Schaffen der Voraussetzungen

Wie andere FHEM-Module benötigt auch das PushNotifier-Modul weitere Skripte direkt auf deinem Pi:

```
cpan -i Try::Tiny
```

### Schritt 3 - Hinzufügen des PushNotifier-Dienstes in FHEM

Nun benötigst du verschiedene Daten aus den vorherigen Schritten, die du alle gesammelt in einem einzigen Befehl in FHEM eintragen musst:
```
define pushmsg PushNotifier <apiToken> <appname> <user> <password> .*
```
Hierbei musst du bei apiToken sowie appname die eben generierten bzw. erstellten Daten aus Schritt 1 angeben. User und Passwort sollten dir bekannt sein, hierbei handelt es sich um deine Login-Daten. Die Angabe .* bedeutet, dass alle Push-Benachrichtigungen an sämtliche eingeloggten Geräte verschickt werden sollen. Natürlich kannst du hier
feingranularer arbeiten und z.B. mit dem Name des Gerätes auch gezielt einzelne Geräte - z.B. nur deines, jedoch nicht die Geräte deiner Kinder - ansteuern.
Der Einfachheit halber legen wir in dieser Abgabe und diesem Tutorial jedoch ein pauschales Versandsystem an alle deine Geräte an.

Anschließend ist es möglich, über den Befehl:
```
set pushmsg message <message>
```
unter Eingabe der gewünschten Nachricht als <message> beliebige Push-Benachrichtigungen zu versenden.
  
### Schritt 4 - Integration eines Windsensors, z.B. mobile-alerts.eu

Der folgende Code integriert einen Windsensor von data199 in dein System. Im Attribut requestData musst du als deviceids und phoneid die Daten wählen, die dein gewünschter mobile-alerts.eu-Sensor besitzt. Du erhältst diese über den direkten Link auf mobile-alerts.eu hinter dem "?". Lasse die restlichen Einstellungen und Befehle jedoch unverändert: 
```
define WindsensorAPI HTTPMOD https://www.data199.com/api/pv1/device/lastmeasurement 420
attr WindsensorAPI userattr requestData
attr WindsensorAPI enableCookies 1
attr WindsensorAPI event-on-change-reading .*
attr WindsensorAPI extractAllJSON 1
attr WindsensorAPI requestData deviceids=0B1D2300E734&phoneid=285142992122
attr WindsensorAPI stateFormat Geschwindigkeit: wind_speed km/h <br> Böe: wind_gust km/h <br> Richtung: wind_direction ° <br> Messung: timestamp
attr WindsensorAPI timeout 410
attr WindsensorAPI userReadings wind_direction:devices_01_measurement_wd.* { int(ReadingsVal("WindsensorAPI","devices_01_measurement_wd",0))/16*360 },  wind_speed:devices_01_measurement_ws.* { ReadingsVal("WindsensorAPI","devices_01_measurement_ws",0)/1000*3600 },  wind_gust:devices_01_measurement_wg.* { ReadingsVal("WindsensorAPI","devices_01_measurement_wg",0)/1000*3600 },  timestamp:devices_01_measurement_ts.* { POSIX::strftime("%F %T", localtime(ReadingsVal("WindsensorAPI","devices_01_measurement_ts",0))) },
```
Das Gerät wird nun alle 420 Sekunden die aktuellen Winddaten abfragen. Warum nur alle 420 Sekunden? Da der Windsensor ohnehin nicht häufiger Daten aufliefert, wäre ein niedrigeres Prüfintervall nicht sinnvoll bzw. würde nur unnötige Abfragen generieren.

Alternativ stellen wir dir mit demselben MODUL die passende Lösung für mobile-alerts.eu direkt vor. Hierüber erhältst du allerdings weniger Daten, insb. beispielsweise die Windrichtung lässt sich so nicht anzeigen. Daher bevorzugen wir die obere Lösung, auch wenn diese syntaktisch vermutlich schwieriger umzusetzen ist, da du im nun folgenden
Beispiel nur den Link ändern musst:
```
define Windsensor HTTPMOD https://measurements.mobile-alerts.eu/Home/MeasurementDetails?deviceid=0B1D2300E734&vendorid=244DD836-16DE-465E-B265-B3F1596A26D4&appbundle=de.synertronixx.remotemonitor 420
attr Windsensor userattr reading01Name reading01OExpr reading01Regex reading02Name reading02Regex
attr Windsensor enableCookies 1
attr Windsensor event-on-change-reading .*
attr Windsensor icon weather_wind_speed
attr Windsensor reading01Name windspeed
attr Windsensor reading01OExpr join ".", (split /,/, $val)
attr Windsensor reading01Regex ([^>]\d*[,]\d)
attr Windsensor reading02Name timestamp
attr Windsensor reading02Regex ([^>]\d+[.]\d+[.]\d+\s\d+[:]\d+[:]\d+)
attr Windsensor stateFormat Windgeschwindigkeit:  windspeed km/h <br> Messung: timestamp
attr Windsensor timeout 410
```
  
### Schritt 5 - Integration des Pushnachrichten-Dienstes in konrekte Anwendungszwecke (in eine Sturmwarnung)

Im Folgenden erstellen wir ein DOIF, welches immer dann, wenn eine neue Windgeschwindigkeit gemessen wird, nachprüft, ob diese größer als oder gleich der von dir definierten Untergrenze ist:
Ist dies der Fall, wird eine Warnungsmeldung mit selbst konfigurierbarem Text an alle Geräte verschickt, über die bekannte Syntax, die wir dir in Schritt 3 vorgestellt haben.
Dabei stellen der Warnungstext und die Untergrenze Variablen dar, die du selbst setzen musst. Unten liest du, wie du dies bspw. direkt in FTUI integrierst.
Natürlich kannst du sie über den klassischen Weg auch direkt in FHEM ändern/setzen. Prüf deine Implementierung, indem du die Wind-Untergrenze niedrig, z.B. bei 0 ansetzt. Sobald nun eine Wind-Bewegung gemessen wird (was quasi immer der Fall ist), erhältst du nun deine Benachrichtigung aufs Smartphone.

```
defmod sturmwarnung DOIF ([WindsensorAPI:wind_speed] >= [$SELF:P_wind_trigger]) (set pushAll message [$SELF:P_warnungstext] - Geschwindigkeit: [WindsensorAPI:wind_speed] km/h)
attr sturmwarnung readingList P_warnungstext P_wind_trigger
attr sturmwarnung room Sturmwarnung
attr sturmwarnung setList P_warnungstext P_wind_trigger
```

### Anmerkung an Nutzer von Android-Geräten mit mglw. aggressiven Energiesparoptionen (z.B. Xiaomi)
Wir empfehlen dir, zu prüfen, dass die Energiesparoptionen bzw. automatische Deaktivierung von Apps im Falle der Nichtbenutzung für die App von PushNotifier deaktiviert sind.

### Tipp - Integration der Einstellungen in FTUI

Für die Abgabe haben wir uns entschlossen, die Push-Benachrichtigungen über FTUI hinsichtlich der gewünschten Windgeschwindigkeit und des Nachrichteninhalts konfigurierbar zu gestalten.

```html
<li data-row="1" data-col="2" data-sizex="4" data-sizey="2" class="left-align">                <header>Sturmwarnung</header>
<div class="sheet">
              <div class="row">
              <div class="cell center-align">
              <div class="big" data-type="label" data-device="WindsensorAPI" data-get="STATE"></div>
              </div>
              </div>
</div>
</li>
<li data-row="2" data-col="2" data-sizex="2" data-sizey="2" class="left-align"> 
<div class="sheet">
                      <div class="row">
                      <div class="cell center-align">
                          <!-- Windgeschwindigkeit Push -->
                          <div class="inline cell" data-type="label">Push Notification Windgeschwindigkeit</div>
                          <div class="inline cell" data-type="input" data-device="sturmwarnung" data-get="P_wind_trigger" data-set="P_wind_trigger" class="w1x"></div>
                          <div class="inline big" data-type="label">km/h</div>
                      </div>  
                      </div>   
</div>
</li>
<li data-row="2" data-col="4" data-sizex="2" data-sizey="2" class="left-align"> 
<div class="sheet">  
          
            <div class="row">  
            <div class="cell center-align">     
						<div class="inline cell" data-type="label">Konfiguration der Sturmwarnung:</div>
						<div class="inline cell" data-type="input"
							data-device="sturmwarnung"
							data-get="P_warnungstext"
							data-set="P_warnungstext"></div>
           </div>
           </div>				
</div>
</li>
```

Eine Schwäche müssen wir unserer Lösung jedoch einräumen: Grundsätzlich wird am Ende des eigenen Benachrichtigungstextes die tatsächlich gemessene Windgeschwindigkeit angezeigt. Warum ist dem so? Eine dynamische Integration der tatsächlichen Windgeschwindigkeit durch das Input-Feld ist nicht möglich, da diese dann nur einmalig zur Laufzeit aus den Readings ausgelesen wird und danach immer statisch mitgegeben wird: Du würdest also zukünftig immer den bei der Erstellung deiner Wunschnachricht gemessenen Wert angezeigt bekommen. Vielleicht hast du aber Lust, dieses Problem selbst zu lösen - mit einem On-/Off-Toggle und einer weiteren Bedingung innerhalb deiner DOIF sollte diese Erweiterung für dich kein Problem darstellen!

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://wiki.fhem.de/wiki/PushNotifier (Letzter Zugriff: 01.02.21)


