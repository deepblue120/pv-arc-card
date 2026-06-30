# PV Arc Card

[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge)](https://github.com/hacs/integration)

Eine Custom Lovelace Card für Home Assistant, die deine PV-Anlage als Halbkreis-Bogen visualisiert — mit zwei umschaltbaren Ansichten für Tageswerte (kWh) und Live-Leistung (W) — Schwesterkarte der Consumption Arc Card, strukturell identisch aufgebaut, aber für Erzeugung statt Verbrauch.

![Screenshot](docs/screenshot-energy.png)
![Screenshot](docs/screenshot-live.png)

## Funktionen

**Tab "Tageswerte"**
- Halbkreis-Bogen von Min- bis Max-Erzeugung eines Tages, Marker zeigt die erzeugte Energiemenge im gewählten Zeitraum
- Zeitraum-Auswahl: Tag / Woche / Monat / Jahr / Gesamt — Werte werden direkt aus der Home-Assistant-Langzeitstatistik berechnet (gleiche Methode wie das offizielle Energy-Dashboard), kein zusätzlicher Sensor pro Zeitraum nötig
- Restprognose (z. B. von Solcast), sichtbar bei Zeitraum "Tag"
- Energiefluss-Sektion: Haus / Batterie / Netz, zeitraumabhängig in kWh
- Bis zu 4 frei konfigurierbare String-/Wechselrichter-Kacheln mit optionaler kWh/Modul-Berechnung

**Tab "Live"**
- Halbkreis-Bogen von 0 bis Max-Leistung, Marker zeigt aktuelle Gesamtleistung
- Autarkiegrad (direkter Sensor oder berechnet aus Leistung & Hausverbrauch)
- Energiefluss-Sektion: Haus / Batterie / Netz in Watt
- Bis zu 4 String-Kacheln inkl. optionaler Wirkungsgrad-Berechnung (kWp pro Modul)

**Weitere Eigenschaften**
- Frei konfigurierbare Farbschwellen (Bogen-Farbe ändert sich je nach Wert)
- Nachkommastellen separat pro Zeitraum einstellbar
- Vollständig über den visuellen Editor konfigurierbar, kein YAML nötig (geht aber auch)

## Installation

### Über HACS (empfohlen)

1. HACS öffnen → Menü (⋮) oben rechts → **Benutzerdefinierte Repositories**
2. Repository-URL eintragen: `https://github.com/deepblue120/pv-arc-card`
3. Kategorie: **Dashboard**
4. **Hinzufügen**, dann die Karte in HACS suchen und installieren
5. Home Assistant neu laden (Browser-Cache leeren, `Strg+Shift+R`)

### Manuell

1. [`pv-arc-card.js`](dist/pv-arc-card.js) herunterladen
2. Nach `/config/www/pv-arc-card.js` kopieren
3. In **Einstellungen → Dashboards → Ressourcen** hinzufügen:
   - URL: `/local/pv-arc-card.js`
   - Typ: **JavaScript-Modul**
4. Browser-Cache leeren (`Strg+Shift+R`)

## Verwendung

Karte im Dashboard hinzufügen, Typ: `custom:pv-arc-card`. Über den visuellen Editor lassen sich alle Entities, Farben und Optionen einstellen — alternativ direkt per YAML:

```yaml
type: custom:pv-arc-card
title: Photovoltaik
default_tab: energy

# Tageswerte (kWh)
produced_entity: sensor.pv_energie_gesamt_taeglich
forecast_remaining_entity: sensor.solcast_pv_forecast_prognose_verbleibend
forecast_total_entity: sensor.solcast_pv_forecast_prognose_heute
energy_min_entity: sensor.pv_energie_min_heute
energy_max_entity: sensor.pv_energie_max_heute
energy_color_stops:
  - { value: 0, color: "#d32f2f" }
  - { value: 10, color: "#fbc02d" }
  - { value: 25, color: "#2e7d32" }
energy_flow_house_entity: sensor.hausverbrauch_heute_kwh
energy_flow_battery_entity: sensor.batterie_heute_kwh
energy_flow_grid_entity: sensor.netz_heute_kwh
energy_tiles:
  - { name: "String 1", entity: sensor.pv_string_1_kwh_heute, module_count: 10 }
  - { name: "String 2", entity: sensor.pv_string_2_kwh_heute, module_count: 8 }

# Live (W)
power_entity: sensor.pv_leistung_gesamt
power_max_entity: input_number.pv_leistung_peak_allzeit
flow_house_entity: sensor.hausverbrauch_aktuell
flow_battery_entity: sensor.batterie_leistung
flow_grid_entity: sensor.netz_leistung
power_tiles:
  - { name: "String 1", entity: sensor.pv_string_1_leistung, module_count: 10, module_peak_wp: 420 }
```

Eine vollständige Liste aller Optionen mit Erklärung steht im Kopfkommentar von [`dist/pv-arc-card.js`](dist/pv-arc-card.js).

## Voraussetzungen

- Für die Zeitraum-Auswahl (Woche/Monat/Jahr/Gesamt) im Tageswerte-Tab benötigt `produced_entity` (und optional die Energiefluss-/Kachel-Entities) `state_class: total` oder `total_increasing` mit aktivierter Langzeitstatistik in Home Assistant.

## Lizenz

[MIT](LICENSE)
