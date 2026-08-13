# Inbetriebnahme eBUS Adapter Shield C6 – WLAN-Einrichtung

Ausführliche Schritt-für-Schritt-Dokumentation der Ersteinrichtung, so dass sie auch eine Person ohne Vorwissen nachvollziehen kann. Wird laufend während der echten Inbetriebnahme ergänzt (jeder Schritt = das, was tatsächlich gemacht und beobachtet wurde, nicht nur Theorie).

**Wichtig für später:** Der hier verwendete Mechanismus – Gerät startet unkonfiguriert einen eigenen WLAN-Access-Point, über den man das Ziel-WLAN einträgt – ist als Vorbild/Referenz für die Firmware der eigenen [Vor-/Rücklauf-Module](../hardware/vorlauf-ruecklauf-modul.md) gedacht. Deshalb wird hier besonders genau dokumentiert, wie es sich in der Praxis verhält (nicht nur, was die Hersteller-Doku behauptet).

## Voraussetzungen

- eBUS Adapter Shield C6
- USB-C-Kabel + haushaltsübliches USB-Netzteil (mind. 1 W)
- Smartphone oder Laptop mit WLAN, im selben Heimnetz wie später Home Assistant

Das Gerät wird **noch nicht** an den eBUS/die Heizung angeschlossen — reine WLAN-Einrichtung am Schreibtisch.

## Schritt 1: Stromversorgung

**Aktion:** Adapter per USB-C mit einem USB-Netzteil verbinden.

**Erwartetes/beobachtetes Verhalten:** LED leuchtet grün → Gerät ist mit Strom versorgt und gebootet.

**Status:** ✅ durchgeführt

## Schritt 2: Mit dem geräteeigenen Access Point "EBUS" verbinden

**Aktion:** In der WLAN-Liste des Smartphones/Laptops nach einem Netzwerk namens **„EBUS"** suchen und sich damit verbinden.

**Hintergrund:** Im unkonfigurierten Zustand macht der Adapter selbst einen WLAN-Access-Point auf, über den man ihn erreichen und ins Heim-WLAN eintragen kann.

**Status:** ✅ durchgeführt — Verbindung zu „EBUS" hergestellt.

**Beobachtung:** Das Netzwerk „EBUS" verlangte **kein Passwort** — offener Access Point.

## Schritt 3: Konfigurationsseite im Browser öffnen

**Aktion:** Browser auf dem mit „EBUS" verbundenen Gerät geöffnet, Adresse `http://192.168.4.1` aufgerufen.

**Beobachtung:** Eine Anmeldeseite erscheint. ✅ Adresse `192.168.4.1` war korrekt.

**Korrektur:** Es handelt sich nicht um eine klassische Login-Seite, sondern um die **easi>-Konfigurationsübersicht** des Adapters. Sie zeigt den Status der verfügbaren Schnittstellen:

- **WIFI Station/Client:** off (noch nicht mit einem Heim-WLAN verbunden)
- **WIFI Access Point:** (off) — gemeint ist vermutlich der Status des separaten AP-Modus, nicht der aktuell aktive „EBUS"-AP selbst
- **Ethernet:** not available (kein optionales Ethernet-Modul gesteckt)
- **eBUS:** enhanced on, TCP enhanced protocol gewählt, connection TCP (Protokoll-Einstellung für die spätere eBUS-Anbindung, an dieser Stelle nicht relevant, da noch nicht an die Heizung angeschlossen)

## Schritt 4: WLAN-Station (Client-Modus) konfigurieren

**Aktion:** Im Bereich „WIFI Station/Client" SSID und Passwort des Heim-WLANs eingetragen, danach den Toggle „On" betätigt.

**Beobachtung:** Es gab kein separates „Speichern"/„Apply"-Element — der **Toggle „On" selbst löst offenbar Speichern + Aktivieren aus**.

**Status:** ⏳ wird in Schritt 5 verifiziert (Verbindungsaufbau zum Heim-WLAN).

