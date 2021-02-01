# Wie Dir mit FHEM ein Licht aufgeht (und zwar dann, wenn du willst) 

 

## Einführung 

 

In diesem Tutorial wirst du lernen eine automatische Beleuchtungssteuerung mittels FHEM zu erstellen. Dabei wirst du verschieden Beleuchtungsmodi anlegen, die manuell, nach Sonnenauf- und Untergang oder mit einer Anwesenheitserkennung geschaltet werden. Anschließend lernst du, wie du eine Bedienoberfläche für die Beleuchtungssteuerung mit FTUI erstellen. 

 

## Voraussetzungen 

 

* FHEM wurde eingerichtet 

* Eine Funk- und eine WLAN-Steckdose wurde eingebunden 

* Eine Menüseite wurde in FTUI erstellt 

* Eine leere Seite für die Beleuchtung wurde in FTUI angelegt 

 

Die Beleuchtung besteht aus zwei verschiedenen Lichterketten: Eine ist in eine Funksteckdose eingesteckt, die andere in eine WLAN-Steckdose. 

Da der Aufbau der Steuerung für beide Lichterketten weitgehend identisch ist, wird im folgenden Verlauf der Dokumentation nur der Aufbau der Lichterkette an der Funksteckdose genauer beschrieben. Für die WLAN-Steckdose liegen lediglich die Befehle bei, die analog zur Funksteckdose implementiert werden. 

 

### Schritt 1 

Vorarbeit: Den Längen- und Breitengrad festlegen 

 

In diesem Modul lernst du, wie du deine Devices automatisch zum Sonnenauf- bzw. Untergang steuern kannst. 

Hierfür benutzt du das Hilfsmodul **“SUNRISE_EL”**. Dieses Modul bietet Funktionen, um Aktionen abhängig von Sonnenauf- und –untergangszeiten durchzuführen.  

**SUNRISE_EL** ist kein Device im klassischen Sinne in FHEM, sondern ein Modul, das nicht definiert, also angelegt werden muss wie andere Devices. Das steht automatisch zur Verfügung und liegt in den Dateien im FHEM/-Verzeichnis. 

 

Damit du **SUNIRSE_EL** nutzen kannst, muss in der Konfiguration (fhem.cfg) der gewünschte Standort definiert werden, da der Sonnenauf- und –untergang nicht nur vom Datum, sondern auch vom Längen- und Breitengrad deines Standortes abhängig ist.  

Dazu benötigen du folgende Definitionen: 

 
```
<attr global latitude 52.51626> 

<attr global longitude 13.37778> 
```
 

Dies solltest du genauso wie im Beispiel schreiben. Die Koordinaten können entweder mit Hilfe eines GPS-Systems oder durch einen Internet-Dienst ermittelt werden.  

Als Internet-Dienst eignet sich beispielsweise Google Maps-Hilfe (Breiten- und Längengrad finden oder eingeben). In diesem Beispiel Latitude 52.51626 und Longitude 13.37778 (Brandenburger Tor in Berlin) 

 

Folgende Funktionen gibt dir **SUNRISE_EL**: 

 

* Sunrise / sunset gibt die absolute Zeit des nächsten Sonnenauf- bzw -untergangs zurück, wobei 24h addiert werden, sofern das entsprechende Ereignis am nächsten Tag stattfindet 

* Sunrise_el /sunset_rel gibt die relative Zeit bis zum nächsten Sonnenauf- bzw. -untergang zurück. 

* Sunrise_abs / sunset_abs gibt die absolute Zeit für den aktuellen Tag. 

 

So steuerst du **SUNRISE_EL**: 

 

* Außenlampe - Steuerung An-/Ausschaltzeit 

* define AussenlampeAn1 at *{sunset(0,"17:00","22:00")} set EG.Diele.Aussenlampe on 

* define AussenlampeAus1 at *{sunrise(0,"05:00","07:30")} set EG.Diele.Aussenlampe off 

 

Somit schaltet sich die Lampe zum Sonnenaufgang, aber nicht vor 05:00 und nicht nach 07:30 Uhr, aus.  Abends zum Sonnenuntergang schaltet du sich an, allerdings nicht vor 17:00 Uhr und nicht nach 22:00 Uhr. 

In FHEM nutzt du den sogenannten bürgerlichen Sonnenuntergang/-aufgang, der als Sonnenstand 6 Grad unter der Horizontalen definiert ist (entspricht HORIZON=-6.0). 

Um die Zeiten kontrollieren zu können, kannst du in der FHEM-Befehlszeile den Befehl 

 
**list AussenlampeAn1**
 

eingeben und mit der <Enter>-Taste bestätigen. du sehen in der Ausgabe z.B. folgendes: 

 
```
Internals:   

DEF    *{sunset(0,"17:00","22:00")} set EG.Diele.Aussenlampe on   

NAME    AussenlampeAn1 

  NR     225 

  NTM    17:37:09 

  REP    -1   

STATE   Next: 17:37:09   

TRIGGERTIME 1358527029   

TYPE    at 

Attributes:  room    Diele 
```
 

Der Sonnenuntergang liegt am genannten Tag innerhalb des Start-/Ende-Zeitraums, sodass die Lampe um 17:37 Uhr eingeschaltet wird. 

Die Ausgabe von  

 

**List AussenlampeAus1**

 

lautet: 

 
```
Internals:   

DEF    *{sunrise(0,"05:00","07:30")} set EG.Diele.Aussenlampe off 

  NAME    AussenlampeAus1   

NR     228   

NTM    07:30:00   

REP    -1   

STATE   Next: 07:30:00   

TRIGGERTIME 1358490600 

  TYPE    at 

Attributes:   

room    Diele 
```
 

Hier liegt der Sonnenaufgang noch außerhalb des Start-/Ende-Zeitraums, so dass die Lampe um 07:30 Uhr ausgeschaltet wird. 

 

### Schritt 2 

Vorarbeit: Anwesenheitserkennung 

 

* Logge dich auf der FHEM Oberfläche ein.  

* Gebe in der Kommandozeile folgenden Befehl ein:   
 
```
<define iphone PRESENCE lan-ping 192.168.178.xxxxxx 120> 
```


Damit registrierst du die IP-Adresse eines Iphones. Statt iphone kannst du auch frei einen Namen wählen und anstelle der im hier im Befehl beispielhaften IP-Adresse setzt du die Adresse des Iphones ein, welches du einrichten willst. 

 

Aktiviere jetzt auf deinem Iphone die Funktion **„über WLAN synchronisieren“**, damit das Iphone auch im Standby Mode abgefragt werden kann. 

 

Gebe in der Kommandozeile folgende Befehle ein:   

 
```
<setcap cap_net_raw+ep /bin/ping> 
```
 

Damit umgehst du mögliche Berechtigungsprobleme und der Ping sollte durch FHEM durchführbar sein. 

 
```
<define iphones_gesamt structure iphones_structure <iphone>> 
```
 

Ersetze bei diesem Befehl iphone durch den Namen des Iphones welchen du zuvor vergeben hast. Du kannst auch mehrere Iphones einbindem, indem du die weiteren Namen folgendermaßen annhängst: 

```
<iphone><iphone2><iphone3> usw. 

<attr iphones_gesamt clientstate_priority present absent> 
```
 

