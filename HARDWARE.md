# 🔧 Hardware Dokumentace / Hardware Documentation

[🇨🇿 Česky](#česká-verze) | [🇬🇧 English](#english-version)

---

## Česká verze

### 📦 Kompletní seznam komponent (BOM)

#### Základní komponenty (povinné)

| #  | Komponenta | Specifikace | Množství | Odhadovaná cena | Odkazy |
|----|------------|-------------|----------|-----------------|--------|
| 1  | ESP32 DevKit v1 | 38-pin, dual-core | 1× | ~150 Kč | [AliExpress](https://www.aliexpress.com/item/1005005626482837.html) |
| 2  | HX711 | 24-bit ADC modul | 4× | ~40 Kč/ks | [AliExpress](https://www.aliexpress.com) |
| 3  | Load Cell | 20 kg, bar type | 4× | ~80 Kč/ks | [AliExpress](https://www.aliexpress.com) |
| 4  | OLED displej | SH1106 128×64 I²C | 1× | ~100 Kč | [AliExpress](https://www.aliexpress.com) |
| 5  | Napájecí zdroj | 5V 2A | 1× | ~50 Kč | - |
| 6  | Propojovací kabely | Dupont M-F, M-M | 1 sada | ~50 Kč | - |

**Celková cena základního sestavení: ~1100 Kč**

#### Volitelné komponenty (doporučené)

| #  | Komponenta | Účel | Cena | Poznámka |
|----|------------|------|------|----------|
| 1  | Stíněné kabely | Pro load cells (lepší stabilita) | ~100 Kč | Redukuje šum |
| 2  | 3.3V regulátor | Stabilní napájení | ~30 Kč | Doporučeno |
| 3  | Kondenzátory | 100nF, 10µF | ~20 Kč | Filtrace napájení |
| 4  | Montážní deska | Plexisklo / dřevo | ~100 Kč | Pro uchycení |
| 5  | Pouzdro ESP32 | 3D tištěné | ~0 Kč | [Model zde](https://www.printables.com/model/1014797) |

---

### 🔌 Detailní zapojení

#### Schéma zapojení

```
                    ESP32 DevKit v1 (38-pin)
                    ┌─────────────────────┐
                    │                     │
    OLED SH1106     │  GPIO25 (SDA)      │
    ┌────────┐      │  GPIO27 (SCL)      │
    │ SDA ───┼──────┤                     │
    │ SCL ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
                    │                     │
    HX711 #1        │  GPIO34 (DOUT)     │
    ┌────────┐      │  GPIO26 (SCK) ◄────┼──── Sdílený CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 1     │                     │
                    │                     │
    HX711 #2        │  GPIO35 (DOUT)     │
    ┌────────┐      │  GPIO26 (SCK) ◄────┼──── Sdílený CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 2     │                     │
                    │                     │
    HX711 #3        │  GPIO13 (DOUT)     │
    ┌────────┐      │  GPIO26 (SCK) ◄────┼──── Sdílený CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 3     │                     │
                    │                     │
    HX711 #4        │  GPIO14 (DOUT)     │
    ┌────────┐      │  GPIO26 (SCK) ◄────┼──── Sdílený CLK
    │ DOUT ──┼──────┤                     │
    │ SCK ───┼──────┤                     │
    │ VCC ───┼──────┤  3.3V              │
    │ GND ───┼──────┤  GND               │
    └────────┘      │                     │
    Load Cell 4     │                     │
                    │                     │
    Napájení 5V     │  VIN (5V)          │
    ┌────────┐      │  GND               │
    │ +5V ───┼──────┤                     │
    │ GND ───┼──────┤                     │
    └────────┘      └─────────────────────┘
```

#### Důležité poznámky k zapojení

1. **Sdílený CLK pin (GPIO26)**
   - Všechny 4 HX711 moduly sdílejí jeden CLK pin
   - To je správná konfigurace a je podporováno hardwarem
   
2. **Napájení HX711**
   - Doporučeno 3.3V (ne 5V!)
   - Důvod: logická kompatibilita s ESP32
   - 5V může fungovat, ale může způsobit problémy
   
3. **Napájení ESP32**
   - 5V na VIN pin (ESP32 má vlastní regulátor)
   - Nebo 3.3V přímo na 3V3 pin (bypass regulátor)
   
4. **Load Cell zapojení**
   - Červený: E+ (Excitation +)
   - Černý: E- (Excitation -)
   - Bílý: A- (Signal -)
   - Zelený: A+ (Signal +)
   - (Barvy se mohou lišit podle výrobce)

---

### ⚙️ Load Cell instalace

#### Mechanická montáž

```
   ┌──────────────┐
   │    SUD 50L   │
   │              │
   └──────────────┘
         │ │
   ┌─────▼─▼─────┐
   │   Platforma  │  ← Horní deska (na load cells)
   └──────────────┘
    ▲    ▲    ▲   ▲
    │    │    │   │
   ┌┴┐  ┌┴┐  ┌┴┐ ┌┴┐
   │1│  │2│  │3│ │4│  ← Load Cells (tenzometry)
   └┬┘  └┬┘  └┬┘ └┬┘
    │    │    │   │
   ┌▼────▼────▼───▼┐
   │   Základna    │  ← Spodní deska (na zemi)
   └───────────────┘
```

#### Tipy pro montáž

1. **Rozmístění load cells**
   - Umístěte do rohů obdélníku / čtverce
   - Rovnoměrné rozložení zátěže
   - Doporučená vzdálenost: 30-40 cm mezi nohy

2. **Materiál platformy**
   - Dřevo: 18-20mm překližka (dobré)
   - Kov: hliníková deska (vynikající)
   - Plast: silný kompozit (možné)

3. **Fixace**
   - Load cells pevně přišroubujte k oběma deskám
   - Používejte podložky a zajišťovací matice
   - Zajistěte, aby se nic nehýbalo

4. **Vyrovnání**
   - Platforma musí být vodorovná
   - Použijte vodováhu pro kontrolu
   - Při kalibraci zkontrolujte, že všechny 4 nohy měří podobně

---

### 🖨️ 3D Tisk - detaily

#### Doporučené modely

1. **ESP32 Pouzdro**
   - Link: [Printables](https://www.printables.com/model/1014797)
   - Materiál: PLA / PETG
   - Výplň: 20%
   - Podpory: Ne
   - Čas tisku: ~3 hodiny

2. **HX711 Držák**
   - Link: [Printables](https://www.printables.com/model/879042)
   - Materiál: PLA / PETG
   - Výplň: 20%
   - Podpory: Ne
   - Čas tisku: ~1 hodina

3. **Custom Load Cell Mount** (vlastní návrh)
   - Můžete si navrhnout vlastní držák
   - Důležité: load cell musí být pevně uchycen
   - Doporučené: otvory pro šrouby M4 nebo M5

---

### 🔋 Napájení

#### Možnosti napájení

1. **USB napájení z počítače**
   - ✅ Jednoduché pro vývoj
   - ❌ Nestabilní napětí
   - ❌ Omezený proud
   - ⚠️ Nedoporučeno pro trvalé použití

2. **Kvalitní 5V adaptér**
   - ✅ Doporučeno pro provoz
   - ✅ Stabilní napětí
   - ✅ Dostatečný proud (min 2A)
   - 💡 Nejlepší volba

3. **Powerbank**
   - ✅ Pro offline / mobilní použití
   - ⚠️ Některé se vypnou při malém odběru
   - 💡 Doporučeno: powerbank s funkcí "always on"

4. **Solární panel + baterie**
   - ✅ Pro venkovní instalace
   - ⚠️ Vyžaduje charge controller
   - 💡 Pro pokročilé uživatele

#### Spotřeba energie

- ESP32: ~80-240 mA (závisí na Wi-Fi)
- HX711 (4×): ~4 mA
- OLED: ~20 mA
- **Celkem: ~100-270 mA @ 5V** (0.5-1.35W)

---

### 🧪 Testování hardwaru

#### Kontrolní seznam před zapnutím

- [ ] Všechna zapojení jsou pevná
- [ ] Žádné zkraty mezi VCC a GND
- [ ] HX711 napájení 3.3V (ne 5V)
- [ ] OLED správně připojen (SDA/SCL)
- [ ] Load cells připojeny k HX711
- [ ] Napájecí zdroj má dostatečný výkon (min 2A)

#### První test

1. Připojte pouze ESP32 + OLED (bez HX711)
2. Nahrajte jednoduchý test kód
3. Zkontrolujte, že OLED zobrazuje
4. Postupně připojte každý HX711
5. Zkontrolujte logy v ESPHome

---

### ⚠️ Časté hardwarové problémy

#### OLED se nezobrazuje

- **Problém:** Špatná I²C adresa
- **Řešení:** Zkuste I²C scanner kód
- **Možné adresy:** 0x3C nebo 0x3D

#### HX711 neměří

- **Problém:** Špatné napájení
- **Řešení:** Zkontrolujte 3.3V na všech HX711
- **Problém:** Vadný HX711 modul
- **Řešení:** Vyzkoušejte jiný modul

#### Nestabilní měření

- **Problém:** Špatné napájení ESP32
- **Řešení:** Použijte kvalitní 5V adaptér
- **Problém:** Nešíněné kabely load cells
- **Řešení:** Použijte stíněné kabely
- **Problém:** Mechanické vibrace
- **Řešení:** Pevně přišroubujte vše

---

## English Version

### 📦 Complete Component List (BOM)

#### Essential Components (Required)

| #  | Component | Specification | Quantity | Estimated Price | Links |
|----|-----------|---------------|----------|-----------------|-------|
| 1  | ESP32 DevKit v1 | 38-pin, dual-core | 1× | ~$6 USD | [AliExpress](https://www.aliexpress.com/item/1005005626482837.html) |
| 2  | HX711 | 24-bit ADC module | 4× | ~$1.5/pc | [AliExpress](https://www.aliexpress.com) |
| 3  | Load Cell | 20 kg, bar type | 4× | ~$3/pc | [AliExpress](https://www.aliexpress.com) |
| 4  | OLED display | SH1106 128×64 I²C | 1× | ~$4 | [AliExpress](https://www.aliexpress.com) |
| 5  | Power supply | 5V 2A | 1× | ~$2 | - |
| 6  | Jumper wires | Dupont M-F, M-M | 1 set | ~$2 | - |

**Total cost for basic build: ~$30 USD**

*For detailed wiring, mechanical installation, and troubleshooting, see the Czech version above.*

---

<div align="center">

Made with 🍺 and ❤️

</div>
