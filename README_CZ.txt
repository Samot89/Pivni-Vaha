# Pivní váha – ESPHome (ESP32 + 4× HX711 + OLED)

Chytrá váha pro měření množství piva v sudu.  
Používá **ESP32**, čtyři vážicí převodníky **HX711**, čtyři tenzometry (load cells), a **OLED SH1106 (I²C)**.  
Umí **TARE**, **dvoukrokovou kalibraci**, **trvalé uložení kalibrace do flash** a zobrazuje **počet piv** (pevně: 1 pivo = 0,505 kg).

- Firmware: **ESPHome**
- Platforma: **ESP32 DevKit (38pin)**
- Zobrazení: **OLED SH1106 128×64, I²C**
- Připojení (volitelné): Wi-Fi / Home Assistant / web server

---

## Funkce

- 4× nezávislá noha (load cell) s filtrováním a součtem
- **Kalibrace A/B**:
  - A = uložit nulu (bez zátěže)
  - B = vypočítat `scale` pro 2,0 kg závaží (automaticky určí polaritu)
- **TARE** (vynulovat všechny nohy)
- **Trvalé uložení** nuly/scale/offsetu sudu do flash (přežije restart)
- **Offset prázdného sudu** (nastavitelný v HA sliderem)
- **Počet piv** z hmotnosti obsahu (pevně 0,505 kg / půllitr)
- **OLED**: zobrazení „Váha sudu“ + „Počet piv“, posun textu doprava
- Běží i **bez Wi-Fi/HA** (čistě offline)

---

## Zapojení

### Pinout (ESP32 DevKit v1 / 38-pin)

| Modul | Pin | ESP32 |
|------|-----|-------|
| **OLED SH1106** | SDA | **GPIO25** |
| | SCL | **GPIO27** |
| | VCC | 3V3 (doporučeno) |
| | GND | GND |
| **HX711 – Noha 1** | DOUT | **GPIO34** |
|  | SCK/CLK | **GPIO26** *(sdílené pro všechny HX711)* |
| **HX711 – Noha 2** | DOUT | **GPIO35** |
|  | SCK/CLK | **GPIO26** |
| **HX711 – Noha 3** | DOUT | **GPIO13** |
|  | SCK/CLK | **GPIO26** |
| **HX711 – Noha 4** | DOUT | **GPIO14** |
|  | SCK/CLK | **GPIO26** |

> **Pozn.:** Všechny HX711 mají **společný CLK** (GPIO26).  
> Napájení HX711 **3.3 V** (doporučeno kvůli kompatibilitě s logikou ESP32) + společná GND.

---

## Komponenty (BOM)

- 1× **ESP32 DevKit v1** (38 pin)
- 4× **HX711** převodník (modul)
- 4× **tenzometr / load cell** (např. 20 kg)
- 1× **OLED 128×64 SH1106** I²C (0.96" / 1.3")
- Kabely, rozbočení GND/3V3, montážní materiál

Volitelné:
- Stabilní napájení 5 V → 3.3 V pro ESP32 (kvalitní zdroj)
- Stíněné vodiče k tenzometrům (lepší stabilita)

---

## Instalace

1. Naklonuj repozitář / stáhni soubory.
2. V ESPHome (v HA nebo standalone) **vytvoř nové zařízení** a nahraj obsah souboru `pivni-vaha.yaml`.
3. Uprav `wifi:` (SSID/heslo) nebo Wi-Fi blok úplně smaž, pokud chceš **offline**.
4. Zkompiluj a flashni přes USB.  Při dalších úpravách lze použít OTA (pokud máš `api:` + Wi-Fi).

---

## Kalibrace (doporučený postup)

1. **Bez zátěže** (nic na váze) → stiskni tlačítko **„Kalibrace A – ulozit nulu“** (uloží RAW nuly `zero1..4` do flash).
2. Polož **přesně 2,0 kg** doprostřed váhy → stiskni **„Kalibrace B – spocitat scale (2.0 kg)”** (vypočítá `scale1..4` z rozdílu RAW; zvládne i obrácené znaménko).
3. Entita **„Hmotnost bez offsetu sudu“** ukáže cca **2.00 kg** (± 0.05).
4. Nastav v HA posuvníkem **„Offset sudu (kg)”** hmotnost **prázdného sudu** (např. 10.0 / 12.5 / 14.0 kg).
5. OLED ukazuje **„Váha sudu“** (obsah) a **„Počet piv“**.

> **Přepočet piv:** pevně 1 pivo = **0,505 kg** (střed hustoty piva).  
> Chceš-li 0.5 kg, uprav v YAML konstantu `MASS_PER_BEER`.

---

## Offline režim

- Firmware běží **i bez HA/Wi-Fi**: měří, sčítá, zobrazuje na OLED.
- Pro offline stabilitu je v YAML `wifi.reboot_timeout: 0s` (neprovádí restart kvůli Wi-Fi).
- Můžeš zcela odstranit bloky `wifi:`, `api:`, `ota:` a používat jen OLED.

---

## Troubleshooting

- **`HX711 is not ready for new measurements yet!`** → ponech `update_interval: 1s` a sdílený CLK (GPIO26).
- **Nesmyslné váhy po startu:** Proveď **Kalibrace A** (bez zátěže) → pak **Kalibrace B** (2 kg).
- **Nestabilní hodnota:** Zkontroluj napájení 3.3 V, stínění tenzometrů, pevné uchycení, případně přidej EMA filtr.
- **OLED posun:** Změň `X` v OLED lambdě (např. `const int X = 20;`) nebo `offset_x: 1`.

---

## Soubory v repozitáři

- `pivni-vaha.yaml` – kompletní konfigurace ESPHome (nahraď svou funkční verzí)
- `README.md` – tento popis (CZ)
- `README_EN.md` – English documentation
- `images/schema.svg` – schéma zapojení
- `.gitignore` – ESPHome build ignor
- `LICENSE` – MIT

---

## Licence

MIT License

---

## Poděkování

Díky komunitě ESPHome a všem autorům knihoven pro HX711 / SSD1306/SH1106.  
Tento projekt vznikl jako praktická pomůcka pro sledování spotřeby piva se zaměřením na spolehlivost a jednoduchou kalibraci.
