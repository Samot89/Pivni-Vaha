<div align="center">

# 🍺 Beer Keg Scale – Smart Monitoring System

### Intelligent scale for monitoring beer keg contents in real time

[![License: Non-Commercial](https://img.shields.io/badge/License-Non--Commercial-red.svg)](LICENSE)
[![ESPHome](https://img.shields.io/badge/ESPHome-Compatible-blue.svg)](https://esphome.io/)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Integration-41BDF5.svg)](https://www.home-assistant.io/)
[![Made with ESP32](https://img.shields.io/badge/Made%20with-ESP32-orange.svg)](https://www.espressif.com/en/products/socs/esp32)

[🇨🇿 Česká dokumentace](README_CZ.md)

</div>

---

## 📸 Photos

<div align="center">

| Scale platform | Keg in fridge | Electronics (bottom) |
|:-:|:-:|:-:|
| ![Scale platform top view](images/scale-platform-top.jpg) | ![Keg on scale](images/scale-in-use.jpg) | ![Electronics underside](images/electronics-bottom.jpg) |

| OLED display – weight + beer count |
|:-:|
| ![OLED display 30.3 kg / 60 beers](images/display-weight-count.jpg) |


</div>

---

## 📋 Overview

Smart scale for **automatic beer keg monitoring** with **Home Assistant** integration. The system uses four load cells for precise measurements, an OLED display for local readout, and advanced calibration with persistent flash storage.

### ✨ Key Features

- **🎯 Precise measurement** – 4× load cells with HX711 amplifiers, median + EMA filtering
- **📺 OLED display** (SSD1306 128×32) – shows beer content, beer count and temperature
- **🔧 Easy two-step calibration** – persistent storage survives restarts
- **🏠 Home Assistant** – full integration via ESPHome API
- **📱 Web interface** – browser access on port 80
- **🔌 Offline mode** – works without Wi-Fi / HA
- **💾 Persistent memory** – calibration and offset survive power cycles
- **🍺 Automatic beer count** – 1 beer = 0.505 kg (half-liter)

---

## 🚀 Quick Start

1. **Prepare hardware** → see [Wiring](#-wiring) table
2. **Download** `pivni-vaha.yaml`
3. **Configure** Wi-Fi credentials in the YAML file
4. **Flash** to ESP32 via ESPHome
5. **Calibrate** → follow the [Calibration](#️-calibration) guide
6. **Set keg offset** → empty keg weight
7. **Done!** 🎉

---

## 📦 Hardware (BOM)

| Component | Specification | Qty | Notes |
|-----------|---------------|-----|-------|
| ESP32 DevKit v1 | 38-pin | 1× | [AliExpress](https://www.aliexpress.com/item/1005005626482837.html) |
| HX711 | 24-bit ADC amplifier | 4× | |
| Load cell | 20 kg bar type | 4× | 20 kg recommended |
| OLED display | SSD1306 128×32 I²C | 1× | 0.96" |
| DS18B20 | Temperature sensor, 1-Wire | 1× | + 4.7 kΩ pull-up resistor |
| Jumper wires | Dupont M-F, M-M | 1 set | Shielded recommended |
| Power supply | 5V → 3.3V regulated | 1× | Quality stabilized |

**Optional:**
- 🔌 Shielded cables for load cells (better signal stability)
- 🏗️ 3D-printed enclosures → [3D Printing](#️-3d-printing)
- 🔋 Backup power / UPS

---

## 🔌 Wiring

### Pinout (ESP32 DevKit v1 – 38-pin)

| Module | Signal | ESP32 Pin |
|--------|--------|-----------|
| **OLED SSD1306** | SDA | **GPIO25** |
| | SCL | **GPIO27** |
| | VCC | 3.3V |
| | GND | GND |
| **DS18B20** | DATA | **GPIO12** |
| | VCC | 3.3V |
| | GND | GND |
| **HX711 – Leg 1** | DOUT | **GPIO34** |
| | SCK/CLK | **GPIO26** *(shared for all HX711)* |
| **HX711 – Leg 2** | DOUT | **GPIO35** |
| | SCK/CLK | **GPIO26** |
| **HX711 – Leg 3** | DOUT | **GPIO13** |
| | SCK/CLK | **GPIO26** |
| **HX711 – Leg 4** | DOUT | **GPIO14** |
| | SCK/CLK | **GPIO26** |

> **⚠️ Note:** All HX711 modules share **one CLK pin (GPIO26)**.
> Power the HX711 modules at **3.3V** for logic-level compatibility with ESP32.

---

## 🛠️ Installation

### Step 1 – ESPHome setup

1. Clone or download this repository
2. In ESPHome (standalone or inside Home Assistant) create a new device
3. Paste the contents of `pivni-vaha.yaml`

### Step 2 – Wi-Fi configuration

```yaml
wifi:
  ssid: "YourSSID"
  password: "YourPassword"
```

For **offline-only** use, remove the `wifi:` block entirely.

### Step 3 – Flash

1. Connect ESP32 via USB
2. Compile and flash
3. Subsequent updates can use **OTA** (Over-The-Air)

---

## ⚙️ Calibration

### Step 1 – Calibration A: Store Zero

1. **Remove all weight** from the scale
2. Press **"Calibration A – Store Zero"** in Home Assistant
3. RAW zero values (`zero1..4`) are saved to flash

### Step 2 – Calibration B: Calculate Scale

1. Place **exactly 2.0 kg** in the center of the scale
2. Press **"Calibration B – Calculate Scale (2.0 kg)"**
3. Scale coefficients (`scale1..4`) are calculated automatically (handles reversed polarity)

### Step 3 – Verify

- **"Raw Weight"** entity should read ~**2.00 kg** (±0.05 kg)
- If not, repeat calibration

### Step 4 – Set Keg Offset

Use the **"Keg Offset (kg)"** slider in Home Assistant:

| Keg size | Empty keg weight |
|----------|-----------------|
| 10 L | 7.0 kg |
| 15 L | 8.0 kg |
| 30 L | 10.0 kg |
| 50 L | 13.0 kg |

> **💡 Tip:** Weigh the empty keg on a kitchen scale for the most accurate offset.

### Step 5 – Done!

The OLED display now shows:
- **Beer Content** – beer weight in kg
- **Beer Count** – number of remaining half-liters

---

## 🏠 Home Assistant Entities

**Sensors:**
| Entity ID | Description |
|-----------|-------------|
| `sensor.pivni_vaha_beer_content` | Beer content (kg) |
| `sensor.pivni_vaha_raw_weight` | Total raw weight (kg) |
| `sensor.pivni_vaha_beer_count` | Remaining beers (pcs) |
| `sensor.pivni_vaha_leg_1` … `leg_4` | Individual leg weights (kg) |

**Controls:**
| Entity ID | Description |
|-----------|-------------|
| `number.pivni_vaha_keg_offset_kg` | Empty keg weight offset |
| `button.pivni_vaha_calibration_a_store_zero` | Calibration A |
| `button.pivni_vaha_calibration_b_calculate_scale_2_0_kg` | Calibration B |

### Dashboard

Import `HA-Dashboard.yaml` into Home Assistant for a ready-made card with:
- Beer content and beer count display
- Keg offset slider
- Quick-set buttons for 10 / 15 / 30 / 50 L kegs

![Home Assistant Dashboard](images/ha-dashboard.jpg)


---

## 🖨️ 3D Printing

| Model | Description | Link |
|-------|-------------|------|
| ESP32 case | Simple enclosure for ESP32 DevKit | [Printables](https://www.printables.com/model/1014797-simple-case-for-the-cheap-esp32-breakout-board-of/files) |
| HX711 bracket | Mount for HX711 module | [Printables](https://www.printables.com/model/879042-hx711-ad-module-board-bracket) |
| OLED SSD1306 case | Case for SSD1306 128×32 display | [Thingiverse](https://www.thingiverse.com/thing:2844143/files) |

**Print settings:** PLA or PETG · 20–30% infill · supports as needed

---

## 🔌 Offline Mode

The firmware runs **fully offline** without Wi-Fi or Home Assistant:

- Measurement and calculations run locally on ESP32
- OLED displays data in real time
- Calibration stored in ESP32 flash
- `wifi.reboot_timeout: 0s` prevents restarts on Wi-Fi loss
- Remove `wifi:`, `api:`, `ota:`, `web_server:` blocks for a minimal offline build

---

## ❓ FAQ / Troubleshooting

**`HX711 is not ready for new measurements yet!`**
→ Keep `update_interval: 1s` and shared CLK on GPIO26. Ensure all HX711 modules are properly powered.

**Nonsense values after startup**
→ Run Calibration A (empty scale) then Calibration B (2 kg weight). Check load cell wiring.

**Unstable readings**
→ Use a quality 3.3V supply (not raw USB). Use shielded cables to load cells. Mechanically secure all components. Lower the EMA alpha in YAML (e.g. 0.1 instead of 0.2).

**OLED display off-center**
→ Adjust `const int X` in the OLED lambda (e.g. `const int X = 20;`) or `offset_x:`.

**Does the display work in a refrigerator?**
→ Yes – the SSD1306 128×32 display has been tested and works correctly in cold environments (refrigerator). Weight, beer count and temperature are all shown reliably.

**Incorrect beer count**
→ Check `MASS_PER_BEER = 0.505` kg. Adjust in YAML for different glass sizes.

---

## 📁 Repository Structure

```
Pivni-Vaha/
├── README.md              # This file – English documentation
├── README_CZ.md           # Czech documentation
├── pivni-vaha.yaml        # ESPHome configuration
├── HA-Dashboard.yaml      # Home Assistant dashboard card
├── HARDWARE.md            # Detailed hardware docs
├── CHANGELOG.md           # Version history
├── CONTRIBUTING.md        # Contributor guide
├── secrets.yaml.example   # Wi-Fi / API secrets template
├── images/                # Project photos
│   ├── scale-platform-top.jpg
│   ├── scale-in-use.jpg
│   └── electronics-bottom.jpg
├── LICENSE
└── .gitignore
```

---

## 🤝 Contributing

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

- 🐛 [Report a bug](https://github.com/Samot89/Beer-Keg-Scale/issues/new?template=bug_report.md)
- ✨ [Request a feature](https://github.com/Samot89/Beer-Keg-Scale/issues/new?template=feature_request.md)
- 🔧 [Hardware documentation](HARDWARE.md)
- 📝 [Changelog](CHANGELOG.md)

---

## 📄 License

**Non-Commercial License** – free for personal and hobbyist use. Commercial use is prohibited. See [LICENSE](LICENSE) for full terms.

---

## 🙏 Acknowledgments

- [ESPHome](https://esphome.io/) community for an excellent IoT platform
- Authors of the HX711 and SSD1306 libraries
- Built as a practical tool for monitoring keg consumption with a focus on reliability and simple calibration

---

<div align="center">

**Made with 🍺 and ❤️ for beer enthusiasts**

If you like this project, give it a ⭐ on GitHub!

</div>
