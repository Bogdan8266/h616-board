![Status](https://img.shields.io/badge/STATUS-IN_DEVELOPMENT-yellow?style=for-the-badge&logo=server)
![KiCad](https://img.shields.io/badge/KiCad-10.0-blue?style=for-the-badge&logo=kicad&logoColor=white)
![AllWinner](https://img.shields.io/badge/SoC-H616_Quad--Core-009688?style=for-the-badge&logo=allwinner&logoColor=white)
![JLCPCB](https://img.shields.io/badge/PCB-8--Layer_JLC08101H-purple?style=for-the-badge&logo=PCB)
![Power](https://img.shields.io/badge/Power-SW6206_PD3.0_22.5W-orange?style=for-the-badge&logo=battery)

# Nebula Board (Allwinner H616 Devboard)

A compact, battery-powered 64-bit ARM development board measuring **50 mm × 50 mm** based on the **Allwinner H616** Quad-Core Cortex-A53 SoC with integrated powerbank PMU, high-speed DDR3, eMMC 5.1, and dual-band Wi-Fi.

![Nebula Board 3D Render](pics/3d-render.png)

Unlike standard commercial H616 boards (Orange Pi, Banana Pi, etc.) that require a 5V/3A wall adapter and shut down the moment you unplug the cable, **Nebula Board** features an onboard bidirectional fast-charging system that runs from a single Li-ion cell (18650 / Li-Po) and recharges via USB-C with USB-PD / QC4+.

---

## 📐 Board Layout & Architecture

The PCB has been floorplanned to strictly separate high-speed digital buses, power conversion, and radio frequency:

![PCB Editor Layout](pics/pcb.png)

* **Top-Right:** **Allwinner H616 SoC** (TFBGA284, 0.65 mm ball pitch).
* **Top-Center:** **DDR3 SDRAM** (BGA-96) — routed horizontally directly into H616 with ~15 mm trace lengths for minimal skew.
* **Bottom-Right:** **eMMC 5.1 Storage** (BGA-153) — routed vertically directly into the SoC for high-speed HS400 mode without crossing the DDR bus.
* **Bottom-Right Corner:** **Micro-HDMI (J2)** — 100 Ω impedance-matched TMDS differential pairs routed cleanly along the board edge.
* **Bottom-Center:** **AXP305 PMIC** — central multi-channel power distribution with 5 dedicated buck inductors (VDD_CPU, VDD_GPU, VCC_DRAM, etc.).
* **Bottom-Left:** **SW6206 Powerbank Controller** — handles 18W–22.5W fast charging, 1S battery fuel gauge, and system power boost.
* **Top-Left:** **Ampak AP6256 / Dual-Band Wi-Fi (2.4G / 5GHz + BT 5.0)** module with external **IPEX/U.FL** antenna connector.
* **Top Edge:** Standard **40-pin GPIO header** (including hardware **I2S3** audio lines and **I2C0**).
* **Bottom Edge:** **Dual USB-C ports** — isolated power and data paths.

---

## 🏭 JLCPCB 8-Layer Stackup & Impedance

The board is designed around JLCPCB's standard 8-layer stackup **`JLC08101H-1080`** with a finished thickness of **1.02 mm ±10%**.

### Layer Stackup Structure
![JLCPCB Stackup](pics/stack2.png)

* **L1 (F.Cu):** High-speed signals, RF 50 Ω, components (1 oz copper, 0.035 mm)
* **L2 (In1.Cu):** **Continuous Ground Plane (GND)** (0.5 oz copper, 0.0152 mm)
* **L3 (In2.Cu):** Inner signal routing — DDR3 striplines (0.5 oz copper, 0.0152 mm)
* **L4 (In3.Cu):** Ground / Power Plane (0.5 oz copper, 0.0152 mm)
* **L5 (In4.Cu):** Power Planes (VDD_CPU, VDD_GPU, +5V, +3.3V) (0.5 oz copper, 0.0152 mm)
* **L6 (In5.Cu):** Inner signal routing (0.5 oz copper, 0.0152 mm)
* **L7 (In6.Cu):** **Continuous Ground Plane (GND)** (0.5 oz copper, 0.0152 mm)
* **L8 (B.Cu):** Decoupling capacitors (0402 directly under BGA balls) & connectors (1 oz copper, 0.035 mm)

### Controlled Impedance Table
![Impedance Calculations](pics/stack.png)

| Signal Group | Impedance | Layer | Trace Width | Spacing | Notes |
|:---|:---:|:---:|:---:|:---:|:---|
| **RF / Single-Ended 50 Ω** | $50\ \Omega$ | **L1** (ref L2) | **0.107 mm** | — | Wi-Fi 50 Ω line, eMMC, Clock |
| **DDR3 Single-Ended 50 Ω** | $50\ \Omega$ | **L3** (ref L2/L4) | **0.106 mm** | — | Inner stripline DDR3 DQ / Address |
| **USB 2.0 Differential 90 Ω** | $90\ \Omega$ | **L1** (ref L2) | **0.102 mm** | **0.140 mm** | USB D+ / D- differential pairs |
| **USB 2.0 Differential 90 Ω** | $90\ \Omega$ | **L3** (ref L2/L4) | **0.105 mm** | **0.130 mm** | Inner USB routing |
| **HDMI / DDR Clock 100 Ω** | $100\ \Omega$ | **L1** (ref L2) | **0.102 mm** | **0.330 mm** | HDMI TMDS clock/data, DDR CK/DQS |
| **HDMI / DDR Clock 100 Ω** | $100\ \Omega$ | **L3** (ref L2/L4) | **0.103 mm** | **0.310 mm** | Inner 100 Ω differential stripline |

> [!IMPORTANT]
> To order this 8-layer board at promotional pricing without astronomical costs, keep minimum via hole size at **0.2 mm / 0.25 mm drill** with a **≥0.4 mm pad** (for a safe ≥0.1 mm annular ring). For BGA breakout, vias are placed via dog-bone or filled via-in-pad.

---

## ⚡ Power System (AXP305 + SW6206)

The board features a dual-PMIC architecture:

1. **SW6206 (Battery & Fast Charger):**
   * Acts as a full-featured powerbank controller supporting **PD 3.0, QC 4+, AFC, FCP, and PPS** up to **22.5 W**.
   * Regulates 1S Li-ion charging (set to **4.20 V** via `FLED/BSET` 10k resistor).
   * Features a hardware 5 mΩ Kelvin-sense resistor (`VOUTSP`/`VOUTSN`) and synchronous buck-boost converter.
   * Generates a stable **+5V system rail** (`+5VP_0`) for the board whether running from an external wall charger or boosting from battery.
   * Controlled over **I2C (slave address `0x3C`)** by H616.

2. **AXP305 (System PMIC):**
   * Takes the `+5VP_0` rail and steps it down via multiple high-efficiency DCDC buck converters:
     * **DCDC A/B:** VDD_CPU (dynamically scaled for Cortex-A53 DVFS, ~0.9V–1.1V).
     * **DCDC C:** VDD_GPU.
     * **DCDC D:** VCC_DRAM (**1.50 V** for DDR3).
     * **DCDC E:** VCC_SYS / IO (**3.30 V**).
     * **ALDO1 / BLDO / CLDO:** Low-noise LDO rails for PLLs, eMMC IO (**1.80 V**), and analog audio.

3. **Dual USB-C Topology:**
   * **Port J4 (Power/Charge):** Connected to SW6206. Used to charge the battery or power the board with fast-charging adapters.
   * **Port J3 (USB OTG / Data):** Connected directly to the Allwinner H616 USB OTG controller (`D+`/`D-`) with 5.1k CC pull-downs and Schottky diode power protection to `+5VP_0`. Allows flashing, ADB, Linux gadget networking, or USB host mode while on battery.

---

## 🧠 RAM (DDR3 16-bit)

The H616 supports 16-bit or 32-bit DRAM memory interfaces. According to the JEDEC standard, a single discrete DDR3 chip is 16-bit wide, so a single-chip design uses a 16-bit interface. Compatible 96-ball FBGA chips include:

### SK Hynix
* `H5TQ1G63BFR` (1 Gb / 128 MB)
* `H5TQ2G63BFR` / `H5TQ2G63DFR` (2 Gb / 256 MB)
* `H5TQ4G63AFR` / `H5TQ4G63CFR` / `H5TQ4G63MFR` (4 Gb / 512 MB)
* `H5TC8G63AMR` / `H5TC8G63CMR` (8 Gb / 1 GB — DDR3L)

### Samsung
* `K4B1G1646G` / `K4B1G1646I` (1 Gb / 128 MB)
* `K4B2G1646E` / `K4B2G1646F` (2 Gb / 256 MB)
* `K4B4G1646D` / `K4B4G1646E` (4 Gb / 512 MB)
* `K4B8G1646D` / `K4B8G1646B` (8 Gb / 1 GB)

### Micron
* `MT41J64M16` (1 Gb / 128 MB)
* `MT41J128M16` / `MT41K128M16` (2 Gb / 256 MB)
* `MT41J256M16` / `MT41K256M16` (4 Gb / 512 MB)
* `MT41J512M16` / `MT41K512M16` (8 Gb / 1 GB)

### Nanya
* `NT5CB64M16` (1 Gb / 128 MB)
* `NT5CB128M16` / `NT5CC128M16` (2 Gb / 256 MB)
* `NT5CB256M16` / `NT5CC256M16` (4 Gb / 512 MB)
* `NT5CC512M16` (8 Gb / 1 GB)

---

## 📡 Wireless & Antenna

* **Module:** **Ampak AP6256** (or pin-compatible Realtek RTL8822CS / BCM43456) in standard 12×12 mm LGA-44 package salvaged directly from TV box donor boards.
* **Interfaces:**
  * **Wi-Fi:** High-speed 4-bit SDIO 3.0 (connected to H616 Port G: `PG0`–`PG5`).
  * **Bluetooth:** High-speed UART with RTS/CTS (`PG6`–`PG9`).
  * **Sleep Clock (LPO):** 32.768 kHz RTC clock fed from H616 `PG10` (`X32KFOUT`).
* **Antenna Connection:** Standard **IPEX1 / U.FL** coaxial RF connector (`U.FL_Hirose_U.FL-R-SMT-1_Vertical`) right next to the module output with a 50 Ω coplanar waveguide and $\pi$-matching network. Connects to any standard 2.4/5GHz flexible FPC adhesive antenna.

---

## 🎸 Audio & Expansion Header (I2S & I2C)

The board routes the H616 hardware **Audio HUB I2S3** controller and **I2C0** bus directly to the 40-pin header:

* **Pin 14:** `PH6` — `H_I2S3_BCLK` (Bit Clock)
* **Pin 13:** `PH7` — `H_I2S3_LRCK` (Word Select / Left-Right Clock)
* **Pin 12:** `PH8` — `H_I2S3_DOUT0` (DAC Audio Output)
* **Pin 11:** `PH9` — `H_I2S3_DIN0` (ADC Audio Input)
* **Pin 29 / 30:** `PI5` / `PI6` — `TWI0_SCK` / `TWI0_SDA` (I2C0 bus)

This allows building low-latency DSP daughterboards, such as a **Neural Amp Modeler (NAM) guitar processor**, Hi-Fi FLAC IEM DAC shield (PCM5102 / CS4272), or connecting micro-OLED status displays (SSD1306) via I2C. When audio drivers are disabled in the Device Tree, all pins function as standard 3.3V GPIOs.

---

## 🛠️ Hand Soldering, Flux & Paste

For double-sided manual assembly with a hot plate and hot air rework station:

* **Top Side (BGAs & QFNs):** **MECHANIC V5B45** Sn42/Bi58 low-temperature solder paste (**melting point ~138°C**) or standard Sn63/Pb37 (**183°C**). Low-temperature bismuth paste is ideal for avoiding PCB warping on dense thin 8-layer boards during manual BGA placement.
* **Bottom Side (0402 Passives & Caps):** High/medium-melting solder paste (**~183°C** leaded or **~217°C** lead-free) so bottom components do not drop off when the top side is reflowed on a hot plate.

---

## 📖 Documentation & Soldering Warning

[***Mandatory Reading Before Soldering BGA by Hand***](https://csbible.com/wp-content/uploads/2018/03/CSB_Pew_Bible_2nd_Printing.pdf) 🙏

<p align="right"> 
  <img src="https://gitviews.com/repo/Bogdan8266/h616-board.svg" alt="Views">
</p>
