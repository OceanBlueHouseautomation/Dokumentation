# Wie du mit Hilfe von FHEM deinen grünen Daumen schonst 

## Einführung  

In diesem Tutorial wirst du die Bewässerung deines Gartens manuell, teilautomatisiert und automatisiert mittels FHEM über die FTUI bedienen können. Du kannst über die FTUI die Bewässerung sofort, zu einen bestimmten Zeitpunkt und für eine bestimmte Dauer starten. 

## Voraussetzungen 

Bestehende FTUI 

Temperatursensor, Windsensor & Regensensor 

Die Bewässerung besteht aus zwei verschiedenen Pumpen: Der Pumpe im Hausgarten, im Folgenden als „Hausgartenpumpe“ bezeichnet, und der Pumpe im Schrebergarten, im Folgenden als „Schrebergartenpumpe“ bezeichnet. 

Da der Aufbau der Pumpen identisch ist, wird im folgenden Verlauf der Dokumentation nur der Aufbau der Hausgartenpumpe genauer beschrieben. Für die Schrebergartenpumpe liegen lediglich die Befehle bei, die analog zur Hausgartenpumpe implementiert werden. 

Damit du deine Bewässeung über den Shelly 2.5 steuern kannst, musst du zunächst den Modus über das Attribut „mode“ auf den Wert „relay“ ändern. Dies kannst du über fhem mit Folgendem Kommando in der Kommandozeile tun: 

  
````
<attr myShelly mode relay> 
````
  

Alternativ dazu kannst du auch das über die FTUI bedienbare Widget-Switch benutzen, welches im Kapitel FTUI-Grundlagen von uns als Beispiel auf der Startseite implementiert wurde. Hier kannst du den mode des Shelly zwischen roller und relay (dieser wird für die Bewässerung benötigt) wechseln. 

 

## FTUI Anlegen   

Zunächst müssen in das HTML-Dokument /opt/fhem/www/tablet/Hausgartenpumpe.html (evtl Pfad) folgende Codezeilen eingefügt werden. 
````html
<!DOCTYPE html> 
<html> 
<head></head> 
<body> 
<div class="gridster"> 
<ul> 
<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li> 
<!--  Teilautomatisierter Betrieb --> 
<!--  Automatisierter Betrieb Menu --> 
<!--  Automatisierter Betrieb Regensensor--> 
<!--  Automatisierter Betrieb Temperatursensor--> 
<!--  Automatisierter Betrieb Windsensor--> 
<!--  Zisterne --> 
</ul> 
</div> 
</body> 
</html> 
````
   

Die Pumpe kann teilautomatisiert und automatisiert betrieben werden. Wir fangen mit dem teilautomatisierten Betrieb an. 

  

## Teilautomatisierter Betrieb 

### Schritt 1 – Anlegen der Devices FHEM 

Der teilautomatisierte Betrieb bietet die Möglichkeit die Bewässerung manuell ein- und auszuschalten, die Dauer der Bewässerung festzulegen und diese im Anschluss automatisiert abzuschalten. Außerdem gibt es die Möglichkeit die Bewässerung einmalig manuell zu einer definierbaren Uhrzeit einzuschalten und die Dauer per Timer zu steuern. 

Füge in der Kommandozeile im FHEM folgenden Befehl ein: 

  
````
<define b_gartenpumpe_t_haus dummy>  
````
  

Die Namensgebung unterliegt folgender Konvention: t_ bedeutet teilautomatisierter Betrieb, a_ automatischer Betrieb, _haus gibt an, dass es sich um einen Bestandteil der Hausgartenpumpe handelt während _schreber Bestandteile der Schrebergartenpumpe kennzeichnet. Um die Einrichtung zu erleichtern wird empfohlen die Konvention so wie in der Dokumentation zu übernehmen, wenn du eigene Namen verwenden möchstest wird dazu geraten eine eigene Konvention zu entwerfen. 

  

Um den Dummy zu konfigurieren fügst du in die Kommandozeile folgende Befehle ein: 

  
````
<attr b_gartenpumpe_t_haus setList on off> 

<attr b_gartenpumpe_t_haus useSetExtensions 1> 
````
  

Es wurde ein Button angelegt und durch das Attribut „setList“ mit dem Wert „on off“ kann dieser die Zustände on und off annehmen. Durch das Attribut „useSetExtensions“ mit dem Wert „1“ werden dem Button die Extrafunktionen hinzugefügt, die unter anderem für den Timerbetrieb notwendig sind („on-for-timer“ ermöglicht den Button für einen bestimmen Zeitraum einzuschalten und danach automatisiert auszuschalten).  

  

Für die Timerfunktion wird noch ein Dummy und ein Button benötigt die mit folgenden Befehlen implementiert werden:  

  
````
<define d_timer_t_haus dummy> 

<define b_timer_t_haus dummy> 

<attr b_timer_t_haus setList on off> 
````
  

Im Dummy d_timer_t_haus wird die Dauer für den Timerbetrieb in Minuten gespeichert, mit dem Button b_timer_t_haus wird der Timerbetrieb aktiviert. 

  

Um die Bewässerung einmalig manuell zu einer definierbaren Uhrzeit für eine bestimmte Dauer einzuschalten, müssen folgende Befehle in die Kommandozeile eingegeben werden: 

  
````
<define d_uhrzeit_t_haus dummy> 

<define d_uhrzeittimer_t_haus dummy> 

<define b_uhrzeit_t_haus dummy> 

<attr b_uhrzeit_t_haus setList on off> 
````
  

Im Dummy d_uhrzeit_t_haus wird die Uhrzeit im Format %H:%M (Stunden:Minuten) gespeichert, der Dummy d_uhrzeittimer_t_haus enthält die Dauer der Bewässerung in Minuten und der Dummy aktiviert die Bewässerung zu einer definierbaren Uhrzeit für eine bestimmte Dauer. 

 

### Schritt 2 - Einfügen der FTUI  

Um die im FHEM angelegten Devices zum teilautomatisierten Betrieb der Pumpe über die FTUI steuern zu können, müssen folgende Codezeilen in das Dokument Hausgartenpumpe.html unter den Kommentar <!--  Teilautomatisierter Betrieb --> eingefügt werden. 
````html
<li data-row="2" data-col="2" data-sizex="3" data-sizey="2" class="left-align">                 

    <header>Hausgarten</header> 

              <div class="sheet"> 

              <div class="cell"> 

                  <div class="big"  

                  data-type="label">Hausgartenpumpe</div> 

    	   <div data-type="switch"  

                  data-device=" b_gartenpumpe_t_haus "  

                  data-set-on="state"  

                  data-states='["on","off"]'  

                  data-colors='["green","red"]'  

                  data-background-colors='["#2A2A2A","#2A2A2A"]'  

                  data-set-states='["off","on"]'  

                  data-icons='["fa-toggle-on","fa-toggle-off"]'  

                  data-background-icons='["fa-blank","fa-blank"]'  

                  data-on-background-color="grey"  

                  data-off-background-color="grey"  

                  class="cell"></div> 

                   

                  <div class="inline large darker thin cell"> 

                  Hausgartenbew&auml;sserung an f&uuml;r: 

                  </div> 

                  <div 

                  data-type="datetimepicker" 

                  data-device=" d_timer_t_haus " 

                  data-datepicker="false" 

                  class="inline bigger thin orange cell" 

                  data-max-time="01:00:00" 

                  data-step="1" 

                  data-format="i" 

                  ></div> 

                  <div class="inline large darker thin cell">Minuten</div> 

                  <div 

                  data-type="checkbox" 

                  data-device=" b_timer_t_haus" 

                  data-set-on="on" 

                  data-set-off="off" 

                  class="inline left-space-3x" 

                  ></div> 

                   

                  <div class="cell"> 

                  <div class="inline large darker thin cell"> 

                  Hausgartenbew&auml;sserung an um: 

                  </div> 

                  <div 

                  data-type="datetimepicker" 

                  data-device=" d_uhrzeit_t_haus " 

                  data-datepicker="false" 

                  class="inline bigger thin orange cell" 

                  data-step="1" 

                  data-format="H:i" 

                  ></div> 

                  <div class="inline large darker thin cell">f&uuml;r</div> 

                  <div 

                  data-type="datetimepicker" 

                  data-device=" d_uhrzeittimer_t_haus " 

                  data-datepicker="false" 

                  class="inline bigger thin orange cell" 

                  data-max-time="01:00:00" 

                  data-step="1" 

                  data-format="i" 

                  ></div> 

                  <div class="inline large darker thin cell">Minuten</div> 

                  <div 

                  data-type="checkbox" 

                  data-device=" b_uhrzeit_t_haus " 

                  data-set-on="on" 

                  data-set-off="off" 

                  class="inline left-space-3x" 

                  ></div> 

              </div> 

              </div> 

    </li> 
````
 

Nach abspeichern des Dokuments ist über die FTUI ein Button zum Ein- und Ausschalten der Bewässerung des Hausgartens bedienbar. Außerdem kann die Bewässerung des Hausgartens für die Dauer eines Timers direkt oder zu einer bestimmten Uhrzeit eingeschaltet werden. 
 

