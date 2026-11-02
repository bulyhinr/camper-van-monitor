# 🔌 Wiring & Pinout Guide — Camper Van Monitor

Complete hardware wiring, pin assignments, schematics, and electrical guidelines for the **ESP32 Camper Van Monitor & Inclinometer**.

---

## 📌 Master Pinout Allocation Table

| Physical Label | ESP32 GPIO | Bus / Protocol | Direction | Connected Device | Device Pin | Purpose / Function |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **P13** | `GPIO 13` | SPI SCK | Output | E-Paper Driver Board | **SCK / CLK** | SPI Clock for 7.5" E-Ink |
| **P14** | `GPIO 14` | SPI MOSI | Output | E-Paper Driver Board | **DIN / MOSI** | SPI Master-Out Slave-In |
| **P15** | `GPIO 15` | SPI CS | Output | E-Paper Driver Board | **CS** | Chip Select (Active LOW) |
| **P27** | `GPIO 27` | GPIO | Output | E-Paper Driver Board | **DC** | Data / Command selector |
| **P26** | `GPIO 26` | GPIO | Output | E-Paper Driver Board | **RST** | Hardware Display Reset |
| **P25** | `GPIO 25` | GPIO | Input | E-Paper Driver Board | **BUSY** | Screen Busy Status (`inverted: true`) |
| **P21** | `GPIO 21` | I2C SDA | Bidirectional | MPU-6050 (GY-521) | **SDA** | I2C Data (Inclinometer) |
| **P22** | `GPIO 22` | I2C SCL | Output | MPU-6050 (GY-521) | **SCL** | I2C Clock (Inclinometer) |
| **P16** | `GPIO 16` | UART RX | Input | GPS NEO-6M / 8M | **TX** | GPS NMEA Sentences (9600 baud) |
| **P17** | `GPIO 17` | UART TX | Output | GPS NEO-6M / 8M | **RX** | GPS Commands (optional) |
| **P18** | `GPIO 18` | 1-Wire | Bidirectional | DS18B20 Probes (1 & 2) | **DATA** (Yellow) | Cabin & Outdoor Temperature |
| **P19** | `GPIO 19` | GPIO (Pullup) | Input | MC-38 Reed Switch #1 | Wire 1 | Roof Hatch 1 (Bathroom) |
| **P5** | `GPIO 5` | GPIO (Pullup) | Input | MC-38 Reed Switch #2 | Wire 1 | Roof Hatch 2 (Kitchen) |
| **P32** | `GPIO 32` | GPIO (Pullup) | Input | MC-38 Reed Switch #3 | Wire 1 | Roof Hatch 3 (Bed Area) |
| **P4** | `GPIO 4` | GPIO (Pullup) | Input | MQ-9 Gas Sensor (Floor) | **DO** | Digital Gas / LPG Alarm |
| **P35** | `GPIO 35` | ADC1 (Input) | Analog Input | MQ-9 Gas Sensor (Floor) | **AO** | Analog Gas Concentration |
| **P33** | `GPIO 33` | GPIO (Pullup) | Input | MQ Smoke Sensor (Ceiling)| **DO** | Digital Smoke / Fire / CO Alarm |
| **P34** | `GPIO 34` | ADC1 (Input) | Analog Input | MQ Smoke Sensor (Ceiling)| **AO** | Analog Smoke Concentration |
| **P23** | `GPIO 23` | LEDC PWM | Output | Piezo Buzzer | **(+) Pin** | 2500Hz Emergency Acoustic Siren |
| **3.3V** | `3.3V` | Power Rail | Output | MPU, DS18B20, E-Paper | **VCC / 3.3V** | 3.3V Logic Power |
| **5V / VIN**| `5V / VIN` | Power Rail | Input/Output | MQ-9, MQ Smoke, GPS | **VCC / 5V** | 5V Power (MQ Heating Coils!) |
| **GND** | `GND` | Ground | Ground | ALL MODULES | **GND** | Common Ground Bus |

---

## 🗺️ Master ASCII System Architecture

