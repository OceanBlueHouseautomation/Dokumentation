## Erläuterung der Systemarchitektur
Nachfolgende Erklärungen können dem Schaubild "Architektur" aus diesem Verzeichnis entnommen werden.

In erster Linie meldet sich der Entwickler mittels SSH oder auch direkt über den Port 8083 auf dem Raspberry Pi an. Dafür wird eine Anmeldedomäne von Nöten, sowie ein Passwort. Alle nachträglichen Programmierarbeiten müssen direkt über SSH vollzogen werden. Als solche Arbeiten werden Wartungen und ähnliches gewertet. Der Arduino Nano ist mit dem Raspberry Pi verbunden und kann mit der Programmiersprache CUL-FW angesprochen und definiert werden. Des Weiteren ist ein USB-Stick mit dem Raspberry Pi gekoppelt, auf dem tägliche Backups des Systems gespeichert werden. Der FHEM-Server befindet sich im Raspberry Pi und hat eine Verbindung zum Arduino und zum Ethernet-Anschluss. Der WLAN-Router ist mittels LAN-Kabel an den RP angeschlossen.

Über den Arduino können Befehle wie beispielsweise ein An- oder Ausoperation per WLAN an andere ans WLAN angeschlossene Geräte gesendet werden. Im obigen Schaubild befindet sich ein Schalter in unmittelbarer Nähe, an den eine Lichterkette angeschlossen ist. Diese Lichterkette kann dann über den Arduino programmiert werden. Des weiteren gibt es einen Shelly, der mit zwei Glühbirnen verbunden ist. Diese Glühbirnen können ebenfalls über eine WLAN-Verbindung angesprochen werden. Über den Arduino können auch diese Birnen programmiert werden.

Wichtig dabei ist, das sich der gesamte Aufbau im Schaubild oben im selben Netz befinden muss!

Der WLAN-Router der an den RP angeschlossen ist, stellt die gesamte Netzwerkfunktionalität für das Gebilde dar. Alle Befehle werden mit 433 MHz weitergeleitet, was also bedeutet, dass darauf geachtet werden muss, dass alle Geräte diese auch empfangen können.