Tipp: Beim Widget “datetimepicker” kann der Abstand der Minutenanzeige über das Attribut “data-step=” eingestellt werden. Bei data-step=”10” ist der Abstand der Minutenanzeige 10 Minuten.   

 

## Automatisierter Betrieb  

Der automatisierte Betrieb bietet die Möglichkeit die Bewässerung automatisch zu einer vorab definierten Uhrzeit, an festgelegten Tagen, starten zu lassen. Abhängig von der Erfüllung der definierten Werte für Regen, Temperatur und Wind startet der automatische Betrieb zur angegebenen Zeit. Der Aufbau des automatischen Betriebs kann in 4 Bereiche aufgeteilt werden: Menu, Regensensor, Temperatursensor und Windsensor.  Sind die Bedingungen Regensensor, Temperatursensor und Windsensor erfüllt, wird die Bewässerung zum angegebenen Zeitpunkt (angegeben im Bereich Menu) gestartet.  

 

## Automatisierter Betrieb - Menu 

### Schritt 1 – Anlegen der Devices in FHEM 

Der Punkt Menu umfasst einen Ein- und Ausschalter für den Automatikbetrieb, die Auswahl der Wochentage, die Dauer und Uhrzeit für den Start der Bewässerung. 

Setzte in der Kommandozeile folgenden Befehl ab: 

 
````
<define b_gartenpumpe_a_haus dummy> 

<attr b_gartenpumpe_a_haus setList on off> 

<attr b_gartenpumpe_a_haus useSetExtensions 1> 

<define d_uhrzeit_a_haus dummy> 

<define d_uhrzeittimer_a_haus dummy> 
````
 

Der Button b_gartenpumpe_a_haus aktiviert den automatische Betrieb.   

Im Dummy d_uhrzeittimer_a_haus wird die Dauer für den Timerbetrieb in Minuten gespeichert Im Dummy define d_uhrzeit_a_haus wird die Uhrzeit für den Start der Bewässerung gespeichert. 

  

Für die Auswahl der Tage musst du folgende Befehle in der Kommandozeile eingeben: 

 
````
<define d_pumpetageswahl_a_haus dummy> 

<attr d_pumpetageswahl_a_haus readingList Montag Dienstag Mittwoch Donnerstag Freitag Samstag Sonntag> 

< attr d_pumpetageswahl_a_haus setList Montag:on,off Dienstag:on,off Mittwoch:on,off Donnerstag:on,off Freitag:on,off Samstag:on,off Sonntag:on,off> 
````
 

Im Dummy d_pumpetageswahl_a_haus wird gespeichert an welchen Tagen die Bewässerung automatisch durchgeführt werden soll. Dazu werden mit “readingList” die Wochentage dem Dummy hinzugefügt. Mit dem Kommando “setList”  werden die möglichen set Kommandos -”on off” - für die Readings spezifiziert. 

 

### Schritt 2 - Einfügen in die FTUI 

Um die im FHEM angelegten Devices über die FTUI steuern zu können, müssen die folgenden Codezeilen und den Kommentar <!--  Automatisierter Betrieb Menu --> eingefügt werden.  

 
````html
<li data-row="3" data-col="2" data-sizex="4" data-sizey="2" class="left-align">                   

<header>Automatikbetrieb</header> 

  

     <div class="sheet"> 

       <div class="row"> 

         <div class="cell-20 center-align"> 

           <div class="large darker thin cell"> 

           Automatikbetrieb der Pumpe: 

           </div> 

           <div 

           data-type="checkbox" 

           data-device=" b_gartenpumpe_a_haus" 

           data-set-on="on" 

           data-set-off="off" 

           class="inline cell" 

           ></div> 

           <div class="cell"> 

           <div class="inline large darker thin cell">um</div> 

           <div 

           data-type="datetimepicker" 

           data-device=" d_uhrzeit_a_haus" 

           data-datepicker="false" 

           class="inline bigger thin orange cell" 

           data-step="1" 

           data-format="H:i" 

           ></div> 

           <div class="inline large darker thin cell">Uhr</div> 

           </div> 

           <div class="cell"> 

           <div class=" inline large darker thin cell">f&uuml;r</div> 

           <div 

           data-type="datetimepicker" 

           data-device=" d_uhrzeittimer_a_haus " 

           data-datepicker="false" 

           class="inline bigger thin orange cell" 

           data-max-time="01:00:00" 

           data-step="1" 

           data-format="i" 

           ></div> 

           <div class="inline large darker thin cell">Minuten</div> 

           </div> 

            

         </div> 

         <div class="cell-20 center-align"> 

         <div class="large darker thin">Aktivieren an den Tagen:</div> 

          <div class="darker thin" 

           data-type="switch"  

           data-device=" d_pumpetageswahl_a_haus "  

           data-icon="" 

           data-background-icon=""  

           data-set="Montag"  

           data-states='["on","off"]' 

           > 

             <div data-type="label"  

             data-device=" d_pumpetageswahl_a_haus " 

             data-get="Montag"  

             data-states='["on","off"]' 

             data-substitution='["on","Montag","off","Montag"]' 

             data-colors='["#aa6900","#505050"]'> 

             </div> 

         </div> 

        

         <div class="darker thin" 

         data-type="switch"  

         data-device=" d_pumpetageswahl_a_haus"  

         data-icon="" 

         data-background-icon=""  

         data-set="Dienstag"  

         data-states='["on","off"]' 

         > 

           <div data-type="label"  

           data-device=" d_pumpetageswahl_a_haus" 

           data-get="Dienstag"  

           data-states='["on","off"]' 

           data-substitution='["on","Dienstag","off","Dienstag"]' 

           data-colors='["#aa6900","#505050"]'> 

           </div> 

         </div> 

        

         <div class="darker thin" 

         data-type="switch"  

         data-device=" d_pumpetageswahl_a_haus"  

         data-icon="" 

         data-background-icon=""  

         data-set="Mittwoch"  

         data-states='["on","off"]' 

         > 

           <div data-type="label"  

           data-device=" d_pumpetageswahl_a_haus" 

           data-get="Mittwoch"  

           data-states='["on","off"]' 

           data-substitution='["on","Mittwoch","off","Mittwoch"]' 

           data-colors='["#aa6900","#505050"]'> 

           </div> 

         </div> 

        

         <div class="darker thin" 

         data-type="switch"  

         data-device=" d_pumpetageswahl_a_haus"  

         data-icon="" 

         data-background-icon=""  

         data-set="Donnerstag"  

         data-states='["on","off"]' 

         > 

           <div data-type="label"  

           data-device=" d_pumpetageswahl_a_haus" 

           data-get="Donnerstag"  

           data-states='["on","off"]' 

           data-substitution='["on","Donnerstag","off","Donnerstag"]' 

           data-colors='["#aa6900","#505050"]'> 

           </div> 

         </div> 

        

         <div class="darker thin" 

         data-type="switch"  

         data-device=" d_pumpetageswahl_a_haus"  

         data-icon="" 

         data-background-icon=""  

         data-set="Freitag"  

         data-states='["on","off"]' 

         > 

           <div data-type="label"  

           data-device=" d_pumpetageswahl_a_haus" 

           data-get="Freitag"  

           data-states='["on","off"]' 

           data-substitution='["on","Freitag","off","Freitag"]' 

           data-colors='["#aa6900","#505050"]'> 

           </div> 

         </div> 

        

         <div class="darker thin" 

         data-type="switch"  

         data-device=" d_pumpetageswahl_a_haus"  

         data-icon="" 

         data-background-icon=""  

         data-set="Samstag"  

         data-states='["on","off"]' 

         > 

           <div data-type="label"  

           data-device=" d_pumpetageswahl_a_haus" 

           data-get="Samstag"  

           data-states='["on","off"]' 

           data-substitution='["on","Samstag","off","Samstag"]' 

           data-colors='["#aa6900","#505050"]'> 

           </div> 

         </div> 

        

         <div class="darker thin" 

         data-type="switch"  

         data-device=" d_pumpetageswahl_a_haus"  

         data-icon="" 

         data-background-icon=""  

         data-set="Sonntag"  

         data-states='["on","off"]' 

         > 

           <div data-type="label"  

           data-device=" d_pumpetageswahl_a_haus " 

           data-get="Sonntag"  

           data-states='["on","off"]' 

           data-substitution='["on","Sonntag","off","Sonntag"]' 

           data-colors='["#aa6900","#505050"]'> 

           </div> 

         </div> 

       </div> 
````
Nach abspeichern des Dokuments ist über die FTUI ein Button zum Ein- und Ausschalten des automatischen Betriebs der Bewässerung des Hausgartens bedienbar. Außerdem ist die Dauer, die Uhrzeit und die Wochentage der Bewässerung wählbar. 

 

## Automatisierter Betrieb - Regensensor 

Im Bereich Regensensor wird definiert ab welchen Regenmengen, zu einem frei festlegbaren Zeitraum, die automatische Bewässerung nicht durchgeführt wird. 

### Schritt 1 – Regensensor anlegen 

