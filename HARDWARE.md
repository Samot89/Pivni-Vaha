# 🔧 Hardware Documentation

[🇬🇧 English](#english-version) | [🇨🇿 Česky](#česká-verze)

---

## English Version

### 📦 Complete Component List (BOM)

#### Essential Components

| # | Component | Specification | Qty | Est. Price | Links |
|---|-----------|---------------|-----|------------|-------|
| 1 | ESP32 DevKit v1 | 38-pin, dual-core | 1× | ~$6 | [AliExpress](https://www.aliexpress.com/item/1005005626482837.html) |
| 2 | HX711 | 24-bit ADC module | 4× | ~$1.5/pc | [AliExpress](https://www.aliexpress.com) |
| 3 | Load cell | 20 kg, bar type | 4× | ~$3/pc | [AliExpress](https://www.aliexpress.com) |
| 4 | OLED display | SH1106 128×64 I²C | 1× | ~$4 | [AliExpress](https://www.aliexpress.com) |
| 5 | Power supply | 5V 2A | 1× | ~$2 | – |
| 6 | Jumper wires | Dupont M-F, M-M | 1 set | ~$2 | – |

**Total estimated cost: ~$30 USD**

#### Optional Components

| # | Component | Purpose | Price | Notes |
|---|-----------|---------|-------|-------|
| 1 | Shielded cables | Load cell wiring (better stability) | ~$4 | Reduces noise |
| 2 | 3.3V voltage regulator | Stable power | ~$1 | Recommended |
| 3 | Capacitors | 100nF, 10µF | ~$1 | Power filtering |
| 4 | Mounting board | Plywood / aluminium | ~$4 | For platform |
| 5 | ESP32 enclosure | 3D-printed | ~$0 | [Model here](https://www.printables.com/model/1014797) |

---

### 🔌 Detailed Wiring

#### Wiring Diagram

```
                    ESP32 DevKit v1 (38-pin)
                    ┌─────────────────────┐
                    │                     │
    OLED SH1106     │  GPIO25 (SDA)       │
    ┌────────┐      │  GPIO27 (SCL)       │
    │ SDA ───┼──────┤                     │
    │ SCL ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
                    │                     │
    HX711 #1        │  GPIO34 (DOUT)      │
    ┌────────┐      │  GPIO26 (SCK) ◄─────┼──── Shared CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 1     │                     │
                    │                     │
    HX711 #2        │  GPIO35 (DOUT)      │
    ┌────────┐      │  GPIO26 (SCK) ◄─────┼──── Shared CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 2     │                     │
                    │                     │
    HX711 #3        │  GPIO13 (DOUT)      │
    ┌────────┐      │  GPIO26 (SCK) ◄─────┼──── Shared CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 3     │                     │
                    │                     │
    HX711 #4        │  GPIO14 (DOUT)      │
    ┌────────┐      │  GPIO26 (SCK) ◄─────┼──── Shared CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 4     │                     │
                    │                     │
    5V Power        │  VIN (5V)           │
    ┌────────┐      │  GND               │
    │ +5V ───┼──────┤                     │
    │ GND ───┼──────┤                     │
    └────────┘      └─────────────────────┘
```

#### Important Wiring Notes

1. **Shared CLK pin (GPIO26)**
   - All 4 HX711 modules share one CLK pin – this is correct and hardware-supported.

2. **HX711 power supply**
   - Use **3.3V** (not 5V!) for logic-level compatibility with ESP32.

3. **ESP32 power**
   - 5V on VIN (ESP32 has an onboard regulator), or 3.3V directly on the 3V3 pin.

4. **Load cell wire colours**
   - Red: E+ (Excitation +)
   - Black: E– (Excitation –)
   - White: A– (Signal –)
   - Green: A+ (Signal +)
   - *(Colours may vary by manufacturer)*

---

### ⚙️ Load Cell Installation

#### Mechanical Layout

```
   ┌──────────────┐
   │   KEG 50L    │
   │              │
   └──────────────┘
         │ │
   ┌─────▼─▼─────┐
   │   Platform   │  ← Top board (on load cells)
   └──────────────┘
    ▲    ▲    ▲   ▲
    │    │    │   │
   ┌┴┐  ┌┴┐  ┌┴┐ ┌┴┐
   │1│  │2│  │3│ │4│  ← Load cells
   └┬┘  └┬┘  └┬┘ └┬┘
    │    │    │   │
   ┌▼────▼────▼───▼┐
   │     Base      │  ← Bottom board (on the floor)
   └───────────────┘
```

#### Mounting Tips

1. **Load cell placement** – place in the corners of a square/rectangle; 30–40 cm spacing recommended.
2. **Platform material** – 18–20 mm plywood, aluminium plate, or thick composite.
3. **Fastening** – bolt load cells firmly to both boards; use washers and locking nuts.
4. **Levelling** – platform must be horizontal; verify with a spirit level.

---

### 🖨️ 3D Printing

| Model | Print settings | Link |
|-------|---------------|------|
| ESP32 enclosure | PLA/PETG, 20% infill, no supports, ~3h | [Printables](https://www.printables.com/model/1014797) |
| HX711 bracket | PLA/PETG, 20% infill, no supports, ~1h | [Printables](https://www.printables.com/model/879042) |

---

### 🔋 Power Options

| Option | Pros | Cons |
|--------|------|------|
| Quality 5V adapter | Stable, sufficient current | Needs mains outlet |
| USB from PC | Easy for development | Unstable, limited current |
| Powerbank | Portable / offline | May cut off at low draw |
| Solar + battery | Outdoor use | Needs charge controller |

**Typical consumption:** ESP32 ~80–240 mA + HX711 ×4 ~4 mA + OLED ~20 mA = **~100–270 mA @ 5V**

---

### 🧪 Pre-Power Checklist

- [ ] All connections secure
- [ ] No shorts between VCC and GND
- [ ] HX711 powered at 3.3V (not 5V)
- [ ] OLED correctly connected (SDA/SCL)
- [ ] Load cells connected to HX711
- [ ] Power supply rated ≥ 2A

---

### ⚠️ Common Hardware Issues

| Symptom | Likely cause | Solution |
|---------|-------------|----------|
| OLED blank | Wrong I²C address | Try 0x3C or 0x3D |
| HX711 not measuring | Bad power | Check 3.3V on all HX711 |
| Unstable readings | Noisy power / vibration | Quality adapter, shielded cables, solid mount |

---

## Česká verze

### 📦 Kompletní seznam komponent (BOM)

#### Základní komponenty (povinné)

| # | Komponenta | Specifikace | Množství | Odh. cena | Odkaz |
|---|------------|-------------|----------|-----------|-------|
| 1 | ESP32 DevKit v1 | 38-pin, dual-core | 1× | ~150 Kč | [AliExpress](https://www.aliexpress.com/item/1005005626482837.html) |
| 2 | HX711 | 24-bit ADC modul | 4× | ~40 Kč/ks | [AliExpress](https://www.aliexpress.com) |
| 3 | Tenzometr | 20 kg, bar type | 4× | ~80 Kč/ks | [AliExpress](https://www.aliexpress.com) |
| 4 | OLED displej | SH1106 128×64 I²C | 1× | ~100 Kč | [AliExpress](https://www.aliexpress.com) |
| 5 | Napájecí zdroj | 5V 2A | 1× | ~50 Kč | – |
| 6 | Propojovací kabely | Dupont M-F, M-M | 1 sada | ~50 Kč | – |

**Celková cena základního sestavení: ~1 100 Kč**

#### Volitelné komponenty

| # | Komponenta | Účel | Cena | Poznámka |
|---|------------|------|------|----------|
| 1 | Stíněné kabely | Pro tenzometry | ~100 Kč | Redukuje šum |
| 2 | Regulátor 3,3V | Stabilní napájení | ~30 Kč | Doporučeno |
| 3 | Kondenzátory | 100nF, 10µF | ~20 Kč | Filtrace napájení |
| 4 | Montážní deska | Překližka / kov | ~100 Kč | Pro platformu |
| 5 | Pouzdro ESP32 | 3D tisk | ~0 Kč | [Model zde](https://www.printables.com/model/1014797) |

---

Detailní schéma zapojení a montážní tipy jsou popsány v [anglické sekci výše](#wiring-diagram).

---

<div align="center">

Made with 🍺 and ❤️

</div>
