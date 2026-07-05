[README.md](https://github.com/user-attachments/files/29678338/README.md)
# HA_Luba_Dashboard
Mammotion Luba Dashboard for your Home Assistant
# Miss Green – Home Assistant Luba Dashboard

Dieses Repository enthält ein Home-Assistant-Dashboard für einen Mammotion/Luba Mähroboter mit dem Namen **Miss Green**. Das Dashboard bietet Bereichsauswahl, Start/Pause/Dock-Steuerung, Akkustatus, WLAN-/GPS-Anzeige sowie einen einfachen Wochenplaner.
<img width="1084" height="860" alt="Screenshot 2026-07-05 210731" src="https://github.com/user-attachments/assets/20bcfc2a-2b25-4122-bc05-fd385e09e358" />


## Funktionen

- Visuelle Bereichsauswahl direkt auf einem Gartenbild
- Start, Pause und Rückkehr zur Ladestation
- Anzeige von Akku, Status, Aufgabenfortschritt, WLAN-Signal und GPS-Satelliten
- Konfigurierbare Mähparameter wie Schnitthöhe, Geschwindigkeit, Spurabstand, Muster und Randrunden
- Planer mit Wochentagen, Startzeit und Bereichsauswahl
- Moderne Optik mit Mushroom Cards, Button Card und Card Mod

## Voraussetzungen

### Home Assistant

- Home Assistant mit aktiviertem Lovelace/YAML-Dashboard
- Zugriff auf den Ordner `/config/www/`
- HACS empfohlen

### Benötigte Integrationen / Frontend-Karten

Installiere diese Komponenten über HACS:

| Komponente | Zweck |
|---|---|
| **Mammotion / Luba Integration** | Stellt den Mähroboter und Sensoren bereit |
| **Button Card** | Für `custom:button-card` |
| **Mushroom Cards** | Für Mushroom Title, Template, Entity und Chips Cards |
| **card-mod** | Für CSS-Anpassungen der Karten |

## Benötigte Entitäten

Das Dashboard erwartet folgende Entitäten. Die Namen müssen entweder genauso existieren oder im Dashboard-Code angepasst werden.

### Luba / Mammotion Entitäten

```yaml
lawn_mower.garten_miss_green
sensor.garten_miss_green_akku
sensor.garten_miss_green_status
sensor.garten_miss_green_aufgabenfortschritt
sensor.garten_miss_green_wlan_signalqualitat_rssi
sensor.garten_miss_green_satelliten_mahroboter
```

### Bereichs-Schalter

```yaml
switch.garten_miss_green_bereich_area_1
switch.garten_miss_green_bereich_area_2
switch.garten_miss_green_bereich_area_3
```

Diese Schalter werden verwendet, um die Mähbereiche direkt im Dashboard auszuwählen.

## Benötigte Bilddateien

Lege folgende Dateien in Home Assistant unter `/config/www/luba/` ab:

```text
/config/www/luba/miss_green_3d_clean.png
/config/www/luba/luba_clean.png
/config/www/luba/luba_battery_panel.png
```

Im Dashboard werden diese Dateien über `/local/luba/...` eingebunden:

```yaml
/local/luba/miss_green_3d_clean.png
/local/luba/luba_clean.png
/local/luba/luba_battery_panel.png
```

## Helper anlegen

Die folgenden Helper kannst du entweder über die Home-Assistant-Oberfläche unter **Einstellungen → Geräte & Dienste → Helfer** anlegen oder per YAML definieren.

### Beispiel `configuration.yaml`

```yaml
input_boolean:
  luba_plan_1_aktiv:
    name: Luba Plan 1 aktiv
  luba_plan_1_bereich_4:
    name: Luba Plan Bereich 1
  luba_plan_1_bereich_5:
    name: Luba Plan Bereich 2
  luba_plan_1_bereich_6:
    name: Luba Plan Bereich 3
  luba_plan_1_mo:
    name: Luba Plan Montag
  luba_plan_1_di:
    name: Luba Plan Dienstag
  luba_plan_1_mi:
    name: Luba Plan Mittwoch
  luba_plan_1_do:
    name: Luba Plan Donnerstag
  luba_plan_1_fr:
    name: Luba Plan Freitag
  luba_plan_1_sa:
    name: Luba Plan Samstag
  luba_plan_1_so:
    name: Luba Plan Sonntag
  luba_regenstopp:
    name: Luba Regenstopp

input_number:
  luba_plan_1_schnitthoehe:
    name: Luba Plan Schnitthöhe
    min: 20
    max: 70
    step: 5
    unit_of_measurement: mm
  luba_plan_1_geschwindigkeit:
    name: Luba Plan Geschwindigkeit
    min: 0.2
    max: 1.2
    step: 0.1
  luba_plan_1_spurabstand:
    name: Luba Plan Spurabstand
    min: 15
    max: 35
    step: 1
    unit_of_measurement: cm
  luba_plan_1_randrunden:
    name: Luba Plan Randrunden
    min: 0
    max: 4
    step: 1
  luba_schnitthoehe:
    name: Luba Schnitthöhe
    min: 20
    max: 70
    step: 5
    unit_of_measurement: mm
  luba_geschwindigkeit:
    name: Luba Geschwindigkeit
    min: 0.2
    max: 1.2
    step: 0.1
  luba_spurabstand:
    name: Luba Spurabstand
    min: 15
    max: 35
    step: 1
    unit_of_measurement: cm
  luba_randrunden:
    name: Luba Randrunden
    min: 0
    max: 4
    step: 1

input_select:
  luba_zone:
    name: Luba Zone
    options:
      - Bereich 1
      - Bereich 2
      - Bereich 3
      - Alle Bereiche
  luba_muster:
    name: Luba Mähmuster
    options:
      - Standard
      - Schachbrett
      - Parallel
      - Diagonal

input_datetime:
  luba_plan_1_zeit:
    name: Luba Plan Startzeit
    has_date: false
    has_time: true
```

Nach Änderungen an der `configuration.yaml` Home Assistant neu starten oder die YAML-Konfiguration neu laden.

## Benötigte Scripts

Das Dashboard ruft diese Scripts auf:

```yaml
script.luba_start_mit_einstellungen
script.luba_pause
script.luba_zur_ladestation
```

Die konkreten Services können je nach Mammotion/Luba-Integration unterschiedlich heißen. Passe die Service-Aufrufe deshalb an deine Integration an.

### Beispiel `scripts.yaml`

```yaml
luba_start_mit_einstellungen:
  alias: Luba Start mit Einstellungen
  mode: single
  sequence:
    - service: lawn_mower.start_mowing
      target:
        entity_id: lawn_mower.garten_miss_green

luba_pause:
  alias: Luba Pause
  mode: single
  sequence:
    - service: lawn_mower.pause
      target:
        entity_id: lawn_mower.garten_miss_green

luba_zur_ladestation:
  alias: Luba zur Ladestation
  mode: single
  sequence:
    - service: lawn_mower.dock
      target:
        entity_id: lawn_mower.garten_miss_green
```

> Hinweis: Falls deine Integration andere Dienste verwendet, ersetze `lawn_mower.start_mowing`, `lawn_mower.pause` und `lawn_mower.dock` durch die Services deiner Integration.

## Installation

1. HACS installieren, falls noch nicht vorhanden.
2. Über HACS folgende Frontend-Komponenten installieren:
   - Button Card
   - Mushroom Cards
   - card-mod
3. Mammotion/Luba-Integration installieren und den Mähroboter einrichten.
4. Prüfen, ob die Entitäten im Dashboard-Code zu deinen echten Entitäten passen.
5. Bilder in `/config/www/luba/` ablegen.
6. Helper anlegen oder YAML aus diesem README übernehmen.
7. Scripts anlegen und Service-Aufrufe an deine Integration anpassen.
8. Home Assistant neu starten oder Konfiguration neu laden.
9. Neues Dashboard oder neue Ansicht anlegen.
10. Den folgenden Dashboard-Code in den YAML-Modus der Ansicht einfügen.

## Dashboard-Code

```yaml
type: sections
title: Miss Green
cards: []
sections:
  - type: grid
    cards:
      - type: vertical-stack
        cards:
          - type: picture-elements
            image: /local/luba/miss_green_3d_clean.png
            elements:
              - type: custom:button-card
                entity: switch.garten_miss_green_bereich_area_1
                name: Bereich 1
                show_icon: false
                tap_action:
                  action: toggle
                styles:
                  card:
                    - background: |
                        [[[
                          return entity.state === 'on'
                            ? 'rgba(117,255,90,0.28)'
                            : 'rgba(117,255,90,0.05)';
                        ]]]
                    - border: |
                        [[[
                          return entity.state === 'on'
                            ? '2px solid rgba(117,255,90,0.95)'
                            : '3px solid rgba(117,255,90,0.35)';
                        ]]]
                    - box-shadow: |
                        [[[
                          return entity.state === 'on'
                            ? '0 0 28px rgba(117,255,90,0.85)'
                            : '0 0 10px rgba(117,255,90,0.25)';
                        ]]]
                    - border-radius: 28px
                    - color: white
                    - font-weight: 900
                    - font-size: 12px
                style:
                  top: 12%
                  left: 42%
                  width: 15%
                  height: 25%
                  transform: rotate(-6deg)
              - type: custom:button-card
                entity: switch.garten_miss_green_bereich_area_2
                name: Bereich 2
                show_icon: false
                tap_action:
                  action: toggle
                styles:
                  card:
                    - background: |
                        [[[
                          return entity.state === 'on'
                            ? 'rgba(117,255,90,0.28)'
                            : 'rgba(117,255,90,0.05)';
                        ]]]
                    - border: |
                        [[[
                          return entity.state === 'on'
                            ? '2px solid rgba(117,255,90,0.95)'
                            : '3px solid rgba(117,255,90,0.35)';
                        ]]]
                    - box-shadow: |
                        [[[
                          return entity.state === 'on'
                            ? '0 0 28px rgba(117,255,90,0.85)'
                            : '0 0 10px rgba(117,255,90,0.25)';
                        ]]]
                    - border-radius: 28px
                    - color: white
                    - font-weight: 900
                    - font-size: 18px
                style:
                  top: 54%
                  left: 50%
                  width: 39%
                  height: 46%
                  transform: rotate(-2deg)
              - type: custom:button-card
                entity: switch.garten_miss_green_bereich_area_3
                name: Bereich 3
                show_icon: false
                tap_action:
                  action: toggle
                styles:
                  card:
                    - background: |
                        [[[
                          return entity.state === 'on'
                            ? 'rgba(117,255,90,0.28)'
                            : 'rgba(117,255,90,0.05)';
                        ]]]
                    - border: |
                        [[[
                          return entity.state === 'on'
                            ? '2px solid rgba(117,255,90,0.95)'
                            : '3px solid rgba(117,255,90,0.35)';
                        ]]]
                    - box-shadow: |
                        [[[
                          return entity.state === 'on'
                            ? '0 0 28px rgba(117,255,90,0.85)'
                            : '0 0 10px rgba(117,255,90,0.25)';
                        ]]]
                    - border-radius: 28px
                    - color: white
                    - font-weight: 900
                    - font-size: 18px
                style:
                  top: 45%
                  left: 12%
                  width: 38%
                  height: 33%
                  transform: rotate(-3deg)
              - type: custom:button-card
                name: Start
                icon: mdi:play-circle
                tap_action:
                  action: call-service
                  service: script.luba_start_mit_einstellungen
                styles:
                  card:
                    - height: 58px
                    - border-radius: 22px
                    - background: linear-gradient(145deg,#75ff5a,#2f8f20)
                    - color: "#06121f"
                    - font-weight: 900
                    - box-shadow: 0 0 28px rgba(117,255,90,.75)
                  icon:
                    - color: "#06121f"
                style:
                  top: 91%
                  left: 50%
                  width: 22%
              - type: custom:button-card
                name: Pause
                icon: mdi:pause-circle
                tap_action:
                  action: call-service
                  service: script.luba_pause
                styles:
                  card:
                    - height: 54px
                    - border-radius: 20px
                    - background: rgba(5,12,20,.82)
                    - color: white
                    - border: 1px solid rgba(255,255,255,.18)
                  icon:
                    - color: "#ffd45b"
                style:
                  top: 91%
                  left: 73%
                  width: 18%
              - type: custom:button-card
                name: Dock
                icon: mdi:home-import-outline
                tap_action:
                  action: call-service
                  service: script.luba_zur_ladestation
                styles:
                  card:
                    - height: 54px
                    - border-radius: 20px
                    - background: rgba(5,12,20,.82)
                    - color: white
                    - border: 1px solid rgba(255,255,255,.18)
                  icon:
                    - color: "#75ff5a"
                style:
                  top: 91%
                  left: 27%
                  width: 18%
          - type: custom:mushroom-title-card
            title: Bereiche auswählen
            subtitle: Diese Auswahl wird für den Mähstart verwendet
          - type: grid
            columns: 3
            square: false
            cards:
              - type: custom:mushroom-template-card
                entity: switch.garten_miss_green_bereich_area_1
                primary: Bereich 1
                secondary: >
                  {{ 'Ausgewählt' if is_state(entity, 'on') else 'Nicht
                  ausgewählt' }}
                icon: mdi:numeric-1-circle
                icon_color: |
                  {{ 'green' if is_state(entity, 'on') else 'grey' }}
                tap_action:
                  action: toggle
              - type: custom:mushroom-template-card
                entity: switch.garten_miss_green_bereich_area_2
                primary: Bereich 2
                secondary: >
                  {{ 'Ausgewählt' if is_state(entity, 'on') else 'Nicht
                  ausgewählt' }}
                icon: mdi:numeric-2-circle
                icon_color: |
                  {{ 'green' if is_state(entity, 'on') else 'grey' }}
                tap_action:
                  action: toggle
              - type: custom:mushroom-template-card
                entity: switch.garten_miss_green_bereich_area_3
                primary: Bereich 3
                secondary: >
                  {{ 'Ausgewählt' if is_state(entity, 'on') else 'Nicht
                  ausgewählt' }}
                icon: mdi:numeric-3-circle
                icon_color: |
                  {{ 'green' if is_state(entity, 'on') else 'grey' }}
                tap_action:
                  action: toggle
          - type: grid
            columns: 3
            square: false
            cards:
              - type: custom:mushroom-template-card
                primary: Start
                icon: mdi:play-circle
                icon_color: green
                tap_action:
                  action: call-service
                  service: script.luba_start_mit_einstellungen
              - type: custom:mushroom-template-card
                primary: Pause
                icon: mdi:pause-circle
                icon_color: amber
                tap_action:
                  action: call-service
                  service: script.luba_pause
              - type: custom:mushroom-template-card
                primary: Dock
                icon: mdi:home-import-outline
                icon_color: blue
                tap_action:
                  action: call-service
                  service: script.luba_zur_ladestation
        grid_options:
          rows: auto
      - type: vertical-stack
        cards:
          - type: custom:mushroom-title-card
            title: Planer
            subtitle: Wähle Bereiche, Tage und Mähparameter
          - type: custom:mushroom-entity-card
            entity: input_boolean.luba_plan_1_aktiv
            name: Planer aktiv
            icon: mdi:calendar-check
            icon_color: green
            card_mod:
              style: |
                ha-card {
                  border-radius: 22px;
                  background: linear-gradient(145deg,#0b2138,#03060c);
                  color: white;
                  box-shadow: 0 0 18px rgba(117,255,90,.14);
                }
          - type: entities
            entities:
              - entity: input_datetime.luba_plan_1_zeit
                name: Startzeit
              - entity: input_number.luba_plan_1_schnitthoehe
                name: Schnitthöhe
              - entity: input_number.luba_plan_1_geschwindigkeit
                name: Geschwindigkeit
              - entity: input_number.luba_plan_1_spurabstand
                name: Spurabstand
              - entity: input_number.luba_plan_1_randrunden
                name: Randrunden
            card_mod:
              style: |
                ha-card {
                  border-radius: 24px;
                  background: linear-gradient(145deg,#0b2138,#03060c);
                  color: white;
                  box-shadow: 0 0 18px rgba(117,255,90,.12);
                }
          - type: custom:mushroom-chips-card
            chips:
              - type: entity
                entity: input_boolean.luba_plan_1_bereich_4
                content_info: name
                name: B1
                icon: mdi:numeric-1-circle
                icon_color: green
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_bereich_5
                content_info: name
                name: B2
                icon: mdi:numeric-2-circle
                icon_color: green
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_bereich_6
                content_info: name
                name: B3
                icon: mdi:numeric-3-circle
                icon_color: green
                tap_action:
                  action: toggle
            card_mod:
              style: |
                ha-card {
                  border-radius: 24px;
                  background: rgba(5,12,20,.86);
                  padding: 10px;
                  box-shadow: 0 0 18px rgba(117,255,90,.12);
                }
          - type: custom:mushroom-chips-card
            chips:
              - type: entity
                entity: input_boolean.luba_plan_1_mo
                name: Mo
                content_info: name
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_di
                name: Di
                content_info: name
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_mi
                name: Mi
                content_info: name
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_do
                name: Do
                content_info: name
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_fr
                name: Fr
                content_info: name
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_sa
                name: Sa
                content_info: name
                tap_action:
                  action: toggle
              - type: entity
                entity: input_boolean.luba_plan_1_so
                name: So
                content_info: name
                tap_action:
                  action: toggle
            card_mod:
              style: |
                ha-card {
                  border-radius: 24px;
                  background: rgba(5,12,20,.86);
                  padding: 10px;
                  box-shadow: 0 0 18px rgba(117,255,90,.12);
                }
  - type: grid
    cards:
      - type: vertical-stack
        cards:
          - type: custom:button-card
            entity: sensor.garten_miss_green_akku
            name: Akku & Laden
            show_icon: false
            show_state: true
            state_display: |
              [[[
                return `${states['sensor.garten_miss_green_akku']?.state || 0}%`;
              ]]]
            styles:
              card:
                - height: 320px
                - border-radius: 28px
                - overflow: hidden
                - background-image: url('/local/luba/luba_clean.png')
                - background-size: cover
                - background-position: center
                - background-repeat: no-repeat
                - color: white
                - padding: 0px
                - box-shadow: |
                    0 0 30px rgba(117,255,90,.12),
                    0 20px 50px rgba(0,0,0,.45)
              name:
                - position: absolute
                - top: 20px
                - left: 24px
                - justify-self: start
                - font-size: 28px
                - font-weight: 900
                - color: white
                - text-shadow: 0 0 12px rgba(117,255,90,.25)
              state:
                - position: absolute
                - top: 58px
                - left: 24px
                - justify-self: start
                - font-size: 36px
                - font-weight: 900
                - color: "#75ff5a"
                - text-shadow: 0 0 14px rgba(117,255,90,.45)
              custom_fields:
                battery:
                  - position: absolute
                  - right: 0
                  - top: 0
                status:
                  - position: absolute
                  - left: 38%
                  - right: 30px
                  - bottom: 30px
            custom_fields:
              battery: |
                [[[
                  const battery = Number(states['sensor.garten_miss_green_akku']?.state) || 0;

                  const color =
                    battery > 60 ? '#75ff5a' :
                    battery > 25 ? '#ffd45b' :
                    '#ff4d4d';

                  const radius = 60;
                  const circumference = 2 * Math.PI * radius;
                  const offset = circumference - (battery / 100) * circumference;

                  return `
                  <div style="
                    position:absolute;
                    right:25px;
                    top:35px;
                  ">
                    <svg width="170" height="170" viewBox="0 0 170 170">

                      <circle
                        cx="85"
                        cy="85"
                        r="${radius}"
                        stroke="rgba(255,255,255,.12)"
                        stroke-width="14"
                        fill="none"
                      />

                      <circle
                        cx="85"
                        cy="85"
                        r="${radius}"
                        stroke="${color}"
                        stroke-width="14"
                        fill="none"
                        stroke-linecap="round"
                        stroke-dasharray="${circumference}"
                        stroke-dashoffset="${offset}"
                        transform="rotate(-90 85 85)"
                        style="
                          filter:drop-shadow(0 0 12px ${color});
                        "
                      />

                      <text
                        x="85"
                        y="82"
                        text-anchor="middle"
                        fill="white"
                        font-size="34"
                        font-weight="900">
                        ${battery}%
                      </text>

                      <text
                        x="85"
                        y="110"
                        text-anchor="middle"
                        fill="${color}"
                        font-size="16"
                        font-weight="700">
                        AKKU
                      </text>

                    </svg>
                  </div>
                  `;
                ]]]
              status: |
                [[[
                  const progress =
                    Number(states['sensor.garten_miss_green_aufgabenfortschritt']?.state) || 0;

                  const status =
                    states['sensor.garten_miss_green_status']?.state || 'Bereit';

                  const mowerState =
                    states['lawn_mower.garten_miss_green']?.state || '';

                  return `
                  <div>

                    <div style="
                      color:rgba(255,255,255,.7);
                      font-size:12px;
                      margin-bottom:4px;
                    ">
                      STATUS
                    </div>

                    <div style="
                      color:white;
                      font-size:18px;
                      font-weight:700;
                      margin-bottom:12px;
                      text-shadow:0 0 8px rgba(0,0,0,.5);
                    ">
                      ${status}
                    </div>

                    <div style="
                      color:rgba(255,255,255,.7);
                      font-size:12px;
                      margin-bottom:4px;
                    ">
                      POSITION
                    </div>

                    <div style="
                      color:#75ff5a;
                      font-size:16px;
                      font-weight:900;
                      margin-bottom:18px;
                    ">
                      ${mowerState}
                    </div>

                    <div style="
                      display:flex;
                      justify-content:space-between;
                      margin-bottom:8px;
                    ">
                      <span style="
                        color:rgba(255,255,255,.7);
                        font-size:13px;
                      ">
                        Aufgabenfortschritt
                      </span>

                      <span style="
                        color:#75ff5a;
                        font-weight:900;
                      ">
                        ${progress}%
                      </span>
                    </div>

                    <div style="
                      height:16px;
                      background:rgba(255,255,255,.12);
                      border-radius:999px;
                      overflow:hidden;
                    ">
                      <div style="
                        width:${progress}%;
                        height:100%;
                        background:linear-gradient(
                          90deg,
                          #2f8f20,
                          #75ff5a
                        );
                        box-shadow:
                          0 0 15px rgba(117,255,90,.7);
                      ">
                      </div>
                    </div>

                  </div>
                  `;
                ]]]
          - type: custom:button-card
            name: WLAN & GPS
            show_icon: false
            show_state: false
            styles:
              card:
                - height: 220px
                - border-radius: 28px
                - background: >
                    radial-gradient(circle at 80% 20%, rgba(117,255,90,.25),
                    transparent 38%), linear-gradient(145deg,#06121f,#020409)
                - color: white
                - padding: 20px
                - box-shadow: |
                    0 0 30px rgba(117,255,90,.12),
                    0 20px 50px rgba(0,0,0,.45)
              name:
                - justify-self: start
                - align-self: start
                - font-size: 26px
                - font-weight: 900
                - color: white
              custom_fields:
                values:
                  - position: absolute
                  - left: 20px
                  - right: 20px
                  - top: 60px
                  - bottom: 20px
            custom_fields:
              values: |
                [[[
                  const rssi =
                    Number(states['sensor.garten_miss_green_wlan_signalqualitat_rssi']?.state) || -100;

                  const sats =
                    Number(states['sensor.garten_miss_green_satelliten_mahroboter']?.state) || 0;

                  const wifi =
                    Math.max(
                      0,
                      Math.min(
                        100,
                        Math.round(((rssi + 100) / 50) * 100)
                      )
                    );

                  const gps =
                    Math.min(
                      100,
                      Math.round((sats / 35) * 100)
                    );

                  return `

                  <div style="width:100%;">

                    <div style="
                      display:flex;
                      justify-content:space-between;
                      align-items:center;
                      margin-bottom:8px;
                      font-size:16px;
                      font-weight:800;
                    ">
                      <span>📶 WLAN</span>
                      <span style="
                        color:#75ff5a;
                        font-weight:900;
                      ">
                        ${rssi} dBm
                      </span>
                    </div>

                    <div style="
                      width:100%;
                      height:22px;
                      background:rgba(255,255,255,.10);
                      border-radius:999px;
                      overflow:hidden;
                      margin-bottom:28px;
                    ">
                      <div style="
                        width:${wifi}%;
                        height:100%;
                        background:
                          linear-gradient(
                            90deg,
                            #2f8f20,
                            #75ff5a
                          );
                        box-shadow:
                          0 0 18px rgba(117,255,90,.85);
                        border-radius:999px;
                      ">
                      </div>
                    </div>

                    <div style="
                      display:flex;
                      justify-content:space-between;
                      align-items:center;
                      margin-bottom:8px;
                      font-size:16px;
                      font-weight:800;
                    ">
                      <span>🛰️ GPS Satelliten</span>
                      <span style="
                        color:#75ff5a;
                        font-weight:900;
                      ">
                        ${sats}
                      </span>
                    </div>

                    <div style="
                      width:100%;
                      height:22px;
                      background:rgba(255,255,255,.10);
                      border-radius:999px;
                      overflow:hidden;
                    ">
                      <div style="
                        width:${gps}%;
                        height:100%;
                        background:
                          linear-gradient(
                            90deg,
                            #2f8f20,
                            #75ff5a
                          );
                        box-shadow:
                          0 0 18px rgba(117,255,90,.85);
                        border-radius:999px;
                      ">
                      </div>
                    </div>

                  </div>
                  `;
                ]]]
          - type: entities
            title: Luba Mini 2 Einstellungen
            entities:
              - entity: input_select.luba_zone
              - entity: input_number.luba_schnitthoehe
              - entity: input_number.luba_geschwindigkeit
              - entity: input_number.luba_spurabstand
              - entity: input_select.luba_muster
              - entity: input_number.luba_randrunden
              - entity: input_boolean.luba_regenstopp
            card_mod:
              style: |
                ha-card {
                  border-radius: 24px;
                  background: linear-gradient(145deg,#0b2138,#03060c);
                  color: white;
                }
          - type: grid
            columns: 3
            square: false
            cards:
              - type: custom:button-card
                name: Start Mähen
                icon: mdi:play-circle
                tap_action:
                  action: call-service
                  service: script.luba_start_mit_einstellungen
                styles:
                  card:
                    - height: 110px
                    - border-radius: 22px
                    - background: linear-gradient(145deg,#0b5223,#06130b)
                    - color: white
                  icon:
                    - color: "#85ff5b"
                    - width: 46px
                  name:
                    - font-size: 18px
                    - font-weight: 800
              - type: custom:button-card
                name: Pause
                icon: mdi:pause-circle
                tap_action:
                  action: call-service
                  service: script.luba_pause
                styles:
                  card:
                    - height: 110px
                    - border-radius: 22px
                    - background: linear-gradient(145deg,#4a3410,#130b04)
                    - color: white
                  icon:
                    - color: "#ffd45b"
                    - width: 46px
                  name:
                    - font-size: 18px
                    - font-weight: 800
              - type: custom:button-card
                name: Zur Ladestation
                icon: mdi:home-import-outline
                tap_action:
                  action: call-service
                  service: script.luba_zur_ladestation
                styles:
                  card:
                    - height: 110px
                    - border-radius: 22px
                    - background: linear-gradient(145deg,#0b2138,#03060c)
                    - color: white
                  icon:
                    - color: "#75ff5a"
                    - width: 46px
                  name:
                    - font-size: 18px
                    - font-weight: 800
        card_mod:
          style: |-
            card_mod:
              style: |
                ha-card {
                  background-image: url('/local/luba/luba_battery_panel.png');
                  background-size: cover;
                  background-position: center;
                  background-repeat: no-repeat;

                  border-radius: 28px;
                  overflow: hidden;

                  box-shadow:
                    0 0 30px rgba(117,255,90,.20),
                    0 15px 40px rgba(0,0,0,.50);

                  backdrop-filter: blur(8px);
                }
max_columns: 2
background:
  opacity: 33
  alignment: center
  size: cover
  repeat: repeat
  attachment: scroll
  image:
    media_content_id: media-source://image_upload/80817a3cd264099b221bd8a5084ace86
    media_content_type: image/png
    metadata:
      title: ChatGPT Image 5. Juli 2026, 17_27_12.png
      thumbnail: /api/image/serve/80817a3cd264099b221bd8a5084ace86/256x256
      media_class: image
      navigateIds:
        - {}
        - media_content_type: app
          media_content_id: media-source://image_upload

```

## Anpassungen

### Entitätsnamen ändern

Falls dein Mähroboter anders heißt, musst du alle Entitäten mit `garten_miss_green` anpassen, zum Beispiel:

```yaml
sensor.garten_miss_green_akku
```

zu:

```yaml
sensor.dein_luba_akku
```

### Bereiche anpassen

Die drei Hauptbereiche sind aktuell:

```yaml
switch.garten_miss_green_bereich_area_1
switch.garten_miss_green_bereich_area_2
switch.garten_miss_green_bereich_area_3
```

Wenn deine Integration andere Bereichs-IDs erzeugt, ersetze diese im Dashboard-Code.

### Bildpositionen anpassen

Die Flächen im Gartenbild werden über `top`, `left`, `width`, `height` und `transform` positioniert. Beispiel:

```yaml
style:
  top: 54%
  left: 50%
  width: 39%
  height: 46%
  transform: rotate(-2deg)
```

Diese Werte musst du an dein eigenes Gartenbild anpassen.

## Fehlerbehebung

### Karte wird nicht angezeigt

Prüfe, ob Button Card, Mushroom Cards und card-mod installiert und als Lovelace-Ressourcen geladen sind.

### Bilder werden nicht angezeigt

Prüfe, ob die Dateien wirklich unter `/config/www/luba/` liegen. Der Browserpfad lautet danach `/local/luba/dateiname.png`.

### Entität nicht gefunden

Passe die Entitätsnamen im Dashboard-Code an deine Home-Assistant-Entitäten an.

### Buttons starten den Mäher nicht

Prüfe, ob die Scripts existieren und ob die darin verwendeten Services von deiner Mammotion/Luba-Integration unterstützt werden.

## Lizenz

Dieses Dashboard kann frei privat genutzt und angepasst werden.