Der folgende Code integriert einen Regensensor von mobile-alerts.eu in dein System: 
````
<define Regensensor HTTPMOD https://measurements.mobile-alerts.eu/Home/MeasurementDetails?deviceid=0824D9A1B72A&vendorid=244DD836-16DE-465E-B265-B3F1596A26D4&appbundle=de.synertronixx.remotemonitor 420> 

<attr Regensensor userattr reading01Name reading01OExpr reading01Regex reading02Name reading02Regex> 

<attr Regensensor enableCookies 1> 

<attr Regensensor event-on-change-reading .*> 

<attr Regensensor icon weather_rain_light> 

<attr Regensensor reading01Name amount> 

<attr Regensensor reading01OExpr join ".", (split /,/, $val)> 

<attr Regensensor reading01Regex ([^>]\d*[,]\d)> 

<attr Regensensor reading02Name timestamp> 

<attr Regensensor reading02Regex ([^>]\d+[.]\d+[.]\d+\s\d+[:]\d+[:]\d+)> 

<attr Regensensor stateFormat Regenmenge: amount mm <br> Messung: timestamp> 

<attr Regensensor timeout 410> 
````
Das Gerät wird nun alle 420 Sekunden die aktuellen Regendaten abfragen. Warum nur alle 420 Sekunden? Da der Windsensor ohnehin nicht häufiger Daten aufliefert, wäre ein niedrigeres Prüfintervall nicht sinnvoll bzw. würde nur unnötige Abfragen generieren. 

 

### Schritt 2 – Erstellen eines Filelogs 

Um die Regenmenge eines definierten Zeitraumes zu ermitteln, müssen die Regenmengen gespeichert und für FHEM auslesbar sein. Dafür muss eine Datei angelegt werden, in welche diese Werte gelogged werden: 
````
<define LogRegensensor FileLog ./log/regensensor.log  Regensensor:(amount).*> 
````
 

Standardmäßig wird neben dem Datum, dem Reading (amount) und Readingwert, das Device eingetragen. Durch den Befehl outputFormat kannst du den Devicenamen rausnehmen:  
````
<attr LogRegensensor outputFormat "$TIMESTAMP $EVENT\n"> 
````
 

### Schritt 3 – Einfügen der Differenzfunktion 

Um die Regenmenge einer Perioade ausrechnen zu können, muss die Differenz zwischen dem ersten und letzten Wert der Periode berechnet werden. Dafür musst du in der Datei 99_myUtils eine Subroutine übernehmen. Hierfür gehst du auf “Edit files” und dann auf “99_myUtils.pm” und fügst folgende Funktion ein: 
````
<########################################################## 

# myDifference 

# berechnet die Differenz aus LogFiles über einen beliebigen Zeitraum 

sub 

myDifference($$$) 

{ 

  my ($offset,$logfile,$cspec) = @_; 

  my $period_s = strftime "%Y-%m-%d\x5f%H:%M:%S", localtime(time-$offset); 

  my $period_e = strftime "%Y-%m-%d\x5f%H:%M:%S", localtime; 

  my $period_n = strftime "%Y-%m-%d\x5f%H:%M:%S", localtime(time-2592000); 

  my $oll = $attr{global}{verbose}; 

  $attr{global}{verbose} = 0;  

  my @logdata = split("\n", fhem("get $logfile - - $period_s $period_e $cspec")); 

  $attr{global}{verbose} = $oll;  

  my $cnt=0; 

  my $diff=0; 

  my $diff1=0;  

  my $diff2=0; 

  foreach (@logdata){ 

   my @line = split(" ", $_); 

   if(defined $line[1] && "$line[1]" ne ""){ 

    $cnt += 1; 

   } 

  } 

   

  if("$cnt" == 0){$diff=sprintf("%0.1f", $diff)} 

  elsif("$cnt" == 1){my @logdata2 = split("\n", fhem("get $logfile - - $period_n $period_e $cspec")); 

  my @diff1 = split(" ", $logdata2[-2]); 

  my @diff2 = split(" ", $logdata2[-3]); 

  $diff=sprintf("%0.1f", $diff1[1]-$diff2[1])} 

  elsif("$cnt" > 1){ 

  my @diff1 = split(" ", $logdata[-2]); 

  my @diff2 = split(" ", $logdata[0]); 

  $diff=sprintf("%0.1f", $diff1[1]-$diff2[1])}; 

  Log 4, ("myAverage: File: $logfile, Field: $cspec, Period: $period_s bis $period_e, Count: $cnt, Difference: $diff"); 

  return $diff; 

} 

##########################################################> 
````
 

Beachten, dass die Funktion zwischen  

“sub 

myUtils_Initialize($$) 

{my ($hash) = @_;}” 

und “1;” eingefügt werden muss.  

Anschließend muss die Datei mit klicken auf “Save 99_myUtils.pm” abgespeichert werden. 

 

### Schritt 4 – Anlegen der Devices in FHEM  

Setzte in der Kommandozeile folgenden Befehl ab: 
````
<define d_rainperiod1_a_haus dummy> 

<define d_rainperiod2_a_haus dummy> 

<define d_rainpastperiod1_a_haus dummy> 

<define d_rainpastperiod2_a_haus dummy> 

<define b_RegenBedingung_haus dummy> 

<attr b_RegenBedingung_haus setList on off> 

<attr b_RegenBedingung_haus useSetExtensions 1> 
````
 

Die Dummys d_rainperiod1_a_haus und d_rainperiod2_a_haus speichern den angegeben Zeitraum.  In den Dummys d_rainpastperiod1_a_haus und d_rainpastperiod2_a_haus wird die Grenze für die Regenmenge in mm/qm gespeichert. Der Button wird je nach Erfüllung oder Nichterfüllung der Bedingungen aktiviert oder nicht.  

 

Um zu prüfen, ob die angegebene Grenze überschritten wurde, muss vorab die die Regenmenge in der angegeben Periode ermittelt werden. Dies geschieht in einem Notify der bei einer Veränderung der Readings die Regenmenge neu ermittelt:  

 
````
<define n_diffrain1_a_haus notify Regensensor:amount.*| d_rainperiod1_a_haus {my $interval=ReadingsVal(' d_rainperiod1_a_haus ','state','0')*3600;;;; my $diffRain=myDifference("".$interval."", "LogRegensensor", "3::");;;; fhem("set d_diffRain1_a_haus ".$diffRain."")}> 
````
 

Ebenso muss die Regenmenge für die zweite Periode ermittelt werden:  

 
````
<define n_diffrain2_a_haus notify Regensensor:amount.*| d_rainperiod2_a_haus {my $interval=ReadingsVal(' d_rainperiod2_a_haus ','state','0')*3600;;;; my $diffRain=myDifference("".$interval."", "LogRegensensor", "3::");;;; fhem("set d_diffRain2_a_haus ".$diffRain."")}> 
````
 

Anmerkung: Solltest du im vorherigen Schritt den Devicenamen nicht aus dem Eintrag entfernt haben, musst du die “3::” im Notify mit “4::” ersetzen.  

 

Die Regenmengen für beide Perioden werden in zwei Dummys gespeichert, die noch angelegt werden müssen:  
````
<define d_diffRain1_a_haus dummy> 

<define d_diffRain2_a_haus dummy> 
````
 

Ist die angegeben Regenmenge kleiner als die tatsächliche Regenmenge der angegebenen Periode, so wird ein Button auf on gesetzt. Andernfalls auf off. Dafür wird in einem Notify geprüft, ob die Bedingungen erfüllt sind, um den Button auf den entsprechenden Zustand zu stellen:  

<define n_pumprain_haus notify d_rainpastperiod1_a_haus|d_rainpastperiod2_a_haus| d_diffRain1_a_haus|d_diffRain2_a_haus {my $rain1_Haus=ReadingsVal('d_rainpastperiod1_a_haus','state','0');; my $rain2_Haus=ReadingsVal('d_rainpastperiod2_a_haus','state','0');;my $avgrain1_Haus=ReadingsVal('d_diffRain1_a_haus','state','0');; my $avgrain2_Haus=ReadingsVal('d_diffRain2_a_haus','state','0');; if($avgrain1_Haus < $rain1_Haus and $avgrain2_Haus < $rain2_Haus){fhem("set b_RegenBedingung_haus on")} else {fhem("set b_RegenBedingung_haus off")}}> 

 

### Schritt 5 - Einfügen in die FTUI 

