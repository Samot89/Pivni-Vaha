<div align="center">

# 🍺 Beer Keg Scale – Smart Monitoring System

### Intelligent scale for measuring beer keg contents

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![ESPHome](https://img.shields.io/badge/ESPHome-Compatible-blue.svg)](https://esphome.io/)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Integration-41BDF5.svg)](https://www.home-assistant.io/)
[![Made with ESP32](https://img.shields.io/badge/Made%20with-ESP32-orange.svg)](https://www.espressif.com/en/products/socs/esp32)

[🇨🇿 Czech Documentation](README_CZ.md) | [🇬🇧 English Documentation](README_EN.md)

</div>

---

## 📋 Overview

Smart scale for **automatic beer keg monitoring** with **Home Assistant** integration. The system uses four load cells for precise measurements, an OLED display for local readout, and advanced calibration features with persistent memory storage.

### ✨ Key Features

- **🎯 Precise measurement** using 4× load cells with HX711 amplifiers
- **📺 OLED display** (SH1106 128×64) showing keg weight and beer count
- **🔧 Easy calibration** - two-step process with persistent storage
- **🏠 Home Assistant** - full integration via ESPHome API
- **📱 Web interface** - browser-based access
- **🔌 Offline mode** - works without Wi-Fi/HA
- **💾 Persistent memory** - calibration survives restarts
- **🍺 Automatic calculation** - displays remaining beer count

---

## 🚀 Quick Start

1. **Prepare hardware** according to [Wiring](#-wiring) table
2. **Download** `pivni-vaha.yaml`
3. **Configure** Wi-Fi credentials in the file
4. **Upload** to ESP32 via ESPHome
5. **Perform calibration** (procedure below)
6. **Set empty keg offset**
7. **Done!** 🎉

---

## 📦 Hardware (BOM)

| Component | Specification | Quantity | Notes |
|-----------|---------------|----------|-------|
| ESP32 DevKit v1 | 38-pin | 1× | [AliExpress link](https://www.aliexpress.com/item/1005005626482837.html) |
| HX711 | Load cell amplifier | 4× | ADC converter |
| Load Cell | 20 kg capacity | 4× | 20kg recommended |
| OLED Display | SH1106 128×64 I²C | 1× | 0.96" or 1.3" |
| Cables | Dupont, jumper wires | - | Shielded recommended |
| Power Supply | 5V → 3.3V regulated | 1× | Quality stabilized |

### Optional Components:
- 🔌 Shielded cables for load cells (better signal stability)
- 🏗️ 3D printed parts for mounting ([3D models](#-3d-printing))
- 🔋 Backup power supply (UPS)

---

## 🎨 System Features

### Measurement and Calibration
- ✅ **4× independent legs** (load cells) with median filtering and EMA
- ✅ **Two-step calibration A/B**
  - **Step A** → Store zero (no load)
  - **Step B** → Calculate scale factor for 2.0 kg weight
- ✅ **TARE function** (zero all legs)
- ✅ **Automatic polarity detection** for load cells

### Storage and Display
- 💾 **Flash memory** - calibration and offset survive ESP32 restarts
- 📊 **OLED display** - keg weight + beer count in real-time
- 🎚️ **Adjustable offset** for empty kegs (10L / 15L / 30L / 50L)
- 🍺 **Automatic beer calculation** (1 beer = 0.505 kg)

### Integration and Connectivity
- 🏠 **Home Assistant** - sensors, controls, automations
- 🌐 **Web server** - browser access (port 80)
- 📡 **OTA updates** - wireless firmware uploads
- 🔌 **Offline mode** - works without Wi-Fi connection

---

## 🔌 Wiring

### Pinout Table (ESP32 DevKit v1 - 38-pin)

| Module             | Signal  | ESP32 Pin                                |
| ------------------ | ------- | ---------------------------------------- |
| **OLED SH1106**    | SDA     | **GPIO25**                               |
|                    | SCL     | **GPIO27**                               |
|                    | VCC     | 3.3V (recommended)                       |
|                    | GND     | GND                                      |
| **HX711 – Leg 1**  | DOUT    | **GPIO34**                               |
|                    | SCK/CLK | **GPIO26** *(shared for all HX711)*      |
| **HX711 – Leg 2**  | DOUT    | **GPIO35**                               |
|                    | SCK/CLK | **GPIO26**                               |
| **HX711 – Leg 3**  | DOUT    | **GPIO13**                               |
|                    | SCK/CLK | **GPIO26**                               |
| **HX711 – Leg 4**  | DOUT    | **GPIO14**                               |
|                    | SCK/CLK | **GPIO26**                               |

> **⚠️ Note:** All HX711 modules share **one CLK pin (GPIO26)**.  
> HX711 power supply recommended at **3.3V** for logic compatibility with ESP32.

---

## 🛠️ Installation

### Step 1: ESPHome Setup

1. Clone or download this repository
2. In **ESPHome** (in Home Assistant or standalone) create a new device
3. Upload the contents of `pivni-vaha.yaml`

### Step 2: Wi-Fi Configuration

Edit the Wi-Fi section in the YAML file:

```yaml
wifi:
  ssid: "YourSSID"
  password: "YourPassword"
```

For **offline mode**, you can completely remove the Wi-Fi section.

### Step 3: Firmware Upload

1. Connect ESP32 via USB
2. Compile and flash firmware
3. After initial upload, you can use **OTA** (Over-The-Air) updates

---

## ⚙️ Calibration (Recommended Procedure)

### Step 1: Calibration A – Store Zero

1. **Remove all load** from the scale (nothing on scale)
2. In Home Assistant, press button **"Calibration A – store zero"**
3. System stores RAW values `zero1..4` to flash memory

### Step 2: Calibration B – Calculate Scale

1. Place **exactly 2.0 kg weight** in the center of the scale
2. In Home Assistant, press button **"Calibration B – calculate scale (2.0 kg)"**
3. System automatically calculates `scale1..4` coefficients (handles reversed polarity)

### Step 3: Verify Calibration

- Entity **"Weight without keg offset"** should show approximately **2.00 kg** (± 0.05 kg)
- If not, repeat calibration

### Step 4: Set Keg Offset

In Home Assistant, use slider **"Keg offset (kg)"** to set empty keg weight:

| Keg Type | Empty Keg Weight |
|----------|------------------|
| 10 L     | 7.0 kg          |
| 15 L     | 8.0 kg          |
| 30 L     | 10.0 kg         |
| 50 L     | 13.0 kg         |

> **💡 Tip:** For precise values, you can weigh the empty keg on a regular scale.

### Step 5: Done!

OLED display now shows:
- **Keg weight** (beer content in kg)
- **Beer count** (automatic calculation)

---

## 🏠 Home Assistant Integration

### Available Entities

**Sensors:**
- `sensor.pivni_vaha_celkova_hmotnost` - Beer content (kg)
- `sensor.pivni_vaha_hmotnost_bez_offsetu_sudu` - Total weight (kg)
- `sensor.pivni_vaha_pocet_piv` - Remaining beers count (pcs)
- `sensor.pivni_vaha_noha_1` - Weight on leg 1 (kg)
- `sensor.pivni_vaha_noha_2` - Weight on leg 2 (kg)
- `sensor.pivni_vaha_noha_3` - Weight on leg 3 (kg)
- `sensor.pivni_vaha_noha_4` - Weight on leg 4 (kg)

**Controls:**
- `number.pivni_vaha_offset_sudu_kg` - Keg offset setting
- `button.pivni_vaha_kalibrace_a` - Calibration A (zero)
- `button.pivni_vaha_kalibrace_b` - Calibration B (scale)

### Dashboard Card

Use the included `HA-Dashboard.yaml` file to create a pre-configured card in Home Assistant with:
- Display of weight and beer count
- Slider for keg offset
- Quick buttons for common keg types (10L, 15L, 30L, 50L)

---

## 🖨️ 3D Printing

### Available Models

| Model | Description | Link |
|-------|-------------|------|
| ESP32 case | Simple case for ESP32 DevKit | [Printables](https://www.printables.com/model/1014797-simple-case-for-the-cheap-esp32-breakout-board-of/files) |
| HX711 bracket | Bracket for HX711 module | [Printables](https://www.printables.com/model/879042-hx711-ad-module-board-bracket) |

### Printing Tips
- Material: **PLA** or **PETG**
- Infill: **20-30%**
- Supports: **yes** (for complex geometry)

---

## 🔌 Offline Mode

Firmware works **fully offline** even without Wi-Fi or Home Assistant connection:

- Measurement and calculations run locally on ESP32
- OLED display shows data in real-time
- Calibration is stored in ESP32 memory
- To activate offline mode:
  - YAML has `wifi.reboot_timeout: 0s` set (no restart due to Wi-Fi)
  - You can completely remove `wifi:`, `api:`, `ota:`, `web_server:` blocks

---

## ❓ FAQ / Troubleshooting

### HX711 is not ready for new measurements yet!
- **Solution:** Keep `update_interval: 1s` and shared CLK pin (GPIO26)
- Ensure all HX711 modules are properly powered

### Nonsense values after startup
- **Solution:** Perform Calibration A (no load) followed by Calibration B (with 2 kg)
- Check proper wiring of all load cells

### Unstable measurements
- **Check:** Quality 3.3V power supply (not directly from USB)
- **Check:** Shielded cables to load cells
- **Check:** Secure mechanical mounting of all components
- **Solution:** Increase EMA filter alpha in YAML (e.g., from 0.2 to 0.1)

### OLED display is off-center
- **Solution:** Change `X` value in OLED lambda section (e.g., `const int X = 20;`)
- Or adjust `offset_x:` value

### Incorrect beer count
- **Check:** Constant `MASS_PER_BEER = 0.505` kg (half-liter beer)
- For more accurate results, you can change this constant in YAML

---

## 📁 Repository Files

```
Pivni-Vaha/
├── README.md              # This file (main documentation)
├── README_CZ.md           # Czech documentation (detailed)
├── README_EN.md           # English documentation
├── pivni-vaha.yaml        # Complete ESPHome configuration
├── HA-Dashboard.yaml      # Dashboard card for Home Assistant
├── LICENSE                # MIT License
└── .gitignore             # Git ignore files
```

---

## 🤝 Contributing

Contributions are welcome! If you have ideas for improvements:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 Changelog

### v1.0 (2025-01)
- ✅ First public release
- ✅ Support for 4× load cells with HX711
- ✅ OLED display SH1106
- ✅ Two-step calibration
- ✅ Home Assistant integration
- ✅ Offline mode

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Thanks to the **ESPHome** community for an excellent platform
- Thanks to all authors of libraries for **HX711** and **SH1106**
- This project was created as a practical tool for monitoring beer consumption with a focus on reliability and simple calibration

---

<div align="center">

**Made with 🍺 and ❤️ for beer enthusiasts**

If you like this project, give it a ⭐ on GitHub!

</div>
