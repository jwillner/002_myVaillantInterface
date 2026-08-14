# Inbetriebnahme ebusd Home-Assistant-Add-on

Schritt-für-Schritt-Dokumentation der Installation und Konfiguration des ebusd-Add-ons, das die Werte des [eBUS Adapter Shield C6](../hardware/ebus-adapter-shield-c6.md) (siehe [Inbetriebnahme-Doku](inbetriebnahme-ebus-adapter-c6.md)) in Home Assistant verfügbar macht.

## Voraussetzung

- Home Assistant OS mit Supervisor (geprüft unter Einstellungen → Info)
- eBUS Adapter Shield C6 bereits im Heimnetz erreichbar unter fester IP `192.168.1.235` (siehe [Inbetriebnahme-Doku](inbetriebnahme-ebus-adapter-c6.md))
- eBUS-Adapter muss zu diesem Zeitpunkt **noch nicht** physisch an die Heizung angeschlossen sein — die Software-Einrichtung funktioniert unabhängig davon.

## Schritt 1: Store-Bezeichnung

Der klassische „Add-on-Store" heißt in aktuellen Home-Assistant-Versionen **„App-Store"**, erreichbar z. B. über `http://<HA-IP>:8123/config/apps/available`.

**Hinweis:** Falls unter „Einstellungen" kein „Add-ons"/„Apps"-Menüpunkt sichtbar ist, prüfen ob die Installation Home Assistant OS mit Supervisor ist (Einstellungen → Info → „Installationsmethode"). Bei uns: ✅ Home Assistant OS, Supervisor 2026.07.5.

## Schritt 2: Community-Repository hinzufügen

ebusd ist kein offizielles Add-on und muss über ein Community-Repository nachgerüstet werden.

**Vorgehen:** App-Store → **„App installieren"** (Button, der das ⋮-Menü mit „Repositories" erst freischaltet) → ⋮ (Drei-Punkte-Menü) → **„Repositories"** → unten rechts **„+ Hinzufügen"** → URL eintragen:

```
https://github.com/LukasGrebe/ha-addons
```

**Begründung der Wahl:** Von mehreren verfügbaren ebusd-Add-on-Repos (`cociweb/ebusd-ha_addons`, `connyg/ebusd`, `nickloman/ebusd-addon`, `LukasGrebe/ha-addons`) war `LukasGrebe/ha-addons` das mit Abstand aktivste (90 GitHub-Stars, letzter Commit Juli 2026).

**Status:** ✅ Repository „eBUSd Add-On" erscheint in der Liste.

## Schritt 3: Add-on installieren

**Aktion:** Im App-Store nach „ebusd" gesucht → Kachel **„eBUSd"** (Beschreibung: „Run ebusd — a daemon for communicating with eBUS devices") angeklickt → **„Installieren"**.

**Status:** ✅ installiert.

## Schritt 4: Autostart-Optionen

Beim ersten Start wurden folgende Schalter aktiviert:
- „Beim Systemstart starten" — an
- „Watchdog" (automatischer Neustart bei Absturz) — an
- „Automatische Updates" — aus (bewusst, um Änderungen kontrolliert zu übernehmen)
- „In Seitenleiste anzeigen" — an

## Schritt 5: Verbindung zum eBUS-Adapter konfigurieren

**Aktion:** Reiter **„Konfiguration"** geöffnet, Schalter **„Nicht verwendete optionale Konfigurationsoptionen einblenden"** aktiviert → dadurch erschien ein Feld **„Netzwerk- / Enhanced-Protokoll-Adapter (--device)"**.

**Eingabe:**
```
ens:192.168.1.235:9999
```

**Bedeutung des Präfix `ens:`** — „enhanced network socket": Enhanced-Protokoll über Netzwerk/TCP (passend, da der Adapter laut easi>-Anzeige „enhanced on, TCP enhanced protocol" spricht). Zum Vergleich: `enh:` wäre Enhanced-Protokoll über eine serielle/USB-Verbindung, `mdns:` löst automatische Netzwerk-Erkennung aus.

Die HA-MQTT-Integrationskonfiguration (`--mqttjson`) blieb auf Standard (aktiviert) — sie sorgt dafür, dass ebusd-Werte automatisch per MQTT Discovery in HA als Entitäten erscheinen.

Danach **„Speichern"** geklickt.

**Status:** ✅ gespeichert.

## Schritt 6: Add-on neu starten

