# Wie dich FHEM nie im Regen stehen lässt

## Einführung

In diesem Tutorial erfährst du, wie du einen Wetterbericht für deine Postleitzahl in FHEM integrierst und die Wetterdaten von heute, morgen und übermorgen
direkt in FTUI integrierst-

## Voraussetzungen
- Funktionierende FHEM-Installation

### Schritt 1 - Anlegen des Moduls PROPLANTA für deine Postleitzahl

Das Modul PROPLANTA ist für diesen Zweck ideal. Warum? Es ermöglicht ohne Registrierung oder Kosten Zugriff auf eine bestehende Wetter-API und zieht dabei -
nur anhand der angegebenen Postleitzahl - alle relevanten Informationen für dich ab. Dabei legst du PROPLANTA bereits mit einem einzelnen Befehl an:

```
define wetterbericht PROPLANTA 71277 de
```
Dieser Befehl legt beispielsweise ein PROPLANTA-Gerät an, welches das Wetter in Rutesheim abfrägt. Ersetze doch mal 71277 durch deine eigene Postleitzahl!

### Schritt 2 - Integration in FTUI

Für die Abgabe haben wir uns entschlossen, dir darzustellen, wie eine mögliche Eigenlösung aussehen könnte, um das Wetter von heute, morgen und übermorgen jeweils zu bestimmten
Uhrzeiten darzustellen. Anbei einmal das Code-Beispiel für eine Abfrage für jew. 09 Uhr morgens:

```html
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
```

## Quellen dieses Tutorials
Du möchtest erfahren, auf welcher Basis dieser Teil deines Blue Ocean Hausautomationssystems erstellt wurde? Dann schau doch mal hier für weitere und ausführliche Hintergrundinformationen:
https://wiki.fhem.de/wiki/PROPLANTA (Letzter Zugriff: 01.02.21)