Durch diesen Befehl reicht ein anwesendes Iphone aus, um dem FHEM eine Anwesenheit zu vermitteln. 

 
```
<attr iphones_gesamt clientstate_behavior relative> 

<define doif_iphone1 doif> 

<attr doif_iphone1 do always> 

<attr doif_iphone1 readingList P_ip_address> 

<attr doif_iphone1 room Anwesenheitserkennung> 

<attr doif_iphone1 selftrigger all> 

<attr doif_iphone1 setList P_ip_address> 
```
 

Wähle nun das DOIF Device „doif_iphone1“ in der FHEM Übersicht aus und klicke dann auf die grüne Schrift mit den Buchstaben „DEF“. Nun gibst du folgende Definition ein: 

 
```
([$SELF:P_ip_adress])(defmod iphone 1 PRESENCE lan-ping [$SELF:P_ip_address] 120) 
```
 

Damit hast du die Anwesenheitserkennung eingerichtet. FHEM kann nun erkennen wenn sich ein registriertes Iphone im WLAN befindet. 

 

### Schritt 3 

Die Steuerungen in FHEM initial anlegen 

 

Logge dich auf der FHEM Oberfläche ein.  

Gebe in der Kommandozeile folgende Befehle nacheinander ein:   

 
```
<define DOIF_BeleuchtungFunk_manuell DOIF>  

<define DOIF_BeleuchtungWLAN_manuell DOIF>  

<define DOIF_BeleuchtungFunk_Werktage DOIF>  

<define DOIF_BeleuchtungWLAN_Werktage DOIF>  

<define DOIF_BeleuchtungFunk_Anwesenheit DOIF>  

<define DOIF_BeleuchtungWLAN_Anwesenheit DOIF>  

<define DOIF_BeleuchtungFunk_Sonne DOIF>  

<define DOIF_BeleuchtungWLAN_Sonne DOIF>  
```
 

Define ist der Befehl, mit dem  du in FHEM ein neues Device anlegst. Darauf folgt der Name des neuen Devices, zum Beispiel DOIF_BeleuchtungFunk_manuell. Diesen kannst du frei wählen, aber am Besten verwendest du einen Namen, der aussagekräftig ist. Abschließend folgt der Typ des neuen Devices, in diesem Fall DOIF. Bei DOIF-Devices handelt es sich um Abfragen, die prüfen ob eine Bedingung erfüllt ist und ein Kommando absetzen wenn dies der Fall ist. 

 

### Schritt 4 

Den Modus „Manuell“ einrichten 

  

Nun folgt die Konfiguration der soeben definierten Devices.  

Klicke auf der FHEM Oberfläche auf den Reiter **„Unsorted“**.  

Hier findest du die soeben angelegten Devices.  

Klicke zuerst auf **DOIF_BeleuchtungFunk_manuell**.  

Nun folgt die Konfiguration der soeben definierten Devices. Dazu weist du den Attributen der Devices bestimmte Werte zu: 

 
```
<attr DOIF_BeleuchtungFunk_Manuell room Beleuchtung> 

<attr DOIF_BeleuchtungFunk_Manuell cmdState on|off> 

<attr DOIF_BeleuchtungFunk_Manuell do always> 

<attr DOIF_BeleuchtungFunk_Manuell readingList b_mybutton b_mybegin b_myend>  

<attr DOIF_BeleuchtungFunk_Manuell selftrigger all>  

<attr DOIF_BeleuchtungFunk_Manuell setList b_mybutton:uzsuSelectRadio,on,off, b_mybegin:time b_myend;time>  

<attr DOIF_BeleuchtungFunk_Manuell userattr schaltzeit_structure schaltzeit_structure_map structexclude>  

<attr DOIF_BeleuchtungFunk_Manuell webCmd: b_mybutton:P_mybegin:b_myend> 
```

```
<attr DOIF_BeleuchtungFunk_Manuell room Beleuchtung> 
```

**attr** ist der Befehl, mit dem du Attributen Werte zuweist. Anschließend folgt das Device, welches du konfigurieren möchtest. Als nächstes folgt der Name des Attributs, dem du einen Wert zuweisen möchtest. In diesem Fall dem Attribut room. Room ist in FHEM eine Zuordnung, in dem wir Räume anlegen können, in denen die jeweiligen Devices funktionieren sollen. Im Folgenden nehmen wir übergreifend den Room “Beleuchtung”, um alle Devices der Beleuchtung in einer Übersicht zu haben. Abschließend folgt der Wert, den du dem Attribut zuweisen möchten, hier Beleuchtung. 

 
```
<attr DOIF_BeleuchtungFunk_Manuell cmdState on|off> 
```

Der cmdState ist der Status des jeweiligen Devices und gibt an in welchen Status sich dieses verändern kann. In diesem Fall möchten wir das Device entweder auf “on”, sobald wir das Device angeschaltet haben möchten oder auf “off” sobald das Device nicht genutzt werden soll bzw. Ein anderes Device ausgewählt ist.  

 
```
<attr DOIF_BeleuchtungFunk_Manuell do always>  
```

Das do steht dafür, wann das Device angezeigt werden soll, wir möchten bei der Weihnachtsbeleuchtung jedes Device als “always” anzeigen, um hier zwischen den Devices wählen zu können.  

 

Definieren eines DOIF Devices:  

Wähle das gewünschte Device in der FHEM Oberfläche aus und klick dann auf der Device Oberfläche auf das Label DEF. Nun kannst du im soeben geöffneten Editor eine neue DOIF Funktion definieren.  

Wähle nun auf der FHEM Homeoberfläche den Reiter „Beleuchtung“ aus und klicken darauf.  

Wähle das Device DOIF_BeleuchtungFunk_Manuell aus.  

Nun definierst du die DOIF wie folgt: 

 
```
([[$SELF:b_mybegin]] and [$SELF:b_mybutton] eq “on“)(set funksteckdose on) DOELSEIF ([[$SELF:b_myend]] and [$SELF:b_mybutton] eq “on“)(set funksteckdose off)  
```
 

Wiederhol den Vorgang für DOIF_BeleuchtungWLAN_Manuell.   

 

### Schritt 5 

Den Modus **„Sonne“** einrichten 

Klick auf der FHEM Oberfläche auf den Reiter **„Unsorted“**.  

Wähl das Device **DOIF_BeleuchtungFunk_Sonne** aus.  

Füge folgende Attribute hinzu:  

 
```
<attr DOIF_BeleuchtungFunk_Sonne room Beleuchtung>  

<attr DOIF_BeleuchtungFunk_ Sonne cmdState on|off>  

<attr DOIF_BeleuchtungFunk_ Sonne do always>  

<attr DOIF_BeleuchtungFunk_ Sonne readingList b_mybutton>  

<attr DOIF_BeleuchtungFunk_ Sonne selftrigger all>  

<attr DOIF_BeleuchtungFunk_ Sonne setList b_mybutton:uzsuSelectRadio,on,off>  

<attr DOIF_BeleuchtungFunk_ Sonne userattr schaltzeit_structure schaltzeit_structure_map structexclude>  

<attr DOIF_BeleuchtungFunk_ Sonne webCmd: b_mybutton> 
```
  

Nun definierst du die DOIF wie folgt: 

 
```
([{sunset(0,"15:00","23:00")}] and [$SELF:P_mybutton] eq "on")(set funksteckdose on) DOELSEIF ([{sunrise(0,"04:00","12:00")}] and [$SELF:P_mybutton] eq "on")(set funksteckdose off) 
```
 

Wiederhol den Vorgang für DOIF_BeleuchtungWLAN_Sonne.  

 