Für die Steuerung über die FTUI, musst du die folgenden Codezeilen unter den Kommentar <!--  Automatisierter Betrieb Regensensor--> einfügen: 
````html
       <div class="cell-20 center-align"> 

         <div class="row"> 

           <div class="cell center-align"> 

             <div class="large darker thin cell"> 

             Aktivieren wenn Regenmenge der 

             </div> 

             <div class="inline large darker thin cell"> 

             letzten 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device=" d_rainperiod1_a_haus "  

             data-get="state" > 

             </div> 

             <div class="large darker thin cell"> 

             Stunden in Summe kleiner als 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device="d_rainpastperiod1_a_haus"  

             data-get="state" > 

             </div> 

             <div class="inline large darker thin cell"> 

             mm/qm ist 

             </div> 

           </div> 

         </div> 

          

         <div class="row"> 

           <div class="cell center-align"> 

             <div class="large darker thin cell"> 

             Aktivieren wenn Regenmenge der 

             </div> 

             <div class="inline large darker thin cell"> 

             letzten 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device="d_rainperiod2_a_haus"  

             data-get="state" > 

             </div> 

             <div class="large darker thin cell"> 

             Stunden in Summe kleiner als 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device="d_rainpastperiod2_a_haus"  

             data-get="state" > 

             </div> 

             <div class="inline large darker thin cell"> 

             mm/qm ist 

             </div> 

           </div> 

         </div>  

       </div> 
````
 

## Automatisierter Betrieb – Temperatursensor 

In diesem Abschnitt wird die Temperatur-Überprüfung für die Bewässerung eingerichtet. Die Bedingung ist erfüllt, wenn die Durchschnittstemperatur einer angegebenen Periode die angegebene Temperatur überschreitet. Dabei kannst du die Grenzen für zwei verschiedene Zeiträume definieren.  

### Schritt 1 – Temperatursensor einrichten 

Falls du beim Rollladen den Temperatursensor implementiert hast kannst du diesen Schritt überspringen. 

Der folgende Code integriert einen Temperatursensor von mobile-alerts.eu in dein System (eine genauere Anleitung zur Integration von Sensoren findest du beim Tutorial der Sturmwarnung):  
````
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
````
 

### Schritt 2 – Erstellen eines FileLogs 

Auch für den Temperatursensor müssen die Temperaturwerte gespeichert. Daher musst du analog zum Regensensorlog eine Datei anlegen:  
````
< defmod LogTemperatursensor FileLog ./log/temperatursensor.log  Temperatursensor:(temperature).*> 

<attr LogTemperatursensor outputFormat "$TIMESTAMP $EVENT\n"> 
````
 

### Schritt 3 - Einfügen der Durchschnittsfunktion  

Analog zur Differenzfunktion, musst du die Durchschnittsfunktion in der 99_myUtils.pm einfügen. Hierfür gehst du auf “Edit files” und dann auf “99_myUtils.pm” und fügen folgende Funktion ein: 
````
<########################################################## 

# myAverage 

# berechnet den Mittelwert aus LogFiles über einen beliebigen Zeitraum 

sub 

myAverage($$$) 

{ 

  my ($offset,$logfile,$cspec) = @_; 

  my $period_s = strftime "%Y-%m-%d\x5f%H:%M:%S", localtime(time-$offset); 

  my $period_e = strftime "%Y-%m-%d\x5f%H:%M:%S", localtime; 

  my $oll = $attr{global}{verbose}; 

  $attr{global}{verbose} = 0;  

  my @logdata = split("\n", fhem("get $logfile - - $period_s $period_e $cspec")); 

  $attr{global}{verbose} = $oll;  

  my ($cnt, $cum, $avg) = (0)x3; 

  foreach (@logdata){ 

   my @line = split(" ", $_); 

   if(defined $line[1] && "$line[1]" ne ""){ 

    $cnt += 1; 

    $cum += $line[1]; 

   } 

  } 

  if("$cnt" > 0){$avg = sprintf("%0.1f", $cum/$cnt)}; 

  Log 4, ("myAverage: File: $logfile, Field: $cspec, Period: $period_s bis $period_e, Count: $cnt, Cum: $cum, Average: $avg"); 

  return $avg; 

} 

##########################################################> 
````
Erinnerung: Die Funktion muss zwischen der Differenzfunktion und “1;” eingefügt werden. Anschließend musst du die Datei abspeichern. 

 

### Schritt 4 – Anlegen der Devices 

Die Devices müssen analog zum Regensensor angelegt werden. Ist die tatsächliche Durchschnittstemperatur höher als die angegebene Durchschnittstemperatur der definierten Periode und die tatsächliche Temperatur höher als die angegebene Temperatur, ist die Bedingung erfüllt: 
````
<define d_avgtempperiod_a_haus dummy>  

<define d_avgTempPast_a_haus dummy> 

<define d_avgTempNow_a_haus dummy>  

<define d_avgtemp_a_haus dummy> 
````
 

Als nächstes muss ein Notify angelegt werden, der die Durchschnittstemperatur der angegebenen Periode berechnet: 
````
<define n_avgtemp_a_haus notify Temperatursensor:temperature.*|d_avgtempperiod_a_haus {my $interval=ReadingsVal('d_avgtempperiod_a_haus','state','0')*3600;;;; my $avgTemp=myAverage("".$interval."", "LogTemperatursensor", "3::");;;; fhem("set d_avgtemp_a_haus ".$avgTemp."")}> 
````
 

Abschließend müssen in einem Notify die Bedingungen geprüft werden. Sind diese Erfüllt wird der Button “b_TempBedingung_a_haus” auf on gesetzt: 
````
<define n_PumpAvgTemp_a_haus notify Temperatursensor:temperature.*|d_avgTempPast_a_haus|d_avgTempNow_a_haus|d_avgtemp_a_haus {my $avgTemp1=ReadingsVal('d_avgtemp_a_haus','state','0');;;; my $avgTempPast=ReadingsVal('d_avgTempPast_a_haus','state','0');;;; my $avgTempNow=ReadingsVal('d_avgTempNow_a_haus','state','0');;;; my $avgTemp2=ReadingsVal('Temperatursensor','temperature','0');;;;if($avgTemp1 > $avgTempPast and $avgTemp2 > $avgTempNow){fhem("set b_TempBedingung_a_haus on")} else {fhem("set b_TempBedingung_a_haus off")}}> 

 

<define b_TempBedingung_a_haus dummy> 

<attr b_TempBedingung_a_haus setList on off> 

<attr b_TempBedingung_a_haus useSetExtensions 1> 
````
 

### Schritt 5 - Einfügen der FTUI 

Für die Steuerung über die FTUI zu ermöglichen, musst du die folgenden Codezeilen unter den Kommentar <!--  Automatisierter Betrieb Temperatursensor--> eingefügt werden: 
````html
       <div class="cell-20"> 

        <div class="sheet"> 

         <div class="row"> 

           <div class="cell center-align"> 

             <div class="large darker thin cell"> 

             Aktivieren wenn Temperatur der 

             </div> 

             <div class="inline large darker thin cell"> 

             letzten 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device="d_avgtempperiod_a_haus"  

             data-get="state" > 

             </div> 

             <div class="large darker thin cell"> 

             Stunden gr&ouml;ßer als: 

             </div> 

             <div class="large darker thin cell" 

             data-type="thermostat" 

             data-device="d_avgTempPast_a_haus" 

             data-get="state" 

             data-set="" 

             data-valve="ValvePosition" 

             data-min="-10" 

             data-off="off"  

             data-max="40" 

             data-boost="40"> 

             </div> 

           </div> 

         </div> 

         <div class="row"> 

           <div class="cell center-align"> 

             <div class="large darker thin"> 

             und aktuelle Temperatur gr&ouml;ßer als: 

             </div> 

             <div class="large darker thin cell" 

             data-type="thermostat" 

             data-device="d_avgTempNow_a_haus" 

             data-get="state" 

             data-set="" 

             data-valve="ValvePosition" 

             data-min="-10" 

             data-off="off"  

             data-max="40" 

             data-boost="40"> 

             </div> 

           </div> 

         </div> 

        </div> 

       </div> 
````
 

## Automatisierter Betrieb – Windsensor 

In diesem Abschnitt wird die Wind-Überprüfung für die Bewässerung eingerichtet. Die Bedingung ist erfüllt, wenn die Windgeschwindigkeit und Wind-Böen-Geschwindigkeit kleiner als die über die FTUI eingegebene Werte sind. 

### Schritt 1 – Windsensor einrichten 

Falls du den Windsensor bereits bei der Sturmwarnung bei der Sturmwarnung implementiert hast kannst du diesen Schritt überspringen. 

Der folgende Code integriert einen Windsensor von data199 in dein System. Im Attribut requestData musst du als deviceids und phoneid die Daten wählen, die dein gewünschter mobile-alerts.eu-Sensor besitzt. Du erhältst diese über den direkten Link auf mobile-alerts.eu hinter dem "?". Lasse die restlichen Einstellungen und Befehle jedoch unverändert: 
````
<defmod WindsensorAPI HTTPMOD https://www.data199.com/api/pv1/device/lastmeasurement 420> 

<attr WindsensorAPI userattr requestData> 

<attr WindsensorAPI enableCookies 1> 

<attr WindsensorAPI event-on-change-reading .*> 

<attr WindsensorAPI extractAllJSON 1> 

<attr WindsensorAPI requestData deviceids=0B1D2300E734&phoneid=285142992122> 

<attr WindsensorAPI stateFormat Geschwindigkeit: wind_speed km/h <br> Böe: wind_gust km/h <br> Richtung: wind_direction ° <br> Messung: timestamp> 

<attr WindsensorAPI timeout 410> 

