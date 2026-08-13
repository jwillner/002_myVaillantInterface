# Home Assistant Integration

Zielinstanz: läuft als **Proxmox-VM**, lokal erreichbar unter `http://192.168.1.30:8123` (bereits produktiv im Einsatz).

Ziel ist ein energetisches Gesamtbild des Hauses aus drei Datenquellen, siehe [Projektziel](../README.md#projektziel).

## Datenquellen

| Quelle | Status | Weg nach HA |
|---|---|---|
| eBUS-Daten (Vaillant) | **aktueller Fokus** | eBUS Adapter Shield C6 → ebusd (HA-Add-on) → HA (siehe unten) |
| Raumtemperaturen (Sonoff-Heizkörperventile) | bereits in HA vorhanden | vorhandene Sonoff-Integration, keine weitere Arbeit nötig |
| Vor-/Rücklauftemperaturen an ca. 20 Heizkörpern | Konzeptphase, für später | eigene batteriebetriebene ESP32-C6/DS18B20-Module, siehe [hardware/vorlauf-ruecklauf-modul.md](../hardware/vorlauf-ruecklauf-modul.md) |

## eBUS-Anbindung: Grundidee

1. eBUS Adapter Shield C6 verbindet sich per WLAN mit dem eBUS der Vaillant-Anlage.
2. [ebusd](https://github.com/john30/ebusd) läuft als **Home-Assistant-Add-on** (existiert bereits im Add-on-Store) und übersetzt die eBUS-Telegramme in lesbare Werte.
3. Die HA Core-Integration `ebusd` verbindet sich per TCP mit dem Add-on und stellt die Werte als Entitäten bereit.

## Aktueller Fokus: möglichst alle eBUS-Parameter sichtbar machen

Ziel ist zunächst, **alle verfügbaren Parameter** der Vaillant-Anlage über ebusd in HA zu holen, dort zusammenzufassen und in Diagrammen darzustellen — Kandidaten dafür:

- **Grafana** (via HA-Add-on + InfluxDB, oder direkt auf Long-Term-Statistics/History-Daten)
- **eigenes Web-Interface** als Alternative/Ergänzung

Noch offen, welcher Weg (Grafana vs. Eigenbau) gewählt wird — dafür muss zuerst geklärt werden, welche Parameter ebusd für das konkrete Vaillant-Gerät überhaupt liefert.

## Vor-/Rücklauf-Erfassung: Grundidee

Siehe [hardware/vorlauf-ruecklauf-modul.md](../hardware/vorlauf-ruecklauf-modul.md) — noch Konzeptphase, wird erst nach der eBUS-Auswertung angegangen.

## Ziel: Energetisches Abbild

Aus den drei Quellen sollen sich ableiten lassen:
- tatsächliche Vorlauftemperaturen im Betrieb (relevant für Wärmepumpen-Eignung)
- Heizlast/-verhalten pro Raum (Sonoff-Ventilstellung + Raumtemperatur vs. Außentemperatur)
- Effizienz-Kennzahlen der bestehenden Anlage als Vergleichsbasis für Wärmepumpe/Klimaanlage/Solar

## Offene Punkte

- Welche Vaillant-spezifische ebusd-Konfiguration (CSV-Dateien für das Gerätemodell) wird benötigt/automatisch erkannt?
- Welche Werte/Entitäten sind relevant (Vorlauftemperatur, Warmwasser, Betriebsmodus, Fehlercodes, ...)?
- Grafana (mit InfluxDB als Backend) oder eigenes Web-Interface für die Diagramme?
