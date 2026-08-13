# Vor-/Rücklauf-Modul pro Heizkörper (Planung)

Status: **Konzeptphase**, Elektronik-Entwicklung noch nicht gestartet.

## Eckdaten

- Zielstückzahl: ca. **20 Heizkörper** im Haus, je ein Modul pro Heizkörper (Vor- und Rücklaufleitung)
- Mikrocontroller: **ESP32-C6**
- Temperatursensor: **DS18B20** (Anlegefühler an Vor- und Rücklaufrohr)
- Display: kleines **OLED**, nur für Inbetriebnahme/kurzfristige Anzeige — nicht für Dauerbetrieb gedacht
- Stromversorgung: **Batteriebetrieb** — impliziert Deep-Sleep-Strategie und sparsames Funksenden, sonst nicht praktikabel bei 20 Stück

## Offene Punkte

- Firmware-Basis (ESPHome mit Deep Sleep vs. eigene Firmware)
- Sendeintervall/Deep-Sleep-Zyklus für akzeptable Batterielaufzeit
- Gehäuse/Befestigung an Heizkörperrohren
- Batterietyp und erwartete Laufzeit bei 20 Modulen (Wartungsaufwand!)
- Übertragungsweg zu Home Assistant (WLAN direkt vs. z. B. ESP-NOW zu einem Gateway)

Wird erst angegangen, nachdem die eBUS-Parameter ausgewertet sind (siehe [Projekt-Fokus](../README.md#aktueller-fokus)).