<attr WindsensorAPI userReadings wind_direction:devices_01_measurement_wd.* { int(ReadingsVal("WindsensorAPI","devices_01_measurement_wd",0))/16*360 },  wind_speed:devices_01_measurement_ws.* { ReadingsVal("WindsensorAPI","devices_01_measurement_ws",0)/1000*3600 },  wind_gust:devices_01_measurement_wg.* { ReadingsVal("WindsensorAPI","devices_01_measurement_wg",0)/1000*3600 },  timestamp:devices_01_measurement_ts.* { POSIX::strftime("%F %T", localtime(ReadingsVal("WindsensorAPI","devices_01_measurement_ts",0))) },> 
````
Das Gerät wird nun alle 420 Sekunden die aktuellen Winddaten abfragen. Warum nur alle 420 Sekunden? Da der Windsensor ohnehin nicht häufiger Daten aufliefert, wäre ein niedrigeres Prüfintervall nicht sinnvoll bzw. würde nur unnötige Abfragen generieren. 

 

### Schritt 2 – Anlegen der Devices 

Zuerst musst du die Dummys anlegen in denen die über die FTUI eingegebenen Werte gespeichert werden: 
````
<define d_windboeengeschwindigkeit_haus dummy> 

<define d_windgeschwindigkeit_haus dummy> 
````
 

Anschließend muss in einem Notify geprüft werden, ob die Bedingung erfüllt ist. Bei Erfüllung der Bedingung wird der Button “b_WindBedingung_haus” auf “on” gesetzt: 

 
````
<define n_PumpWind_Haus notify d_windgeschwindigkeit_Haus|d_windboeengeschwindigkeit_haus|WindsensorAPI:wind_speed.*|WindsensorAPI:wind_gust.* {my $wind=ReadingsVal('WindsensorAPI','wind_speed','0')/3.6 ;; my $boee=ReadingsVal('WindsensorAPI','wind_gust','0')/3.6;;my $windKleinerAls=ReadingsVal('d_windgeschwindigkeit_haus','state','0');;my $boeeKleinerAls=ReadingsVal('d_windboeengeschwindigkeit_haus','state','0');; if($wind<$windKleinerAls and $boee<$boeeKleinerAls){fhem("set b_WindBedingung_haus on")}else {fhem("set b_WindBedingung_haus off")}}> 

 

<define b_WindBedingung_haus dummy> 

<attr b_WindBedingung_haus setList on off> 

<attr b_WindBedingung_haus useSetExtensions 1> 
````
 

### Schritt 3 - Einfügen der FTUI 

Für die Steuerung über die FTUI zu ermöglichen, müssen die folgenden Codezeilen unter den Kommentar <!--  Automatisierter Betrieb Windsensor--> eingefügt werden: 
````html
       <div class="cell-20 center-align"> 

             <div class="large darker thin cell"> 

             Aktivieren wenn die Wingeschwindigkeit kleiner als 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device="d_windgeschwindigkeit_Haus"  

             data-get="state" > 

             </div> 

             <div class="inline large darker thin cell"> 

             m/s ist 

             </div> 

             <div class="large darker thin cell"> 

             und die Wind-B&ouml;en-Geschwindigkeit kleiner als 

             </div> 

             <div data-type="input"  

             class="inline w1x"  

             data-device="d_windboeengeschwindigkeit_Haus"  

             data-get="state" > 

             </div> 

             <div class="inline large darker thin cell"> 

             m/s ist. 

             </div> 

       </div> 

      </div> 

     </div> 

</li> 
````
 

## Automatisierter Betrieb - Aktivierung 

Nach Einrichtung der Zeitpunktauswahl und der aller Bedingungen, musst du eine DOIF anlegen, welche überprüft, ob alle Bedingungen erfüllt sind. Bei Erfüllung wird die automatische Bewässerung zur angegebenen Zeit gestartet:  
````
<define doif_PumpHaus DOIF { if((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "on" and [b_TempBedingung_haus] eq "on" and [b_WindBedingung_haus] eq "on") {my $timer=ReadingsVal('d_uhrzeittimer_a_haus','state','0')*60 ;; fhem("set  b_gartenpumpe_t_haus on-for-timer ".$timer."");; fhem("set pushAll message Automatische Bewässerung im Hausgarten aktiviert!")}}> 
````
 

Nun möchtest du noch eine Nachricht bekommen, die dir den Grund nennt, falls die Bewässerung startet: 
````
<{if((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "off" and [b_TempBedingung_haus] eq "on" and [b_WindBedingung_haus] eq "on") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da es zu viel regnet!")} elsif((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "on" and [b_TempBedingung_haus] eq "off" and [b_WindBedingung_haus] eq "on") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da die Temperatur zu niedrig ist!")} elsif((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "on" and [b_TempBedingung_haus] eq "on" and [b_WindBedingung_haus] eq "off") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da es zu windig ist!")} elsif((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "off" and [b_TempBedingung_haus] eq "off" and [b_WindBedingung_haus] eq "on") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da es zu viel regnet hat und es zu kalt ist!")} elsif((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "off" and [b_TempBedingung_haus] eq "on" and [b_WindBedingung_haus] eq "off") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da es zu viel regnet und es zu windig ist!")} elsif((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "on" and [b_TempBedingung_haus] eq "off" and [b_WindBedingung_haus] eq "off") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da es zu kalt ist und es zu windig ist!")} elsif((([d_pumpetageswahl_a_haus:Montag] =~ "on" and [[d_uhrzeit_a_haus]|Mo])or([d_pumpetageswahl_a_haus:Dienstag] =~ "on" and [[d_uhrzeit_a_haus]|Di])or([d_pumpetageswahl_a_haus:Mittwoch] =~ "on" and [[d_uhrzeit_a_haus]|Mi])or([d_pumpetageswahl_a_haus:Donnerstag] =~ "on" and [[d_uhrzeit_a_haus]|Do])or([d_pumpetageswahl_a_haus:Freitag] =~ "on" and [[d_uhrzeit_a_haus]|Fr])or([d_pumpetageswahl_a_haus:Samstag] =~ "on" and [[d_uhrzeit_a_haus]|Sa])or([d_pumpetageswahl_a_haus:Sonntag] =~ "on" and [[d_uhrzeit_a_haus]|So])) and [b_gartenpumpe_a_haus] eq "on" and [b_RegenBedingung_haus] eq "off" and [b_TempBedingung_haus] eq "off" and [b_WindBedingung_haus] eq "off") {fhem("set pushAll message Automatische Bewässerung im Hausgarten wurde nicht aktiviert, da es zu viel regnet, zu kalt ist und zu windig ist!")}}> 
````
 

## Schrebergarten 

Analog zum Hausgarten, muss der Teilautomatisierte und Automatisierte Betrieb für den Schrebergarten eingerichtet werden. Laut Namenskonvention wird “haus” durch “schreber” ersetzt. Und der HTML Code in /opt/fhem/www/tablet/Schrebergartenpumpe.html eingefügt. 

FHEM Befehle: 

Die Folgenden Befehle musst du in der Kommandozeile ausführen. 

 
Teilautomatisierter Betrieb 
````
<define b_gartenpumpe_t_schreber dummy> 

<attr b_gartenpumpe_t_schreber setList on off>  

<attr b_gartenpumpe_t_schreber useSetExtensions 1>  

<define d_timer_t_schreber dummy>  

<define b_timer_t_schreber dummy>  

<attr b_timer_t_schreber setList on off>  

  

<define d_uhrzeit_t_schreber dummy>  

<define d_uhrzeittimer_t_schreber dummy>  

<define b_uhrzeit_t_schreber dummy>  

<attr b_uhrzeit_t_schreber setList on off> 

 

Automatisierter Betrieb – Menu 

<define b_gartenpumpe_a_schreber dummy>  

<attr b_gartenpumpe_a_schreber setList on off>  

<attr b_gartenpumpe_a_schreber useSetExtensions 1>  

<define d_uhrzeit_a_schreber dummy>  

<define d_uhrzeittimer_a_schreber dummy>  

  

<define d_pumpetageswahl_a_schreber dummy>  

<attr d_pumpetageswahl_a_schreber readingList Montag Dienstag Mittwoch Donnerstag Freitag Samstag Sonntag>  

< attr d_pumpetageswahl_a_schreber setList Montag:on,off Dienstag:on,off Mittwoch:on,off Donnerstag:on,off Freitag:on,off Samstag:on,off Sonntag:on,off> 
````
 

Automatisierter Betrieb – Regensensor 
````
<define d_rainperiod1_a_schreber dummy>  

<define d_rainperiod2_a_schreber dummy>  

<define d_rainpastperiod1_a_schreber dummy>  

<define d_rainpastperiod2_a_schreber dummy>  

<define b_RegenBedingung_schreber dummy>  

<attr b_RegenBedingung_schreber setList on off>  

<attr b_RegenBedingung_schreber useSetExtensions 1>  

  

<define n_diffrain1_a_schreber notify Regensensor:amount.*| d_rainperiod1_a_schreber {my $interval=ReadingsVal(' d_rainperiod1_a_schreber ','state','0')*3600;;;; my $diffRain=myDifference("".$interval."", "LogRegensensor", "3::");;;; fhem("set d_diffRain1_a_schreber ".$diffRain."")}>  

  

