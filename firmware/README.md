# Firmware

Für das [eBUS Adapter Shield C6](../hardware/ebus-adapter-shield-c6.md) ist keine eigene Firmware-Entwicklung nötig — das Gerät wird mit vorinstallierter Firmware ausgeliefert und über OTA aktualisiert.

## Anbindungsoptionen an ebusd

Der Adapter dient als Netzwerk-/USB-Gateway zum eBUS. Die eigentliche Protokoll-Auswertung übernimmt [ebusd](https://github.com/john30/ebusd) auf einem separaten Host (z. B. dem Home-Assistant-Server):

- **WLAN**: `ebusd -d ens:<IP-Adresse-Adapter>:9999`
- **USB**: erscheint als `/dev/ttyACM<x>`
- **Raspberry Pi GPIO**: `ebusd -d ens:/dev/ttyAMA0 --latency=50`
- **Ethernet**: über optionales W5500-Modul

## Alternative: micro-ebusd on-device

Der Adapter kann ebusd-Funktionalität (reduziert) auch direkt an Bord ausführen (**micro-ebusd**, benötigt Token) und Daten per MQTT oder HTTP bereitstellen — dann entfällt ein separater ebusd-Host.

Entscheidung für dieses Projekt: **offen** — siehe [home-assistant/README.md](../home-assistant/README.md) für den geplanten Integrationsweg.