```
                                  ┌────────────────────────┐
                                  │ Leisure Battery Bank   │
                                  │ LiFePO4 (12V / 24V)    │
                                  └───────────┬────────────┘
                                              │ 12V DC
                                              ▼
                                 ┌─────────────────────────┐
                                 │ 12V -> 5V 3A Buck Conv. │
                                 └────────────┬────────────┘
                                              │ +5V & GND
                                              ▼
                   ┌────────────────────────────────────────────────────────┐
                   │                     COMMON 5V BUS                      │
                   └───────┬───────────────────┬────────────────────┬───────┘
                           │ +5V               │ +5V                │ +5V
                           ▼                   ▼                    ▼
                    ┌─────────────┐     ┌─────────────┐     ┌──────────────┐
                    │ MQ-9 (Floor)│     │ MQ (Ceiling)│     │ GPS (NEO-6M) │
                    │ Gas Sensor  │     │ Smoke Sensor│     │              │
                    └──────┬──────┘     └──────┬──────┘     └──────┬───────┘
                           │ DO: P4            │ DO: P33           │ TX: P16
                           │ AO: P35           │ AO: P34           │
                           │                   │                   │
  ┌────────────────────────┴───────────────────┴───────────────────┴──────────┐
  │                           ESP32 CONTROLLER BOARD                          │
  │                     (Waveshare E-Paper Driver Board)                      │
  ├───────────────────────────────────────────────────────────────────────────┤
  │  [ BLE 4.2 Engine ]  ◄- - - - (Wireless) - - - -►  JBD Smart BMS Pack(s)  │
  │                                                                           │
  │  [ SPI Bus: P13, P14, P15, P25, P26, P27 ] ──► Waveshare 7.5" E-Ink       │
  │                                                                           │
  │  [ I2C Bus: P21 (SDA), P22 (SCL) ] ──────────► MPU-6050 (Inclinometer)    │
  │                                                                           │
  │  [ 1-Wire Bus: P18 (+4.7kΩ Pullup) ] ────────► Dual DS18B20 Temp Probes  │
  │                                                                           │
  │  [ Digital GPIOs: P19, P5, P32 (Pullup) ] ───► 3x MC-38 Reed Hatch Sensors│
  │                                                                           │
  │  [ PWM Timer: P23 @ 2500Hz ] ────────────────► Emergency Piezo Buzzer     │
  └───────────────────────────────────────────────────────────────────────────┘
```

---

## 🔬 Component Connection Details

### 1. Waveshare 7.5" E-Paper Display (800x480)
If using the **Waveshare Universal e-Paper Driver Board**, the display connects directly via the FFC ribbon cable. If wiring via the 8-pin JST-PH cable:
* `VCC` $\rightarrow$ `3.3V`
* `GND` $\rightarrow$ `GND`
* `DIN` $\rightarrow$ `GPIO 14`
* `CLK` $\rightarrow$ `GPIO 13`
* `CS`  $\rightarrow$ `GPIO 15`
* `DC`  $\rightarrow$ `GPIO 27`
* `RST` $\rightarrow$ `GPIO 26`
* `BUSY`$\rightarrow$ `GPIO 25`

---

### 2. MPU-6050 (GY-521) 6-DOF Inclinometer
* `VCC` $\rightarrow$ `3.3V` (GY-521 board has onboard 3.3V LDO, works on 3.3V or 5V; 3.3V recommended)
* `GND` $\rightarrow$ `GND`
* `SDA` $\rightarrow$ `GPIO 21`
* `SCL` $\rightarrow$ `GPIO 22`
* `AD0` $\rightarrow$ Leave unconnected (defaults to I2C address `0x68`)
* `INT` $\rightarrow$ Leave unconnected

#### 🧭 Physical Orientation Rule:
* **Position:** Horizontal, parallel to vehicle floor, **chips/components facing UP** towards ceiling.
* **X-Arrow:** Points **FORWARD** (towards front windshield).
* **Y-Arrow:** Points **LEFT** (towards driver door).
* *Math in firmware automatically maps this to a spirit level where the bubble rises to the highest point.*

---

### 3. Dual DS18B20 Temperature Probes (1-Wire Bus)
Both sensors share the **exact same 3 wires** in parallel:

```
3.3V Rail ──────┬───────────────────────────────────┐
                │                                   │
              ┌─┴─┐                                 │
              │4.7│ 4.7 kΩ Pull-up Resistor         │
              │ k │ (CRITICAL for long wires!)      │
              └─┬─┘                                 │
                │                                   │
GPIO 18 ────────┼──────────────┬────────────────────┤
(1-Wire Bus)    │              │                    │
             [Yellow]       [Yellow]                │
           ┌──────────┐   ┌──────────┐              │
           │ DS18B20  │   │ DS18B20  │              │
           │ Probe 1  │   │ Probe 2  │              │
           │ (Inside) │   │(Outside) │              │
           └────┬─────┘   └────┬─────┘              │
             [Black]        [Black]                 │
                │              │                    │
GND Rail ───────┴──────────────┴────────────────────┘
```

* **Probe 1 (Cabin):** Sensor ID `temp_inside`.
* **Probe 2 (Outside):** Sensor ID `temp_outside`.
* **Wire Colors:** Red = `3.3V`, Black/Blue = `GND`, Yellow/White = `DATA` (`GPIO 18`).
* ⚠️ **Mandatory:** Place a **4.7 kΩ** (or 3.3kΩ–10kΩ) resistor between `3.3V` and `GPIO 18`.

---

### 4. Dual Gas & Smoke Sensors (MQ Series)

Both sensors require **5V** because the internal heating coils need 5V to reach operating temperature (~150mA each).

#### Floor Sensor — MQ-9 (LPG / Propane / CO):
* `VCC` $\rightarrow$ `5V`
* `GND` $\rightarrow$ `GND`
* `DO`  $\rightarrow$ `GPIO 4`
* `AO`  $\rightarrow$ `GPIO 35`
* *Mounting:* 20–30 cm above floor near gas cylinder / kitchen stove.

#### Ceiling Sensor — MQ-2 or MQ-9 (Smoke / Fire / CO):
* `VCC` $\rightarrow$ `5V`
* `GND` $\rightarrow$ `GND`
* `DO`  $\rightarrow$ `GPIO 33`
* `AO`  $\rightarrow$ `GPIO 34`
* *Mounting:* On ceiling above living / cooking area.

---

### 5. Roof Hatch Reed Switches (MC-38 Magnetic Contacts)
Each MC-38 has 2 wires with no polarity:
* **Hatch 1 (Bathroom Area):** Wire 1 $\rightarrow$ `GPIO 19`, Wire 2 $\rightarrow$ `GND`
* **Hatch 2 (Kitchen Area):** Wire 1 $\rightarrow$ `GPIO 5`, Wire 2 $\rightarrow$ `GND`
* **Hatch 3 (Bed Area):** Wire 1 $\rightarrow$ `GPIO 32`, Wire 2 $\rightarrow$ `GND`

*How it works:*
* Magnet attached to hatch near sensor (Hatch Closed) $\rightarrow$ circuit closed to `GND` $\rightarrow$ Pin reads `LOW` (`CLOSED`).
* Hatch opened $\rightarrow$ magnet moves away $\rightarrow$ circuit opens $\rightarrow$ ESP32 internal pullup pulls pin `HIGH` $\rightarrow$ triggers `OPEN` badge.

---

### 6. GPS Module (NEO-6M / NEO-8M)
* `VCC` $\rightarrow$ `3.3V` or `5V` (most breakout boards have onboard 3.3V regulator)
* `GND` $\rightarrow$ `GND`
* `TX`  $\rightarrow$ `GPIO 16` (ESP32 UART RX)
* `RX`  $\rightarrow$ `GPIO 17` (ESP32 UART TX, optional)

---

### 7. Emergency Piezo Buzzer
* **Positive (+):** Connect to `GPIO 23`
* **Negative (-):** Connect to `GND`
* *Driven by hardware LEDC PWM at 2500 Hz (supports both active and passive buzzers).*

---

## ⚡ Power Supply & Best Practices

1. **Common Ground:** Every single module (`GND`) must be tied to a common ground bus connected to the ESP32 `GND`.
2. **Never Power MQ Heaters from ESP32 3.3V:** The 3.3V regulator will overheat and burn out. Feed 5V directly from your 12V-to-5V DC-DC buck converter.
3. **Decoupling Capacitor:** Place a 100µF – 470µF electrolytic capacitor across the 5V and GND rail near the ESP32 to smooth voltage spikes caused by inductive loads.