<define n_diffrain2_a_schreber notify Regensensor:amount.*| d_rainperiod2_a_schreber {my $interval=ReadingsVal(' d_rainperiod2_a_schreber ','state','0')*3600;;;; my $diffRain=myDifference("".$interval."", "LogRegensensor", "3::");;;; fhem("set d_diffRain2_a_schreber ".$diffRain."")}>  

  

<define d_diffRain1_a_schreber dummy>  

<define d_diffRain2_a_schreber dummy>  

  

<define n_pumprain_schreber notify d_rainpastperiod1_a_schreber|d_rainpastperiod2_a_schreber| d_diffRain1_a_schreber|d_diffRain2_a_schreber {my $rain1_schreber=ReadingsVal('d_rainpastperiod1_a_schreber','state','0');; my $rain2_schreber=ReadingsVal('d_rainpastperiod2_a_schreber','state','0');;my $avgrain1_schreber=ReadingsVal('d_diffRain1_a_schreber','state','0');; my $avgrain2_schreber=ReadingsVal('d_diffRain2_a_schreber','state','0');; if($avgrain1_schreber < $rain1_schreber and $avgrain2_schreber < $rain2_schreber){fhem("set b_RegenBedingung_schreber on")} else {fhem("set b_RegenBedingung_schreber off")}}> 
````
 

Automatisierter Betrieb – Temperatursensor 
````
<define d_avgtempperiod_a_schreber dummy>   

<define d_avgTempPast_a_schreber dummy>  

<define d_avgTempNow_a_schreber dummy>   

<define d_avgtemp_a_schreber dummy>  

  

<define n_avgtemp_a_schreber notify Temperatursensor:temperature.*|d_avgtempperiod_a_schreber {my $interval=ReadingsVal('d_avgtempperiod_a_schreber','state','0')*3600;;;; my $avgTemp=myAverage("".$interval."", "LogTemperatursensor", "3::");;;; fhem("set d_avgtemp_a_schreber ".$avgTemp."")}>  

  

<define n_PumpAvgTemp_a_schreber notify Temperatursensor:temperature.*|d_avgTempPast_a_schreber|d_avgTempNow_a_schreber|d_avgtemp_a_schreber {my $avgTemp1=ReadingsVal('d_avgtemp_a_schreber','state','0');;;; my $avgTempPast=ReadingsVal('d_avgTempPast_a_schreber','state','0');;;; my $avgTempNow=ReadingsVal('d_avgTempNow_a_schreber','state','0');;;; my $avgTemp2=ReadingsVal('Temperatursensor','temperature','0');;;;if($avgTemp1 > $avgTempPast and $avgTemp2 > $avgTempNow){fhem("set b_TempBedingung_a_schreber on")} else {fhem("set b_TempBedingung_a_schreber off")}}>  

  

<define b_TempBedingung_a_schreber dummy>  

<attr b_TempBedingung_a_schreber setList on off>  

<attr b_TempBedingung_a_schreber useSetExtensions 1> 
````
 

Automatisierter Betrieb - Windsensor 
````
<define d_windboeengeschwindigkeit_schreber dummy>  

<define d_windgeschwindigkeit_schreber dummy>  

  

<define n_PumpWind_schreber notify d_windgeschwindigkeit_schreber|d_windboeengeschwindigkeit_schreber|WindsensorAPI:wind_speed.*|WindsensorAPI:wind_gust.* {my $wind=ReadingsVal('WindsensorAPI','wind_speed','0')/3.6 ;; my $boee=ReadingsVal('WindsensorAPI','wind_gust','0')/3.6;;my $windKleinerAls=ReadingsVal('d_windgeschwindigkeit_schreber','state','0');;my $boeeKleinerAls=ReadingsVal('d_windboeengeschwindigkeit_schreber','state','0');; if($wind<$windKleinerAls and $boee<$boeeKleinerAls){fhem("set b_WindBedingung_schreber on")}else {fhem("set b_WindBedingung_schreber off")}}>  

  

<define b_WindBedingung_schreber dummy>  

<attr b_WindBedingung_schreber setList on off>  

<attr b_WindBedingung_schreber useSetExtensions 1> 
````
 

Automatisierter Betrieb – Aktivierung  
````
<define doif_Pumpschreber DOIF { if((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "on" and [b_TempBedingung_schreber] eq "on" and [b_WindBedingung_schreber] eq "on") {my $timer=ReadingsVal('d_uhrzeittimer_a_schreber','state','0')*60 ;;;; fhem("set  b_gartenpumpe_t_schreber on-for-timer ".$timer."")}}> 

 

<{if((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "off" and [b_TempBedingung_schreber] eq "on" and [b_WindBedingung_schreber] eq "on") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da es zu viel regnet!")} elsif((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "on" and [b_TempBedingung_schreber] eq "off" and [b_WindBedingung_schreber] eq "on") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da die Temperatur zu niedrig ist!")} elsif((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "on" and [b_TempBedingung_schreber] eq "on" and [b_WindBedingung_schreber] eq "off") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da es zu windig ist!")} elsif((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "off" and [b_TempBedingung_schreber] eq "off" and [b_WindBedingung_schreber] eq "on") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da es zu viel regnet hat und es zu kalt ist!")} elsif((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "off" and [b_TempBedingung_schreber] eq "on" and [b_WindBedingung_schreber] eq "off") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da es zu viel regnet und es zu windig ist!")} elsif((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "on" and [b_TempBedingung_schreber] eq "off" and [b_WindBedingung_schreber] eq "off") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da es zu kalt ist und es zu windig ist!")} elsif((([d_pumpetageswahl_a_schreber:Montag] =~ "on" and [[d_uhrzeit_a_schreber]|Mo])or([d_pumpetageswahl_a_schreber:Dienstag] =~ "on" and [[d_uhrzeit_a_schreber]|Di])or([d_pumpetageswahl_a_schreber:Mittwoch] =~ "on" and [[d_uhrzeit_a_schreber]|Mi])or([d_pumpetageswahl_a_schreber:Donnerstag] =~ "on" and [[d_uhrzeit_a_schreber]|Do])or([d_pumpetageswahl_a_schreber:Freitag] =~ "on" and [[d_uhrzeit_a_schreber]|Fr])or([d_pumpetageswahl_a_schreber:Samstag] =~ "on" and [[d_uhrzeit_a_schreber]|Sa])or([d_pumpetageswahl_a_schreber:Sonntag] =~ "on" and [[d_uhrzeit_a_schreber]|So])) and [b_gartenpumpe_a_schreber] eq "on" and [b_RegenBedingung_schreber] eq "off" and [b_TempBedingung_schreber] eq "off" and [b_WindBedingung_schreber] eq "off") {fhem("set pushAll message Automatische Bewässerung im Schrebergarten wurde nicht aktiviert, da es zu viel regnet, zu kalt ist und zu windig ist!")}}> 
````
 

HTML Code Einfügen  

Folgenden HMTL Code in /opt/fhem/www/tablet/Schrebergartenpumpe.html einfügen: 
````html
<!DOCTYPE html> 

<html> 

<head></head> 

<body> 

<div class="gridster"> 

<ul> 

<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li> 

<!--  Teilautomatisierter Betrieb --> 

<li data-row="2" data-col="2" data-sizex="3" data-sizey="2" class="left-align">                  

  

    <header>Schrebergarten</header>  

  

              <div class="sheet">  

  

              <div class="cell">  

  

                  <div class="big"   

  

                  data-type="label">schrebergartenpumpe</div>  

  

    	   <div data-type="switch"   

  

                  data-device=" b_gartenpumpe_t_schreber "   

  

                  data-set-on="state"   

  

                  data-states='["on","off"]'   

  

                  data-colors='["green","red"]'   

  

                  data-background-colors='["#2A2A2A","#2A2A2A"]'   

  

                  data-set-states='["off","on"]'   

  

                  data-icons='["fa-toggle-on","fa-toggle-off"]'   

  

                  data-background-icons='["fa-blank","fa-blank"]'   

  

                  data-on-background-color="grey"   

  

                  data-off-background-color="grey"   

  

                  class="cell"></div>  

  

                    

  

                  <div class="inline large darker thin cell">  

  

                  Schrebergartenbew&auml;sserung an f&uuml;r:  

  

                  </div>  

  

                  <div  

  

                  data-type="datetimepicker"  

  

                  data-device=" d_timer_t_schreber "  

  

                  data-datepicker="false"  

  

                  class="inline bigger thin orange cell"  

  

                  data-max-time="01:00:00"  

  

                  data-step="1"  

  

                  data-format="i"  

  

                  ></div>  

  

                  <div class="inline large darker thin cell">Minuten</div>  

  

                  <div  

  

                  data-type="checkbox"  

  

                  data-device=" b_timer_t_schreber"  

  

                  data-set-on="on"  

  

                  data-set-off="off"  

  

                  class="inline left-space-3x"  

  

                  ></div>  

  

                    

  

                  <div class="cell">  

  

                  <div class="inline large darker thin cell">  

  

                  Schrebergartenbew&auml;sserung an um:  

  

                  </div>  

  

                  <div  

  

                  data-type="datetimepicker"  

  

                  data-device=" d_uhrzeit_t_schreber "  

  

                  data-datepicker="false"  

  

                  class="inline bigger thin orange cell"  

  

                  data-step="1"  

  

                  data-format="H:i"  

  

                  ></div>  

  

                  <div class="inline large darker thin cell">f&uuml;r</div>  

  

                  <div  

  

                  data-type="datetimepicker"  

  

                  data-device=" d_uhrzeittimer_t_schreber "  

  

                  data-datepicker="false"  

  

                  class="inline bigger thin orange cell"  

  

                  data-max-time="01:00:00"  

  

                  data-step="1"  

  

                  data-format="i"  

  

                  ></div>  

  

                  <div class="inline large darker thin cell">Minuten</div>  

  

                  <div  

  

                  data-type="checkbox"  

  

                  data-device=" b_uhrzeit_t_schreber "  

  

                  data-set-on="on"  

  

                  data-set-off="off"  

  

                  class="inline left-space-3x"  

  

                  ></div>  

  

              </div>  

  

              </div>  

  

    </li>  

 

