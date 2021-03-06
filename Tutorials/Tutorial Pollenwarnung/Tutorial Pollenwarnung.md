# Wie du mit Hilfe von FHEM nie wieder niesen musst

## Einführung

In diesem Tutorial wirst du lernen, wie FHEM automatisiert täglich den Pollenflug ermittelt und dir die für dich relevanten Pollenarten in FTUI anzeigt. Unterstützt werden
Ambrosia, Ampfer, Beifuß, Birke, Buche, Eiche, Erle, Gräser, Hasel, Pappel, Roggen, Ulme, Wegerich, Weide.

## Voraussetzungen
- Laufende FHEM-Installation (siehe Tutorial Hardware)
- Bestehende FTUI (siehe Tutorial Startseite)
- Raspberry Pi benötigt Zugriff auf das Internet

### Schritt 1 - Erweiterung des Raspberry Pi um ein benötigtes Modul

Mit dem nachfolgenden Befehl installierst du auf deinem Pi (d.h. wie gewohnt über Putty) ein benötigtes Perl-Modul, welches im Hintergrund von deinem FHEM-System benötigt wird.
```
sudo apt-get install libxml-simple-perl
```

### Schritt 2 - Anlegen der Pollenvorhersage

Mit den nachfolgenden Befehlen legst du dein eigenes Pollen-Warnungs-System an. Dabei legst du in der ersten Zeile deine Postleitzahl, z.B. 71277 für Rutesheim, fest. 
Via levelsFormat legst du fest, dass der Status des Pollenflugs als farbige Icons angezeigt wird. Das Ergebnis siehst du später.
```
define Pollenflugvorhersage allergy 71277
attr Pollenflugvorhersage levelsFormat rc_dot@white,rc_dot@yellow,rc_dot@orange,rc_dot@red
attr Pollenflugvorhersage room Wetter-vorhersage
attr Pollenflugvorhersage stateFormat fc1_maximum
attr Pollenflugvorhersage updateEmpty 1
attr Pollenflugvorhersage updateIgnored 1
```

### Schritt 3 - Anlegen der Readings-Group

Die nachfolgenden Befehle legen nun eine sog. Readings-Group an. Diese fasst mehrere Readings (in dem Fall unsere verschiedenen Pollen-Arten) zusammen. Übernimm daher aus dem nachfolgenden
Beispiel nur diejenigen Pollenarten, die du gerne in deiner FTUI-Installation anzeigen möchtest.

