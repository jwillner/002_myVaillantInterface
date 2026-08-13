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

## Nächste Schritte (offen)

1. Adapter physisch an die Vaillant-Heizung anschließen (eBUS-Klemmen).
2. `ebusctl info` erneut prüfen — Signal sollte kommen.
3. Verfügbare Werte scannen (`ebusctl find` bzw. passende Vaillant-CSV-Konfiguration für das konkrete Gerätemodell klären).
4. Home-Assistant-Entitäten prüfen (über MQTT Discovery sollten automatisch Sensoren erscheinen).
5. Diagramm-/Auswertungsweg festlegen (Grafana vs. eigenes Web-Interface) — siehe [home-assistant/README.md](../home-assistant/README.md).
