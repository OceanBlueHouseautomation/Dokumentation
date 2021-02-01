# Wie bei FHEM das Auge mit isst 

## Einführung 

In diesem Tutorial lernst du die Grundlagen zur Gestaltung deiner FTUI. Außerdem wirst du die Basisdokumente anlegen, die du für die weiteren Tutorials benötigst. 

## Voraussetzungen 

Funktionierende FHEM-Installation 

### Schritt 1 – Tablet UI installieren 
Zuerst Beginn musst du alle Dateien von FHEM Tablet UI in das FHEM Verzeichnis **/opt/fhem/www kopiert werden**. Das geht mit folgendem update-Befehl über die Kommandozeile in Fhem: 
```
< update all https://raw.githubusercontent.com/knowthelist/fhem-tablet-ui/master/controls_fhemtabletui.txt > 
```
Anschließend musst du ein neues HTTPSRV-Device in FHEM anlegen. Dieses verweist auf den Ordner mit den gerade heruntergeladenen Dateien. Dafür kopierst du folgenden Befehl in die Komandozeile: 
```
< define TABLETUI HTTPSRV ftui/ ./www/tablet/ Tablet-UI > 
```
Außerdem musst du eine Attribut namens **“longpoll”** setzten, damit FHEM Tablet UI mit FHEM kommunizieren kann: 
```
< attr WEB longpoll websocket > 
```
bzw. bei Problemen mit websocket 
```
< attr WEB longpoll 1 > 
```
Um zu sehen ob alles geklappt hast, kannst du die index-example.html in die index.html kopieren. Dazu musst du folgenden Befehl auf dem Raspberry Pi absetzen:  
```
< sudo cp -a /opt/fhem/www/tablet/index-example.html /opt/fhem/www/tablet/index.html > 
```
Durch den Befehl  
```
< shutdown restart > 
```
In der Pi Kommandozeile, startest du den Fhem neu und speicherst damit die Änderungen. Nun solltest du die FTUI über die URL http://<fhem-server>:8083/fhem/ftui/ oder den Link im FHEM-Menü öffnen können. 

Schritt 2 – Erstellen einer Menü-Leiste 

Wir empfehlen dir in den folgenden Tutorials immer wieder, wie du die Funktionen mit einer von uns erstellten FTUI einbinden kannst. Wenn du diesen Weg gehen möchtest und unser Design nachbauen willst, kannst du durch den Folgenden html-Code ganz einfach ein Seitenmenü erhalten, durch welches du dann navigieren kannst. Unsere Code Beispiele setzen dieses Voraus. Wenn du eine eigene FTUI aufbauen möchtest, kannst du dich zunächst ja trotzdem von unserer UI inspirieren lassen.  

Zuerst musst du das html-Dokument menu.html auf deinem pi anlegen.  