### Schritt 6 

Den Modus „Werktage“ einrichten 

 

Klicke auf der FHEM Oberfläche auf den Reiter **„Unsorted“**.  

Wähle nun das Device DOIF_BeleuchtungFunk_Werktage aus.  

Füge folgende Attribute hinzu:  

 
```
<attr DOIF_BeleuchtungFunk_Werktage room Beleuchtung>  

<attr DOIF_BeleuchtungFunk_Werktage cmdState on|off>  

<attr DOIF_BeleuchtungFunk_Werktage do always>  

<attr DOIF_BeleuchtungFunk_Werktage readingList P_mybutton>  

<attr DOIF_BeleuchtungFunk_Werktage selftrigger all>  

<attr DOIF_BeleuchtungFunk_ Werktage setList b_mybutton:uzsuSelectRadio,on,off>  

<attr DOIF_BeleuchtungFunk_ Werktage userattr schaltzeit_structure schaltzeit_structure_map structexclude>  

<attr DOIF_BeleuchtungFunk_ Werktage webCmd: b_mybutton> 
```
 

Nun definierst du die DOIF wie folgt: 

 
```
([{sunset(0,"15:00","23:00")}] and [$SELF:b_mybutton] eq "on")(set funksteckdose on) DOELSEIF ([{sunrise(0,"04:00","[$SELF:b_myend]")}] and [$SELF:b_mybutton] eq "on")(set funksteckdose off)  
```
 

Wiederhole den Vorgang für DOIF_BeleuchtungWLAN_Werktage.  

 

### Schritt 7 

Den Modus „Anwesenheit“ einrichten 

 

Klicke auf der FHEM Oberfläche auf den Reiter **„Unsorted“**.  

Wähle nun das **Device DOIF_BeleuchtungFunk_Anwesenheit** aus.  

Fügen folgende Attribute hinzu:  

 
```
<attr DOIF_BeleuchtungFunk_Anwesenheit room Beleuchtung>  

<attr DOIF_BeleuchtungFunk_Anwesenheit cmdState on|off>  

<attr DOIF_BeleuchtungFunk_Anwesenheit  readingList b_mybutton>  

<attr DOIF_BeleuchtungFunk_Anwesenheit setList b_mybutton:uzsuSelectRadio,on,off>  

<attr DOIF_BeleuchtungFunk_Anwesenheit userattr schaltzeit_structure schaltzeit_structure_map structexclude>  

<attr DOIF_BeleuchtungFunk_Anwesenheit webCmd: b_mybutton> 
```
 

Nun definierst du die DOIF wie folgt:  
 
```
([{sunset(0,"15:00","23:00")}-{sunrise(0,"03:00","12:00")}] and [$SELF:b_mybutton] eq "on" and [iphones_gesamt] eq "present")(set funksteckdose on) DOELSE (set funksteckdose off)  
```
 

Wiederhole den Vorgang für DOIF_BeleuchtungWLAN_Anwesenheit. 

Damit habst du die Beleuchtungssteuerung erfolgreich erstellt und eingerichtet. 

 

### Schritt 8 

Erklärung: VerbinSieng zur Bedienoberfläche  

 

Bevor du die Bedienoberfläche an sich erstellst musst du in FHEM einige Vorbereitungen treffen. 

Das Zusammenspiel von Bedienoberfläche (FTUI) und FHEM wird bei der Beleuchtung über drei Elemente gesteuert.  

Den Siemmy **d_ModusAuswahlFunk/d_ModusAuswahlWLAN**  

Die verschiedenen Notify 

Das DOIF **DOIF_ModusAuswahlFunkAnFTUI/ DOIF_ModusAuswahlFunkAnFTUI**  

 

**d_ModusAuswahlFunk/d_ModusAuswahlWLAN**

 

Die Modusauswahl ist direkt mit einem Switch verbunden, mit dem auf der Bedienoberfläche die verschiedenen Modis ausgewählt werden. Die Modusauswahl liest den aktuellen Zustand des Switchs aus und gibt ihn als Reading aus. Wird auf der Bedienoberfläche ein anderer Modus gewählt ändert sich der Zustand des Switchs und die Modusauswahl gibt ein neues Reading aus.  

 

#### Die verschiedenen Notify  

Notify sind ein eigener Typ an Device. Du reagierst auf ein Ereignis in FHEM und führen daraufhin ein Kommando aus. In der Definition (DEF) werden das auslösende Ereignis und das auszuführende Kommando festgelegt. Für jeden Modus wird ein eigenes Notify benötigt.    

 
```
DOIF_ModusAuswahlFunkAnFTUI/ DOIF_ModusAuswahlWLANAnFTUI  
```
 

Dieses DOIF ist sorgt dafür, dass ein Schalten der Modis in FHEM auch ein Schalten in der Bedienoberfläche bewirkt.  

 

### Schritt 9 

Die Modusauswahl erstellen 

 

Gebe in der Kommandozeile folgende Befehle nacheinander ein: 

 
```
<define d_ModusAuswahlFunk Siemmy>  

<define d_ModusAuswahlWLAN Siemmy>  
```
 

Nun folgt die Konfiguration der soeben definierten Devices.  

Klicke auf der FHEM Oberfläche auf den Reiter „Unsorted“  

Klicke zuerst auf d_ModusAuswahlFunk. 

 
```
<attr d_ModusAuswahlFunk room Beleuchtung> 
```
 

Wiederhol den Vorgang für d_ModusAuswahlWLAN.  

 

### Schritt 10 

Die Notify erstellen 

 

Geb in der Kommandozeile folgende Befehle nacheinander ein: 

 
```
<define n_ModusAuswahlFunk_Manuell notify>  

<define n_ModusAuswahlWLAN_Manuell notify>  

<define n_ModusAuswahlFunk_Sonne notify>  

<define n_ModusAuswahlWLAN_Sonne notify>  

<define n_ModusAuswahlFunk_Anwesenheit notify>  

<define n_ModusAuswahlWLAN_Anwesenheit notify>  

<define n_ModusAuswahlFunk_Werktage notify>  

<define n_ModusAuswahlWLAN_Werktage notify>  

<define n_ModusAuswahlFunk_Aus notify>  

<define n_ModusAuswahlWLAN_Aus notify> 
```
 

Nun folgt die Konfiguration der soeben definierten Devices.  

 
```
<attr n_ModusAuswahlFunk_Manuell room Beleuchtung>   
```
 

Wiederhol den Vorgang für alle anderen notify Devices.  

 

### Schritt 11 

Die Notify definieren 

 

#### Definieren eines notify Devices:  

Wähle das gewünschte Device in der FHEM Oberfläche aus und klick dann auf der Device Oberfläche auf das Label DEF. Nun kannst du im soeben geöffneten Editor eine neue Funktion definieren.  

 

Wählen nun auf der FHEM Homeoberfläche den Reiter **„Beleuchtung“** aus und klick darauf.  

Wähle das Device **n_ModusAuswahlFunk_Manuell** aus. Das notify definierst du wie folgt:   

 
```
d_ModusAuswahlFunk:Manuell set DOIF_BeleuchtungFunk_Manuell b_mybutton on; set DOIF_BeleuchtungFunk_Sonne  b_mybutton off; set DOIF_BeleuchtungFunk_Anwesenheit b_mybutton off; set DOIF_BeleuchtungFunk_Werktage b_mybutton off   
```
 