<!--  Automatisierter Betrieb Menu --> 

<li data-row="3" data-col="2" data-sizex="4" data-sizey="2" class="left-align">                    

  

<header>Automatikbetrieb</header>  

  

     <div class="sheet">  

  

       <div class="row">  

  

         <div class="cell-20 center-align">  

  

           <div class="large darker thin cell">  

  

           Automatikbetrieb der Pumpe:  

  

           </div>  

  

           <div  

  

           data-type="checkbox"  

  

           data-device=" b_gartenpumpe_a_schreber"  

  

           data-set-on="on"  

  

           data-set-off="off"  

  

           class="inline cell"  

  

           ></div>  

  

           <div class="cell">  

  

           <div class="inline large darker thin cell">um</div>  

  

           <div  

  

           data-type="datetimepicker"  

  

           data-device=" d_uhrzeit_a_schreber"  

  

           data-datepicker="false"  

  

           class="inline bigger thin orange cell"  

  

           data-step="1"  

  

           data-format="H:i"  

  

           ></div>  

  

           <div class="inline large darker thin cell">Uhr</div>  

  

           </div>  

  

           <div class="cell">  

  

           <div class=" inline large darker thin cell">f&uuml;r</div>  

  

           <div  

  

           data-type="datetimepicker"  

  

           data-device=" d_uhrzeittimer_a_schreber "  

  

           data-datepicker="false"  

  

           class="inline bigger thin orange cell"  

  

           data-max-time="01:00:00"  

  

           data-step="1"  

  

           data-format="i"  

  

           ></div>  

  

           <div class="inline large darker thin cell">Minuten</div>  

  

           </div>  

  

             

  

         </div>  

  

         <div class="cell-20 center-align">  

  

         <div class="large darker thin">Aktivieren an den Tagen:</div>  

  

          <div class="darker thin"  

  

           data-type="switch"   

  

           data-device=" d_pumpetageswahl_a_schreber "   

  

           data-icon=""  

  

           data-background-icon=""   

  

           data-set="Montag"   

  

           data-states='["on","off"]'  

  

           >  

  

             <div data-type="label"   

  

             data-device=" d_pumpetageswahl_a_schreber "  

  

             data-get="Montag"   

  

             data-states='["on","off"]'  

  

             data-substitution='["on","Montag","off","Montag"]'  

  

             data-colors='["#aa6900","#505050"]'>  

  

             </div>  

  

         </div>  

  

         

  

         <div class="darker thin"  

  

         data-type="switch"   

  

         data-device=" d_pumpetageswahl_a_schreber"   

  

         data-icon=""  

  

         data-background-icon=""   

  

         data-set="Dienstag"   

  

         data-states='["on","off"]'  

  

         >  

  

           <div data-type="label"   

  

           data-device=" d_pumpetageswahl_a_schreber"  

  

           data-get="Dienstag"   

  

           data-states='["on","off"]'  

  

           data-substitution='["on","Dienstag","off","Dienstag"]'  

  

           data-colors='["#aa6900","#505050"]'>  

  

           </div>  

  

         </div>  

  

         

  

         <div class="darker thin"  

  

         data-type="switch"   

  

         data-device=" d_pumpetageswahl_a_schreber"   

  

         data-icon=""  

  

         data-background-icon=""   

  

         data-set="Mittwoch"   

  

         data-states='["on","off"]'  

  

         >  

  

           <div data-type="label"   

  

           data-device=" d_pumpetageswahl_a_schreber"  

  

           data-get="Mittwoch"   

  

           data-states='["on","off"]'  

  

           data-substitution='["on","Mittwoch","off","Mittwoch"]'  

  

           data-colors='["#aa6900","#505050"]'>  

  

           </div>  

  

         </div>  

  

         

  

         <div class="darker thin"  

  

         data-type="switch"   

  

         data-device=" d_pumpetageswahl_a_schreber"   

  

         data-icon=""  

  

         data-background-icon=""   

  

         data-set="Donnerstag"   

  

         data-states='["on","off"]'  

  

         >  

  

           <div data-type="label"   

  

           data-device=" d_pumpetageswahl_a_schreber"  

  

           data-get="Donnerstag"   

  

           data-states='["on","off"]'  

  

           data-substitution='["on","Donnerstag","off","Donnerstag"]'  

  

           data-colors='["#aa6900","#505050"]'>  

  

           </div>  

  

         </div>  

  

         

  

         <div class="darker thin"  

  

         data-type="switch"   

  

         data-device=" d_pumpetageswahl_a_schreber"   

  

         data-icon=""  

  

         data-background-icon=""   

  

         data-set="Freitag"   

  

         data-states='["on","off"]'  

  

         >  

  

           <div data-type="label"   

  

           data-device=" d_pumpetageswahl_a_schreber"  

  

           data-get="Freitag"   

  

           data-states='["on","off"]'  

  

           data-substitution='["on","Freitag","off","Freitag"]'  

  

           data-colors='["#aa6900","#505050"]'>  

  

           </div>  

  

         </div>  

  

         

  

         <div class="darker thin"  

  

         data-type="switch"   

  

         data-device=" d_pumpetageswahl_a_schreber"   

  

         data-icon=""  

  

         data-background-icon=""   

  

         data-set="Samstag"   

  

         data-states='["on","off"]'  

  

         >  

  

           <div data-type="label"   

  

           data-device=" d_pumpetageswahl_a_schreber"  

  

           data-get="Samstag"   

  

           data-states='["on","off"]'  

  

           data-substitution='["on","Samstag","off","Samstag"]'  

  

           data-colors='["#aa6900","#505050"]'>  

  

           </div>  

  

         </div>  

  

         

  

         <div class="darker thin"  

  

         data-type="switch"   

  

         data-device=" d_pumpetageswahl_a_schreber"   

  

         data-icon=""  

  

         data-background-icon=""   

  

         data-set="Sonntag"   

  

         data-states='["on","off"]'  

  

         >  

  

           <div data-type="label"   

  

           data-device=" d_pumpetageswahl_a_schreber "  

  

           data-get="Sonntag"   

  

           data-states='["on","off"]'  

  

           data-substitution='["on","Sonntag","off","Sonntag"]'  

  

           data-colors='["#aa6900","#505050"]'>  

  

           </div>  

  

         </div>  

  

       </div>  

 

 

<!--  Automatisierter Betrieb Regensensor--> 

<div class="cell-20 center-align">  

  

         <div class="row">  

  

           <div class="cell center-align">  

  

             <div class="large darker thin cell">  

  

             Aktivieren wenn Regenmenge der  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             letzten  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device=" d_rainperiod1_a_schreber "   

  

             data-get="state" >  

  

             </div>  

  

             <div class="large darker thin cell">  

  

             Stunden in Summe kleiner als  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device="d_rainpastperiod1_a_schreber"   

  

             data-get="state" >  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             mm/qm ist  

  

             </div>  

  

           </div>  

  

         </div>  

  

           

  

         <div class="row">  

  

           <div class="cell center-align">  

  

             <div class="large darker thin cell">  

  

             Aktivieren wenn Regenmenge der  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             letzten  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device="d_rainperiod2_a_schreber"   

  

             data-get="state" >  

  

             </div>  

  

             <div class="large darker thin cell">  

  

             Stunden in Summe kleiner als  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device="d_rainpastperiod2_a_schreber"   

  

             data-get="state" >  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             mm/qm ist  

  

             </div>  

  

           </div>  

  

         </div>   

  

       </div>  

    

    

   <!--  Automatisierter Betrieb Temperatursensor--> 

          <div class="cell-20">  

  

        <div class="sheet">  

  

         <div class="row">  

  

           <div class="cell center-align">  

  

             <div class="large darker thin cell">  

  

             Aktivieren wenn Temperatur der  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             letzten  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device="d_avgtempperiod_a_schreber"   

  

             data-get="state" >  

  

             </div>  

  

             <div class="large darker thin cell">  

  

             Stunden gr&ouml;ßer als:  

  

             </div>  

  

             <div class="large darker thin cell"  

  

             data-type="thermostat"  

  

             data-device="d_avgTempPast_a_schreber"  

  

             data-get="state"  

  

             data-set=""  

  

             data-valve="ValvePosition"  

  

             data-min="-10"  

  

             data-off="off"   

  

             data-max="40"  

  

             data-boost="40">  

  

             </div>  

  

           </div>  

  

         </div>  

  

         <div class="row">  

  

           <div class="cell center-align">  

  

             <div class="large darker thin">  

  

             und aktuelle Temperatur gr&ouml;ßer als:  

  

             </div>  

  

             <div class="large darker thin cell"  

  

             data-type="thermostat"  

  

             data-device="d_avgTempNow_a_schreber"  

  

             data-get="state"  

  

             data-set=""  

  

             data-valve="ValvePosition"  

  

             data-min="-10"  

  

             data-off="off"   

  

             data-max="40"  

  

             data-boost="40">  

  

             </div>  

  

           </div>  

  

         </div>  

  

        </div>  

  

       </div>  

 

