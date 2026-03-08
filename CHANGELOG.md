# Changelog

All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [Unreleased]

### Added
- Project photos in `images/` directory
- Improved documentation structure (English primary, Czech secondary)
- GitHub issue and PR templates
- `CONTRIBUTING.md`
- `.gitignore` for ESPHome projects
- `secrets.yaml.example` template

### Changed
- All sensor/button/number names translated to English
- Entity IDs updated:
  - `sensor.pivni_vaha_celkova_hmotnost` → `sensor.pivni_vaha_beer_content`
  - `sensor.pivni_vaha_hmotnost_bez_offsetu_sudu` → `sensor.pivni_vaha_raw_weight`
  - `sensor.pivni_vaha_pocet_piv` → `sensor.pivni_vaha_beer_count`
  - `number.pivni_vaha_offset_sudu_kg` → `number.pivni_vaha_keg_offset_kg`
- `README.md` is now the primary English documentation
- `README_CZ.md` updated as secondary Czech documentation
- `README_EN.md` redirects to `README.md`

---

## [1.0.0] - 2025-01-24

### Added
- ✅ First public release
- ✅ Support for 4× load cells with HX711
- ✅ OLED display SH1106 128×64
- ✅ Two-step calibration (A/B)
- ✅ Persistent calibration storage in flash
- ✅ Home Assistant integration
- ✅ Web server
- ✅ OTA firmware updates
- ✅ Offline mode
- ✅ Automatic beer count calculation
- ✅ Adjustable empty keg offset
- ✅ Median + EMA filtering for stable readings
- ✅ Automatic load cell polarity detection

### Hardware
- ESP32 DevKit v1 (38-pin)
- 4× HX711 amplifier modules
- 4× 20 kg load cells
- OLED SH1106 128×64 I²C display

---

## How to Contribute

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.
