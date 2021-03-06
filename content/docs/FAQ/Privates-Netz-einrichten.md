# Privates Netz einrichten

## Diese Anleitung wird zur Zeit überarbeitet und funktioniert noch nicht!

Was macht diese Anleitung? Was sind die angestrebten Setups?

- **Szenario A1**: Geroutetes Subnetz auf den LAN-Ports.
- **Szenario A2**: Wie **A1**. Zusätzlich privates WLAN gebridged.
- **Szenario B**: Wie **A**, jedoch mit IPv4 Uplink über den WAN-Port.

## TODO

- Subnetzreservierung dokumentieren
- default route über WAN
- MTU Probleme (ip-proto-1 fragments) debuggen

## Vorbereitung im Wiki

[[Netzwerk:IP Subnetze]]

- IPv6 Subnetz reservieren (ein /64)
- IPv4 Subnetz reservieren (ein /27 mit 30 IPs)

## Benötigte Pakete

Für ein privates Netz werden zusätzliche Pakete benötigt:

- bird6
- kmod-nat46
- odhcpd

Diese können mittels `opkg update` gefolgt von `opkg install bird6 kmod-nat46 odhcpd` installiert
werden.

Außerdem muss die Datei `siit.sh` von https://gist.github.com/tcatm/3230254ae5da1a9f60d3 nach `/lib/netifd/proto/siit.sh` kopiert und ausführbar gemacht werden.

## Konfiguration

Zur Konfiguration einmal folgendes in die Shell einfügen und die Zeilen mit `<-- anpassen!` entsprechend abändern.

```
uci set firewall.client.forward=ACCEPT
uci add_list firewall.client.network=lan
uci add_list firewall.client.network=nat64

uci set network.nat64=interface
uci set network.nat64.ip6addr=fe80::1
uci set network.nat64.ip6prefix=2001:67c:2d50:1::/96
uci set network.nat64.proto=siit

uci set network.wan_default=route
uci set network.wan_default.interface=nat64
uci set network.wan_default.netmask=0.0.0.0
uci set network.wan_default.target=0.0.0.0

uci set network.client.ifname=bat0

uci set network.lan=interface
uci set network.lan.ip6assign=64
uci set network.lan.proto=static
uci set network.lan.type=bridge
uci set network.lan.ifname=$(cat /lib/gluon/core/sysconfig/lan_ifname)
uci set network.lan.igmp_snooping=0

uci set network.lan.ip6prefix=2001:67c:2d50:0xxx::/64  <-- anpassen!
uci set network.lan.ipaddr=10.130.yy.1                 <-- anpassen!
uci set network.lan.netmask=255.255.255.224            <-- ggf. anpassen

uci commit
```

## /etc/bird6.conf
Die vorhandene Datei muss durch diese Eintragungen ersetzt werden.

```
router id 1.1.1.1;

protocol static {
  route 2001:67c:2d50:0xxx::/64 reject;                       <-- anpassen!
  route 2001:67c:2d50:0001::10.130.yy.0/123 via "siit-nat64"; <-- anpassen!
  # /123 = /96 + /27
}

protocol kernel {
  scan time 20;
  import none;
  export all;
}

protocol device {
  scan time 10;
}

protocol rip {
  import where rip_metric < 16;
  export all;
  interface "br-client";
}
```

## Initscripte aktivieren

    /etc/init.d/bird6 enable
    /etc/init.d/odhcpd enable

# Optional: WLAN hinzufügen

```
uci set wireless.private_radio0=wifi-iface
uci set wireless.private_radio0.device=radio0
uci set wireless.private_radio0.network=lan
uci set wireless.private_radio0.mode=ap
uci set wireless.private_radio0.encryption=psk2
uci set wireless.private_radio0.ssid=Privates Netz <-- anpassen!
uci set wireless.private_radio0.key=test1234       <-- anpassen!
uci commit
```


Bei Geräten mit zwei WLAN-Bändern (2,4Ghz und 5Ghz) müssen die gleichen Befehle nochmal mit `radio1` statt `radio0` wiederholt werden.

## Port Forwarding (optional)

Wenn auf dem nun im privaten Netz hängenden Computer ein Dienst läuft, der aus dem Internet erreichbar sein soll, so muss eine entsprechende Portweiterleitung sowohl im DSL-Router als auch im Freifunk-Knoten konfiguriert werden. Die Anleitung zur Konfiguration des Freifunk-Knotens kann hier gefunden werden:
http://wiki.openwrt.org/doc/howto/port.forwarding

Es müssen hierzu lediglich die Option "dest_ip" und "target" (freifunk) angepasst werden.
