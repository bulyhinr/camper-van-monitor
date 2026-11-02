# 🚐 Camper Van Monitor — 7.5" E-Ink Overland Telemetry & Safety Hub

[![ESPHome](https://img.shields.io/badge/ESPHome-2024.x-black?logo=home-assistant)](https://esphome.io/)
[![Hardware](https://img.shields.io/badge/Hardware-ESP32-blue?logo=espressif)](https://www.espressif.com/)
[![Display](https://img.shields.io/badge/Display-7.5%22_E--Paper_800x480-brightgreen)](https://www.waveshare.com/7.5inch-e-paper-hat.htm)
[![Bluetooth](https://img.shields.io/badge/BLE-JBD_Smart_BMS-orange?logo=bluetooth)](https://github.com/syssi/esphome-jbd-bms)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An automotive-grade, ultra-low-power, standalone monitoring dashboard engineered for DIY camper vans, motorhomes, and overland expedition vehicles. 

Powered by an **ESP32** and a **7.5" Waveshare Black & White E-Paper display (800×480)**, it provides essential living telemetry, battery metrics, vehicle leveling, and life-safety monitoring — **100% offline with zero cloud or Wi-Fi dependency**.

---

## 📸 Dashboard Interface Overview

```
┌─────────────────────────────────────────────────────────────┬─────────────────────────────────────────────────────────┐
│ ⚡ POWER & BATTERY SYSTEM                                   │ 🧭 GPS NAVIGATION & TELEMETRY                           │
├───────────────────────────────┬─────────────────────────────┼───────────────────────────────┬─────────────────────────┤
│ TOTAL SOC                     │ 87W  DISCHARGING            │ SPEED                         │ ALTITUDE: 420m          │
│                               │ 13.32V  -6.5A               │                               │ SATELLITES: 11          │
│ 84%                           │ REMAINING: 168.0Ah          │ 65 km/h                       │ 48.85N, 2.35E           │
├───────────────────────────────┴─────────────────────────────┼───────────────────────────────┴─────────────────────────┤
│ 🌡️ CLIMATE & ROOF HATCHES                                   │ 📐 PARKING LEVEL (SPIRIT LEVEL)                         │
├───────────────────────────────┬─────────────────────────────┼───────────────────────────────┬─────────────────────────┤
│ INSIDE                        │ HATCH 1 (Bathroom) ╔══════╗ │ ┌───────────────┐             │ ╔══════════╗            │
│ 21.5°C                        │ Front Area         ║ OPEN ║ │ │       ┼       │             │ ║ LEVEL OK ║            │
├───────────────────────────────┤────────────────────╚══════╝─┤ │ └───────────────┘             │ ╚══════════╝            │
│ OUTSIDE                       │ HATCH 2 (Kitchen)  [CLOSED] ├───────────────────────────────┴─────────────────────────┤
│ 18.2°C                        │ Middle Area                 │ ROLL (SIDE TILT):                                 +0.4° │
├───────────────────────────────┤─────────────────────────────┼─────────────────────────────────────────────────────────┤
│ AIR & SAFETY:                 │ HATCH 3 (Bed Area) [CLOSED] │ PITCH (FRONT TILT):                               -0.8° │
│ NORMAL  2 Zones Safe          │ Rear Bedroom                │                                                         │
└───────────────────────────────┴─────────────────────────────┴─────────────────────────────────────────────────────────┘
```

---

## 📋 Feature Summary & Status

| Category | Feature | Hardware / Protocol | Status |
| :--- | :--- | :--- | :---: |
| 🔋 **Power** | Smart LiFePO4 Battery Monitoring (Single or Multi-Pack) | JBD Smart BMS (BLE) | ✅ Ready |
| 🔋 **Power** | Net Current, Voltage, Power & Capacity Remaining | BLE Telemetry calculation | ✅ Ready |
| 📐 **Leveling** | 2D Spirit Level (Roll & Pitch with 0.1° resolution) | MPU-6050 (I2C) | ✅ Ready |
| 📐 **Leveling** | Level Status Badge (`[ LEVEL OK ]` / `[ UNEVEN ]`) | $\pm 2.0^\circ$ threshold check | ✅ Ready |
| 🚨 **Safety** | Floor Combustible Gas Leak Alarm (LPG/Propane) | MQ-9 Sensor (GPIO 4) | ✅ Ready |
| 🚨 **Safety** | Ceiling Smoke, Fire & Toxic CO Alarm | MQ Sensor (GPIO 33) | ✅ Ready |
| 🚨 **Safety** | Cabin Thermal Fire Safeguard (>55°C) | DS18B20 1-Wire (GPIO 18) | ✅ Ready |
| 🚨 **Safety** | Emergency Full-Screen Takeover + 2500Hz Siren | E-Paper Hardware Wipe + PWM | ✅ Ready |
| 🚗 **Drive Alert**| Open Roof Hatch Warning While Driving | GPS Doppler ($\ge 8$ km/h, 7s) | ✅ Ready |
| 🚗 **Drive Alert**| Open Hatch Location Identification (3 Zones) | Dynamic Screen + Double Chime | ✅ Ready |
| 🪟 **Security** | 3x Roof Hatch Monitoring (Customizable Zones) | MC-38 Reed Switches | ✅ Ready |
| 🌡️ **Climate** | Cabin Indoor & Outdoor Temperatures | Dual DS18B20 (1-Wire) | ✅ Ready |
| 🧭 **Telemetry**| Speed, Altitude, Satellite Count & Coordinates | u-blox NEO-6M/8M (UART) | ✅ Ready |
| 🛰️ **Time** | Satellite Atomic Clock Synchronization | GPS NMEA Time | ✅ Ready |
| 🖥️ **Display** | High-Contrast Sunlight Legible Dashboard | Waveshare 7.5" V2 E-Ink (SPI) | ✅ Ready |
| 🖥️ **Display** | Zero-Ghosting 10-Min Deep Pigment Full Refresh | Custom ESPHome e-Paper logic | ✅ Ready |

---

## ✨ Key Features

### 🔋 1. Smart LiFePO4 Battery Monitoring & Aggregation
* **Wireless BLE Integration:** Connects wirelessly via Bluetooth Low Energy (BLE) to any JBD / Xiaoxiang Smart BMS. Supports single battery systems or multi-pack parallel setups (12V / 24V / 48V, any capacity such as 100Ah, 200Ah, 300Ah+).
* **Live Telemetry & Metrics:** Computes aggregate State of Charge (SoC %), net charge/discharge power in Watts, net current in Amps, pack voltage, and total remaining Amp-hours (Ah).
* **Charge Direction Detection:** Automatically recognizes whether the system is `CHARGING` (Solar/Alternator/Shore), `DISCHARGING`, or in `STANDBY`.

### 📐 2. Automotive Parking Inclinometer (Spirit Level)
* **Real-time Vehicle Leveling:** Uses an MPU-6050 6-DOF sensor to compute **Roll** (side-to-side) and **Pitch** (front-to-back) angles with 0.1° resolution.
* **Bubble Level Visualizer:** Features an animated crosshair target where the bubble rises to the highest point (identical to a physical spirit level).
* **Level Status Badge:** Displays a bold `[ LEVEL OK ]` badge when the vehicle is within $\pm 2.0^\circ$ of true horizontal, ensuring a flat bed for sleeping.

### 🚨 3. Dual-Zone Life Safety & Acoustic Siren
* **Floor Zone (LPG / Propane / Butane):** Detects heavy combustible gas pooling near the floor using an **MQ-9** sensor.
* **Ceiling Zone (Smoke / Fire / CO):** Detects rising hot smoke and toxic Carbon Monoxide near the ceiling using an **MQ** sensor.
* **Thermal Fire Detection:** Triggers an alarm if cabin temperature exceeds **+55°C** (independent fire safeguard).
* **Emergency Full-Screen Takeover:** Instantly replaces the dashboard with an emergency evacuation screen and sounds a 2500Hz pulsed acoustic siren.
* **Dynamic Threat Identification:** Identifies the exact origin (`GAS LEAK AT FLOOR` vs `FIRE / SMOKE AT CEILING`).
* **Zero Ghosting Restoration:** Uses hardware-level E-Ink charge reset (`ClearHelper`) when returning to normal mode.

### 🪟 4. Roof Hatch & Ventilation Security
* **3 Monitored Zones:** Real-time reed switch monitoring of **Bathroom**, **Kitchen**, and **Bed Area** roof vents.
* **Open Vent Warning:** Displays a prominent double-outline `OPEN` badge when any hatch is open, preventing accidental drive-offs with open vents.

### 🌡️ 5. Dual Climate Probes (1-Wire)
* Dedicated Dallas **DS18B20** waterproof probes for inside cabin and outdoor weather temperatures.

### 🛰️ 6. Standalone GPS Telemetry
* Reads NMEA sentences from an onboard **u-blox NEO-6M / NEO-8M** GPS receiver.
* Reports real-time ground speed in **km/h**, altitude in **meters**, active satellite count, and coordinates.

### 🖥️ 7. E-Ink Engineered UI Architecture
* **Zero Glare & High Sunlight Contrast:** Perfect readability in direct desert sunlight and zero glow at night.
* **Partial Refresh Optimized:** Uses clean white backgrounds and 2-pass stroke bolding to avoid E-Ink ghosting.
* **Periodic Hardware Reset:** Automated full-refresh every 10 minutes (`full_update_every: 20`) to maintain deep black pigment density.

---

## 🚨 Emergency Full-Screen Mode

When gas, smoke, or excessive heat is detected, the display immediately locks into emergency mode:

```
╔═══════════════════════════════════════════════════════════════════════════════════════╗
║ █████████████████████████████████████████████████████████████████████████████████████ ║
║ ██                            !   D A N G E R   !                                  ██ ║
║ ██         FIRE / SMOKE DETECTED AT CEILING -- EVACUATE!                           ██ ║
║ █████████████████████████████████████████████████████████████████████████████████████ ║
║                                                                                       ║
║                            EMERGENCY SAFETY ACTIONS                                   ║
║   ─────────────────────────────────────────────────────────────────────────────────   ║
║   ┌─────────────────────────────────────────────────────────────────────────────┐     ║
║   │  [ 1 ]  OPEN ALL ROOF HATCHES AND CABIN DOORS                               │     ║
║   │  [ 2 ]  SHUT OFF GAS CYLINDER VALVE IMMEDIATELY                             │     ║
║   │  [ 3 ]  TURN OFF DIESEL / GAS HEATER (NO SPARKS)                            │     ║
║   │  [ 4 ]  DO NOT USE ANY ELECTRICAL SWITCHES                                  │     ║
║   │  [ 5 ]  EVACUATE VEHICLE UNTIL SAFE LEVEL RESTORED                          │     ║
║   └─────────────────────────────────────────────────────────────────────────────┘     ║
║                                                                                       ║
║ █████████████████████████████████████████████████████████████████████████████████████ ║
║ ██        ACOUSTIC SIREN ACTIVE  *  AUTO-RECOVERY WHEN SAFE                        ██ ║
║ █████████████████████████████████████████████████████████████████████████████████████ ║
╚═══════════════════════════════════════════════════════════════════════════════════════╝
```

---

## 🚗 Open Roof Hatch Drive Warning Screen

If any of the 3 roof hatches are open and the vehicle begins driving ($\ge 8$ km/h for $\ge 7$ seconds, verified via GPS Doppler speed and $\ge 4$ satellites), the dashboard immediately takes over with an automotive drive alert and double-chime audible reminder:

```
╔═══════════════════════════════════════════════════════════════════════════════════════╗
║ █████████████████████████████████████████████████████████████████████████████████████ ║
║ ██           ⚠️  DRIVE WARNING: ROOF HATCH OPEN WHILE DRIVING  ⚠️                  ██ ║
║ ██           SPEED: 18 km/h  •  PULL OVER SAFELY & CLOSE HATCHES                   ██ ║
║ █████████████████████████████████████████████████████████████████████████████████████ ║
║                                                                                       ║
║   ┌─────────────────────────────────────────────────────────────────────────────┐     ║
║   │   [ ! OPEN ! ]     HATCH 1: BATHROOM AREA       (Front Zone)                │     ║
║   │   [  CLOSED  ]     HATCH 2: KITCHEN AREA        (Middle Zone)               │     ║
║   │   [ ! OPEN ! ]     HATCH 3: BED AREA            (Rear Zone)                 │     ║
║   └─────────────────────────────────────────────────────────────────────────────┘     ║
║                                                                                       ║
║            Risk of aerodynamic damage or tearing hatch off at highway speed!          ║
║                                                                                       ║
║ █████████████████████████████████████████████████████████████████████████████████████ ║
║ ██    CHIME ACTIVE  •  DISMISSES AUTOMATICALLY ONCE STOPPED OR HATCHES CLOSED     ██ ║
║ █████████████████████████████████████████████████████████████████████████████████████ ║
╚═══════════════════════════════════════════════════════════════════════════════════════╝
```

* **Zero False Alarms:** Stationary GPS drift (<2–3 km/h) is completely ignored through a 3-stage persistence filter.
* **Auto-Dismiss:** Once the vehicle stops or the open hatches are latched closed, the chime silences and the screen restores cleanly.

---

## 📁 Repository Structure

```
camper-display/
├── camper_dashboard.yaml   # Main ESPHome configuration and C++ display lambda
├── huge_app.csv            # Custom partition table (3MB app partition for BLE + E-Paper)
├── WIRING.md               # Complete hardware wiring diagrams and pinout table
├── HARDWARE.md             # Bill of Materials (BOM), part models, and sourcing guide
├── .gitignore              # Ignores build artifacts and secrets
└── README.md               # Main project documentation
```

---

## 🚀 Getting Started

### 1. Prerequisites
Install [ESPHome](https://esphome.io/) on your computer:
```bash
pip install esphome
```

### 2. Configure Your Battery BMS Addresses
Open `camper_dashboard.yaml` and set your JBD BMS Bluetooth MAC address(es):
```yaml
ble_client:
  - id: jbd_client1
    mac_address: "XX:XX:XX:XX:XX:XX" # <-- Replace with your Battery 1 BMS MAC address
  # Optional: For dual/multi-battery setups, add your additional packs:
  # - id: jbd_client2
  #   mac_address: "YY:YY:YY:YY:YY:YY" # <-- Replace with your Battery 2 BMS MAC address
```
*(Tip: You can easily find your BMS MAC address using any BLE Scanner app on iOS/Android).*

### 3. Flash to ESP32
Connect your ESP32 board via USB and run:
```bash
esphome run camper_dashboard.yaml
```

---

## ⚡ Hardware Pinout Summary

See [WIRING.md](WIRING.md) for full schematics, wire colors, and pull-up resistor guidelines.

| Function | ESP32 GPIO | Connected Component |
| :--- | :--- | :--- |
| **E-Paper SPI** | `13, 14, 15, 25, 26, 27` | Waveshare 7.5" V2 Display (CLK, DIN, CS, BUSY, RST, DC) |
| **Inclinometer I2C** | `GPIO 21 (SDA), GPIO 22 (SCL)` | MPU-6050 (GY-521) |
| **GPS UART** | `GPIO 16 (RX), GPIO 17 (TX)` | u-blox NEO-6M / NEO-8M |
| **1-Wire Temperature**| `GPIO 18` | Dual DS18B20 Probes (+ 4.7kΩ pull-up to 3.3V) |
| **Gas Sensor (Floor)**| `GPIO 4 (DO), GPIO 35 (AO)` | MQ-9 Gas Sensor |
| **Smoke Sensor (Roof)**| `GPIO 33 (DO), GPIO 34 (AO)` | MQ Smoke / Fire Sensor |
| **Roof Hatch 1** | `GPIO 19` | Bathroom Area Reed Switch |
| **Roof Hatch 2** | `GPIO 5` | Kitchen Area Reed Switch |
| **Roof Hatch 3** | `GPIO 32` | Bed Area Reed Switch |
| **Alarm Buzzer** | `GPIO 23` | Piezo Buzzer (2500Hz PWM) |

---

## 📚 Documentation Links
* 🔌 [Complete Wiring & Pinout Guide (WIRING.md)](WIRING.md)
* 🛠️ [Bill of Materials & Component Sourcing (HARDWARE.md)](HARDWARE.md)

---

## 📄 License
This project is open-source under the [MIT License](LICENSE). Built for the camper van and overland community with ❤️.
