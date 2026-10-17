# LTspice simulations of the EMC demo flyback (rev C)

Rev C changes in both schematics: the clamp resistor is R9 ∥ R10 ∥ R20 (3 × 4.7 kΩ = 1.57 kΩ, was 2 × 3.0 kΩ), and the rectifier is D3 ∥ D7 (2 × SS510C, was one).

Rev D (aluminium case as the Q1 heatsink, right-angle sockets) changes only the mechanics, so these files and results still apply. The Q1 tab-to-case capacitance (≈ 30 pF) is not in the schematics; its common-mode effect is estimated in the design report (Section 6.3).

| File | What it is |
|---|---|
| `EMC_flyback_power_stage.asc` | Open-loop power stage with a fixed duty cycle: stresses, waveforms, switching speed vs Rg |
| `EMC_flyback_closed_loop.asc` | The same power stage plus the board's real control loop: regulation, load steps, loop robustness |
| `EMC_flyback_control.lib` | Behavioural models of U1 UC3845B, U3 TL431 and U4 LTV-817S, used by the closed-loop file |
| `UC3845B_beh.asy`, `TL431_beh.asy`, `LTV817_beh.asy` | Block symbols for those three models |

**Keep all files in one folder.** LTspice finds the `.lib` and the `.asy` symbols next to the `.asc`.

---

## 1. Open-loop power stage (`EMC_flyback_power_stage.asc`)

**How to run**
1. Open the file and click **Run**. The simulation covers 0–3 ms and saves data from 2.8 ms.
2. Probe `V(drain)`, `I(Lp)`, `V(gate)`, `V(vout)` and `I(Ls)`.
3. Read the `.meas` results under **View → SPICE Error Log**.
4. To compare the three gate resistors, turn the commented `.step param Rg list 4.7 22 100` line into a directive.

**Knobs**
- `Rg`: gate resistor (jumper RG).
- `SNUB`: drain RC snubber C16/R11 on or off (jumper SNUB).
- `Lpcb`: hot-loop stray inductance.
- `Vin`, `D`, `fs`, `Rload`: input voltage, duty cycle, switching frequency and load.

**Reference results (rev B values).** Conditions: 24 V in, D = 0.245, 12.2 Ω load. Checked in ngspice. The rev C changes shift these by less than about 1 %.

| Rg | Vout | Vds max | Ipri pk | Drain dv/dt | Efficiency |
|---|---|---|---|---|---|
| 4.7 Ω | 11.95 V | 66 V | 4.6 A | 6 ns, ~5.0 V/ns | 86 % |
| 22 Ω | 11.98 V | 66 V | 4.6 A | 12 ns, ~2.5 V/ns | 83 % |
| 100 Ω | 12.21 V | 66 V | 4.9 A | 46 ns, ~0.65 V/ns | 74 % |

An earlier version of this table listed Q1 losses of 2.1 / 2.4 / 3.6 W. Those were measured wrongly (they included the current into the clamp after turn-off). The correct losses are in the closed-loop loss table in section 2.

Waveforms: `sim_waveforms_Rg.png`.

---

## 2. Closed loop (`EMC_flyback_closed_loop.asc`)

### What is in the loop
The schematic uses the board's own component values:

- **U1 UC3845B, peak-current mode.**
  - Oscillator: R2 5.6 k and C10 1 nF give a 307 kHz oscillator, so Q1 switches at 153 kHz (the UC3845 divides by 2, so max. duty is < 50 %).
  - Current sense: R4 0.15 Ω, then the R3 1 k / C13 220 pF filter into ISENSE.
  - Peak-current limit: 1 V / 0.15 Ω = 6.7 A.
  - FB is grounded, so COMP is pulled up internally (0.8 mA). C12 1 nF sits on COMP.
- **U4 LTV-817S optocoupler.** The collector is on COMP and the emitter on PGND, so the opto pulls COMP down.
- **U3 TL431 with its divider.**
  - R15 39 k / R16 10 k set Vout = 2.495 V × 4.9 = **12.23 V**.
  - Compensation is R19 10 k + C22 47 nF from cathode to REF.
  - R17 1 k is the LED series resistor; it is also the "fast lane" path from Vout to the LED. R18 1 k sits across the LED.
- **U2 L78L12** is modelled as an ideal 12 V source (V2).

### How to run
1. Open the file and click **Run** (6 ms).
2. Probe `V(vout)`, `V(comp)`, `V(fbk)`, `I(Lp)`, `V(drain)` and `V(gate)`.
3. Read the `.meas` results in the SPICE Error Log:
   - `Vout_1A`, `Vout_0A5`
   - `Vout_max`, `Vout_min` (overshoot and undershoot)
   - `Vripple`, `Vcomp_1A`, `Vds_max`, `Ipri_pk`
   - `Pin`, `Pout`, `Eff`

### Load profile
- The load is Rload 24.4 Ω (0.5 A) plus `Istep`, a 0.5 A current source.
- That gives 1 A until 2 ms, 0.5 A from 2 to 4 ms, then 1 A again.

### Why it starts from the steady state
The run starts from steady state (`.ic … uic`) because the TL431 integrator (R15·C22 ≈ 1.8 ms) needs about 10 ms to settle from zero. The first ~0.3 ms is the model initialising.