## Schritt 5: Neustart und Verbindung zum Heim-WLAN

**Aktion:** „Reboot" auf der easi>-Seite ausgelöst.

**Beobachtung:** Nach dem Neustart ist das Gerät im Heim-WLAN sichtbar: IP **192.168.1.235**, Hostname/Gerätename **„ebus_f4dce8"** (vermutlich `ebus_<Teil der MAC-Adresse>`).

**Status:** ✅ WLAN-Einbindung erfolgreich abgeschlossen. Der „EBUS"-Access-Point ist damit nicht mehr aktiv, das Gerät ist ab jetzt unter `192.168.1.235` im Heimnetz erreichbar.

## Ergebnis dieser Inbetriebnahme-Phase

| Eigenschaft | Wert |
|---|---|
| IP-Adresse (Heimnetz) | 192.168.1.235 |
| Hostname | ebus_f4dce8 |
| eBUS-Protokoll (easi>-Anzeige) | enhanced on, TCP enhanced protocol, connection TCP |

**Empfehlung:** Im Router für die MAC-Adresse dieses Geräts eine **feste IP-Reservierung (DHCP-Reservation)** einrichten, damit sich `192.168.1.235` später nicht ändert — diese Adresse wird für die ebusd-Konfiguration in Home Assistant gebraucht.

## Schritt 6: DHCP-Reservierung im Router (TP-Link Archer C6)

**Aktion:** Router-Oberfläche unter `192.168.1.1` → **Advanced → Network → DHCP Server → Address Reservation → Add New**. In der DHCP-Client-Liste war das Gerät bereits als `ebus-f4dce8` mit MAC `58-E6-C5-F4-DC-E8` und IP `192.168.1.235` sichtbar. Beide Werte ins Formular übernommen und gespeichert.

**Ergebnis:** Feste IP-Reservierung angelegt:

| MAC-Adresse | Reservierte IP | Gerätename (Router) |
|---|---|---|
| 58-E6-C5-F4-DC-E8 | 192.168.1.235 | ebus-f4dce8 |

**Status:** ✅ WLAN-Inbetriebnahme inkl. fester IP-Reservierung vollständig abgeschlossen. Der Adapter kann jetzt an die Heizung montiert werden; für die spätere ebusd-Konfiguration in Home Assistant gilt fest: `192.168.1.235:9999` (Standardport, enhanced protocol).

## Rückblick: Mechanismus für eigene Firmware (Vor-/Rücklauf-Module)

Zusammengefasst zeigte der Adapter folgendes Onboarding-Verhalten, das als Vorbild dienen kann (siehe [hardware/vorlauf-ruecklauf-modul.md](../hardware/vorlauf-ruecklauf-modul.md)):

1. Unkonfiguriertes Gerät öffnet automatisch einen eigenen, passwortlosen WLAN-AP (hier: SSID „EBUS").
2. Web-Oberfläche unter der Standard-AP-Gateway-IP (`192.168.4.1`) erreichbar, zeigt Status aller Schnittstellen.
3. ZieL-WLAN (SSID/Passwort) wird in einem Formular eingetragen; ein einzelner Toggle „On" übernimmt Speichern + Aktivieren (kein separater Speichern-Button nötig).
4. Reboot verbindet das Gerät endgültig mit dem Ziel-WLAN; danach ist der eigene AP inaktiv, Gerät läuft im Zielnetz unter einem Hostname-Schema `<produktname>_<mac-suffix>`.

Für die eigenen batteriebetriebenen Module ist zusätzlich zu bedenken: Deep-Sleep-Firmware kann diesen AP-Mechanismus nur beim allerersten Setup nutzen (AP-Modus kostet zu viel Strom für Dauerbetrieb) — vermutlich reicht ESPHomes eingebauter „Improv"/Fallback-AP-Mechanismus für die einmalige Ersteinrichtung.

