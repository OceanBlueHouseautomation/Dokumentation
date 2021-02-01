# Wie du mit Fhem deinen Rollladen aufs nächste Level hebst 

## Einführung 

In diesem Tutorial wird ein Rollladen, welcher über den Shelly 2.5 gesteuert wird, eingerichtet und über die FTUI steuerbar gemacht. 

## Voraussetzungen 

Funktionierende FHEM-Installation 

Angeschlossener Shelly 

FTUI-Grundlagen 

### Schritt 1 – Modus des Shellys ändern 

Damit du einen Rollladen über den Shelly 2.5 steuern kannst, musst du zunächst den Modus über das Attribut „mode“ auf den Wert „roller“ ändern. Dies kannst du über fhem mit Folgendem Kommando in der Kommandozeile tun: 

  
```
<attr myShelly mode roller> 
```
  

Alternativ dazu kannst du auch das über die FTUI bedienbare Widget-Switch benutzen, welches im Kapitel FTUI-Grundlagen von uns als Beispiel auf der Startseite implementiert wurde. Hier kannst du den mode des Shelly zwischen roller und relay (dieser wird für die Bewässerung benötigt) wechseln. 

Außerdem nutzen wir die Gelegenheit und raten dir direkt das Attribut „maxtime“ zu setzen, welches definiert, wie lange der Rollladen benötigt um vom komplett geöffneten Zustand in den komplett geschlossenen Zustand zu schließen. Wir haben uns für 20s entschieden. Wir empfehlen dir dieselbe Zeit zu wählen, dadurch kannst du in den nächsten Schritten ohne Anpassungen weiterarbeiten. Kopiere dafür den Befehl in die Kommandozeile in fhem: 

  
```
<attr myShelly maxtime 20> 
```
### Schritt 2 - Erstellen des FTUI-Grundgerüsts 