### Knobs (`.param` lines)
| Parameter | Meaning |
|---|---|
| `CTR` | Opto current-transfer ratio. LTV-817S without a rank letter spreads from 0.5 to 6. |
| `FOPTO` | Opto bandwidth (pole), default 20 kHz |
| `RDIV` | Load on COMP from the UC3845's internal R/2R divider. Not given in the datasheet; assumed 30 k. Results barely change at 100 k. |
| `Vin`, `Rg`, `SNUB`, `Lpcb` | As in the open-loop file |

### Suggested experiments
- `.step param CTR list 0.5 1 2`: opto tolerance.
- `.step param Vin list 18 24 30`: line range.
- Change C22, R19 or C12 to see how the compensation changes the response.
- **Cold start:** replace the `.ic` line with `.ic V(vout)=0 V(vbus)=24` and run 20 ms. The UC3845 has no soft-start, so the converter starts at the peak-current limit.

### Reference results
Checked in ngspice with the same netlist and the same `.lib`.

| Case | Vout at 1 A | Vout at 0.5 A | Load-step ΔVout (averaged) | Vds max | Ipri pk | Pin at 1 A | Efficiency |
|---|---|---|---|---|---|---|---|
| Default (24 V, Rg 4.7 Ω, CTR 1), rev C | 12.22 V | 12.24 V | ±22 mV, settled in ~40 µs, no ringing | 66 V | 4.73 A | 14.7 W | 83.5 % |
| Vin = 18 V (rev B) | 12.22 V | 12.24 V | same | – | 4.76 A | 15.2 W | 81 % |
| Vin = 30 V (rev B) | 12.23 V | 12.24 V | same | – | 4.75 A | 14.6 W | 84 % |
| Rg = 100 Ω, rev C | 12.23 V | 12.24 V | same | – | 4.78 A | 15.8 W | 77.3 % |
| CTR = 0.5 / 2 | stable, no ringing | | | | | | |

For CTR = 0.5 and 2, the run starts from the CTR = 1 initial state, so the TL431 integrator takes several ms to trim Vout back to 12.23 V.

**Other numbers.** Switching ripple is about 75 mV peak-to-peak. With ripple included, the output overshoots by +63 mV and undershoots by −35 mV. The plot is in `sim_closed_loop.png`.

**Power balance (rev C, closed loop, 24 V, 1 A, 2 ns time step).**

| Case | Pin | Pout | Q1 | Clamp (R9/R10/R20 + D2) | D3 + D7 | R4 | Efficiency |
|---|---|---|---|---|---|---|---|
| Rg 4.7 Ω | 14.66 W | 12.24 W | 0.73 W | 0.93 W | 0.35 W | 0.29 W | 83.5 % |
| Rg 22 Ω | 14.89 W | 12.24 W | 0.97 W | 0.93 W | 0.35 W | 0.30 W | 82.2 % |
| Rg 100 Ω | 15.84 W | 12.24 W | 1.92 W | 0.90 W | 0.35 W | 0.33 W | 77.3 % |
| Rg 4.7 Ω + snubber | 14.63 W | 12.24 W | 0.67 W | 0.91 W | 0.35 W | 0.29 W | 83.7 % |

D3 and D7 share the current equally in the simulation (identical models): 0.5 A average and 4.1 A peak each.

**Cold start.** This run was done separately, with the 0 V initial condition described above and a 1 A constant-current load. The plot is in `sim_cold_start.png`.

- The converter runs at the current limit for the first ~2.7 ms.
- The peak primary current is **7.5 A**, and **8.5 A** in the first cycles. That is above the nominal 6.7 A because of the R3/C13 filter and the comparator delay.
- The drain peaks at **81 V** while the clamp works harder (66 V in steady state).
- Vout reaches 12 V after 6.8 ms with no overshoot.
- T1 (750311591) saturates at 20 A, so 8.5 A is safe. The 81 V peak still leaves 19 V margin to the IRF520N's 100 V rating.

**What it shows**
- The loop regulates within about 20 mV and is stable over CTR 0.5–2, Vin 18–30 V and all Rg settings. No sub-harmonic oscillation appears; that is expected for DCM.
- **Fast response, slow trim.** R17 (the fast lane) answers a load step within about 40 µs. The small offset that remains is then removed slowly by the TL431 integrator (R15·C22). That is a typical TL431 / opto behaviour worth showing students.
- **Efficiency.** It is about 4 points lower than in the open-loop file. The real UC3845 output drives the gate with only about 10.5 V through about 4 Ω, and the regulated output is 12.2 V instead of 11.95 V.

---

## Limitations (both files)
- **Device models are approximate.**
  - IRF520N: a VDMOS fit to the datasheet.
  - ES2D and SS510C: generic diode models.
  - UC3845B, TL431 and LTV-817: behavioural models built from datasheet typical values. They are not vendor models.
  - Use the results for trends and margins, not for exact loss numbers. LTspice ships an LT1245, a pin-compatible equivalent of the UC3845, if you want to compare against a vendor model.
- **Secondary ground.** SGND is tied to the simulator ground. The coupled inductors still model the isolation, but common-mode and Y-cap paths are not included, and there is no LISN or EMI filter in these files.
- **Not run in LTspice itself.** The schematics were generated and checked in ngspice, because LTspice was not available. If LTspice reports a convergence problem, try `.options gmin=1e-10`, or set Solver to Alternate (Control Panel → SPICE).