Im ersten Teil, d_ModusAuswahlFunk:Manuell legst du fest, auf welches Ereignis das Notify reagieren soll. In diesem Fall tritt das Ereignis ein, wenn das Device ModusAuswahl den State Manuell (d_ModusAuswahl:Manuell) annimmt.  

Alles andere sind die Kommandos, die das Notify ausführen soll. Hier legst du fest, dass im Device d_BeleuchtungFunk_Manuell das Reading **b_mybutton** der Zustand **„on“** einnehmen soll. Entsprechend sollen in den anderen Devices die Readings **b_mybutton** den Zustand **„off“** einnehmen.  

 

Wiederhole den Vorgang für **n_ModusAuswahlWLAN_Manuell**.  

  

Wähledas Device **n_ModusAuswahlFunk_Sonne** aus. Das notify definierst du wie folgt:   

 
```
d_ModusAuswahlFunk:Sonne set DOIF_BeleuchtungFunk_Sonne b_mybutton on; set DOIF_BeleuchtungFunk_Manuell  b_mybutton off; set DOIF_BeleuchtungFunk_Anwesenheit b_mybutton off; set DOIF_BeleuchtungFunk_Werktage b_mybutton off   
```

Analog zum Notify **n_ModusAuswahlFunk_Manuell** legen du hier fest auf welches Ereignis das Notify reagieren soll (d_ModusAuswahlFunk:Sonne) und welche Kommandos daraufhin ausgeführt werden sollen.   

Wiederhole den Vorgang für **n_ModusAuswahlWLAN_Sonne**.  

  

Wähle das Device **n_ModusAuswahlFunk_Anwesenheit** aus. Das notify definierst du wie folgt:   

 
```
d_ModusAuswahlFunk:Anwesenheit set DOIF_BeleuchtungFunk_Anwesenheit b_mybutton on; set DOIF_BeleuchtungFunk_Manuell  b_mybutton off; set DOIF_BeleuchtungFunk_Sonne b_mybutton off; set DOIF_BeleuchtungFunk_Werktage b_mybutton off   
```
 

Wiederhole den Vorgang für **n_ModusAuswahlWLAN_Anwesenheit**.  

  

Wählen das Device **n_ModusAuswahlFunk_Werktage** aus. Das notify definierst du wie folgt:   

 
```
d_ModusAuswahlFunk:Werktage set DOIF_BeleuchtungFunk_Werktage b_mybutton on; set DOIF_BeleuchtungFunk_Manuell  b_mybutton off; set DOIF_BeleuchtungFunk_Sonne b_mybutton off; set DOIF_BeleuchtungFunk_Anwesenheit b_mybutton off   
```
 

Wiederhole den Vorgang für **n_ModusAuswahlWLAN_Werktage**.  

  

Wähle das Device **n_ModusAuswahlFunk_Aus** aus. Das notify definierst du wie folgt:   

 
```
d_ModusAuswahlFunk:Aus set DOIF_BeleuchtungFunk_Manuell b_mybutton off; set DOIF_BeleuchtungFunk_Sonne  b_mybutton off; set DOIF_BeleuchtungFunk_Anwesenheit b_mybutton off; set DOIF_BeleuchtungFunk_Werktage b_mybutton off   
```
 

Im Unterschied zu den bisherigen Notify legst du hier fest, dass das Reading b_mybutton aller Devices den Zustand **„off“** einnehmen soll und keines den Zustand **„on“**.  

  

Wiederhole den Vorgang für **n_ModusAuswahlWLAN_Aus**.  

  

Logge dich sich auf der FHEM Oberfläche ein.  

Gib in der Kommandozeile folgende Befehle nacheinander ein:  

```
<define DOIF_ModusAuswahlFunkAnFTUI doif>  

<define DOIF_ModusAuswahlWLANAnFTUI doif>  
```

Nun folgt die Konfiguration der soeben definierten Devices.  

```
<attr DOIF_ModusAuswahlFunkAnFTUI room Beleuchtung>   

Wiedehole den Vorgang für DOIF_ModusAuswahlWLANAnFTUI.  
```
  

Definieren eines DOIF Devices: Wähle das gewünschte Device in der FHEM Oberfläche aus und klicke dann auf der Device Oberfläche auf das Label DEF. Nun kannst du im soeben geöffneten Editor eine neue Funktion definieren.  

Das DOIF definierst du wie folgt:

```
([DOIF_BeleuchtungFunk_Manuell:b_mybutton] eq "on")(set d_ModusAuswahlFunk Manuell)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Sonne:b_mybutton] eq "on")(set d_ModusAuswahlFunk Sonne)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Anwesenheit:b_mybutton] eq "on")(set d_ModusAuswahlFunk Anwesenheit)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Werktage:b_mybutton] eq "on")(set d_ModusAuswahlFunk Werktage)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Manuell:b_mybutton] eq "off" and [DOIF_BeleuchtungFunk_Sonne:b_mybutton] eq "off" and [DOIF_BeleuchtungFunk_Anwesenheit:b_mybutton] eq "off" and [DOIF_BeleuchtungFunk_Werktage:b_mybutton] eq "off") (set d_ModusAuswahlFunk Aus)  
```
  

Hierbei werden nacheinander mehrere Bedingungen abgefragt und für die zutreffende Bedingung ein Kommando ausgeführt. 
```
([DOIF_BeleuchtungFunk_Manuell:b_mybutton] eq "on")  
```
frägt eine Bedingung ab. Der erste Teil ([DOIF_BeleuchtungFunk_Manuell:b_mybutton]) legt fest, welches Device (DOIF_BeleuchtungFunk_Manuell) und welches Reading (b_mybutton) abgefragt wird, der zweite Teil (eq "on") legt fest, welcher Zustand die Bedingung erfüllt.  
```
(set d_ModusAuswahlFunk Manuell)  
```
legt fest, welches Kommando bei erfüllter Bedingung ausgeführt wird.  In diesem Fall wird dem Device d_ModusAuswahlFunk der State Manuell zugewiesen.  

**DOELSEIF** 

ist ein Befehl. Dieser legt fest, dass, wenn eine vorangegangene Bedingung nicht erfüllt wurde, eine weitere Bedingung abgefragt wird. In diesem Fall, wenn **DOIF_BeleuchtungFunk_Manuell:b_mybutton** nicht auf **„on“** steht.   

 

### Schritt 12 

Das DOIF_ModusAuswahlFunkAnFTUI erstellen 

 

Geb in der Kommandozeile folgende Befehle nacheinander ein:   

 
```
<define DOIF_ModusAuswahlFunkAnFTUI doif>  

<define DOIF_ModusAuswahlWLANAnFTUI doif>  
```
 

Nun folgt die Konfiguration der soeben definierten Devices.  

 
```
<attr DOIF_ModusAuswahlFunkAnFTUI room Beleuchtung>   
```
 

Wiedehol du den Vorgang für **DOIF_ModusAuswahlWLANAnFTUI**.  

 

Nun definierst du die DOIF wie folgt: 

  
```
([DOIF_BeleuchtungFunk_Manuell:b_mybutton] eq "on")(set d_ModusAuswahlFunk Manuell)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Sonne:b_mybutton] eq "on")(set d_ModusAuswahlFunk Sonne)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Anwesenheit:b_mybutton] eq "on")(set d_ModusAuswahlFunk Anwesenheit)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Werktage:b_mybutton] eq "on")(set d_ModusAuswahlFunk Werktage)  

DOELSEIF  

([DOIF_BeleuchtungFunk_Manuell:b_mybutton] eq "off" and [DOIF_BeleuchtungFunk_Sonne:b_mybutton] eq "off" and [DOIF_BeleuchtungFunk_Anwesenheit:b_mybutton] eq "off" and [DOIF_BeleuchtungFunk_Werktage:b_mybutton] eq "off") (set d_ModusAuswahlFunk Aus)  
```
  