Wenn du die FTUI-Grundlagen von uns verwendest kannst du Folgenden html-Code in die Datei rollladen.html unter /opt/fhem/www/tablet/ auf deinem pi einfügen. 

  
```html
<!DOCTYPE html> 

<html> 

<head></head> 

<body> 

<div class="gridster"> 

<ul> 

<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li> 

<!--  Grundfunktionen -->  

<!--  Wochentags- und Wochenendbetrieb -->  

<!--  Automatische Beschattung-->  

</li> 

</ul> 

</div> 

</body> 

</html> 
````
  

Alternativ dazu kannst die Folgenden Schritte natürlich auch in deine eigene FTUI implementieren. 

Schritt 3 - Erstellen der Grundfunktionen der Rollladensteuerung 

In diesem Kapitel lernst du die Grundfunktionen der Rollladensteuerung und kannst sie direkt in deiner FTUI implementieren. Dazu gehört das manuelle Öffnen, Schließen und Stoppen des Rollladens. Außerdem gibt es die Möglichkeit den Rollladen auf eine bestimmte Prozentzahl zu öffnen/schließen. Dies kannst du über den Fhem mit den Befehlen in der Kommandozeile erreichen: 

<set myShelly open> 

  

<set myShelly closed> 

  

<set myShelly stop> 

  

<set myShelly pct [Prozentzahl z.B. 30]> 

  

erreichen. Um diese Funktionen in die FTUI zu übertragen kannst du Folgenden Code in dein HTML-Dokument rollladen.hmtl unter das Kommentar <!--  Grundfunktionen --> einfügen: 

  

<li data-row="1" data-col="2" data-sizex="2" data-sizey="2" class="left-align">                 

    <header>Rollladen</header> 

        <div class="sheet"> 

          <div class="row"></div> 

          <div class="row"> 

            <div class="cell center-align"> 

            <!-- Status des Rollladens --> 

            <div class="inline big cell"  

            data-type="label"  

            data-device="myShelly"  

            data-get="STATE"></div> 

            <div class="inline big cell"  

            data-type="label"  

            data-device="RollladenStatusanzeige"></div> 

            </div> 

          </div> 

          <div class="row"> 

            <div class="cell center-align"> 

            <!-- Öffnen, Schließen und Stoppen des Rollladens --> 

            <div class="inline cell"  

            data-type="push"  

            data-device="myShelly"  

            data-valve="ValvePosition"  

            data-icon="fa-chevron-up"  

            data-set-on="open"  

            data-fhem-cmd="set myShelly open; set RollladenStatusanzeige offen"></div> 

            <div class="inline cell"  

            data-type="push"  

            data-device="myShelly"  

            data-valve="ValvePosition"  

            data-icon="fa-stop"  

            data-set-on="stop"></div> 

            <div class="inline cell"  

            data-type="push"  

            data-device="ShellyRollladen"  

            data-valve="ValvePosition"  

            data-icon="fa-chevron-down"  

            data-set-on="closed"  

            data-fhem-cmd="set myShelly closed; set RollladenStatusanzeige geschlossen"></div> 

            </div> 

          </div> 

          <div class="row"> 

            <div class="cell center-align"> 

            <!-- Prozentuales Öffnen und Schließen des Rollladens --> 

            <div class="inline cell"  

            data-type="label">Schließen zu</div> 

            <div class="inline cell w1x"  

            data-type="select" 

            data-device="RollladenProzentanzeige" 

            data-items='["0","10","20","30","40","50","60","70","80","90","100"]'> 

            </div> 

            <div class="inline cell"  

            data-type="label">%</div> 

            </div> 

          </div> 

          <div class="row"></div>    

        </div> 

</li>            

  

Wie dir bestimmt auffällt wird hier in unserem Beispiel das prozentualle Einstellen des Rollladens nicht über den geeigneten set-Befehl erreicht sondern über einen Umweg. Dies liegt daran, dass der Shelly bei uns mit Lämpchen und nicht mit einem Rollladen verbunden ist und deshalb nicht erkennt auf welchen Prozentstand geschalten werden soll. Falls du dieses Beispiel auch implementieren möchtest erklären wir dir im Folgenden wie du dies über Fhem erreichst „hinzumocken“. Außerdem haben wir in die FTUI eine Statusanzeige eingebaut. Auch diese wird im Folgenden im Backend angelegt.  

  

Prozentuales Schließen des Rollladens: 

Damit du den Rollladen auf eine bestimmte Prozentzahl schließen kannst, ohne dass du den Grundbefehl <set myShelly pct [Prozentzahl z.B. 30]> verwendest, musst du zunächst zwei Dummys und einen Button anlegen. Kopiere dafür Folgende Befehle in die Kommandozeile: 

  

<define RollladenProzentanzeige dummy> 

  

<attr RollladenProzentanzeige room Rollladen> 

  

<attr RollladenProzentanzeige setList 0 10 20 30 40 50 60 70 80 90 100> 

  

  

<define RolladenZeitZumOeffnen dummy> 

  

<attr RollladenZeitZumOeffnen room Rollladen> 

  

  

<define b_RollladenCheck dummy> 

  

<attr b_RollladenCheck room Rollladen> 

  

<attr b_RollladenCheck setList on off> 

  

<attr b_RollladenCheck useSetExtensions 1> 

  

Der dummy RollladenProzentanzeige enthält die Prozentzahl, auf welche der Rollladen geschlossen werden soll. Wenn du unser Beispiel für die FTUI kopiert hast, stellst du diese über ein Dropdown ein. In den dummy RolladenZeitZumOeffnen soll die Zeit in Sekunden gespeichert werden, die der Rollladen bräuchte, um auf die ausgewählt Prozentzahl zu schließen. Da wir 20 Sekunden als „maxtime“ definiert haben wird mit folgendem notify die Zeit prozentual zu den 20 Sekunden umgerechnet. Wenn du die selbe „maxtime“ definiert hast kannst du die Befehle genauso kopieren, ansonsten musst du die Funktion auf deine „maxtime“ anpassen und dann in die Kommandozeile in fhem kopieren: 

  

<define n_RollladenZeitZumOeffnen notify RollladenProzentanzeige {my $timer=ReadingsVal('RollladenProzentanzeige','state','0')*0.01*20 ;; fhem("set RollladenZeitZumOeffnen ".$timer."")} > 

  

<attr n_RollladenZeitZumOeffnen room Rollladen> 

  

Der notify wird ausgelöst sobald du einen neuen Wert für RollladenProzentanzeige setzt oder über die FTUI auswählst. Der Button b_RollladenCheck soll nun auf die errechnete RollladenZeitZumOeffnen aktiviert werden und danach wieder ausgeschaltet werden. Dafür kopierst du den notify n_RollladenProzentsteuerung in die Kommandozeile: 

  

<define n_Rollladenprozentsteuerung notify ZeitZumOeffnen {my $timer=ReadingsVal('ZeitZumOeffnen','state','0') ;;;; my $timerreset=ReadingsVal('ZeitZumOeffnen','state','0') ;;;; fhem("set b_rollladenCheck on-for-timer ".$timer."")\ 

;;;; fhem("define timeroff at +00:00:".$timerreset." set              \ 

b_rollladenCheck off")}> 

  

<attr n_Rollladenprozentsteuerung room Rollladen> 

  

Damit der Rollladen nun auch für die errechnete Zeit schließt und damit entsprechend auf die gewählte Prozentzahl, musst du die Folgende DOIF in die Kommandozeile  kopieren: 

  

<define doif_RollladenProzentsteuerung DOIF {if([b_rollladenCheck] eq "on"){fhem("set ShellyRollladen closed");;;; my $prozent=ReadingsVal('RollladenProzentanzeige','state','0') ;;;; if($prozent == 0){fhem("set RollladenStatusanzeige offen")} elsif($prozent == 10){fhem("set RollladenStatusanzeige 10% geschlossen")}elsif($prozent == 20){fhem("set RollladenStatusanzeige 20% geschlossen")}elsif($prozent == 30){fhem("set RollladenStatusanzeige 30% geschlossen")}elsif($prozent == 40){fhem("set RollladenStatusanzeige 40% geschlossen")}elsif($prozent == 50){fhem("set RollladenStatusanzeige 50% geschlossen")}elsif($prozent == 60){fhem("set RollladenStatusanzeige 60% geschlossen")}elsif($prozent == 70){fhem("set RollladenStatusanzeige 70% geschlossen")}elsif($prozent == 80){fhem("set RollladenStatusanzeige 80% geschlossen")}elsif($prozent == 90){fhem("set RollladenStatusanzeige 90% geschlossen")}elsif($prozent == 100){fhem("set RollladenStatusanzeige geschlossen")}} else{fhem("set ShellyRollladen stop")}}> 

  

<attr doif_RollladenProzentsteuerung room Rollladen> 

  

Diese DOIF schließt den Rollladen so lange wie der Button b_RollladenCheck auf on ist. Wenn der timer des Buttons abgelaufen ist wird dieser durch die notify n_Rollladenprozentsteuerung auf off gestellt, was ebenso bedeutet, dass der Rollladen stoppt. In die DOIF wurde ebenfalls direkt die Befüllung der Statusanzeige eingefügt. Diese hast du bereits in die FTUI eingebunden, wenn du unseren Beispiel Code zu den Grundfunktionen kopiert hast. Damit diese endgültig funktioniert muss noch ein dummy über die Kommandozeile angelegt werden, der den Inhalt des Labels beinhaltet: 

  

<define RollladenStatusanzeige dummy> 

  

<attr RollladenStatusanzeige room Rollladen> 

Glückwunsch! Nun hast du die Grundfunktionen des Rollladens sowie eine Statusanzeige erstellt und in die FTUI implementiert. 

Schritt 4 – Wochentag- und Wochenendbetrieb 

In diesem Schritt erfährst du wie du deinen Rollladen an Wochentagen und am Wochenende zu einer bestimmten Uhrzeit öffnen bzw. schließen kannst. Wir empfehlen dir die vorherigen Schritte durchgeführt zu haben.  

Zunächst musst du im fhem eine DOIF über die Kommandozeile anlegen: 

<define RollladenZeitsteuerung DOIF ([[$SELF:WochentagsHOCH]|Mo Di Mi Do Fr] or [[$SELF:SamstagHOCH]|Sa] or [[$SELF:SonntagHOCH]|So] ) (set ShellyRollladen open;; set RollladenStatusanzeige geöffnet) DOELSEIF ([[$SELF:WochentagsHOCH]|Mo Di Mi Do Fr] or [[$SELF:SamstagRUNTER]|Sa] or [[$SELF:SonntagRUNTER]|So]) (set ShellyRollladen closed;; set RollladenStatusanzeige geschlossen) > 

  

<attr RollladenZeitsteuerung do always> 

  

<attr RollladenZeitsteuerung readingList WochentagsHOCH WochentagsRUNTER SamstagHOCH SamstagRUNTER SonntagHOCH SonntagRUNTER> 

  

<attr RollladenZeitsteuerung room Rollladen> 

  

<attr RollladenZeitsteuerung selftrigger alattr RollladenZeitsteuerung setList WochentagsHOCH:time WochentagsRUNTER:time SamstagHOCH:time SamstagRUNTER:time SonntagHOCH:time SonntagRUNTER:time> 

  

Die DOIF RollladenZeitsteuerung enthält die Readings „WochentagsHOCH WochentagsRUNTER SamstagHOCH SamstagRUNTER SonntagHOCH SonntagRUNTER“ in welche die Uhrzeit gespeichert wird, zu welcher der Rollladen hoch oder runter gehen soll. Abhängig von der Uhrzeit und ob es unter der Woche oder am Wochenende ist wird der Rollladen dann geöffnet oder geschlossen.  

Damit die Uhrzeiten über die FTUI gesetzt werden können, kannst du unser Code Beispiel und das Kommentar <!--  Wochentags- und Wochenendbetrieb --> in die rollladen.html kopieren:  

  

<li data-row="1" data-col="3" data-sizex="2" data-sizey="2" class="left-align"> 

<header>Wochentagssteuerung</header> 

    <div class="sheet"> 

      <div class="row"> 

        <div class="cell center-align"> 

        <!-- Wochentags Öffnen des Rollladens --> 

        <div class="inline cell" data-type="label">Wochentags öffnen um:</div> 

        <div class="inline cell"> 

        <div data-type="input" class="w1x" data-device="RollladenZeitsteuerung" data-get="WochentagsHOCH" data-set="WochentagsHOCH" data-format="H:i"></div> 

        </div> 

        </div> 

      </div> 

                       

      <div class="row"> 

        <div class="cell center-align"> 

        <!-- Wochentags Schließen des Rollladens --> 

        <div class="inline cell" data-type="label">Wochentags schließen um:</div> 

        <div class="inline cell"> 

        <div data-type="input" class="w1x" data-device="RollladenZeitsteuerung" data-get="WochentagsRUNTER" data-set="WochentagsRUNTER" data-format="H:i"></div> 

        </div> 

        </div> 

      </div> 

  

      <div class="row"> 

        <div class="cell center-align"> 

        <!-- Samstag Öffnen des Rollladens --> 

        <div class="inline cell" data-type="label">Samstag öffnen um:</div> 

        <div class="inline cell"> 

        <div data-type="input" class="w1x" data-device="RollladenZeitsteuerung" data-get="SamstagHOCH" data-set="SamstagHOCH" data-format="H:i"></div> 

        </div> 

        </div> 

      </div>  

  

      <div class="row"> 

        <div class="cell center-align"> 

        <!-- Samstag Schließen des Rollladens --> 

        <div class="inline cell" data-type="label">Samstag schließen um:</div> 

        <div class="inline cell"> 

        <div data-type="input" class="w1x" data-device="RollladenZeitsteuerung" data-get="SamstagRUNTER" data-set="SamstagRUNTER" data-format="H:i"></div> 

        </div> 

        </div> 

      </div>  

  

      <div class="row"> 

        <div class="cell center-align"> 

        <!-- Sonntag Öffnen des Rollladens --> 

        <div class="inline cell" data-type="label">Sonntag öffnen um:</div> 

        <div class="inline cell"> 

        <div data-type="input" class="w1x" data-device="RollladenZeitsteuerung" data-get="SonntagHOCH" data-set="SonntagHOCH" data-format="H:i"></div> 

        </div> 

        </div> 

      </div> 

  

      <div class="row"> 

        <div class="cell center-align"> 

        <!-- Sonntag Schließen des Rollladens --> 

        <div class="inline cell" data-type="label">Sonntag schließen um:</div> 

        <div class="inline cell"> 

        <div data-type="input" class="w1x" data-device="RollladenZeitsteuerung" data-get="SonntagRUNTER" data-set="SonntagRUNTER" data-format="H:i"></div> 

        </div> 

        </div> 

      </div> 

  

    </div> 

</li> 

  

Und das wars auch schon, nun kannst du deinen Rollladen automatisch zu bestimmten Uhrzeiten unter der Woche und am Wochenende öffnen und schließen lassen. 

  

Schritt 5 – Automatisch Beschattung 

Du hast im Sommer auch immer das Gefühl, wenn du abends heimkommst eine gut temperierte Sauna zu betreten? Dann kannst du dir in diesem Schritt selbst zu einer Lösung verhelfen. Denn du kannst deinen Rollladen auch abhängig von der Außentemperatur auf einen bestimmten Prozentwert schließen und musst dir mit dieser automatischen Beschattung nie mehr über zu heiße Räume Gedanken machen. 

Der folgende Code integriert einen Temperatursensor von mobile-alerts.eu in dein System (eine genauere Anleitung zur Integration von Sensoren findest du beim Tutorial der Sturmwarnung):  

< define Temperatursensor HTTPMOD https://measurements.mobile-alerts.eu/Home/MeasurementDetails?deviceid=03589FAD85A0&vendorid=244DD836-16DE-465E-B265-B3F1596A26D4&appbundle=de.synertronixx.remotemonitor 420 > 

< attr Temperatursensor userattr reading01Name reading01OExpr reading01Regex reading02Name reading02Regex reading03Name reading03Regex > 

< attr Temperatursensor enableCookies 1 > 

< attr Temperatursensor event-on-update-reading .* > 

< attr Temperatursensor icon temp_outside > 

< attr Temperatursensor reading01Name temperature > 

< attr Temperatursensor reading01OExpr join ".", (split /,/, $val) > 

< attr Temperatursensor reading01Regex ([^>]\d*[,]\d) > 

< attr Temperatursensor reading02Name timestamp > 

< attr Temperatursensor reading02Regex ([^>]\d+[.]\d+[.]\d+\s\d+[:]\d+[:]\d+) > 

< attr Temperatursensor stateFormat Temperatur: temperature °C <br> Messung: timestamp > 

Ansonsten funktioniert die komplette Logik ähnlich wie die normale prozentuale Steuerung die du bereits in Schritt 3 implementiert hast. Allerdings wird zusätzlich zu den zwei Dummys die Zeit und Prozentzahl enthalten noch ein Dummy mit der Temperatur, bei welcher beschattet werden soll, erstellt. Außerdem brauchst du wieder einen Button mit der Timer-Funktion. Mit folgenden Befehlen, die du in die Kommandozeile kopieren kannst, implementierst du alles in dein Backend:  

<define AutoBeschattungRollProzent dummy> 

  

<attr AutoBeschattungRollProzent room Rollladen> 

  

  

<define AutoBeschattungZeitZumOeffnen dummy> 

  

<attr AutoBeschattungZeitZumOeffnen room Rollladen> 

  

  

<define AutoBeschattungTemp dummy> 

  

<attr AutoBeschattungTemp room Rollladen> 

  

  

<define b_AutoBeschattungRollladenCheck dummy> 

  

<attr b_AutoBeschattungRollladenCheck room Rollladen> 

  

<attr b_AutoBeschattungRollladenCheck setList on off> 

  

<attr b_AutoBeschattungRollladenCheck useSetExtensions 1> 

  

Wie in Schritt 2 bereits erklärt wird nun die Prozentzahl in eine Zeit in Sekunden umgerechnet, prozentual zur gesetzten „maxtime“. Kopiere hierfür die Folgenden Befehle in die Kommandozeile in fhem:  

  

<define n_AutoBeschattungZeitZumOeffnen notify AutoBeschattungRollProzent {my $timer=ReadingsVal('AutoBeschattungRollProzent','state','0')*0.01*20 ;; fhem("set AutoBeschattungZeitZumOeffnen ".$timer."")}> 

  

<attr n_AutoBeschattungZeitZumOeffnen room Rollladen> 

  

Da die Beschattung abhänigig von einer bestimmten Tempertatur aktiviert werden soll, wird durch folgenden notify der Button b_AutoBeschattungRollladenCheck erst auf on-for-timer gesetzt, wenn die Temperatur des Temperatursensors größer als die aus AutoBeschattungTemp ist. Kopiere den Code zu n_AutoBeschattungRollProzent in deine Kommandozeile:  

  

<define n_AutoBeschattungRollProzent notify AutoBeschattungZeitZumOeffnen|AutoBeschattungTemp {my $timer=ReadingsVal('AutoBeschattungZeitZumOeffnen','state','0') ;; my $timerreset=ReadingsVal('AutoBeschattungZeitZumOeffnen','state','0') ;; my $temp=ReadingsVal('Temperatursensor','temperature','0') ;; my $AutoBeschattungTemp =ReadingsVal('AutoBeschattungTemp','state','0') ;; if($temp > $AutoBeschattungTemp){fhem("set b_BeschattungNotify on");; fhem("define timeroff at +00:05 set b_AutoBeschattungRollladenCheck on-for-timer ".$timer."") 

;; fhem("define timeroff at +00:05:".$timerreset." set                 

b_AutoBeschattungRollladenCheck off");; fhem("set b_BeschattungNotify off")}}> 

  

<attr n_AutoBeschattungRollProzent room Rollladen> 

  

Wie in Schritt 2 musst du nun den Button mit einer DOIF mit dem Shelly verknüpft. Damit wird der Rollladen bewegt und der Status geändert. Wenn du die Folgenden Befehle in die Kommandozeile kopierst ist auch schon fast geschafft: 

  

<define doif_AutoBeschattungRollProzent DOIF {if([b_AutoBeschattungRollladenCheck] eq "on"){fhem("set ShellyRollladen closed");;;; my $prozent=ReadingsVal('AutoBeschattungRollProzent','state','0') ;;;; if($prozent == 0){fhem("set RollladenStatusanzeige offen")} elsif($prozent == 10){fhem("set RollladenStatusanzeige 10% geschlossen")}elsif($prozent == 20){fhem("set RollladenStatusanzeige 20% geschlossen")}elsif($prozent == 30){fhem("set RollladenStatusanzeige 30% geschlossen")}elsif($prozent == 40){fhem("set RollladenStatusanzeige 40% geschlossen")}elsif($prozent == 50){fhem("set RollladenStatusanzeige 50% geschlossen")}elsif($prozent == 60){fhem("set RollladenStatusanzeige 60% geschlossen")}elsif($prozent == 70){fhem("set RollladenStatusanzeige 70% geschlossen")}elsif($prozent == 80){fhem("set RollladenStatusanzeige 80% geschlossen")}elsif($prozent == 90){fhem("set RollladenStatusanzeige 90% geschlossen")}elsif($prozent == 100){fhem("set RollladenStatusanzeige geschlossen")}} else{fhem("set ShellyRollladen stop")> 

  

<attr doif_AutoBeschattungRollProzent room Rollladen> 

  

Sicherlich möchtest du auch über einen notify benachrichtigt werden wenn die automatische Beschattung beginnt. Hierfür musst du noch einen Button implementieren, der aktiviert wird sobald der Rollladen geschlossen wird. Die Aktivierung wird bereits über das Notify n_AutoBeschattungRollProzent 5 Minuten vor Beginn der Beschattung erreicht. Mit einer DOIF soll dann bei aktiviertem Button eine Textnachricht als notify versendet werden, die du bei kopieren unserer FTUI Vorlage auch über die FTUI ändern kannst. Den Button und die DOIF einfach in die Kommandozeile kopieren: 

  

<define b_BeschattungNotify dummy> 

  

<attr b_BeschattungNotify room Rollladen> 

  

<attr b_BeschattungNotify setList on off> 

  

  

<define doif_RollladenNotify DOIF ([b_BeschattungNotify] eq "on")(set pushAll message [$SELF:P_ROLLwarnungstext])> 

  

<attr doif_RollladenNotify readingList P_ROLLwarnungstext> 

  

<attr doif_RollladenNotify room Rollladen> 

  

<attr doif_RollladenNotify setList P_ROLLwarnungstext> 

  

Super, im Backend hast du nun alles erledigt. Um die automatische Beschattung auch in deiner FTUI zu implementieren kopiere einfach folgenden Code unter das Kommentar <!--  Automatische Beschattung--> im html-Dokument rollladen.html: 

  

<li data-row="2" data-col="2" data-sizex="4" data-sizey="2" class="left-align"> 

<header>Automatische Beschattung</header> 

  

                    <div class="sheet"> 

                      <div class="row"> 

                          <div class="cell center-align"> 

                          <!-- Automatische Beschattung --> 

                          <div class="inline cell" data-type="label">Rollladen zu</div> 

                          <div class="inline w1x" data-type="select" 

                          data-device="AutoBeschattungRollProzent" 

                          data-items='["0","10","20","30","40","50","60","70","80","90","100"]'> 

                          </div> 

                          <div class="inline cell" data-type="label">% schließen, wenn Temperatur größer als</div> 

                          <div class="inline large darker thin cell" 

                          data-type="thermostat" 

                          data-device="AutoBeschattungTemp" 

                          data-get="state" 

                          data-set="" 

                          data-valve="ValvePosition" 

                          data-min="-10" 

                          data-off="off"  

                          data-max="40" 

                          data-boost="40"> 

                          </div> 

                          <div class="inline cell" data-type="label">ist.</div> 

                        </div> 

                        </div> 

                      <div class="row"> 

                        <div class="cell center-align"> 

                          <div class="inline cell" data-type="label">Notifytext der Beschattung:</div> 

              						<div class="cell" data-type="input" 

                                                                                                                       data-device="doif_RollladenNotify" 

                                                                                                                       data-get="P_ROLLwarnungstext" 

                                                                                                                       data-set="P_ROLLwarnungstext"></div> 

                        </div> 

                      </div> 

                    </div>    

  

                   

</li> 

  

Herzlichen Glückwunsch, du hast es geschafft! Durch die automatische Beschattung musst du nun nie wieder schwitzen, wenn du im Sommer gestresst von der Arbeit Heim kommst.  

  

  

Quellen dieses Tutorials 

Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen: https://wiki.fhem.de/wiki/Modul_Shelly (Letzter Zugriff: 01.02.21) https://forum.fhem.de/index.php?topic=107293.0 (Letzter Zugriff: 01.02.21) 

 

 
