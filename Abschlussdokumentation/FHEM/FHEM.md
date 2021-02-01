# Erreichbarkeit
FHEM läuft auf dem Raspberry Pi und durch DynDNS mit Portweiterleitung aus dem Internet erreichbar unter:
https://87nr4b7s6c0r4wv9.myfritz.net:8083/
 
Dabei werden Zugangsdaten (Base64) benötigt:
Username: pi
Password: projekt5
 
Wenn der Pi läuft, sollte i.d.R. auch der FHEM-Server laufen,
zugesichert wurde dies durch
 
# Autostart von FHEM
siehe Raspberry Pi: Autostart von FHEM › mkleine.de
https://mkleine.de/blog/2014/01/19/raspberry-pi-autostart-von-fhem/
 
# Sicherheitskonzept
### HTTPS-verschlüsselte Datenübertragung
Gemäß Raspberry Pi: Zugriff auf FHEM per HTTPS sichern › mkleine.de 
https://mkleine.de/blog/2014/01/19/raspberry-pi-zugriff-auf-fhem-per-https-sichern/

und 

Raspberry Pi & HTTPS – FHEMWiki
https://wiki.fhem.de/wiki/Raspberry_Pi_%26_HTTPS
 
### FAIL2BAN
Gemäß FHEM Tutorial-Reihe - Part 32: Zugriff auf FHEM mit Fail2Ban absichern | haus-automatisierung.com (haus-automatisierung.com)
https://haus-automatisierung.com/hardware/fhem/2017/06/15/fhem-tutorial-reihe-part-32-zugriff-auf-fhem-mit-fail2ban-absichern.html

### Wetterdaten
siehe Ohne REST API Mobile Alerts Temperatur Sensor in FHEM einbinden – Technik – Hausautomatisierung – PC (tips-und-mehr.de)
https://tips-und-mehr.de/ohne-rest-api-mobile-alerts-temperatur-sensor-in-fhem-einbinden/
