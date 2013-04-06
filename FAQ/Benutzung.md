# FAQ - Knoten-Benutzung

## Inhaltsübersicht
[[_TOC_]]

## VPN Verbindung bricht von alleine ab. Neustart hilft.

Für das VPN wird das verbindungslos arbeitende UDP verwendet. Einige Heimrouter (= Gerät zwischen Freifunkknoten und Internet) haben damit Probleme, z.B. wenn sich die IP bei einer 24h-Trennung ändert. Abhilfe wurde seit der Firmwareversion (0.3.2) geschaffen. 

Sollte Dein Freifunkknoten noch auf einer älteren Version laufen, helfen wir Dir gerne und zeigen Dir wie Du in aktualisierst.


## Grundlagenwissen für die Administrierung von Lübecker Freifunkknoten
### TP-Link Firmware Defaults
Gelbe Buchse: LAN

IP: 192.168.0.1<br />
Netmask: 255.255.255.0

user: admin<br />
pass: admin


### Buchsenbelegungen bei Freifunkknoten
Gelbe Buchse: Freifunk<br />
Blaue Buchse: LAN

Schließt man sein LAN, das Zugang zum Internet bietet, statt an die blaue an die gelbe Buchse an, wird das Freifunk-WLAN nicht über das Freifunk-Netzwerk geroutet sondern geht direkt über den eigenen Router ins Internet. (Wird in einer zukünftigen Firmware geändert.)

Ausnahmen zu dieser Regel werden auf der Wikiseite des jeweiligen Routers festgehalten.

### Configmode
Der Configmode dient der grundlegenden Einstellung eines Knotens und ist der empfohlene Weg.

Dafür muss der Knoten zuerst komplett gebootet sein und laufen.
Im laufenden Betrieb wird nun die QSS-Taste ca. 5 Sekunden gedrückt gehalten, bis der Knoten merklich neustartet.
<br />
Dann muss der Button ''sofort'' losgelassen werden, damit er in den Configmode geht.

Dann hängt man seinen Rechner an einen der gelben LAN-Ports und lässt sich eine IP geben.
Im Browser wird nun die 192.168.1.1 aufgerufen und die notwendigen Einstellungen werden vorgenommen.
Im letzten Schritt des Configmodes ist der Knoten per Klick neuzustarten, da man sich erst jetzt sicher sein kann, dass er die Einstellungen übernimmt und danach in den Normalzustand als Freifunkknoten neustartet.

### Failsafemode
Der Failsafemode dient der Rettung eines zerschossenen Freifunkrouters und sollte nur im Notfall benutzt werden!

IP: 192.168.1.1<br />
Netmask: 255.255.255.0

Im Failsafemode wird Telnet benutzt, um Zugang zur Kommandozeile des Routers zu erhalten.
Hierfür genügt folgendes Kommando auf der Kommandozeile unter Linux: *telnet 192.168.1.1*

Um Änderungen am System durchführen zu können, muss mit dem Kommando *mount_root* zunächst ein Overlay-Dateisystem über das Wurzelverzeichnis '/' gemountet werden.

Das Passwort für das Webinterface und den SSH-Zugang des Routers kann im ConfigMode (und auch später) mit dem Kommando *passwd* geändert werden, falls es vergessen wurde. Es ist hierfür kein weiteres Passwort nötig. Hiermit kann somit der Zugang zu einem Knoten wiedererlangt werden, wenn dessen Passwort nicht länger bekannt ist und physischer Zugang besteht.

Das Webinterface ist seit Firmware v0.3.1 standardmäßig deaktiviert.
Es sollte aus Sicherheitsgründen auch nicht per Kommandozeile wieder aktiviert werden.

### Normaler Betrieb
Bei Anschluss an das vorhandene Heimnetz bezieht der Knoten seine IP im normalen Betrieb automatisch via DHCP. Dies setzt natürlich voraus, dass ein DHCP-Server im lokalen Netz existiert. Meistens erfüllt diese Aufgabe der DSL-Router über den man ins Internet gelangt.

Statt Telnet wird im normalen Betrieb SSH benutzt, um auf die Kommandozeile des Routers zuzugreifen. Für den SSH-Login gilt das gleiche Passwort wie für das Webinterface. Um sich per SSH auf dem Router einzuloggen genügt folgendes Kommando auf der Kommandozeile unter Linux: *ssh &lt;per DHCP zugewiesene IP&gt;*

### Public fastd-Schlüssel vom Freifunk-Knoten für den VPN-Zugang anzeigen
Auf der Kommandozeile: */etc/init.d/fastd show_key mesh_vpn*


## In den ConfigMode gelangen

### Der Unterschied zwischen Configmode und Failsafemode
Der _Configmode_ ist der Zustand, den ein neuer Knoten nach dem ersten Flashen hat.
In diesem ist er auf den LAN-Ports per modifiziertem Web-Interface einstellbar.
Im ConfigMode blinkt die System-LED langsam.

Das Web-Interface ist in diesem Falle deaktiviert.
Im Configmode blinkt die Sys-LED langsam.
Im Failsafemode blinkt die System-LED schnell.

### Allgemein
* QSS-Taste im normalen Betrieb für mindestens 3 Sekunden gedrückt halten. Loslassen, wenn die Lampen merklich darauf reagieren, in jedem falle **vor** dem blinken der sys-lampe
* Bei erfolgtem Neustart den QSS-Button ''sofort'' los lassen, die sys-lampe sollte dann langsam blinken. Wenn man zu lange gedrückt hält bootet der Knoten in den Failsave-Modus (schnelles blinken)
* Nun an einen der LAN-Ports hängen und die 192.168.1.1 im Browser aufrufen.

### Ausnahmen
* Da der [[841ND|Firmware:841nd]] und der [[842|Firmware:842nd]] nur einen gemeinsamen WPS/RESET-Taster haben, ist dieser zu benutzen.

## Verwaltung
Router können in die [[Knotenliste für Lübeck|Knoten]] bzw. in die [[Knotenliste für Mölln|Moelln:Knoten]] eintragen werden, wenn sie im Knotengraph mit ihrem Namen angezeigt werden sollen.