Hierfür navigierst du in den Pfad: **/opt/fhem/www/tablet/** 

Dies machst du entweder über Filezilla indem du dich durch die Ordner klickst oder durch den Befehl auf deinem Pi: 
```
< cd /opt/fhem/www/tablet/ > 
```
Hier legst du ein neues Dokument mit dem Befehl  
```
< sudo vi menu.html > 
```
Nun kopierst du folgenden Inhalt in das Dokument: 
```html
<!DOCTYPE html> 

<html> 

<head> 

</head> 

<body> 

<div class="sheet"> 

  

<div class="row"> 

<div data-type="pagetab" 

 data-url="startseite.html" 

 data-icon="mi-home" 

 class="cell"></div> 

</div> 

  

<div class="row"> 

<div data-type="pagetab" 

 data-url="beleuchtung.html" 

 data-icon="mi-star_border" 

 class="cell"></div> 

</div> 

  

<div class="row"> 

<div data-type="pagetab" 

 data-icon="mi-gradient" 

 data-url="rollladen.html"	 

 class="cell"></div> 

</div> 

  

<div class="row"> 

<div data-type="pagetab" 

 data-icon="mi-toys" 

 data-url="sturmwarnung.html" 

 class="cell"></div> 

</div> 

  

<div class="row"> 

<div data-type="pagetab" 

 data-icon="mi-drive_eta" 

 data-url="verkehrsinfo.html" 

 class="cell"></div> 

</div> 

   

<div class="row"> 

<div data-type="pagetab" 

 data-icon="fa-home" 

 data-url="hausgarten.html" 

 class="cell"></div> 

</div> 

  

<div class="row"> 

  <div data-type="pagetab" 

      	  data-icon="mi-local_florist" 

      	  data-url="schrebergarten.html" 

     	  class="cell"></div> 

</div> 

  

<div class="row"> 

<div data-type="pagetab" 

 data-icon="mi-videocam" 

 data-url="camera.html" 

 class="cell"></div> 

</div> 

 

<div class="row"> 

<div data-type="pagetab" 

 data-icon="mi-settings" 

 data-url="settings.html" 

 class="cell"></div> 

</div> 

 

</div> 

</body> 

</html> 
```
Mit Filzilla kannst du das html-Dokument **menu.html** auch auf deinem Desktop vorbereiteten und einfach in den oben genannten Pfad kopieren. 

Jede “row” in diesem Dokument legt einen neuen Menüpunkt an, wichtig ist, das der div-Tag “data-type” den Wert “pagetab” enthält. Über die data-url kannst du dann die einzelnen Funktionen über extra html-Dokumente implementieren. 

### Schritt 3 – Erstellen der Pagetabs für das Menü  

In diesem Schritt sollst du die für die in den anderen Kapiteln benötigten Pagetabs bereits leer angelegt werden. Dadurch kannst du dort direkt loslegen und musst dich nicht mit nervigen vorarbeiten beschäftigen.  

Nach demselben Prinzip wie in Schritt 2 erstellst du auch hier folgende html-Dokumente:  

* startseite.html 

* beleuchtung.html 

* rollladen.html 

* sturmwarnung.html 

* settings.html 

* verkehrsinfo.html 

* camera.html 

* hausgarten.html 

* schrebergarten.html 

Die Startseite und die Settingsseite haben wir für dich bereits in einem coolen Kachel Design entwickelt. Wenn du dir dieses mal anschauen willst kopiere einfach Folgenden Code in deine Dokumente:  

TIPP: Die Position der Kacheln kannst du im li-Tag über data-col, data-row bestimmen, die Größe der Kacheln über data-sizex und data-size-y! 

In die startseite.html: 
```html
<!DOCTYPE html> 

<html> 

<head> 

<meta name="gridster_cols" content="5"> 

<meta name="gridster_rows" content="4"> 

</head> 

<body> 

<div class="gridster"> 

  

<ul> 

<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li> 

<li data-row="1" data-col="2" data-sizex="1" data-sizey="1"> 

<header><div data-type="label" class="large">Willkommen!</div></header> 

 

<div	data-type="clock" 

data-format="H:i" 

class="bigger"> 

</div> 

 

<div	data-type="clock" 

data-format="d.M." 

class="bigger"> 

</div> 

<div	data-type="label" 

data-device="Temperatursensor" 

data-get="temperature" 

data-unit="%B0C%0A" 

data-limits='[-30,10,23]'  

data-colors='["#6699FF","#AA6900","#FF0000"]' 

class="bigger"> 

</div> 

</li> 

<li data-row="1" data-col="3" data-sizex="1" data-sizey="2"> 

<header><div data-type="label" class="large" data-device="doif_plz" data-get="P_PLZ" data-pre-text="Wetter 9 Uhr für "></div></header> 

<div	data-type="label" 

class="darker large top-space">Heute 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc0_weather09Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc0_weather09"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc0_temp09" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

 

<div	data-type="label" 

class="darker large top-space">Morgen 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc1_weather09Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc1_weather09"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc1_temp09" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

  

<div	data-type="label" 

class="darker large top-space">Übermorgen 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc2_weather09Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc2_weather09"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc2_temp09" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

</li> 

<li data-row="1" data-col="4" data-sizex="1" data-sizey="2"> 

<header><div data-type="label" class="large" data-device="doif_plz" data-get="P_PLZ" data-pre-text="Wetter 15 Uhr für "></div></header> 

<div	data-type="label" 

class="darker large top-space">Heute 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc0_weather15Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc0_weather15"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc0_temp15" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

 

<div	data-type="label" 

class="darker large top-space">Morgen 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc1_weather15Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc1_weather15"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc1_temp15" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

  

<div	data-type="label" 

class="darker large top-space">Übermorgen 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc2_weather15Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc2_weather15"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc2_temp15" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

</li> 

<li data-row="1" data-col="5" data-sizex="1" data-sizey="2"> 

<header><div data-type="label" class="large" data-device="doif_plz" data-get="P_PLZ" data-pre-text="Wetter 21 Uhr für "></div></header> 

<div	data-type="label" 

class="darker large top-space">Heute 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc0_weather21Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc0_weather21"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc0_temp21" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

 

<div	data-type="label" 

class="darker large top-space">Morgen 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc1_weather21Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc1_weather21"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc1_temp21" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

  

<div	data-type="label" 

class="darker large top-space">Übermorgen 

</div> 

<div	data-type="weather" 

data-device="wetterbericht" 

data-get="fc2_weather21Icon" 

class="big"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc2_weather21"> 

</div> 

<div	data-type="label" 

data-device="wetterbericht" 

data-get="fc2_temp21" 

data-unit="%B0C%0A" 

class="large"> 

</div> 

</li> 

<li data-row="2" data-col="2" data-sizex="1" data-sizey="1" class="left-align"> 

<header>Auf einen Klick!</header> 

     <div class="sheet"> 

     <div class="row"> 

          <div class="cell center-align"> 

                  <div class="inline large darker thin cell">Funksteckdose</div> 

          </div> 

          <div class="cell center-align"> 

                  <div 

                  data-type="checkbox" 

                  data-device="funksteckdose" 

                  data-set-on="on" 

                  data-set-off="off" 

                  class="cell inline left-space-3x" 

                  ></div> 

          </div> 

     </div> 

     <div class="row"> 

           <div class="cell center-align"> 

                  <div class="inline large darker thin cell">WLAN-Steckdose</div> 

           </div> 

           <div class="cell center-align"> 

  <div 

                  data-type="checkbox" 

                  data-device="WLANSteckdose" 

                  data-set-on="on" 

                  data-set-off="off" 

                  class="cell inline left-space-3x" 

                  ></div> 

        </div> 

      </div>     

  

       <div class="row">    

           <div class="cell center-align"> 

              <div class="blue" data-type="switch" data-device="d_switchRelayRollo" data-get-off="!on" data-set-off="" data-icon="fa-tint" data-get-on="Relay"></div> 

           </div> 

           <div class="cell center-align"> 

             <div class="inline" data-type="switch" data-device="d_switchRelayRollo" data-get-off="!on" data-set-off="" data-icon="oa-fts_shutter_50" data-get-on="Rollo"></div>  

           </div> 

       </div> 

       <div class="row"> 

           <div class="cell center-align">  

               <div>Bewässerung</div> 

           </div> 

           <div class="cell center-align">  

               <div >Rollladen</div> 

           </div> 

       </div> 

    </div>  

  

</li> 

<li data-row="3" data-col="2" data-sizex="3" data-sizey="2" class="left-align">                <header><div data-type="label" data-device="nachrichten1" data-get="f_description"></div></header> 

                <div class="cell left-align"> 

<div data-type="label" data-device="nachrichten1" data-get="n00_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n01_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n02_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n03_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n04_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n05_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n06_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n07_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n08_description" class="left-align"></div><hr color="#6699FF"> 

<div data-type="label" data-device="nachrichten1" data-get="n09_description" class="left-align"></div> 

                </div>  

</li> 

<li data-row="3" data-col="5" data-sizex="1" data-sizey="2" class="left-align">                <header>Pollenflug</header> 

          <div class="sheet"> 

<div data-type="readingsgroup" data-device="rgPollenvorhersage"></div> 

<div class="big" data-type="label">für PLZ </div><div data-type="label" data-device="doif_plz" data-get="P_PLZ"></div> 

          </div>  

</li> 

</ul> 

</div> 

</body> 

</html> 
```
In die settings.html: 
```html
<!DOCTYPE html> 

<html> 

<head></head> 

<body> 

<div class="gridster"> 

<ul> 

<li data-row="1" data-col="1" data-sizex="1" data-sizey="4" data-template="menu.html"></li> 

<li data-row="1" data-col="2" data-sizex="2" data-sizey="2" class="left-align"><header>Ortsangaben</header> 

    <div class="sheet"> 

        <div class="row"> 

        <div class="cell center-align">  

<div class="inline cell" data-type="label">Postleitzahl:</div> 

        <div class="inline cell" data-type="input" data-device="doif_plz" data-get="P_PLZ" data-set="P_PLZ" class="w1x"></div> 

    </div> 

        </div> 

         

        <div class="row"> 

        <div class="cell center-align">  

<div class="inline cell" data-type="label">Längengrad:</div> 

        <div class="inline cell" data-type="input" data-device="global" data-get="longitude" data-set="longitude" class="w1x"></div> 

    </div> 

        </div> 

         

        <div class="row"> 

        <div class="cell center-align">  

<div class="inline cell" data-type="label">Breitengrad:</div> 

        <div class="inline cell" data-type="input" data-device="global" data-get="latitude" data-set="latitude" class="w1x"></div> 

    </div> 

        </div> 

    </div> 

  

</li> 

  

<li data-row="1" data-col="4" data-sizex="2" data-sizey="2" class="left-align"><header>Zisterne</header> 

    <div class="sheet"> 

        <div class="row"> 

        <div class="cell center-align">  

<div class="inline cell" data-type="label">Höhe der Zisterne:</div> 

        <div class="inline cell" data-type="input" data-device="zisterne" data-get="hoehe" data-set="hoehe" class="w1x"></div> 

    </div> 

        </div> 

         

        <div class="row"> 

        <div class="cell center-align">  

<div class="inline cell" data-type="label">Fassungsvermögen in Litern:</div> 

        <div class="inline cell" data-type="input" data-device="zisterne" data-get="literangabe" data-set="literangabe" class="w1x"></div> 

    </div> 

        </div> 

         

        <div class="row"> 

        <div class="cell center-align">  

<div class="inline cell" data-type="label">Abstand zur Wasseroberfläche neu messen:</div> 

        <div data-type="push"  

        data-device="doif_zisterneCalibration"   

        data-icon="fa-ruler" 

        data-fhem-cmd="set doif_zisterneCalibration P_mybutton on" 

        class="inline cell"></div> 

    </div> 

        </div> 

    </div> 

  

</li> 

<li data-row="2" data-col="2" data-sizex="2" data-sizey="2" class="left-align"><header>Tankstellen</header> 

      <div class="sheet"> 

  <div class="row"> 

                    <div class="cell center-align">                                                                                                     

  <div class="inline cell" data-type="label">Tankstelle AVIA:</div> 

  <div class="inline cell" data-type="input" data-device="doif_avia" data-get="P_tankstelle_url" data-set="P_tankstelle_url" class="w3x"></div> 

  </div> 

                    </div>     

  

  <div class="row"> 

                    <div class="cell center-align"> 

  <div class="inline cell" data-type="label">Tankstelle Aral:</div> 

  <div class="inline cell" data-type="input" data-device="doif_aral" data-get="P_tankstelle_url" data-set="P_tankstelle_url" class="w3x"></div> 

  </div>  

                    </div>    

                                                                                                                                                  

  <div class="row"> 

                    <div class="cell center-align"> 

  <div class="inline cell" data-type="label">Tankstelle Esso: </div> 

  <div class="inline cell" data-type="input" data-device="doif_esso" data-get="P_tankstelle_url" data-set="P_tankstelle_url" class="w3x"></div> 

  </div> 

                    </div>     

  

  <div class="row"> 

                    <div class="cell center-align"> 

  <div class="inline cell" data-type="label">Tankstelle Shell:</div> 

  <div class="inline cell" data-type="input" data-device="doif_shell" data-get="P_tankstelle_url" data-set="P_tankstelle_url" class="w3x"></div> 

  </div>  

                    </div>   

  

      </div> 

 

</li>                         

<li data-row="2" data-col="4" data-sizex="2" data-sizey="2" class="left-align"><header>Anwesenheitserkennung</header>        

      <div class="sheet"> 

  <div class="row"> 

                    <div class="cell center-align">                                                                                                     

    <div class="inline cell" data-type="label">IP-Adresse von iPhone1:</div> 

  <div class="inline cell" data-type="input" data-device="doif_iphone1" data-get="P_ip_address" data-set="P_ip_address" class="w2x"></div> 

  </div> 

                    </div>     

  

  <div class="row"> 

                    <div class="cell center-align"> 

  <div class="inline cell" data-type="label">IP-Adresse von iPhone2:</div> 

  <div class="inline cell" data-type="input" data-device="doif_iphone2" data-get="P_ip_address" data-set="P_ip_address" class="w2x"></div> 

  </div>  

                    </div>    

                                                                                                                                                  

  <div class="row"> 

                    <div class="cell center-align"> 

  <div class="inline cell" data-type="label">IP-Adresse von iPhone3:</div> 

  <div class="inline cell" data-type="input" data-device="doif_iphone3" data-get="P_ip_address" data-set="P_ip_address" class="w2x"></div> 

  </div> 

                    </div>     

  

  <div class="row"> 

                    <div class="cell center-align"> 

  <div class="inline cell" data-type="label">IP-Adresse von iPhone4:</div> 

  <div class="inline cell" data-type="input" data-device="doif_iphone4" data-get="P_ip_address" data-set="P_ip_address" class="w2x"></div> 

  </div>  

                    </div> 

      </div>  				 

</li> 

</ul> 

</div> 

</body> 

</html> 
```
 

Hast du alles vorbereitet? SUPER! Dann nichts wie los in die anderen Tutorials mit welchen du deine FTUI mit richtig coolen Funktionen füllst. 

## Quellen dieses Tutorials 

Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:  

* https://wiki.fhem.de/wiki/FTUI_Layout_Sheet 

* https://wiki.fhem.de/wiki/FTUI_Layout_Gridster 

* https://wiki.fhem.de/wiki/FHEM_Tablet_UI 

 
