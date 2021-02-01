# Wilkommen, zu deiner eignen Hausautomation
Vielen Dank, dass du dich für Ocean Blue Hausautomation entschieden hast. In diesem Online-Verzeichnis findest du alles um dein eigenes Hausautomationssystem zu erstellen. Die Anleitung ist dabei in verschiedene Tutorials aufgegliedert, die als einfach zu folgende Schritt für Schritt Anleitung entworfen wurden. Wenn es etwas schneller gehen soll kannst du auch den Quickguide nutzen. Nachfolgend erhältst Du ein paar Tipps, die das Benutzen der Tutorials erleichtern.\
Viel Spaß beim nachbauen wünscht dir das Ocean Blue Hausautomation Team!


## Struktur
Die einzelnen Module sind in der obigen Ordnerstruktur unter "Tutorials" angelegt. Die empfohlene Reihenfolge bei der Bearbeitung der Tutorials ist:\

1. Modul Hardware
2. Modul Grundlagen FTUI
3. Modul Sturmwarnung
4. Modul Wetter
5. Modul Pollenwarnung
6. Modul RSS-Feed (Nachrichten)
7. Modul Spritpreis
8. Modul Verkehrsinfo
9. Modul Weihnachtsbeleuchtung
10. Modul Gartenbewässerung
11. Modul Rolladensteuerung
12. Modul Kamera

Die Schnellstartanleitung ist unter "Quickguide" zu finden.

Weitere Dokumente der Abschlussdokumentation finden sich im Ordner "Abschlussdokumentation". Idealer weise ist die Darstellung der **.md** Dateien über den Browser bzw. direkt in Github. Sie können aber auch in einem Textbearbeitungsprogramm wie "Editor", "Notepad++" oder "TextEdit" angesehen werden. Dabei ist die Formatierung jedoch nicht ideal, da Markup für die Ansicht im Browser ausgelegt ist.

## Code
Um die Arbeit zu erleichtern ist der benötigte Code in den Tutorials hinterlegt. Es kann sein, dass Änderungen am Code nötig sind. Diese Stellen sind durch Codeschnipsel erkenntlich gemacht und erklärt wo die Änderung gemacht werden muss. Zudem wird immer erklärt was genau zu ändern ist.\
z.B. könnte dies so aussehen:\
Ändere in Zeile 98 in folgender Funktion den Namen **add** zu deinem gewünschten Funktionsnamen:
```javascript
function add(num1, num2) {
    return num1 + num2
}
```

## Fehleranalyse
Eigentlich sollte dies nicht passieren, doch aufgrund von Softwareinkompatibilitäten oder Hardwarefehlern können Probleme auftreten. Solche Fehler lassen sich in der Regel schnell herausfinden und beheben. Sollte bei der bearbeitung der Tutorials mal ein Fehler unterlaufen ist dies auch nicht schlimm. Dann ist es ratsam einfach zum vorherigen Schritt zu gehen und es erneut zu versuchen. Das gehört dazu. Werden in FHEM unverständliche Fehlermeldungen angezeigt, ist es oft Sinnvoll den RaspberryPi und FHEM neu zu starten. Bei weiteren Fehlern hilft auch ein Blick in das [FHEM-Forum](https://forum.fhem.de/). 
