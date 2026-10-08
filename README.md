# Full-Custom Mixed-Signal NE555 Timer IC in 90nm CMOS

![Technology](https://img.shields.io/badge/Technology-GPDK%2090nm%201.2V-007ACC?style=flat-square)
![EDA](https://img.shields.io/badge/EDA-Cadence%20Virtuoso%20%7C%20Spectre%20%7C%20Assura-E05C2B?style=flat-square)
![Physical Verification](https://img.shields.io/badge/Verification-Assura%20DRC%20%2F%20LVS%20Clean-success?style=flat-square)
![Sign-off](https://img.shields.io/badge/Sign--off-Post--Layout%20RCX%20(av__extracted)-purple?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

Full-custom transistor-level design, physical layout, parasitic extraction (PEX), and post-layout sign-off simulation of an NE555 Mixed-Signal Timer IC. Designed and verified in **Cadence Virtuoso** using the **GPDK 90nm CMOS** technology library operating at a nominal supply of $V_{DD} = 1.2\text{ V}$.

<p align="center">
  <img src="docs/final_layout.png" alt="Complete NE555 Full-Custom Layout" width="55%">
  <img src="docs/layout_astable.png" alt="Astable Free-Running Transient" width="41%">
</p>

---

## Key Hardware & Physical Highlights (Sign-off)

* **Closed-Loop Physical Sign-Off:** Full design flow completed from transistor sizing, full-custom physical layout, **Assura DRC Clean** (0 violations), and **Assura LVS Clean** with zero net/device mismatches.
* **Parasitic-Aware Sign-Off:** Extraction completed via **Assura RCX** generating parasitic-annotated `av_extracted` views; simulations validated under **Spectre** via Cadence Hierarchy Editor (`config` view).
* **Calibrated Switching Thresholds:** Integrated high-matching 3-stage precision resistive voltage divider providing switching hysteresis boundaries at $\frac{1}{3}V_{DD} = 0.4\text{ V}$ (Trigger) and $\frac{2}{3}V_{DD} = 0.8\text{ V}$ (Threshold).
* **Dual Operational Modes:** Sign-off transient verification confirms robust rail-to-rail operation in both continuous **Astable** (relaxation oscillator) and edge-triggered **Monostable** pulse generation under post-layout parasitic extraction.

---

## Top-Level Circuit Architecture

The complete NE555 timer architecture integrates a reference ladder, dual differential comparators, an SR flip-flop latch, and an open-drain discharge stage sized for fast capacitor reset.

<p align="center">
  <img src="docs/555Timer.png" alt="NE555 Top-Level Schematic" width="90%">
</p>

---

## Architectural Sub-Blocks (Schematic & Layout)

### 1. Analog Differential Comparator
High-gain differential pair architecture comparing external input pins (`TRIGGER`, `THRESHOLD`) against internal reference divider nodes with fast output transitions.

<p align="center">
  <img src="docs/comparator_schema_final.png" alt="Comparator Schematic" width="48%">
  <img src="docs/comparator_layout_final2.png" alt="Comparator Layout" width="48%">
</p>

### 2. Bistable SR Latch
High-speed cross-coupled bistable multivibrator driving the internal switching state and holding output values during timing phases.

<p align="center">
  <img src="docs/RS_schema_final.png" alt="RS Latch Schematic" width="48%">
  <img src="docs/Capture%20d’écran%202026-10-04%20025228.png" alt="RS Latch Layout" width="48%">
</p>

---

## Physical Verification (Sign-off)

Full physical sign-off achieved via Assura design rule checker and layout-versus-schematic verification decks with zero remaining errors.

| Verification Stage | Tool | Status | Results Summary |
| :--- | :--- | :---: | :--- |
| **Design Rule Check (DRC)** | Assura DRC | **PASS** | 0 DRC Errors across all GPDK 90nm mask layers |
| **Layout vs. Schematic (LVS)** | Assura LVS | **PASS** | Schematic and Layout are fully matched |
| **Parasitic Extraction (PEX)** | Assura RCX | **PASS** | Generated parasitic `av_extracted` view |

<p align="center">
  <img src="docs/DRC.png" alt="Assura DRC 0 Errors" width="48%">
  <img src="docs/lvscomp.png" alt="Assura LVS Match Report" width="48%">
</p>

---

## Post-Layout Simulation Results

Transient simulations performed under **Spectre** using the `av_extracted` view linked via Cadence Hierarchy Editor (`config`).

### 1. Monostable Pulse Generator Mode
Triggered by an active-low pulse below $\frac{1}{3}V_{DD}$ ($0.4\text{ V}$). The output latches HIGH while the external capacitor charges exponentially toward $\frac{2}{3}V_{DD}$ ($0.8\text{ V}$), triggering comparator reset and discharging the timing node back to ground.

<p align="center">
  <img src="docs/monostable_layout_grqphe.png" alt="Monostable Post-Layout Transient Waveform" width="85%">
</p>

### 2. Astable Multivibrator Mode (Free-Running Relaxation Oscillator)
Autonomous square-wave oscillation operating continuously between switching limits $[0.4\text{ V}, 0.8\text{ V}]$. Output exhibits full rail-to-rail swing ($0.0\text{ V} \to 1.2\text{ V}$) with minimal parasitic skew.

<p align="center">
  <img src="docs/layout_astable.png" alt="Astable Post-Layout Transient Waveform" width="85%">
</p>

---

## Performance Summary Table

| Metric / Parameter | Value | Unit | Condition |
| :--- | :---: | :---: | :--- |
| **Technology Node** | 90 | nm | GPDK090 CMOS |
| **Supply Voltage ($V_{DD}$)** | 1.2 | V | Nominal |
| **Lower Trigger Threshold ($V_{TRIG}$)** | 0.40 ($\frac{1}{3}V_{DD}$) | V | Precision Resistor Divider |
| **Upper Threshold ($V_{TH}$)** | 0.80 ($\frac{2}{3}V_{DD}$) | V | Precision Resistor Divider |
| **Output Voltage Swing** | $0.0 - 1.2$ | V | Rail-to-Rail |
| **Physical Sign-Off** | Clean | - | DRC = 0, LVS = Matched, RCX = Done |

---

## Repository Structure

```text
.
├── cds.lib                 # Cadence library definition
├── .gitignore              # Filters locks (*.cdslck), temp logs & simulation runs
├── docs/                   # Waveforms, layout captures, and verification reports
└── Timer_555/              # Cadence Virtuoso Library
    ├── comparator/         # Differential comparator (sch, sym, layout, av_extracted)
    ├── RS/                 # Bistable latch cell
    ├── voltage_divider/    # Matched reference ladder & extracted views
    ├── Tb_555_astable/     # Astable testbench (schematic & config)
    └── Tb_555_monostable/  # Monostable testbench (schematic & config)