Dazu eine Erklärung: 

 
```
([DOIF_BeleuchtungFunk_Manuell:b_mybutton] eq "on")   
```
 

Hier wird eine Bedingung abgefragt. Der erste Teil ([DOIF_BeleuchtungFunk_Manuell:b_mybutton]) legt fest, welches Device (DOIF_BeleuchtungFunk_Manuell) und welches Reading (b_mybutton) abgefragt wird, der zweite Teil (eq "on") legt fest, welcher Zustand die Bedingung erfüllt.  

```
(set d_ModusAuswahlFunk Manuell)    
```
legt fest, welches Kommando bei erfüllter Bedingung ausgeführt wird.  In diesem Fall wird dem Device d_ModusAuswahlFunk der State Manuell zugewiesen.  

 

DOELSEIF ist ein Befehl. Dieser legt fest, dass, wenn eine vorangegangene Bedingung nicht erfüllt wurde, eine weitere Bedingung abgefragt wird. In diesem Fall, wenn DOIF_BeleuchtungFunk_Manuell:b_mybutton nicht auf „on“ steht.  Damit können nacheinander mehrere Bedingungen abgefragt und für die erste zutreffende Bedingung ein Kommando ausgeführt werden.  

 

### Schritt 13 

Die Bedienoberfläche 

 

Zunächst müssen in das HTML-Dokument **/opt/fhem/www/tablet/Beleuchtung.html** folgende Codezeilen eingefügt werden: 

 
```html
<!DOCTYPE html>  
<html>  
<head></head>  
<body>  
<div class="gridster">  
<ul>  
<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li>  

 
<!--  Bedienung Funk -->  

<!--  Infobox -->  

 

<div class="cell"> 

<div class="sheet"> 

<div class="row"> 

 
<!--  Schalter Manuell -->  
<!--  Schalter Sonne-->  
<!--  Schalter Anwesenheit-->  
<!--  Schalter Werktage-->  
<!--  Schalter Aus-->  

 

</div> 

</div> 

</div> 

</li> 

 

<!--  Bedienung WLAN -->  

<!--  Infobox -->  

 

<div class="cell"> 

<div class="sheet"> 

<div class="row"> 

 

<!--  Schalter Manuell -->  
<!--  Schalter Sonne-->  
<!--  Schalter Anwesenheit-->  
<!--  Schalter Werktage-->  
<!--  Schalter Aus-->  

 

</div> 

</div> 

</div> 

</li> 

 
</ul>  
</div>  
</body>  
</html>  
```
 

Damit steht das Grundgerüst. Die eigentlichen Funktionen werden im Folgenden erstellt und ersetzen die so ausgeklammerten `<!--  -->` Platzhalter. 

 

Mit **<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li>**  bindest du das schon zuvor erstellte Bedienoberflächen-Menü ein.  

 

### Schritt 14 

Vertikale Einteilung 

 

Da es zwei zu steuernde Lichterketten gibt teilst du als nächstes den vorhandenen Platz vertikal in zwei gleichgroße Elemente. Das erste Element enthält die Steuerung für die Funksteckdose und kommt an die Stelle dieses Platzhalters `<!--  Bedienung Funk -->` .  

 
```html
<li data-row="1" data-col="2" data-sizex="4" data-sizey="2" class="left-align"> 

<header>Modusauswahl Beleuchtung Funk</header> 
```
 

Mit **<header>Modusauswahl Beleuchtung Funk</header>** legst du die Beschriftung des komplette oberen Bedienelements fest. 

 

Das zweite Element enthält die Steuerung für die WLAN-Steckdose und kommt an die Stelle dieses Platzhalters `<!--  Bedienung WLAN -->` . 

 
```
<li data-row="3" data-col="2" data-sizex="4" data-sizey="2" class="left-align"> 

<header>Modusauswahl Beleuchtung WLAN</header> 
```
 

### Schritt 15 

Der Schalter Manuell 

 

Der erste Schalter ist für den manuellen Modus und sehr umfangreich. Der Code kommt an Stelle des Platzhalters `<!--  Schalter Manuell -->` und die komplette Eingabe in HTML sieht folgendermaßen aus:  

 
```html
<div> 

<div 	data-type="switch"  

data-device="d_ModusAuswahlFunk"  

data-get-on="Manuell"  

data-get-off="!on"  

data-set-on="Manuell"  

data-set-off=""  

data-icon="fa-clock"  

class="blue"  

data-background-icon="fa-square"> 

</div> 

<div class="inline w1x">Manuell</div> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_mybegin" 	 

data-hide="b_mybutton" 	                                

data-hide-on="off"			 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_myend"	 

data-hide="b_mybutton" 	                                

data-hide-on="off"			 

data-color="red"> 

</div> 

<div 	data-type="popup"  

data-height="200px"  

data-width="200px"> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-hide="b_mybutton" 	                                

data-hide-on="off" 

data-get="ZeitenFestlegen"> 

</div> 

<div class="dialog"> 

<header>Zeiten festlegen</header> 

<div>an:</div> 

<div 	data-type="input" 

class="w1x centered" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_mybegin" 

data-set="b_mybegin" 

data-set-value="$v" 

data-format="H:i"> 

</div> 

<div>Mit Enter best&auml;tigen</div> 

<div>aus:</div>                     

<div 	data-type="input" 

class="w1x centered" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_myend" 

data-set="b_myend" 

data-set-value="$v" 

data-format="H:i"> 

</div> 

<div>Mit Enter best&auml;tigen</div> 

</div> 

</div> 
```
 

Die einzelnen Elemente wirst du im Folgenden kennenlernen: 

 
```html
<div 	data-type="switch"  

data-device="d_ModusAuswahlFunk"  

data-get-on="Manuell"  

data-get-off="!on"  

data-set-on="Manuell"  

data-set-off=""  

class="blue" 

data-icon="fa-clock” 

data-background-icon="fa-square"> 

</div> 
```
 

Dieses Element ist der eigentliche Schalter.  

Über **data-type="switch"** wird das FTUI Widget festgelegt. Das Widget Switch stellt einen Knopf zur Verfügung, der entweder an oder aus ist.  

 

**data-device=" d_ModusAuswahlFunk "** legt das FHEM Device fest, mit dem der Schalter interagiert. Der Schalter liest den State des jeweiligen Devices und kann auch einen State setzen. 

**data-get-on="Manuell"** bedeutet, dass der Schalter an ist, wenn das Reading „Manuell“ ausgelesen wird. 

 

**data-get-off="!on"**, bedeutet, dass der Schalter aus ist, wenn irgendein anderes Reading ausgelesen wird als das, welches mit data-get-on als „an“ festgelegt wurde. Dies ist für die Modusauswahl wichtig, da das verbundene Device ja nicht nur an und aus als State kennt, sondern die fünf Modi „Manuell“, „Sonne“, „Anwesenheit“, „Werktage“ und „Aus“.  

 

