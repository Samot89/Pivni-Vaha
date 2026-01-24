<div align="center">

# 🍺 Pivní Váha – Smart Beer Keg Scale

### Inteligentní váha pro měření množství piva v sudu

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![ESPHome](https://img.shields.io/badge/ESPHome-Compatible-blue.svg)](https://esphome.io/)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Integration-41BDF5.svg)](https://www.home-assistant.io/)
[![Made with ESP32](https://img.shields.io/badge/Made%20with-ESP32-orange.svg)](https://www.espressif.com/en/products/socs/esp32)

[🇨🇿 Česká dokumentace](README_CZ.md) | [🇬🇧 English Documentation](README_EN.md)

</div>

---

## 📋 Přehled

Chytrá váha pro **automatické sledování množství piva v sudu** s integrací do **Home Assistant**. Systém používá čtyři tenzometry pro přesné měření, OLED displej pro lokální zobrazení a pokročilé funkce kalibrace s trvalým uložením do paměti.

### ✨ Klíčové vlastnosti

- **🎯 Přesné měření** pomocí 4× tenzometrů (load cells) s HX711 převodníky
- **📺 OLED displej** (SH1106 128×64) pro zobrazení váhy a počtu piv
- **🔧 Snadná kalibrace** - dvoukrokový proces s trvalým uložením
- **🏠 Home Assistant** - plná integrace přes ESPHome API
- **📱 Webové rozhraní** - přístup přes prohlížeč
- **🔌 Offline režim** - funguje i bez Wi-Fi/HA
- **💾 Persistentní paměť** - kalibrace přežije restart
- **🍺 Automatický přepočet** - zobrazení počtu zbývajících piv

---

## 🚀 Rychlý start

1. **Připravte hardware** podle tabulky [Zapojení](#-zapojení)
2. **Stáhněte** `pivni-vaha.yaml`
3. **Nakonfigurujte** Wi-Fi credentials v souboru
4. **Nahrajte** do ESP32 přes ESPHome
5. **Proveďte kalibraci** (postup níže)
6. **Nastavte offset** prázdného sudu
7. **Hotovo!** 🎉

---

## 📦 Hardware (BOM)

| Komponenta | Specifikace | Množství | Poznámka |
|------------|-------------|----------|----------|
| ESP32 DevKit v1 | 38-pin | 1× | [AliExpress link](https://www.aliexpress.com/item/1005005626482837.html) |
| HX711 | Load cell amplifier | 4× | ADC převodník |
| Tenzometr | 20 kg load cell | 4× | Doporučeno 20kg |
| OLED Display | SH1106 128×64 I²C | 1× | 0.96" nebo 1.3" |
| Kabely | Dupont, propojovací | - | Doporučeno stíněné |
| Zdroj | 5V → 3.3V regulovaný | 1× | Kvalitní stabilizovaný |

### Volitelné komponenty:
- 🔌 Stíněné kabely pro tenzometry (lepší stabilita signálu)
- 🏗️ 3D tištěné díly pro montáž ([3D modely](#-3d-tisk))
- 🔋 Záložní napájení (UPS)

---

## 🎨 Funkce systému

### Měření a kalibrace
- ✅ **4× nezávislá noha** (load cell) s mediánovým filtrováním a EMA
- ✅ **Dvoukroková kalibrace A/B**
  - **Krok A** → Uložení nuly (bez zátěže)
  - **Krok B** → Výpočet scale faktoru pro 2,0 kg závaží
- ✅ **TARE funkce** (vynulování všech nohou)
- ✅ **Automatická detekce polarity** tenzometrů

### Ukládání a zobrazení
- 💾 **Flash paměť** - kalibrace a offset přežijí restart ESP32
- 📊 **OLED zobrazení** - váha sudu + počet piv v reálném čase
- 🎚️ **Nastavitelný offset** prázdného sudu (10L / 15L / 30L / 50L)
- 🍺 **Automatický přepočet piv** (1 pivo = 0,505 kg)

### Integrace a konektivita
- 🏠 **Home Assistant** - senzory, ovládací prvky, automatizace
- 🌐 **Web server** - přístup přes prohlížeč (port 80)
- 📡 **OTA aktualizace** - bezdrátové nahrávání firmware
- 🔌 **Offline režim** - funguje i bez Wi-Fi připojení

---

## 🔌 Zapojení

### Pinout tabulka (ESP32 DevKit v1 - 38-pin)

| Modul              | Pin     | ESP32                                    |
| ------------------ | ------- | ---------------------------------------- |
| **OLED SH1106**    | SDA     | **GPIO25**                               |
|                    | SCL     | **GPIO27**                               |
|                    | VCC     | 3V3 (doporučeno)                         |
|                    | GND     | GND                                      |
| **HX711 – Noha 1** | DOUT    | **GPIO34**                               |
|                    | SCK/CLK | **GPIO26** *(sdílené pro všechny HX711)* |
| **HX711 – Noha 2** | DOUT    | **GPIO35**                               |
|                    | SCK/CLK | **GPIO26**                               |
| **HX711 – Noha 3** | DOUT    | **GPIO13**                               |
|                    | SCK/CLK | **GPIO26**                               |
| **HX711 – Noha 4** | DOUT    | **GPIO14**                               |
|                    | SCK/CLK | **GPIO26**                               |

> **⚠️ Poznámka:** Všechny HX711 moduly sdílejí **společný CLK pin (GPIO26)**.  
> Napájení HX711 doporučeno **3.3V** kvůli logické kompatibilitě s ESP32.

---

## 🛠️ Instalace

### Krok 1: Příprava ESPHome

1. Naklonujte nebo stáhněte tento repozitář
2. V **ESPHome** (v Home Assistant nebo standalone) vytvořte nové zařízení
3. Nahrajte obsah souboru `pivni-vaha.yaml`

### Krok 2: Konfigurace Wi-Fi

Upravte Wi-Fi sekci v YAML souboru:

```yaml
wifi:
  ssid: "VaseSSID"
  password: "VaseHeslo"
```

Pro **offline režim** můžete Wi-Fi sekci úplně odstranit.

### Krok 3: Nahrání firmware

1. Připojte ESP32 přes USB
2. Zkompilujte a flashněte firmware
3. Po prvním nahrání lze používat **OTA** (Over-The-Air) aktualizace

---

## ⚙️ Kalibrace (doporučený postup)

### Krok 1: Kalibrace A – Uložení nuly

1. **Odstraňte veškerou zátěž** z váhy (nic na váze)
2. V Home Assistant stiskněte tlačítko **„Kalibrace A – uložit nulu"**
3. Systém uloží RAW hodnoty `zero1..4` do flash paměti

### Krok 2: Kalibrace B – Výpočet scale

1. Umístěte **přesně 2,0 kg závaží** doprostřed váhy
2. V Home Assistant stiskněte tlačítko **„Kalibrace B – spočítat scale (2.0 kg)"**
3. Systém automaticky vypočítá `scale1..4` koeficienty (zvládne i obrácenou polaritu)

### Krok 3: Ověření kalibrace

- Entita **„Hmotnost bez offsetu sudu"** by měla zobrazovat cca **2.00 kg** (± 0.05 kg)
- Pokud ne, opakujte kalibraci

### Krok 4: Nastavení offsetu sudu

V Home Assistant nastavte posuvníkem **„Offset sudu (kg)"** hmotnost prázdného sudu:

| Typ sudu | Hmotnost prázdného sudu |
|----------|------------------------|
| 10 L     | 7.0 kg                 |
| 15 L     | 8.0 kg                 |
| 30 L     | 10.0 kg                |
| 50 L     | 13.0 kg                |

> **💡 Tip:** Pro přesné hodnoty můžete prázdný sud zvážit na běžné váze.

### Krok 5: Hotovo!

OLED displej nyní zobrazuje:
- **Váha sudu** (obsah piva v kg)
- **Počet piv** (automatický přepočet)

---

## 🏠 Home Assistant integrace

### Dostupné entity

**Senzory:**
- `sensor.pivni_vaha_celkova_hmotnost` - Obsah piva (kg)
- `sensor.pivni_vaha_hmotnost_bez_offsetu_sudu` - Celková váha (kg)
- `sensor.pivni_vaha_pocet_piv` - Počet zbývajících piv (ks)
- `sensor.pivni_vaha_noha_1` - Váha na noze 1 (kg)
- `sensor.pivni_vaha_noha_2` - Váha na noze 2 (kg)
- `sensor.pivni_vaha_noha_3` - Váha na noze 3 (kg)
- `sensor.pivni_vaha_noha_4` - Váha na noze 4 (kg)

**Ovládací prvky:**
- `number.pivni_vaha_offset_sudu_kg` - Nastavení offsetu sudu
- `button.pivni_vaha_kalibrace_a` - Kalibrace A (nula)
- `button.pivni_vaha_kalibrace_b` - Kalibrace B (scale)

### Dashboard karta

Použijte přiložený soubor `HA-Dashboard.yaml` pro vytvoření předpřipravené karty v Home Assistant s:
- Zobrazením váhy a počtu piv
- Posuvníkem pro offset sudu
- Rychlými tlačítky pro běžné typy sudů (10L, 15L, 30L, 50L)

---

## 🖨️ 3D Tisk

### Dostupné modely

| Model | Popis | Link |
|-------|-------|------|
| ESP32 pouzdro | Jednoduché pouzdro pro ESP32 DevKit | [Printables](https://www.printables.com/model/1014797-simple-case-for-the-cheap-esp32-breakout-board-of/files) |
| HX711 držák | Držák pro modul HX711 | [Printables](https://www.printables.com/model/879042-hx711-ad-module-board-bracket) |

### Tipy pro tisk
- Materiál: **PLA** nebo **PETG**
- Výplň: **20-30%**
- Podpory: **ano** (pro komplexní geometrii)

---

## 🔌 Offline režim

Firmware funguje **plně offline** i bez připojení k Wi-Fi nebo Home Assistant:

- Měření a výpočty běží lokálně v ESP32
- OLED displej zobrazuje data v reálném čase
- Kalibrace je uložena v paměti ESP32
- Pro aktivaci offline režimu:
  - V YAML je nastaveno `wifi.reboot_timeout: 0s` (žádný restart kvůli Wi-Fi)
  - Můžete úplně odstranit bloky `wifi:`, `api:`, `ota:`, `web_server:`

---

## ❓ FAQ / Řešení problémů

### HX711 is not ready for new measurements yet!
- **Řešení:** Ponechte `update_interval: 1s` a sdílený CLK pin (GPIO26)
- Ujistěte se, že všechny HX711 jsou správně napájeny

### Nesmyslné hodnoty po startu
- **Řešení:** Proveďte kalibraci A (bez zátěže) a následně kalibraci B (s 2 kg)
- Zkontrolujte správné zapojení všech load cells

### Nestabilní měření
- **Kontrola:** Kvalitní napájení 3.3V (ne přímo z USB)
- **Kontrola:** Stíněné kabely k tenzometrům
- **Kontrola:** Pevné mechanické uchycení všech komponent
- **Řešení:** Zvyšte alfa EMA filtru v YAML (např. z 0.2 na 0.1)

### OLED displej je mimo střed
- **Řešení:** Změňte hodnotu `X` v OLED lambda sekci (např. `const int X = 20;`)
- Nebo upravte `offset_x:` hodnotu

### Nesprávný počet piv
- **Kontrola:** Konstanta `MASS_PER_BEER = 0.505` kg (půllitr piva)
- Pro přesnější výsledky můžete změnit tuto konstantu v YAML

---

## 📁 Soubory v repozitáři

```
Pivni-Vaha/
├── README.md              # Tento soubor (hlavní dokumentace)
├── README_CZ.md           # Česká dokumentace (podrobná)
├── README_EN.md           # Anglická dokumentace
├── pivni-vaha.yaml        # Kompletní ESPHome konfigurace
├── HA-Dashboard.yaml      # Dashboard karta pro Home Assistant
├── LICENSE                # MIT License
└── .gitignore             # Git ignore soubory
```

---

## 🤝 Přispívání

Příspěvky jsou vítány! Pokud máte nápady na vylepšení:

1. Forkněte repozitář
2. Vytvořte feature branch (`git checkout -b feature/AmazingFeature`)
3. Commitněte změny (`git commit -m 'Add some AmazingFeature'`)
4. Pushněte do branch (`git push origin feature/AmazingFeature`)
5. Otevřete Pull Request

---

## 📝 Changelog

### v1.0 (2025-01)
- ✅ První veřejné vydání
- ✅ Podpora 4× load cells s HX711
- ✅ OLED displej SH1106
- ✅ Dvoukroková kalibrace
- ✅ Home Assistant integrace
- ✅ Offline režim

---

## 📄 Licence

Tento projekt je licencován pod **MIT License** - viz soubor [LICENSE](LICENSE).

---

## 🙏 Poděkování

- Díky komunitě **ESPHome** za vynikající platformu
- Díky všem autorům knihoven pro **HX711** a **SH1106**
- Tento projekt vznikl jako praktická pomůcka pro sledování spotřeby piva se zaměřením na spolehlivost a jednoduchou kalibraci

---

<div align="center">

**Vyrobeno s 🍺 a ❤️ pro pivní nadšence**

Pokud se vám projekt líbí, dejte mu ⭐ na GitHubu!

</div>
