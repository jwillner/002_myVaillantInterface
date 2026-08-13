# eBUS Adapter Shield C6 (Elecrow)

Fertige Hardware-Lösung für die eBUS-Anbindung, ausgewählt statt Eigenbau.

- Produktseite: https://www.elecrow.com/ebus-adapter-shield-c6.html
- Technische Doku: https://adapter.ebusd.eu/v5-c6

## Spezifikationen

- Mikrocontroller: ESP32-C6 mit integrierter Antenne
- Anschlüsse: USB-C (Host/Stromversorgung), steckbare 3,5-mm-eBUS-Buchse
- Abmessungen: 37×26 mm (ohne Zubehör)
- Stromversorgung: 5 V, <120 mA mit WLAN (empfohlen 2 W / 400 mA)
- Schutz: eFuse gegen Über-Spannung/-Strom
- Galvanische Isolierung zum eBUS

## Konnektivität

- WLAN (onboard)
- USB (erscheint als CDC ACM Device, `/dev/ttyACM<x>` unter Linux)
- Raspberry Pi GPIO-Modus (`/dev/ttyAMA0`)
- Ethernet über optionales W5500-Modul (USR-ES1)

## Konfiguration

- Web-Interface **easi>**, erreichbar über USB oder Netzwerk
- WLAN-Einrichtung wahlweise über Firmware-Seite, automatischen "EBUS"-Access-Point oder WPS-Taster
- Firmware-Updates per OTA über Browser oder USB JTAG/Serial

## D1-Mini-Shield-Kompatibilität (Steckplatz W2)

OLED 0.66", BMP280/BME280/BMP180, 8x8-RGB, DC-Power-Shield, geplant: PIR/DHT/SHT30

Siehe [firmware/README.md](../firmware/README.md) für die Software-Anbindung (ebusd / micro-ebusd).