**Aktion:** Reiter „Info"/„Steuerung" → Add-on neu gestartet, damit die neue `--device`-Konfiguration greift.

**Status:** ✅ neu gestartet.

## Schritt 7: Verbindung verifizieren (Terminal statt Web-UI)

**Beobachtung:** „Benutzeroberfläche öffnen" führte in ein **Terminal innerhalb des ebusd-Containers** (Prompt `root@<container-id>-ebusd:/#`), nicht in eine grafische Oberfläche — die eingebaute ebusd-HTTP-Oberfläche ist standardmäßig deaktiviert (`--httpport` nicht gesetzt).

**Aktion:** Im Terminal `ebusctl info` ausgeführt (fragt den laufenden ebusd-Prozess nach Status).

**Ausgabe:**
```
version: ebusd 26.1.26.1
device: 192.168.1.235:9999, TCP, enhanced, firmware 1.1[6112].1[6112]
signal: no signal
reconnects: 0
masters: 1
messages: 11
conditional: 0
poll: 0
update: 4
address 31: master #8, ebusd
address 36: slave #8, ebusd
```

**Interpretation:**
- ✅ ebusd hat sich erfolgreich per Netzwerk mit dem Adapter verbunden (Device-Zeile zeigt IP, TCP, enhanced-Protokoll, sogar die Adapter-Firmware-Version).
- ⚠️ `signal: no signal` ist an dieser Stelle **erwartet und kein Fehler** — es kommt noch kein eBUS-Signal an, weil der Adapter noch nicht an die Heizungsanlage angeschlossen ist.

**Status:** ✅ Software-Einrichtung des ebusd-Add-ons vollständig abgeschlossen. Sobald der Adapter physisch an den eBUS der Heizung angeschlossen wird, sollte `ebusctl info` unter „signal" einen aktiven Zustand zeigen und `messages`/`update` ansteigen.

## Schritt 8: Adapter an der Heizung angeschlossen — Fehlersuche „meldet sich nicht"

**Aktion:** Adapter physisch vom Schreibtisch zur Heizung umgezogen und an den eBUS angeschlossen.

**Beobachtung:** Nach dem Umzug leuchtete die LED am Adapter **gar nicht** mehr.

**Ursache:** Der C6-Shield wird über **USB-C** mit Strom versorgt — die eBUS-Klemmen liefern selbst keinen Strom für den Adapter. Beim Umzug wurde die USB-Stromversorgung nicht wieder angeschlossen.

**Behoben:** Adapter erneut per USB-C an ein Netzteil angeschlossen → LED leuchtet wieder.