<!--  Automatisierter Betrieb Windsensor--> 

        <div class="cell-20 center-align">  

  

             <div class="large darker thin cell">  

  

             Aktivieren wenn die Windgeschwindigkeit kleiner als  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device="d_windgeschwindigkeit_schreber"   

  

             data-get="state" >  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             m/s ist  

  

             </div>  

  

             <div class="large darker thin cell">  

  

             und die Wind-B&ouml;en-Geschwindigkeit kleiner als  

  

             </div>  

  

             <div data-type="input"   

  

             class="inline w1x"   

  

             data-device="d_windboeengeschwindigkeit_schreber"   

  

             data-get="state" >  

  

             </div>  

  

             <div class="inline large darker thin cell">  

  

             m/s ist.  

  

             </div>  

  

       </div>  

  

      </div>  

  

     </div>  

  

 </li> 

 

<!--  Zisterne --> 

 

</ul> 

</div> 

</body> 

</html> 
````
 

 

Herzlichen Glückwunsch, du hast es geschafft! Durch die automatische Bewässerung vergisst du nie wieder zu gießen. 

  

 

## Zisterne 

Im Kapitel FTUI-Grundlagen hast du im Reiter Einstellungen eine Oberfläche für die Eingabe der Variablen gestaltet. In diesem Abschnitt möchtest du deinen Zisternenfüllstand über die FTUI visualisieren.  

 

### Schritt 1 – Kalibrierung  

Im Kapitel Hardware hast du den Abstandssensor konfiguriert und den Wert im Dummy UC1 speichern lassen. Dieser Wert gibt den Abstand vom Sensor bis zur Wasseroberfläche bei einer komplett gefüllten Zisterne an.  Damit du den Abstand initial nicht manuell eingeben musst, kannst du über die FTUI die automatische Kalibrierung starten. Hierfür muss du eine Device anlegen, dass beim Klicken auf der FTUI einen den Abstand zur Wasseroberfläche misst und im Dummy Zisterne im Reading BOffset speichert. Alternativ gibt es auch die Möglichkeit BOffset manuell über Fhem als Readingvalue zu ändern. 

Vorsicht: Die Kalibrierung nur durchführen, wenn die Zisterne zu 100% gefüllt ist. Ansonsten ist der berechnete Füllstand nicht korrekt.  

 

Zuallererst muss du einen Dummy für die Zisterne anlegen. In diesem werden die Werte für die Zisterne gespeichert: 

 
''``
<define zisterne dummy> 

<attr zisterne room Zisterne> 

<attr zisterne readingList BOffset fuellstand hoehe literangabe literberechnet fuellstandprozent> 

<attr zisterne setList BOffset fuellstand hoehe literangabe literberechnet fuellstandprozent> 
''``
 

Die Höhe und die Litermenge kannst du nun über die FTUI bei Bedarf ändern.  

 

 

Setzte folgenden Befehl über die Kommandozeile ab, um die automatische Kalibrierung zu implementieren:  

 
````
<define doif_zisterneCalibration DOIF ([$SELF:P_mybutton] eq "on")(set zisterne BOffset [UC1:Abstand];;set doif_zisterneCalibration P_mybutton off)> 

<attr doif_zisterneCalibration do always> 

<attr doif_zisterneCalibration readingList P_mybutton> 

<attr doif_zisterneCalibration room Zisterne> 

<attr doif_zisterneCalibration selftrigger all> 

<attr doif_zisterneCalibration setList P_mybutton:uzsuSelectRadio,on,off> 
````
 

Nun kannst du mit einem Klick ein deinen Einstellungen den Abstand kalibrieren. 

 

### Schritt 2 - Füllstand der Zisterne berechnen 

Nun möchtest du den Füllstand deiner Zisterne berechnen. Dafür musst du die folgenden Befehle absetzten:  

 
````
<define efmod doif_zisterneBerechnung DOIF ([UC1:Abstand])(set zisterne fuellstand {(1-((([zisterne:hoehe]/2)*([zisterne:hoehe]/2)*acos(1-(([UC1:Abstand]-[zisterne:BOffset])/([zisterne:hoehe]/2)))-sqrt(2*([UC1:Abstand]-[zisterne:BOffset])*([zisterne:hoehe]/2)-(([UC1:Abstand]-[zisterne:BOffset])*([UC1:Abstand]-[zisterne:BOffset])))*(([UC1:Abstand]-[zisterne:BOffset])-([zisterne:hoehe]/2)))/(3.14*(([zisterne:hoehe]/2)*([zisterne:hoehe]/2)))))} )> 

<attr doif_zisterneBerechnung do always> 

<attr doif_zisterneBerechnung room Zisterne> 
````
 

Durch das angelegte DOIF wird dein Füllstand immer aktuell berechnet und als Dezimalzahl gespeichert. Als nächstes zeigen wir dir, wie du deinen Füllstand in Liter angibst und visualisierst. 

 

Um den Füllstand auf der FTUI anzuzeigen, legst du einen Notify an, welcher die Liter in der Zisterne berchnet:  

 
````
<define n_zistereneLiterBerechnung notify zisterne:fuellstand.* {my $literangabe=ReadingsVal('zisterne','literangabe','0');;;; my $fuellstand=ReadingsVal('zisterne','fuellstand','0');;;; my $rechnung=$literangabe*$fuellstand;;;; fhem("set zisterne literberechnet ".$rechnung."")}> 
````
 

Der berechnete Literstand wird im Reading literberechnet gespeichert. 

 

Um den Füllstand in der FTUI zu visualisieren, muss der Füllstand in einer ganzen Zahl angegeben werden. Dafür muss folgendes Notify angelegt werden: 

 
````
<define n_zistereneFuellstandProzent notify zisterne:fuellstand.* { my $fuellstand=ReadingsVal('zisterne','fuellstand','0');;;; my $rechnung=int(100*$fuellstand);;;; fhem("set zisterne fuellstandprozent ".$rechnung."")}> 

 ````

Der Wert wird im Reading fuelllstandprozent gespeichert. 

 

### Schritt 3 – In die FTUI einfügen 

Du musst die folgenden Codezeilen unter den Kommentar <!--  Zisterne --> einfügen: 
````html
<li data-row="2" data-col="2" data-sizex="1" data-sizey="2" class="left-align">  

<header>Zisternenf&uuml;llstand</header> 

  <div class="sheet"> 

    <div class="row"> 

    <div class="cell center-align"> 

        <div class="cell"> 

        <div class="fullsize"  

        data-icon="" 

        data-type="controller"  

        data-device="zisterne"  

        data-get="fuellstandprozent"  

        data-set="fuellstandprozent" 

        data-lock="ftuiLock"  

        data-height="6em"  

        data-width="10em" 

        data-color="blue" 

        data-background-color="white"></div> 

        <div class="large darker thin">F&uuml;llstand der Zisterne in Litern:</div> 

        <div data-type="label" 

        data-device="zisterne" 

        data-get="literberechnet" 

        data-fix="2"></div> 

        </div>       

    </div> 

    </div> 

  </div> 

</li> 
````
 

Solltest du dich dafür entscheiden den Füllstand nur auf einem der Reiter anzeigen zu lassen, so musst du die HTML anpassen. Hierfür gehst du auf die jeweilige HTML Datei der Seite, welche den Füllstand nicht anzeigen soll. Den HTML Code der Zisterne fügst du nicht ein und änderst data-sizex von 3 auf 4. 

 
````
<li data-row="2" data-col="2" data-sizex="4" data-sizey="2" class="left-align"> 
````
  

 

Quellen dieses Tutorials 

Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen: https://wiki.fhem.de/wiki/Zisterne:_F%C3%BCllstandsberechnung_mittels_Ultraschallsensor (Letzter Zugriff: 01.02.21),  

https://wiki.fhem.de/wiki/Gleitende_Mittelwerte_berechnen_und_loggen (Letzter Zugriff: 01.02.21)  
