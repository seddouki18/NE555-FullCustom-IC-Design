# Full-Custom NE555 Timer IC Design & Post-Layout Sign-Off (90nm CMOS)

A full-custom integrated circuit design of the classic NE555 Timer, implemented using **Cadence Virtuoso** on a **90nm CMOS process (GPDK090)** operating at $V_{DD} = 1.2\text{ V}$.

The design covers the complete analog flow: transistor-level schematic capture, custom physical layout, physical verification (DRC/LVS), parasitic extraction (Assura RCX), and sign-off post-layout simulation.

---

## Architecture & Sub-blocks
The circuit consists of four custom sub-blocks:
1. **Resistive Voltage Divider:** Precision reference ladder generating the switching thresholds ($V_{TH} = \frac{2}{3}V_{DD} = 0.8\text{ V}$ and $V_{TRIG} = \frac{1}{3}V_{DD} = 0.4\text{ V}$).
2. **Dual Analog Comparators:** Differential pair architectures detecting threshold trigger conditions.
3. **SR Latch:** High-speed bistable multivibrator driving the internal switching state.
4. **Discharge & Output Stage:** Sized NMOS switch handling rapid timing capacitor discharge alongside output buffering.

---

## Design & Verification Flow
* **Schematic Capture:** Virtuoso Schematic Editor
* **Simulation & Analysis:** Spectre Simulation Engine (ADE L)
* **Physical Layout:** Virtuoso Layout Suite (DRC Clean & LVS Clean)
* **Parasitic Extraction (PEX):** Assura RCX producing parasitic-annotated `av_extracted` views
* **Sign-Off Verification:** Hierarchy Editor (`config` view) linking extracted views for pre vs. post-layout transient validation

---

## Simulation Results

### 1. Monostable Multivibrator
Triggered pulse generator producing a calibrated output pulse upon active-low trigger pulse:
- Output switches high immediately when trigger falls below $0.4\text{ V}$ ($\frac{1}{3}V_{DD}$).
- Timing capacitor charges exponentially to $0.8\text{ V}$ ($\frac{2}{3}V_{DD}$) before being discharged back to ground.

### 2. Astable Multivibrator (Free-Running Oscillator)
Continuous square-wave relaxation oscillator operating between hysteresis thresholds ($\frac{1}{3}V_{DD}$ and $\frac{2}{3}V_{DD}$):
- Stable output toggling with negligible post-layout parasitic drift.
- Rail-to-rail square wave output transitions validated under post-layout extraction.

---

## Project Structure
```text
.
├── cds.lib                 # Cadence library definition
├── Timer_555/              # Virtuoso library containing cell views
│   ├── comparator/         # Schematic, Symbol, Layout & Extracted views
│   ├── RS/                 # SR Latch implementation
│   ├── voltage_divider/    # Reference ladder & extracted views
│   ├── Tb_555_astable/     # Astable testbench (schematic & config)
│   └── Tb_555_monostable/  # Monostable testbench (schematic & config)
└── docs/                   # Waveforms and layout screenshots