**Verifiziert:** Ping auf `192.168.1.235` erfolgreich (Adapter im Heimnetz erreichbar, WLAN-Konfiguration/DHCP-Reservierung hat den Standortwechsel unbeschadet überstanden — kein Fallback in den unkonfigurierten „EBUS"-AP-Modus).

**`ebusctl info` nach Anschluss an die Heizung:**
```
signal: acquired
scan: finished
masters: 6
messages: 242
address 00: master #1
address 03: master #11
address 04: slave #25, scanned "MF=Vaillant;ID=NETX3;SW=0129;HW=0404"
address 08: slave #11, scanned "MF=Vaillant;ID=BAI00;SW=0704;HW=7603", loaded "vaillant/08.bai.csv"
address 10: master #2
address 15: slave #2, scanned "MF=Vaillant;ID=EMM00;SW=0104;HW=8503"
address 31: master #8, ebusd
address 36: slave #8, ebusd
address f1: master #10
address f6: slave #10, scanned "MF=Vaillant;ID=NETX3;SW=0129;HW=0404"
address ff: master #25
```

**Interpretation:** eBUS-Anbindung an die Heizung erfolgreich. Gefundene Teilnehmer:
- `NETX3` (Adressen 04/f6) — Bedienmodul/Regler
- `BAI00` (Adresse 08) — Wärmeerzeuger (Heizgerät), passende CSV-Konfiguration automatisch geladen
- `EMM00` (Adresse 15) — Erweiterungsmodul

**Status:** ✅ Adapter erfolgreich an der Heizung angeschlossen, eBUS-Signal wird empfangen und Teilnehmer erkannt.

## Schritt 9: MQTT Discovery verifizieren

**Voraussetzung geprüft:** Unter **Einstellungen → Geräte & Dienste** war bereits eine **MQTT**-Integration verbunden (Broker lief schon produktiv, u. a. für eine Zigbee2MQTT-Bridge) — keine zusätzliche Einrichtung nötig.

**Aktion:** Unter **Einstellungen → Geräte & Dienste → Entitäten** nach „ebusd" gesucht.

**Ergebnis:** Discovery hat automatisch mehrere Geräte angelegt, u. a. **„ebusd bai"** (= Wärmeerzeuger BAI00) mit ca. 80 Sensor-Entitäten (alle internen Zähler, Zustände und Temperaturen, die ebusd aus der `vaillant/08.bai.csv`-Konfiguration kennt).

**Beobachtung — Warmwasser-Werte sind Platzhalter:** Die Anlage hat **kein Warmwasser/keinen Speicher**. Entsprechende Sensoren wie `HwcTemp` (116,1 °C) und `StorageTemp` (-14,9 °C) zeigen unplausible Werte — vermutlich codiert eBUS bei nicht vorhandener Hardware einen Platzhalter/Ungültig-Wert, der bei manchen Feldern als „Unbekannt", bei anderen als unplausible Zahl dargestellt wird. Diese Sensoren daher nicht fürs Dashboard verwenden.

**Beobachtung — Rücklauftemperatur bei fehlender Heizanforderung:** `Expertlevel_ReturnTemp` zeigte im Sommer ohne aktive Heizanforderung einen negativen Wert (-1,8 °C), obwohl Außentemperatur 23,9 °C betrug. Vermutete Ursache: ohne Wasserfluss durch den Rücklauffühler (Heizkreis-Pumpe inaktiv/„post_run") liefert das Feld keinen echten Messwert, sondern denselben Art Platzhalter-Code wie oben. **Noch zu verifizieren:** Wird der Wert plausibel, sobald die Heizung tatsächlich eine aktive Heizanforderung fährt?

**Status:** ✅ MQTT Discovery funktioniert wie erwartet.

## Schritt 10: Dashboard-Karte „Heizung" angelegt

**Aktion:** Auf dem „Übersicht"-Dashboard über **Bearbeiten → Karte hinzufügen → Entitäten → YAML bearbeiten** folgende Karte angelegt:

```yaml
type: entities
title: Heizung
entities:
  - entity: sensor.heating_ebusd_bai_flowtemp_temp
    name: Vorlauf Ist
  - entity: sensor.heating_ebusd_bai_expertlevel_returntemp_temp
    name: Rücklauf Ist
  - entity: sensor.heating_ebusd_bai_flowtempdesired
    name: Vorlauf Soll
  - entity: sensor.heating_ebusd_bai_flowtempmax
    name: Vorlauf Max
  - entity: sensor.heating_ebusd_bai_outdoorstempsensor_temp
    name: Außentemperatur
  - entity: sensor.heating_ebusd_bai_hcpumpmode
    name: Heizkreis-Pumpe
  - entity: sensor.heating_ebusd_bai_hcstarts
    name: Heizkreis-Starts (gesamt)
  - entity: sensor.heating_ebusd_bai_hcunderhundredstarts
    name: Kurztakt-Starts (<100s)
```

**Auswahlkriterium:** Fokus auf Werte, die auf das Projektziel „energetisches Abbild fürs Wärmepumpen-Invest" einzahlen — Vorlauf/Rücklauf für die Spreizung (ΔT), Außentemperatur als Referenz, Start-/Kurztakt-Zähler als Indikator für eine mögliche Überdimensionierung des bestehenden Wärmeerzeugers. Warmwasser-Sensoren bewusst ausgelassen (Anlage hat kein Warmwasser).

**Status:** ✅ Karte gespeichert und auf der Übersicht sichtbar.

## Nächste Schritte (offen)

1. Verifizieren, ob `Expertlevel_ReturnTemp` bei aktiver Heizanforderung einen plausiblen Wert liefert (siehe Beobachtung in Schritt 9).
2. Prüfen, ob `VortexFlowSensor` und `PrEnergyCountHc1-3` (aktuell „Unbekannt") jemals Werte liefern — daraus ließe sich die tatsächliche Heizleistung (Durchfluss × ΔT) berechnen, die stärkste Kennzahl für die Wärmepumpen-Entscheidung.
3. Diagramm-/Auswertungsweg festlegen (Grafana vs. eigenes Web-Interface) — siehe [home-assistant/README.md](../home-assistant/README.md).