```
define rgPollenvorhersage readingsGroup Pollenflugvorhersage:<Pollen>,fc0_day_of_week,fc1_day_of_week,fc2_day_of_week,fc3_day_of_week,fc4_day_of_week,fc5_day_of_week,fc6_day_of_week,fc7_day_of_week \
                                        Pollenflugvorhersage:<Ambrosia>,fc0_Ambrosia,fc1_Ambrosia,fc2_Ambrosia,fc3_Ambrosia,fc4_Ambrosia,fc5_Ambrosia,fc6_Ambrosia,fc7_Ambrosia \
                                        Pollenflugvorhersage:<Ampfer>,fc0_Ampfer,fc1_Ampfer,fc2_Ampfer,fc3_Ampfer,fc4_Ampfer,fc5_Ampfer,fc6_Ampfer,fc7_Ampfer \
                                        Pollenflugvorhersage:<Beifuß>,fc0_Beifuss,fc1_Beifuss,fc2_Beifuss,fc3_Beifuss,fc4_Beifuss,fc5_Beifuss,fc6_Beifuss,fc7_Beifuss \
                                        Pollenflugvorhersage:<<b>Birke<Birke</b>>,fc0_Birke,fc1_Birke,fc2_Birke,fc3_Birke,fc4_Birke,fc5_Birke,fc6_Birke,fc7_Birke \
                                        Pollenflugvorhersage:<Buche>,fc0_Buche,fc1_Buche,fc2_Buche,fc3_Buche,fc4_Buche,fc5_Buche,fc6_Buche,fc7_Buche \
                                        Pollenflugvorhersage:<Eiche>,fc0_Eiche,fc1_Eiche,fc2_Eiche,fc3_Eiche,fc4_Eiche,fc5_Eiche,fc6_Eiche,fc7_Eiche \
                                        Pollenflugvorhersage:<<b>Erle<Erle</b>>,fc0_Erle,fc1_Erle,fc2_Erle,fc3_Erle,fc4_Erle,fc5_Erle,fc6_Erle,fc7_Erle \
                                        Pollenflugvorhersage:<<b>Gräser</b>>,fc0_Graeser,fc1_Graeser,fc2_Graeser,fc3_Graeser,fc4_Graeser,fc5_Graeser,fc6_Graeser,fc7_Graeser \
                                        Pollenflugvorhersage:<<b>Hasel<Hasel</b>>,fc0_Hasel,fc1_Hasel,fc2_Hasel,fc3_Hasel,fc4_Hasel,fc5_Hasel,fc6_Hasel,fc7_Hasel \
                                        Pollenflugvorhersage:<Pappel>,fc0_Pappel,fc1_Pappel,fc2_Pappel,fc3_Pappel,fc4_Pappel,fc5_Pappel,fc6_Pappel,fc7_Pappel\
                                        Pollenflugvorhersage:<Roggen>,fc0_Roggen,fc1_Roggen,fc2_Roggen,fc3_Roggen,fc4_Roggen,fc5_Roggen,fc6_Roggen,fc7_Roggen \
                                        Pollenflugvorhersage:<Ulme>,fc0_Ulme,fc1_Ulme,fc2_Ulme,fc3_Ulme,fc4_Ulme,fc5_Ulme,fc6_Ulme,fc7_Ulme \
                                        Pollenflugvorhersage:<Wegerich>,fc0_Wegerich,fc1_Wegerich,fc2_Ulme,fc3_Wegerich,fc4_Wegerich,fc5_Wegerich,fc6_Wegerich,fc7_Wegerich \
                                        Pollenflugvorhersage:<Weide>,fc0_Weide,fc1_Weide,fc2_Weide,fc3_Weide,fc4_Weide,fc5_Weide,fc6_Weide,fc7_Weide
attr rgPollenvorhersage mapping %READING
attr rgPollenvorhersage room Wetter-vorhersage
attr rgPollenvorhersage valueIcon %VALUE
attr rgPollenvorhersage valueStyle %VALUE
attr rgPollenvorhersage alias Pollenflug der nächsten 7 Tage
```
Mit dem Attribut alias versorgen wir die ReadingsGroup dabei gleichzeitig mit einem treffenden Namen, der in FTUI später als Überschrift angezeigt wird.

### Schritt 4 - Integration in FTUI

Eine Integration in FTUI könnte dann zum Beispiel so realisiert werden. Vielleicht etwas direkt für die Startseite?

```html
	<li data-row="3" data-col="5" data-sizex="1" data-sizey="2" class="left-align">                
      <header>Pollenflug</header>
			<div data-type="readingsgroup" data-device="rgPollenvorhersage"></div>
			<div class="big" data-type="label">für PLZ </div><div data-type="label" data-device="doif_plz" data-get="P_PLZ"></div>
	</li>
```


## Abschluss
Schon fertig?
Wir haben noch weitere Tutorials, um dir den Alltag zu erleichtern.
Lerne hier, wie du immer über die Verkehrslage auf deinen Verkehrsrouten informiert bleibst:
https://github.com/OceanBlueHouseautomation/Dokumentation/blob/main/Tutorials/Modul%20Verkehrsinfo/Tutorial%20Verkehrsinfo.md

Schau dir gerne auch an, wie du eine Sturmwarnung implementieren kannst, die dich bei Sturm direkt auf deinem iPhone warnt:
https://github.com/OceanBlueHouseautomation/Dokumentation/blob/main/Tutorials/Modul%20Sturmwarnung/Tutorial%20Sturmwarnung.md

Viel Spaß!

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://wiki.fhem.de/wiki/Allergy (Letzter Zugriff: 29.01.2021)