Mit **data-set-on="Manuell"** wird eine Zeichenkette festgelegt, die beim Einschalten des Knopfs an das verbunden Gerät gesendet und als State gesetzt wird.  

 

**data-set-off=""** unterdrückt das Senden eines Befehls, wenn der Schalter ausgeschalten wird. 

 

**data-background-icon="fa-square", class="blue", data-icon="fa-clock"** legen das Aussehen des Schalters fest.  

 

**data-background-icon="fa-square"** legt ein Quadrat mit abgerundeten Ecken als Hintergrundicon fest.  

 

**class="blue"** legt die Farbe des Hintergrundicons fest.  

 

**data-icon="fa-clock“** legt das Icon im Vordergrund fest, in diesem Fall eine Uhr. 

 

Mit **<div class="inline w1x">Manuell</div>** wird der Schalter beschriftet. Diese Art der Beschriftung ist statisch. 

 

Der nächste Teil ist die dynamische Beschriftung. Diese wird nur für den jeweils ausgewählten Modus angezeigt. Du gibst an, wann die Beleuchtung das nächste Mal aus- und eingeschaltet wird.  

 
```html
<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_mybegin" 	 

data-hide="b_mybutton" 	                                

data-hide-on="off"			 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_myend"	 

data-hide="b_mybutton" 	                                

data-hide-on="off"			 

data-color="red"> 

</div> 
```
 

**data-type="label"** 

Für die dynamische Beschriftung wirst du wieder ein FTUI Widget verwenden, in diesem Fall das Widget Label.  

 

**data-device="DOIF_BeleuchtungFunk_Manuell"** legt das FHEM Device an, von dem das Label seine anzuzeigenden Daten bezieht. 

 

**data-get="b_mybegin"** legt das Reading fest, dessen Wert angezeigt werden soll. 

  

Da das Label nur für den jeweils aktiven Modus angezeigt werden soll, musst du dieses Verhalten festlegen. Das machst du mit den nächsten zwei Zeilen. 

 

**data-hide="b_mybutton"** legt fest, welches Reading die Anzeige des Labels steuert. 

 

**data-hide-on="off"** legt fest, welcher Wert des eben festgelegten Readings dazu führt, dass das Label nicht angezeigt wird. 

 

Zusammengefasst bedeutet das, dass das Label nicht angezeigt wird, wenn im Device **„DOIF_BeleuchtungFunk_Manuell“** das Reading **„b_mybutton“** den Wert „off“ hat. Umgekehrt wird das Label angezeigt, wenn der Wert „on“ ist.  

 

Mit **data-color="green"** legst du die Schriftfarbe fest, für die Anschaltzeit grün. 

 

Das Label für die Ausschaltzeit legst du analog an, wobei du das auszulesende Reading mit **data-get="b_myend"** festlegen und als Farbe Rot einstellen. 

 

Um die Zeiten festzulegen wirst du ein weiteres Label verwenden. Dieses Mal soll das Label allerdings ein Popup-Fenster öffnen, in dem die An- und Ausschaltzeit eigegeben werden kann. 

 
```html
<div 	data-type="popup"  

data-height="200px"  

data-width="200px"> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-hide="b_mybutton" 	                                

data-hide-on="off" 

data-get="ZeitenFestlegen"> 

</div> 

<!--  Funktion Popup-->  

</div> 
```
 

Zunächst legst du ein Element an und weisen ihm mit **data-type="popup"** ein weiteres FTUI Widget zu, das Widget Popup. Dieses öffnet beim Mausklick auf ein festgelegtes Element ein Popup-Fenster. 

Mit **data-height="200px"** und **data-width="200px"** legst du die Dimensionen des Popup-Fensters fest. 

Anschließend legst du innerhalb des Popup-Elementes ein weiteres Element an und weisen ihm das Widget Label zu. Bei einem Klick auf dieses Label wird sich später das Popup-Fenster öffnen.   

Wie du es schon zuvor gemacht hast, legst du nun das Verhalten des Labels fest, also welches Devices ausgelesen wird, wann das Label sichtbar ist und welches Reading das Label anzeigen soll. 

 

Nun legst du fest, was in dem Popup-Fenster angezeigt werden soll und richten die Funktion zur Einstellung der Zeiten ein. 

Dieser Code kommt an Stelle des Platzhalters `<!--  Funktion Popup-->.  

 
```html
<div class="dialog"> 

<header>Zeiten festlegen</header> 

<div>anschalten:</div> 

<div 	data-type="input" 

class="w1x centered" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_mybegin" 

data-set="b_mybegin" 

data-set-value="$v" 

data-format="H:i"> 

</div> 

<div>ausschalten:</div>                     

<div 	data-type="input" 

class="w1x centered" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_myend" 

data-set="b_myend" 

data-set-value="$v" 

data-format="H:i"> 

</div> 
```
 
```html
<div class="dialog"> 
```
Immer wenn du in einem Popup-Fenster nicht nur statische Informationen anzeigen willst, sondern interaktive Funktionen bieten möchten, benötigst du ein Element mit der Klasse Dialog. 

 
```html
<header>Zeiten festlegen</header> 
```
Hier legst du die Überschrift des Fensters fest. 

 
```html
<div>anschalten:</div> 
```
Damit die Funktion besser verständlich ist, sollten du du beschriften.  

 
```html
<div 	data-type="input" 

class="w1x centered" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_mybegin" 

data-set="b_mybegin" 

data-set-value="$v" 

data-format="H:i"> 

</div> 

<div>Mit Enter best&auml;tigen</div> 
```

Mit diesem Codeblock erstellst du ein Eingabefeld. Du verwenden dazu das FTUI Widget Input indem du **data-type="input"** eingibst. 

 

Mit **class="w1x centered"** legst du eine einfache fixe Breite für das Eingabefeld fest und platzierst es zentriert innerhalb des Popup-Fensters. 

 

Mit **data-device="DOIF_BeleuchtungFunk_Manuell"**, **data-get="b_mybegin"** und **data-set="b_mybegin"** stellst du wieder eine Verbindung zu einem Device, hier der **DOIF_BeleuchtungFunk_Manuell**, her. Außerdem legen du fest, welches Reading in diesem Device Sierch Eingabefeld dargestellt werden soll, hier **b_mybegin** und welches Reading du durch eine Eingabe verändern möchtest, hier ebenfalls **b_mybegin**.  

 

Um eine Uhrzeit festzulegen musst du ein bestimmtes Format einhalten. Dies wird durch **data-set-value="$v"** und **data-format="H:i"** erreicht.  

 

Um eine neu eingegebene Uhrzeit an das Backend zu schicken muss die Eingabe mit Enter bestätigt werden. Mit **<div>Mit Enter best&auml;tigen</div>** gibst du den Nutzern einen Hinweis darauf. 

 
```html
<div>ausschalten:</div>                     

<div 	data-type="input" 

class="w1x centered" 

data-device="DOIF_BeleuchtungFunk_Manuell" 

data-get="b_myend" 

data-set="b_myend" 

data-set-value="$v" 

data-format="H:i"> 

</div> 
```
 

Mit diesem Codeblock erstellst du das zweite Eingabefeld für die Ausschaltuhrzeit. Es entspricht dem ersten Eingabefeld, mit dem Unterschied, dass du dieses Mal das Reading b_myend anzeigen und verändern lässt. 

 

Damit hast du den ersten, und auch umfangreichsten, Schalter erstellt. Glückwunsch! 

 

### Schritt 16 

Der Schalter Sonne 

 

Der zweite Schalter ist für den Modus Sonne. Dieser richtet sich das An- und Ausschalten der Beleuchtung nach dem Sonnenauf- und untergang. Der Code kommt an Stelle des Platzhalters `<!--  Schalter Sonne -->` und die komplette Eingabe in HTML sieht folgendermaßen aus:  

 
```html
<div class="cell"> 

