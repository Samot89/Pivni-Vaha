# Beer Keg Scale – ESPHome (ESP32 + 4× HX711 + OLED)

Smart beer keg scale built on **ESPHome**.  
Measures total keg weight and calculates number of beers left.  
Includes full calibration, persistent memory, and OLED display.

- **Hardware:** ESP32 + 4× HX711 + 4× Load Cells + OLED SH1106
- **Firmware:** ESPHome (YAML)
- **Display:** SH1106 128×64 (I²C)
- Works **offline**, with **Home Assistant**, or via **Wi-Fi**

---

## Features

- 4 independent load cells with median filtering
- **Calibration A/B**:
  - A → store zero (TARE)
  - B → calculate `scale` from 2.0 kg reference
- **Persistent memory** (TARE + scale + keg offset) in flash
- **OLED display** with keg weight + beer count
- **Beer counter:** fixed ratio → 1 beer = 0.505 kg
- **Works offline** (no HA or Wi-Fi needed)
- Adjustable **empty keg offset**
- LED off, silent logging, stable readings

---

## Pinout (ESP32 DevKit v1 – 38-pin)

| Module | Signal | ESP32 Pin |
|:--------|:--------|:----------|
| **OLED SH1106** | SDA | GPIO25 |
| | SCL | GPIO27 |
| | VCC | 3.3 V |
| | GND | GND |
| **HX711 #1** | DOUT | GPIO34 |
| | SCK | GPIO26 |
| **HX711 #2** | DOUT | GPIO35 |
| | SCK | GPIO26 |
| **HX711 #3** | DOUT | GPIO13 |
| | SCK | GPIO26 |
| **HX711 #4** | DOUT | GPIO14 |
| | SCK | GPIO26 |

> All HX711 modules share **one CLK line (GPIO26)**.  
> Power: 3.3 V recommended for logic compatibility.

---

## Components (BOM)

- 1× ESP32 DevKit v1 (38-pin)
- 4× HX711 load cell amplifier modules
- 4× load cells (20 kg recommended)
- 1× OLED SH1106 128×64 (I²C)
- Wires, connectors, power supply (5 V → 3.3 V regulated)

Optional:
- Shielded wires for load cells
- Solid 3.3 V power source

---

## Installation

1. Clone this repo or download `pivni-vaha.yaml`.
2. Open **ESPHome** (in Home Assistant or standalone).
3. Create a new device and paste YAML content.
4. Adjust your Wi-Fi credentials.
5. Compile and flash (USB). OTA updates work afterward.

---

## Calibration

1. **Calibration A:** no load → press *"Calibrate A – zero"*
2. **Calibration B:** place **2.0 kg** weight → press *"Calibrate B – calculate scale"*
3. Total weight ≈ 2.0 kg (± 0.05)
4. Set empty keg offset (typical): 10 L = 8.5 kg, 15 L = 10.0 kg, 30 L = 12.5 kg, 50 L = 14.0 kg
5. OLED shows “Keg weight” and “Beer count”.

---

## Offline use

- Works even without Wi-Fi or Home Assistant.
- `wifi.reboot_timeout: 0s` prevents reboot loops without Wi-Fi.
- You may remove `wifi:`, `api:`, `ota:` blocks entirely.

---

## Files

- `pivni-vaha.yaml` – full ESPHome config (replace with your working YAML)
- `README.md` – Czech documentation
- `README_EN.md` – English documentation
- `images/schema.svg` – wiring diagram
- `.gitignore` – ESPHome ignores
- `LICENSE` – MIT
