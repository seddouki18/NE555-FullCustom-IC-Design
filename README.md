# Full-Custom Mixed-Signal NE555 Timer IC in 90nm CMOS

![Technology](https://img.shields.io/badge/Technology-GPDK%2090nm%201.2V-007ACC?style=flat-square)
![EDA](https://img.shields.io/badge/EDA-Cadence%20Virtuoso%20%7C%20Spectre%20%7C%20Assura-E05C2B?style=flat-square)
![Physical Verification](https://img.shields.io/badge/Verification-Assura%20DRC%20%2F%20LVS%20Clean-success?style=flat-square)
![Sign-off](https://img.shields.io/badge/Sign--off-Post--Layout%20RCX%20(av__extracted)-purple?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

Full-custom transistor-level design, physical layout, parasitic extraction (PEX), and post-layout sign-off simulation of an NE555 Mixed-Signal Timer IC. Designed and verified in **Cadence Virtuoso** using the **GPDK 90nm CMOS** technology library operating at a nominal supply of $V_{DD} = 1.2\text{ V}$.

<p align="center">
  <img src="docs/layout_555.png" alt="Full-Custom Physical Layout" width="48%">
  <img src="docs/astable_sim.png" alt="Post-Layout Astable Simulation" width="48%">
</p>

---

## Key Hardware & Physical Highlights (Sign-off)

* **Closed-Loop Physical Sign-Off:** Full design flow completed from transistor sizing, full-custom layout, **Assura DRC Clean**, and **Assura LVS Clean** with zero layout-versus-schematic mismatches.
* **Parasitic-Aware Validation:** Parasitic extraction completed via **Assura RCX** producing parasitic-annotated `av_extracted` views; simulations performed under **Spectre** via Cadence Hierarchy Editor (`config` view).
* **Calibrated Switching Thresholds:** Integrated high-matching 3-stage precision resistive voltage divider providing switching hysteresis boundaries at $\frac{1}{3}V_{DD} = 0.4\text{ V}$ (Trigger) and $\frac{2}{3}V_{DD} = 0.8\text{ V}$ (Threshold).
* **Dual Operational Modes:** Sign-off transient verification confirms robust rail-to-rail operation in both continuous **Astable** (relaxation oscillator) and edge-triggered **Monostable** modes under post-layout conditions.

---

## Architectural Sub-Blocks

<p align="center">
  <img src="docs/schematic_top.png" alt="Transistor-Level Top Schematic" width="85%">
</p>

The core timer architecture consists of four full-custom functional blocks sized for low-voltage operation ($1.2\text{ V}$):

1. **Precision Voltage Divider (`voltage_divider`):**
   * Three matched integrated resistors generating stable internal reference voltages ($0.4\text{ V}$ and $0.8\text{ V}$).
   * Symmetric physical layout floorplanned to minimize mismatch and process gradient effects.
2. **Dual Analog Differential Comparators (`comparator`):**
   * High-gain differential pairs comparing external inputs against internal divider reference nodes.
   * Tail current sources sized for fast slew rate and switching transitions.
3. **Bistable SR Latch (`RS`):**
   * High-speed CMOS cross-coupled NAND/NOR bistable latch storing output trigger states.
   * Sized with minimum internal delay to avoid metastability during threshold crossing.
4. **Discharge & Output Buffer Stage:**
   * Sized NMOS open-drain discharge transistor providing rapid discharge of external timing capacitors to ground.
   * High-drive output inverter buffer isolating internal latch states from external load impedances.

---

## Physical Verification & Extraction Flow

```text
[ Schematic Capture ] ──> [ Virtuoso Layout Suite ] ──> [ Assura DRC ] (Clean)
                                                               │
[ Post-Layout Sim ] <── [ Hierarchy Editor (HED) ] <── [ Assura LVS & RCX ]
  (Spectre ADE L)           (av_extracted view)           (Parasitic Netlist)
.
├── cds.lib                 # Cadence library path definition
├── .gitignore              # Ignores locks (*.cdslck), simulation runs & temp logs
├── docs/                   # Waveforms, verification logs & layout screenshots
│   ├── layout_555.png
│   ├── astable_sim.png
│   ├── monostable_sim.png
│   └── schematic_top.png
└── Timer_555/              # Cadence Virtuoso Library
    ├── comparator/         # Differential comparator (sch, sym, layout, av_extracted)
    ├── RS/                 # Bistable latch cell
    ├── voltage_divider/    # Matched reference ladder & extracted views
    ├── Tb_555_astable/     # Astable sign-off testbench (schematic & config)
    └── Tb_555_monostable/  # Monostable testbench (schematic & config)