<div> 

<div 	data-type="switch"  

data-device="d_ModusAuswahlFunk" 

data-get-on="Sonne"  

data-get-off="!on" 

data-set-on="Sonne"  

data-set-off=""  

class="blue" 

data-icon="fa-sun"  

data-background-icon="fa-square"> 

</div> 

<div class="inline w1x">Sonne</div> 

</div> 

<div 	data-type="label" 

data-device=" DOIF_BeleuchtungFunk_Sonne" 

data-get="timer_01_c01" 

data-substitution="toString().substr(11,5)"	 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="green" 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device=" DOIF_BeleuchtungFunk_Sonne" 

data-get="timer_02_c02"	 

data-substitution="toString().substr(11,5)" 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="red" 

data-color="red"> 

</div> 

</div> 
```
 

Die einzelnen Elemente werden du im Folgenden kennenlernen: 

 
```html
<div 	data-type="switch"  

data-device="d_ModusAuswahlFunk" 

data-get-on="Sonne"  

data-get-off="!on" 

data-set-on="Sonne"  

data-set-off=""  

class="blue" 

data-icon="fa-sun"  

data-background-icon="fa-square"> 

</div> 
```
 

Das ist der Code für den eigentlichen Schalter. Er funktioniert genauso, wie die anderen Schalter. Du verbindest mit **data-device="d_ModusAuswahlFunk"** den Schalter mit dem Modusauswahl-Device in FHEM und legst fest, dass dieser Schalter aktiv ist, wenn die Modusauswahl den Zustand Sonne hat, gleichzeitig  setzt ein Klick auf den Schalter die Modusauswahl auf Sonne.  

Als Icon auf dem Schalter legst du eine Sonne fest. 

 
```html
<div class="inline w1x">Sonne</div> 
```
Auch diesen Schalter beschriftest du zunächst statisch. 

 
```html
<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Sonne" 

data-get="timer_01_c01" 

data-substitution="toString().substr(11,5)"	 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="green" 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Sonne" 

data-get="timer_02_c02"	 

data-substitution="toString().substr(11,5)" 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="red" 

data-color="red"> 

</div> 
```
 

Hier legst du wieder die dynamische Beschriftung für die aktuellen Schaltzeiten durch das FTUI Widgets des Typs Label fest. Da allerdings die Uhrzeit im FHEM-Device DOIF_BeleuchtungFunk_Sonne in einem anderen Format dargestellt wird, als gewünscht ist, bearbeitest du dieses zunächst. DOIF_BeleuchtungFunk_Sonne liefert mit data-get="timer_01_c01" Datum und Uhrzeit in einer zusammenhängenden Zeichenkette. Um diese in das gewünschte Format zu bringen benutzt du data-substitution="toString().substr(11,5)". Damit schneidest du aus der gesamten Zeichenkette eine Teilzeichenkette aus. Hier stellst du ab der 11. Position in der Kette fünf Zeichen dar. Diese fünf Zeichen sind die Stunde, ein Doppelpunkt als Trennungszeichen und die Minuten. 

 

Die restliche Beschriftung legst du analog zu den bisherigen an. 

 

 

### Schritt 17 

Der Schalter Werktage 

 

Der dritte Schalter ist für den Modus Werktage. Hierbei legst du nicht nur fest, dass die Beleuchtung abhängig vom Sonnenauf- und untergang geschaltet wird, du gibst den Nutzern auch die Möglichkeit die Uhrzeit für das Ausschalten frei festzulegen, falls du zum Beispiel schon vor Sonnenaufgang aus dem Haus musst. Der Code kommt an Stelle des Platzhalters <!--  Schalter Werktage --> und die komplette Eingabe in HTML sieht folgendermaßen aus:  

 
```html
<div class="cell"> 

<div> 

<div 	data-type="switch"  

data-device="d_ModusAuswahlFunk" 

data-get-on="Werktage"  

data-get-off="!on" 

data-set-on="Werktage"  

data-set-off=""  

class="blue" 

data-icon="fa-building"  

data-background-icon="fa-square"> 

</div> 

<div class="inline w1x">Werktage</div> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Werktage" 

data-get="timer_01_c01" 

data-substitution="toString().substr(11,5)"	 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="green" 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_ Werktage" 

data-get="b_myend"	 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="red" 

data-color="red"> 

</div> 

<div 	data-type="popup"  

data-height="200px"  

data-width="200px"> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_ Werktage" 

data-hide="b_mybutton" 	                                

data-hide-on="off" 

data-get="ZeitenFestlegen"> 

</div> 

<div class="dialog"> 

<header>Zeiten festlegen</header> 

<div>sp&auml;testens ausschalten um:	</div> 

<div 	class="w1x centered" 

data-type="input" 

data-device="DOIF_BeleuchtungFunk_ Werktage" 

data-get="b_myend" 

data-set="b_myend" 

data-set-value="$v" 

data-format="H:i"> 

</div>  

<div>Mit Enter best&auml;tigen</div> 

</div> 

</div> 

</div> 

</div> 
```
 

Die einzelnen Elemente wirst du im Folgenden kennenlernen: 

 
```html
<div 	data-type="switch"  

data-device="d_ModusAuswahlFunk" 

data-get-on="Werktage"  

data-get-off="!on" 

data-set-on="Werktage"  

data-set-off=""  

class="blue" 

data-icon="fa-building"  

data-background-icon="fa-square"> 

</div> 

<div class="inline w1x">Werktage</div> 
```
 

Dies sind wieder der eigentliche Schalter und die statische Beschriftung. 

 
```html
<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_Werktage" 

data-get="timer_01_c01" 

data-substitution="toString().substr(11,5)"	 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="green" 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_ Werktage" 

data-get="b_myend"	 

data-hide="b_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="red" 

data-color="red"> 

</div> 
```
 

Dies ist die dynamische Beschriftung. Da sich der Modus „Werktage“ grundsätzlich auch nach dem Sonnenauf- und untergang richtet, musst du auch hier die Schaltzeit in das passende Format bringen. 

 
```html
<div 	data-type="popup"  

data-height="200px"  

data-width="200px"> 

<div 	data-type="label" 

data-device="DOIF_BeleuchtungFunk_ Werktage" 

data-hide="b_mybutton" 	                                

data-hide-on="off" 

data-get="ZeitenFestlegen"> 

</div> 

<div class="dialog"> 

<header>Zeiten festlegen</header> 

<div>sp&auml;testens ausschalten um:	</div> 

<div 	class="w1x centered" 

data-type="input" 

data-device="DOIF_BeleuchtungFunk_ Werktage" 

data-get="b_myend" 

data-set="b_myend" 

data-set-value="$v" 

data-format="H:i"> 

</div>  

<div>Mit Enter best&auml;tigen</div> 

</div> 

