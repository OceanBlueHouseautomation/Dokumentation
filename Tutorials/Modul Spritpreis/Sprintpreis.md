# Wie du mithilfe von FHEM nie wieder zu viel für Benzin ausgibst

## Einführung

In diesem Tutorial wirst du vier Tankstellen deiner Wahl in FHEM einbinden. Du kannst dir die Spritpreise in FTUI ansehen und auch nachträglich die überwachten Tankstellen ändern.

## Voraussetzungen
- Laufende FHEM-Installation (siehe Tutorial Hardware)
- Bestehende FTUI (siehe Tutorial Startseite)
- Raspberry Pi benötigt Zugriff auf das Internet

### Schritt 1 - Anlegen einer Tankstelle

FHEM bringt ein Modul mit, welches Daten von Websiten auslesen kann. Dieses wird nicht nur hier, sondern zum Beispiel auch bei den Wettersensoren genutzt. Vielleicht ist dir die Vorgehensweise daher schon bekannt. Wir legen zum Test eine beliebige Tankstelle von clever-tanken.de an. In diesem Fall eine Tankstelle der Kette Shell aus Langen. Das wird später noch geändert, keine Sorge!

Die folgende Datenstruktur sollte für nahezu alle Tankstellen standardmäßig funktionieren:
```
define tankstelle1 HTTPMOD http://www.clever-tanken.de/tankstelle_details/4871 600
attr tankstelle1 enableControlSet 1
attr tankstelle1 reading01Name Diesel
attr tankstelle1 reading01Regex "price-type-name">Diesel<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr tankstelle1 reading01OExpr $val + 0.009
attr tankstelle1 reading02Name SuperE5
attr tankstelle1 reading02Regex "price-type-name">Super.E5<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr tankstelle1 reading02OExpr $val + 0.009
attr tankstelle1 reading03Name SuperE10
attr tankstelle1 reading03Regex "price-type-name">Super.E10<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr tankstelle1 reading03OExpr $val + 0.009
attr tankstelle1 reading04Name SuperPlus
attr tankstelle1 reading04Regex "price-type-name">SuperPlus<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr tankstelle1 reading04OExpr $val + 0.009
attr tankstelle1 stateFormat E5: SuperE5, E10: SuperE10, D: Diesel, SP: SuperPlus
attr tankstelle1 timeout 5
```

Profi-Tipp: Für spezielle Tankstellen und Treibstoffe, z.B. Shells bekannte V-Power-Marke sind Anpassungen nötig, um diese ebenfalls einzubinden.
Daher hier ein Beispiel speziell für Shell:
```
attr Shell reading05Name ShellVPowerRacing
attr Shell reading05Regex "price-type-name">Shell.V-Power.Racing<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr Shell reading06Name ShellVPowerDiesel
attr Shell reading06Regex "price-type-name">Shell.V-Power.Diesel<[\d\D]{600,900}"current-price-.">(\d.\d\d)
```
Oder für ARAL:
```
attr Aral reading04Regex "price-type-name">ARAL.Superplus<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr Aral reading05Name Autogas
attr Aral reading05Regex "price-type-name">Autogas<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr Aral reading06Name AralUltimate102
attr Aral reading06Regex "price-type-name">ARAL.Ultimate.102<[\d\D]{600,900}"current-price-.">(\d.\d\d)
attr Aral reading07Name ARALUltimateDiesel
attr Aral reading07Regex "price-type-name">ARAL.Ultimate.Diesel<[\d\D]{600,900}"current-price-.">(\d.\d\d)
```
Es kann daher u.U. Sinn machen, Tankstellen speziell z.B. nach Kette zu erstellen, wenn dir die Angabe der "Standard-Kraftstoffe" nicht ausreicht.

Wiederhole diesen Schritt so oft wie du unterschiedliche Tankstellen vergleichen möchtest und lege diese mit unterschiedlichen Bezeichnungen, z.B. nummeriert, an.

### Schritt 2 - Tankstelle konfigurierbar gestalten

Wir möchten die Tankstellen auch nachträglich konfigurierbar gestalten.
Daher arbeiten wir mit einem DOIF, welches ein sogenanntes defmod aufruft. Mit defmod ist es möglich, defines nachträglich mit aktualisierten Informationen (wie der aktualisierten Tankstellen-Nummer) zu überschreiben.

Wir legen dabei die Zeit, in der die Tankstellenpreise aktualisiert werden soll, auf 600 Sekunden fest. Dies überlastet den Pi nicht, führt aber gleichzeitig zu einigermaßen aktuellen Spritpreisen:

```
define doif_tankstelle1 DOIF ([$SELF:P_tankstelle_url])(defmod Tankstelle1 HTTPMOD [$SELF:P_tankstelle_url] 600)
attr doif_tankstelle1 do always
attr doif_tankstelle1 readingList P_tankstelle_url
attr doif_tankstelle1 room Spritpreise
attr doif_tankstelle1 selftrigger all
attr doif_tankstelle1 setList P_tankstelle_url
```

Dieses doif musst du für jede Tankstelle erstellen, die du in Schritt 1 angelegt hast.

### Schritt 3 - Erstellen eines FileLogs

Nun möchten wir dafür sorgen, dass die ermittelten Werte protokolliert werden. Dies machen wir, um im nächsten Schritt einen Spritpreisverlauf zu ermöglichen, so sehen wir bereits nach kurzer Zeit, wie die Preisentwicklung ist und an welchen Tagen bzw. Uhrzeiten sich das Tanken besonders oder überhaupt nicht lohnt.
Ein FileLog-Modul bringt FHEM dabei praktischerweise bereits mit. Beachte hierzu beim Erstellen, dass du in der nächsten Zeile alle deine erstellten Tankstellen einbindest (getrennt durch | ) sowie alle gewünschten Kraftstoffe:
```
define FileLog_Spritpreise FileLog /media/usblog/fhem/log/spritpreise-%Y-%m.log (tankstelle1|tankstelle2|tankstelle3):(SuperE5|Diesel).*
attr FileLog_Spritpreise alias Log Spritpreise
attr FileLog_Spritpreise group Logfile
attr FileLog_Spritpreise logtype text
attr FileLog_Spritpreise room Spritpreise
```


### Schritt 4 - Erstellen einer ReadingsGroup zur Anzeige der Preise

Als nächstes erstellen wir eine sogenannte readingsGroup. Mit der readingsGroup kannst du Werte aus verschiedenen Geräten zusammenführen und gemeinsam anzeigen lassen. Dies eignet sich ideal, um unsere Tankstellenpreise untereinander aufzulisten.
Beachte hierzu beim Erstellen, dass du in der nächsten Zeile alle deine erstellten Tankstellen einbindest (getrennt durch | ) sowie alle gewünschten Kraftstoffe.

```
define Spritpreise readingsGroup (tankstelle1|tankstelle2|tankstelle3):(SuperE5|Diesel).*
attr Spritpreise group Spritpreisuebersicht
attr Spritpreise notime 1
attr Spritpreise room Spritpreise
#attr Spritpreise style style="font-size:16px"
attr Spritpreise valueFormat {'%.2f €'}
#attr Spritpreise valueStyle {Werte($READING,$VALUE)}
```

Jetzt möchten wir, dass die Preise je nach aktuellem Wert in unterschiedlichen Farben markiert werden. Hierzu ist eine Anpassung der Datei 99_myUtils.pm notwendig. Klicke in FHEM auf "Edit Files" und wähle unter "Own modules and helper files" die Datei 99_myUtils.pm aus.
Füge dort unten folgenden Code ein, der dem FHEM-Wiki entstammt:
```
###################################################
###     Spritpreisübersicht - Farbsortierung    ###
###################################################

sub Werte($$) {
  my ($name, $wert) = @_;
# Log(3,"$name $wert");
  if ($name eq "Diesel") {
    return 'style="color:red"' if($wert >= 1.39); 
    return 'style="color:blue"' if(($wert >= 1.33) && ($wert < 1.39));
    return 'style="color:green;;font-weight:bold"' if($wert <= 1.32);
  }elsif ($name eq "SuperE10") {
    return 'style="color:crimson"' if($wert >= 1.70); 
    return 'style="color:yellow"' if(($wert >= 1.55) && ($wert < 1.70));
    return 'style="color:lightgreen;;font-weight:bold"' if($wert < 1.55);
  }elsif ($name eq "SuperE5") {
    return 'style="color:red"' if($wert >= 1.59); 
    return 'style="color:blue"' if(($wert >= 1.49) && ($wert < 1.59));
    return 'style="color:green;;font-weight:bold"' if($wert <= 1.48);
  }  
}
```


## Abschluss
Schon fertig?
Wir haben noch weitere Tutorials, um dir den Alltag zu erleichtern.
Lerne hier, wie du immer über die Verkehrslage auf deinen Verkehrsrouten informiert bleibst:
https://github.com/OceanBlueHouseautomation/Dokumentation/blob/main/Tutorials/Modul%20Verkehrsinfo/Verkehrinfo.md

Viel Spaß!

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://wiki.fhem.de/wiki/Spritpreismonitor (Letzter Zugriff: 29.01.2021)
https://wiki.fhem.de/wiki/99_myUtils_anlegen (Letzter Zugriff: 29.01.2021)
