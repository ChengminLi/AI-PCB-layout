# Electrical layout decisions

## Switching paths and topology

Use the actual schematic, device behavior, and operating mode. Draw current arrows for adjacent switching states, then identify which branch currents change rapidly during the transition. Include the physically closing capacitor, winding, semiconductor capacitance, and clamp where applicable. Distinguish the full energy-transfer path from the smaller commutation path.

Topology examples to check, not substitute for tracing the circuit:
- Buck half-bridge: local input HF capacitor, high-side device, low-side device/diode, and return form the principal commutation cell. The inductor current is comparatively continuous during an edge.
- Boost: switch, rectifier/synchronous device, and local output HF capacitor form the principal commutation cell. The input inductor is not interchangeable with the output capacitor.
- Conventional low-side flyback: the on-state primary path includes input capacitor, primary winding, switch, shunt if present, and input return. At turn-off, leakage current transfers into the actual clamp and capacitances. For a conventional RCD clamp returned to the input-positive node, trace the winding/clamp-diode/clamp-capacitor path explicitly. Trace secondary winding, rectifier, output capacitor, and winding return separately.
- Bridges: inspect each leg's local DC-link loop, dead-time conduction and recovery, bootstrap/recharge paths where used, and gate loops at their own moving references.
- Resonant, active-clamp, bidirectional, and wide-bandgap circuits require their own transition analysis; do not force them into the examples above.

Decoupling supplies pulsed current, a snubber damps a selected resonance, and a clamp limits voltage/redirects energy. Do not add a bare capacitor across a switch as a universal remedy. Identify the connection, voltage swing, pulse current, damping, and extra switching/dissipation cost. Leave tuning footprints only when their parasitic effect and space cost are acceptable.

## Gate drive and sensing

Draw gate charging and discharging paths separately, including the driver's source/sink devices, local supply capacitors, resistor(s), package pins, and return. Determine source/sink impedance, permissible positive and negative gate voltage, driver UVLO behavior, and device-specific turn-off requirements.

For a separate driver with a compatible reference, return directly to the dedicated Kelvin-source/emitter terminal when available. Otherwise minimize shared power impedance using the appropriate source terminal. A PCB Kelvin connection does not remove internal shared package impedance.

For a controller with shared signal/driver ground and a low-side current shunt, preserve the datasheet's sensing reference. Do not move controller ground to the source side of the shunt merely to reduce gate-loop inductance. If architecture changes are needed, evaluate a separate driver or different sensing scheme explicitly.

Route both shunt sense connections from the intended terminals without power current in the sense traces. Locate the current-sense filter according to the controller guidance; verify that its delay does not compromise current limiting. Check common-mode/input voltage limits, leading-edge behavior, and noise susceptibility of protection circuits.

Use net ties or separate named return nets only when they express the intended electrical connection and can be verified in CAD. They are not a substitute for deciding where currents return. Prefer short, unique functional reference designators for test points and configuration jumpers (for example DRAIN, GATE, CS, VOUT, PGND1) so the silkscreen explains itself; check that the assembler's BOM/CPL flow and any symbol-identity scheme accept them. Where they do not, keep conventional references (TP1) and add the functional label on silkscreen.

Start driver decoupling from C_eff >= Q_pulse / deltaV, including all relevant gate/driver charge demand over the interval before replenishment. Check effective capacitance under DC bias and temperature, tolerance, ESR/ESL, supply impedance, and driver recommendations. State any extra margin; no universal factor of 20 is required.

Pair gate and return paths tightly. Judge distance, width, vias, and orientation by loop impedance, coupling, assembly constraints, and device recommendations rather than a universal millimetre limit. Negative bias or a Miller clamp must be compatible with the selected device and driver.

## Placement, planes, and routing

Set fixed connectors, mounting holes, isolation keep-outs, cooling interfaces, and stack-up first. Minimize the commutation cell while maintaining insulation and manufacturability. Gate resistors normally belong near the controlled device; verify special split-output/clamp arrangements against the driver circuit.

Keep sensitive feedback, compensation, timing, and protection routes short and away from high-dv/dt copper and strong magnetic fields. Protect their reference continuity. Do not split reference planes indiscriminately: inspect where return current crosses boundaries.

Overlapping forward/return paths can reduce inductance. Copper under switching nodes can also increase displacement current, so evaluate the relevant voltage domain and capacitive coupling. Limit switch-node extent consistent with current capability, thermal needs, and assembly.

After filling zones, check pad entries, thermal spokes, necks, slots, island removal, and layer changes. Select thermal relief versus solid connection using electrical and soldering requirements. Place stitching vias for a defined current-return, thermal, or shielding purpose, not a universal grid.

## EMC source-path-receiver map

For each important switching node, record the receiving conductor/domain and return path: nearby planes, transformer windings, heatsink, enclosure, cables, probes, earth, and intentional capacitors. Use i = C * dv/dt as a first-order displacement-current estimate, with geometry and waveform assumptions.

Separate conducted differential-mode, conducted common-mode, and radiated concerns. Consider filter resonance/damping and interaction with converter input impedance where relevant. Minimize capacitive and inductive coupling from filter output back to input; evaluate copper under and around a common-mode choke using its circuit and manufacturer guidance. Avoid both direct copper bypasses and unintended capacitive bridges. Do not impose a blanket ban on all supply-side ground copper.

Local ringing or low loop inductance is not an emissions test. Record proposed pre-compliance measurements and their actual configuration.

## Technical references

- [TI UC3845 family datasheet](https://www.ti.com/lit/ds/symlink/uc3845.pdf): ground-referenced current sensing, gate drive, compensation, and layout; verify the actual selected controller's datasheet.
- [Analog Devices flyback reference design](https://www.analog.com/en/resources/reference-designs/maxrefdes1176.html): primary current paths and RCD clamp operation.
- [Analog Devices snubber discussion](https://www.analog.com/en/resources/technical-articles/snubber-circuits-suppress-voltage-transient-spikes.html): snubber connections, losses, and parasitic resonance.
- [TI common-mode EMI discussion](https://www.ti.com/document-viewer/lit/html/ssztdb0): parasitic capacitance and switch-node slew rate.
- [Wurth filter-layout guidance](https://www.we-online.com/files/pdf1/emi-debugging---designing-your-ideal-filter-v1.pdf): choke placement and coupling around filters.

These references explain mechanisms; their example values do not define the current project's limits.