</div> 
```
 

Dies ist wieder ein Popup-Fenster, wie du es beim Schalter Manuell erstellt hast. Dieses Mal ist allerdings nur die Ausschaltzeit frei wählbar. 

 

### Schritt 18 

Der Schalter Anwesenheit 

 

Der vierte Schalter ist für den Modus Anwesenheit. Dieser richtet sich auch nach dem Sonnenauf- und untergang. Allerdings wird die Beleuchtung nur eingeschaltet, wenn auch jemand zuhause ist. Diese Funktion ist allerdings komplett in FHEM erstellt und hat auf das FTUI Frontend keinen Einfluss. Der Code kommt an Stelle des Platzhalters `<!--  Schalter Anwesenheit --> und die komplette Eingabe in HTML sieht folgendermaßen aus:  

 
```html
<div class="cell"> 

<div> 

<div 	data-type="switch"  

data-device="d_ModusAuswahl" 

data-get-on="Anwesenheit"  

data-get-off="!on" 

data-set-on="Anwesenheit"  

data-set-off=""  

class="blue" 

data-icon="fa-user-friends"  

data-background-icon="fa-square"> 

</div> 

<div class="inline w1x">Anwesenheit</div> 

</div> 

<div 	data-type="label" 

data-device="lichterketteAAnwesenheit" 

data-get="P_mybegin"	 

data-hide="P_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="green" 

data-color="green"> 

</div> 

<div 	data-type="label" 

data-device="lichterketteAAnwesenheit" 

data-get="P_myend"	 

data-hide="P_mybutton" 	                                

data-hide-on="off" 			 

data-off-color="red" 

data-color="red"> 

</div> 

</div> 
```
 

Die Elemente dieses Schalters funktionieren analog zu den bereits angelegten Schaltern. 

 

 

### Schritt 19 

Der Schalter Aus 

 

Der letzte Schalter ist der Ausschalter. Dieser schaltet die Beleuchtung und die komplette Beleuchtungssteuerung aus. Der Code kommt an Stelle des Platzhalters `<!--  Schalter Aus -->` und die komplette Eingabe in HTML sieht folgendermaßen aus:  

 
```html
<div class="cell"> 

<div> 

<div 	class="blue" 

data-type="switch"  

data-device="d_ModusAuswahlFunk" 

data-get-on="Aus"  

data-get-off="!on" 

data-set-on="Aus"  

data-set-off=""  

data-icon="fa-toggle-off"  

data-background-icon="fa-square"> 

</div> 

<div class="inline w1x">Aus</div> 

</div> 

</div> 
```
 

Auch diesen Schalter erstellst du analog zu den bisherigen. Hierbei musst du allerdings keine dynamische Beschriftung erstellen. 

 

### Schritt 20 

Die Infobox 

 

Zum Schluss erstellst du noch eine Infobox, in der die Funktionsweisen der einzelnen Modis erklärt werden. Diese öffnet sich durch Klick auf ein Fragezeichen oberhalb der Schalter. Der Code kommt an Stelle des Platzhalters `<!--  Infobox-->` und die komplette Eingabe in HTML sieht folgendermaßen aus:  

 
```html
<div class="cell"> 

<div 	data-type="popup"  

data-height="550px"  

data-width="800px"> 

<div 	data-type="symbol"  

class="right"  

data-icon="fa-question-circle"> 

</div> 

<div class="dialog"> 

<header>Beleuchtung</header> 

<div> 

</br>Es stehen vier verschiedene Beleuchtungsmodi zur Verf&uuml;gung:  

</br> 

</br> 

<div 	class="topleft"  

data-type="symbol"  

data-icon="fa-clock"  

data-background-icon="fa-square"  

data-background-color="blue"> 

</div> 

<div 	style="text-align: left">Manuell: Hierbei k&ouml;nnen Sie frei einstellen, wann die Beleuchtung angeschaltet (gr&uuml;n) und wann Sie ausgeschaltet wird (rot). 

</div> 

</br> 

<div 	class="topleft"  

data-type="symbol"  

data-icon="fa-sun"  

data-background-icon="fa-square"  

data-background-color="blue"> 

</div> 

<div 	style="text-align: left">Sonne: Hierbei richtet sich das An- und Ausschalten der Beleuchtung nach den aktuellen Zeiten f&uuml;r den Sonnenunter- und aufgang. 

</div> 

</br> 

<div 	class="topleft"  

data-type="symbol" 	 

data-icon="fa-building"  

data-background-icon="fa-square"  

data-background-color="blue"> 

</div> 

<div 	style="text-align: left">Werktage: Hierbei richtet sich das An- und Ausschalten der Beleuchtung nach den aktuellen Zeiten f&uuml;r den Sonnenunter- und aufgang. Au&szlig;erdem k&ouml;nnen Sie f&uuml;r Werktage manuell Zeiten festlegen, wann die Beleuchtung sp&auml;testens ausgeschaltet werden soll, falls Sie schon vor Sonnenaufgang aus dem Haus gehen. 

</div> 

</br> 

<div 	class="topleft"  

data-type="symbol"  

data-icon="fa-user-friends"  

data-background-icon="fa-square"  

data-background-color="blue"> 

</div> 

<div 	style="text-align: left">Anwesenheit: Hierbei richtet sich das An- und Ausschalten der Beleuchtung zum einen nach den aktuellen Zeiten f&uuml;r den Sonnenunter- und aufgang. Au&szlig;erdem ermittelt das Heimautomatisierungssystem, ob sich eines der eingerichtet Handys (-> siehe Einstellungen) im lokalen WLAN befindet. Ist keines der Handys anwesend wird die Beleuchtung ausgeschaltet. 

</div> 

</br> 

<div 	class="topleft"  

data-type="symbol"  

data-icon="fa-toggle-off"  

data-background-icon="fa-square"  

data-background-color="blue"> 

</div> 

<div 	style="text-align: left">Aus: Die Beleuchtung und die Beleuchtungssteuerung werden komplett ausgeschalten. 

</div> 

</br> 

</div> 

</div> 

</div> 

</div> 
```
 

Die einzelnen Elemente wirst du im Folgenden kennenlernen: 

 
```html
<div 	data-type="popup"  

data-height="550px"  

data-width="800px"> 

<div 	data-type="symbol"  

class="right"  

data-icon="fa-question-circle"> 

</div> 

<div class="dialog"> 

<header>Beleuchtung</header> 
```
 

Für die Infobox verwendest du wieder das FTUI Widget Popup. Dieses Mal verwendest du allerdings keine Beschriftung, sondern ein Fragezeichen-Icon, über das das Popup-Fenster geöffnet wird. 

 
```html
<div> 

</br>Es stehen vier verschiedene Beleuchtungsmodi zur Verf&uuml;gung:  

</br> 

</br> 

<div 	class="topleft"  

data-type="symbol"  

data-icon="fa-clock"  

data-background-icon="fa-square"  

data-background-color="blue"> 

</div> 

<div 	style="text-align: left">Manuell: Hierbei k&ouml;nnen Sie frei einstellen, wann die Beleuchtung angeschaltet (gr&uuml;n) und wann sie ausgeschaltet wird (rot). 

</div> 
```
 

Im Popup-Fenster erstellst du hier keine Funktion, sondern zeigst nur die Icons der Modi und einen Erklärungstext darunter. `</br>` sorgt für einen Zeilenumbruch und damit für eine bessere Lesbarkeit. 

 

Die Erklärungen zu den übrigen Modi erstellst du analog zu dem dargestellten Modus Manuell. 

 

Damit hast du die komplette Bedienoberfläche für die Beleuchtungssteuerung der Funksteckdose fertiggestellt! 

Die Steuerung der WLAN-Steckdose erfolgt analog dazu. 

 
