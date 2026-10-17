# Electrical net table

Use this file in step 2 to build the table, in step 3 to update it from simulation, in steps 5 and 7 to turn it into rules and routing constraints, and in step 8 to check it against the built board.

The table records the worst-case electrical stress of each key node and current path, and the layout constraint each value drives. Every constraint must end up as a CAD rule, a placement note, or a routing-plan item, and must have a verification method.

## Sources of values

Give every value its source case and evidence type: simulation, calculation, datasheet, or measurement.

- **With simulation:** run the bounding cases (maximum input and transients, start-up and shutdown, current limit, load steps, faults the models can represent; minimum input, full load, and overload for current). Take the worst case of each quantity across all runs and record which run produced it. The nominal run alone is not enough: start-up or a fault often sets the peak voltage.
- **Without simulation:** use calculations, datasheets, and available waveforms. For a review, do not start a simulation project only to fill the table.
- **Missing values:** mark the value and the constraints that depend on it unresolved, and continue with unrelated work.

## The three parts

Split the table by what each quantity belongs to. Peak voltage and dv/dt are properties of a node; RMS current, peak current, and di/dt belong to a current path. One PCB net can carry very different currents in different places (for example a power ground near the shunt and near the controller), so give each branch its own row.

### Nodes

| Column | Content |
|---|---|
| Node | Schematic net and the PCB net(s) it maps to |
| Reference | The node the voltage is measured against (for example PGND, SGND, chassis) |
| Working voltage | The value the applicable standard requires (RMS, DC, or repetitive peak), with its case |
| Peak voltage | Highest value in any run, including start-up, faults, and ringing, with its case |
| dv/dt | Maximum during switching, with its case and how it was measured |
| Ringing | Frequency and amplitude, where relevant |
| Constraints | Voltage class and the spacing rules it feeds; copper-area limit; spacing to heatsinks, enclosures, and planes; keep-out from sensitive nets |
| Verification | How step 8 checks each constraint |

### Branches and loops

| Column | Content |
|---|---|
| Branch or loop | Ordered path (parts and pins) and the PCB segments it flows through |
| RMS current | Worst case over a whole number of switching periods, with its case |
| Peak current | Highest value in any run, including start-up and current limit, with its case |
| di/dt | Maximum during the commutation, with the transition |
| Fault | Fault current, duration, and the protective device that ends it |
| Parasitic budget | Maximum loop inductance (or other parasitic) from a sweep, and the estimated or extracted value |
| Constraints | Design current for conductor and via sizing; loop placement, layer, and return path; Kelvin connections |
| Verification | How step 8 checks each constraint |

### Sensitive nets

| Column | Content |
|---|---|
| Net | Schematic and PCB net, for example current sense, feedback, compensation, gate, timing |
| Function and threshold | What the net does and the signal level that matters (for example a comparator threshold) |
| Noise margin | How much coupled noise the net tolerates, and its source |
| Aggressors | The high-dv/dt nodes and high-di/dt loops near it |
| Constraints | Filter placement at the receiving pin, distance from aggressors, shielding, reference connection |
| Verification | How step 8 checks each constraint |

## Extracting values from simulation

- **RMS current:** over a whole number of switching periods in steady state, for each operating point.
- **Peak voltage and current:** over the whole run, including start-up, current limit, and transients.
- **dv/dt and di/dt:** state the method, either the maximum of the derivative during the edge or the 10–90 % change divided by its time. Check sensitivity to the time step by halving it; a coarse step underestimates edge rates and ringing peaks.
- **Measurement statements:** use the simulator's measurement commands so the table can be regenerated after every change. RMS and maximum work in both LTspice and ngspice, for example:

  ```
  .meas TRAN Vdrain_pk MAX V(drain)
  .meas TRAN Iprim_rms RMS I(R4) FROM=2.8m TO=3m
  ```

  For derivatives, use the derivative function of the installed simulator (check its manual) or post-process the exported waveform. The times and net names above are placeholders.
- **Branch currents:** use native device currents where available, and check their polarity. Map each simulated branch to the PCB copper it flows through.

Peak voltages and ringing depend on the parasitics assumed in the model. Before layout, simulate with a range of plausible parasitic values and record the range in the table. After layout, replace the range with extracted values (step 8) and update the table.

## From value to constraint

| Quantity | Constraint it drives | Where the method is |
|---|---|---|
| Working and peak voltage | Voltage class of the net, then clearance, creepage, and solid insulation from the applicable standard | [Insulation and copper](insulation-and-copper.md) |
| dv/dt | Maximum copper area of the node; spacing to heatsinks, enclosures, and planes (common-mode current is about C·dv/dt); keep-out from sensitive nets; shielding layers | [Electrical layout](electrical-layout.md) |
| RMS current | Design current (worst-case RMS plus a stated margin) for track width, copper area, and via count | [Insulation and copper](insulation-and-copper.md) |
| Peak and fault current | Pad and via necks, shunt and sense connections, fault stress of conductors and joints | [Insulation and copper](insulation-and-copper.md) |
| di/dt | Loop-inductance budget, then which parts sit side by side, layer assignment, return path, and Kelvin connections | [Electrical layout](electrical-layout.md) |
| Noise margin | Routing distance, filter placement, and reference connection of the sensitive net | [Electrical layout](electrical-layout.md) |

**Parasitic budgets.** To set a loop-inductance budget, sweep the parasitic in the simulation, find where a criterion fails (device voltage margin, ringing amplitude, gate overshoot, unwanted turn-on), and set the budget below that value with a stated margin and its basis. Use the same method for switch-node capacitance against common-mode current or loss. A budget is only as good as the model: record its limitations.

## Rules for the table

1. Every value has a source case and evidence type. Example values are not defaults.
2. Use the worst case across all runs, not the nominal run.
3. One row per branch, not one current per net.
4. Every constraint has an implementation (net class, rule, placement note, or routing-plan item) and a verification method. The step 5 gate shows this traceability.
5. If a value is missing, its constraints stay unresolved.
6. Update the table whenever a design change invalidates its values, and after parasitic extraction in step 8.

## Worked example (illustrative)

From a 24 V to 12 V / 1 A isolated flyback (DCM, 153.6 kHz, 100 V MOSFET). The values belong to that design only.

| Item | Peak V | dv/dt | I RMS | I peak | Constraint and implementation |
|---|---|---|---|---|---|
| Drain node, ref. PGND | 66 V steady state; 81 V at cold start (simulation) | 5.0 V/ns at Rg 4.7 Ω | — | — | Voltage class up to 100 V (MOSFET rating as ceiling), spacing from the standard; smallest practical copper area; away from current sense and feedback |
| Primary power loop | — | — | 1.43 A | 4.73 A; 8.5 A at cold start | Design current for the loop copper; hand-routed and locked |
| Clamp commutation loop | — | — | — | about 4.7 A commutated in about 5 ns | Smallest loop on the board; each 1 nH adds about 1 V of turn-off spike |
| Secondary rectifier loop | 38 V reverse on the diodes (simulation) | not extracted | 2.12 A | 8.3 A (4.1 A per diode) | Diodes and ceramic capacitors directly at the transformer pins; design current per diode branch |
| Current-sense input (sensitive) | 1 V limit threshold | — | — | — | RC filter at the controller pin; sense trace taken from the shunt pad, away from the drain node |
