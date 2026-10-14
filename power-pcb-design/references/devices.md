# Devices: what the technology changes in the layout

Use this file in steps 2, 3, 6, and 7 when the semiconductor technology affects gate drive, loop priorities, protection, or measurement. The ranges below only orient the reader. **The selected part's datasheet and application notes govern every limit.**

## Quick comparison

| Technology | Gate drive (typical, check datasheet) | Edge rates | Main layout priorities |
|---|---|---|---|
| Si MOSFET, low/medium voltage | Often 10–12 V on, 0 V off; logic-level parts lower. Gate rating often ±20 V | Moderate to fast | Power-loop inductance; body-diode reverse recovery in bridges; current sharing when paralleled |
| Si superjunction MOSFET | Similar to Si MOSFET | Very fast at turn-off, strongly non-linear C_oss | Gate damping; ringing; dv/dt into control circuits; body-diode recovery in bridges |
| IGBT | Often +15 V on, 0 to −15 V off | Moderate; tail current at turn-off | Gate drive with negative bias in bridges; desaturation protection routing; diode recovery |
| SiC MOSFET | Often +15 to +20 V on, 0 to −5 V off, depending on generation; negative limit is often tight | Fast, high dv/dt | Kelvin source; tight gate loop; gate voltage margins on both sides; Miller clamp or negative bias; driver dv/dt immunity |
| GaN HEMT, e-mode | Often about 5–6 V on, with an absolute maximum only slightly higher; some need negative off-bias. Gate-injection types need a steady gate current (RC drive network): follow the vendor drive circuit | Very fast, highest dv/dt and di/dt | Minimal power and gate loops; Kelvin source; overshoot on the gate; no avalanche capability; very low package inductance |
| GaN cascode / d-mode with driver | Driven like a Si MOSFET or by an integrated driver | Very fast | Follow the vendor layout closely; integrated parts move the gate loop inside the package |

## Diodes and reverse conduction

- **Si PN fast-recovery diodes:** reverse-recovery charge adds turn-on loss and ringing. Choose and place snubbers accordingly.
- **Schottky diodes (Si and SiC):** little reverse recovery, but junction capacitance still rings with loop inductance. SiC Schottky diodes suit high-voltage boost and PFC stages.
- **MOSFET body diodes in bridges:** check Q_rr at the operating temperature. SiC body diodes have a high forward voltage, so keep dead time short.
- **E-mode GaN reverse conduction:** there is no body diode. Reverse-conduction voltage depends on the off-state gate voltage and can be several volts, so dead time strongly affects loss.
- **Cascode GaN reverse conduction:** current flows through the low-voltage Si MOSFET's body diode, with small reverse-recovery charge.

## Cross-cutting topics

**Kelvin source and common-source inductance**
- Use a Kelvin-source pin (4-pin TO-247, D2PAK-7, TOLL or TOLT variants with a driver-source pin, LGA, and similar) whenever the driver reference allows it.
- A PCB Kelvin trace from a 3-pin package reduces only the external shared path. The bond wire and lead remain shared.
- With a ground-referenced controller and a source shunt, the sense reference wins. See electrical-layout.

**Gate voltage limits on both sides**
- Check positive overshoot and negative undershoot at the device pins, not at the driver.
- SiC and GaN often have small margins between the recommended and the absolute gate voltages.
- Split turn-on and turn-off resistors where the turn-off speed must differ from turn-on.

**Unwanted turn-on**
- Drain dv/dt through C_gd and source di/dt through common-source inductance can lift the gate.
- Remedies, depending on device and driver: lower turn-off impedance, Miller clamp, negative bias, Kelvin source, slower edges.
- Check the selected device's threshold at the hot end of its range.

**Driver and isolator dv/dt immunity**
- Compare the worst switch-node dv/dt with the driver or isolator's common-mode transient immunity (CMTI), with margin.
- Count the capacitance across the barrier of isolated supplies and isolators: it injects common-mode current at every edge.

**Bootstrap supplies**
- Check initial charge, refresh at minimum duty, diode recovery, and negative switch-node undershoot below the driver ground.
- Fast or high-voltage stages may need an isolated supply instead.

**Avalanche and overvoltage**
- Many Si MOSFETs have an avalanche rating; GaN HEMTs do not. Keep the overvoltage margin for GaN without counting on avalanche.
- For SiC, check the device's single-pulse and repetitive avalanche data before relying on it.

**Paralleling**
- Use a symmetric layout, an individual gate resistor per device, and equal source paths.
- Positive temperature coefficient of on-resistance helps static sharing but not dynamic sharing.

**Packages and parasitics**
- Through-hole packages (TO-220, TO-247) add lead inductance and usually need a heatsink with an insulator.
- Surface-mount power packages (D2PAK, TOLL, LFPAK, QFN, LGA) lower inductance; their thermal path goes through PCB copper and vias.
- Top-side cooled packages move the thermal path to a heatsink above the board and change the mechanical design.
- An insulated tab on a grounded heatsink is a capacitor from the switch node to the heatsink. Include it in the common-mode map and in the loss model.

**Power modules and integrated power stages**
- Modules and smart power stages fix the inner loops. The board must supply low-inductance DC-link connections, the recommended decoupling, and correct gate and sense references.
- Follow the vendor reference layout; deviations need a reason.

## Protection and measurement by technology

- **Overcurrent:** shunt plus comparator; desaturation detection for IGBT and SiC, with its sense line routed away from switch nodes; current transformer or Hall sensor for floating legs.
- **Gate measurement:** measure gate–source at the device pins, with a low-inductance probe connection. For floating or high-side devices, use an isolated or differential probe rated for the dv/dt.
- **Edge rates beyond the probe:** fast GaN and SiC edges can exceed the bandwidth of common probes. Choose probes and current sensors for the actual rise times, and report measurement limits.
