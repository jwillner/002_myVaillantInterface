# Vaillant-eBUS-Interface

## Projektziel

Ziel ist ein möglichst vollständiges **energetisches Abbild des Hauses** in Home Assistant, um darauf basierend Invest-Entscheidungen (Wärmepumpe, Klimaanlage, Solaranlage) treffen zu können. Dazu werden mehrere Datenquellen zusammengeführt:

- **eBUS-Daten der Vaillant-Heizungsanlage** (möglichst viele Parameter: Betriebszustände, Temperaturen, Laufzeiten, Fehlercodes, ...) über das [eBUS Adapter Shield C6](hardware/ebus-adapter-shield-c6.md) von Elecrow
- **Raumtemperaturen** über vorhandene Sonoff-Heizkörperventile (bereits in Home Assistant eingebunden)
- **Vor-/Rücklauftemperaturen an ca. 20 Heizkörpern**, erfasst über noch zu bauende batteriebetriebene Eigenbau-Module (siehe [hardware/](hardware/vorlauf-ruecklauf-modul.md))

Alles läuft in Home Assistant zusammen (Proxmox-VM, lokal erreichbar unter `http://192.168.1.30:8123`), siehe [home-assistant/README.md](home-assistant/README.md) für den Integrationsplan.

## Aktueller Fokus

1. **Jetzt**: möglichst alle eBUS-Parameter der Vaillant-Anlage in Home Assistant sichtbar machen und in Diagrammen auswerten (Grafana oder eigenes Web-Interface).
2. Raumtemperaturen sind über die Sonoff-Heizkörperventile bereits vorhanden.
3. Vor-/Rücklauf-Module pro Heizkörper sind für später geplant, Elektronik-Entwicklung startet noch nicht.

## Verzeichnisstruktur

```text
.
├── docs/             Projektdokumentation
├── firmware/         Zukünftige Firmware des Interfaces
├── hardware/         Hardwareunterlagen und Schaltpläne
├── home-assistant/   Zukünftige Home-Assistant-Integration
└── notes/            Notizen und Rechercheergebnisse
```
