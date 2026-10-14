# Topologies: what to trace and what goes wrong

Use this file in steps 2, 6, and 7 to find the commutation cells, the high-dv/dt nodes, the sensing references, and the usual layout mistakes for a topology. It is a checklist, not a substitute for tracing the actual circuit. Variants such as synchronous rectification, interleaving, coupled inductors, and soft switching change the details. Always draw the current paths for each switching transition of the real schematic.

## Method for any topology

1. List the switching states and draw the current in each state.
2. For each transition, mark the branches whose current changes quickly. Together with the capacitor that supplies or absorbs that current, they form the **commutation cell** (hot loop). It is usually smaller than the energy-transfer path.
3. Mark the nodes whose voltage swings quickly (**high-dv/dt nodes**). Their copper area and the capacitance to other domains, heatsinks, cables, and earth set the common-mode current.
4. Mark each **sensing reference**: where a current-sense amplifier, comparator, or controller measures, and which ground it uses.
5. Mark each **floating reference**: high-side drivers, bootstrap supplies, isolated domains.
6. Record the result in the design record (SKILL.md step 2).

Parallel devices need a symmetric layout, an individual gate resistor per device, and equal common-source paths. Otherwise current and switching losses are shared unequally.

## Non-isolated DC-DC

**Buck (synchronous or diode)**
- Commutation cell: local input HF capacitor → high-side switch → low-side switch or diode → back to the capacitor.
- High-dv/dt node: switch node (between the switches, and the inductor pin).
- Sensing: inductor DCR, low-side shunt, or integrated sensing; output voltage sensed at the load.
- Traps:
  - input HF capacitor placed far from the switches, or behind vias;
  - large switch-node copper;
  - feedback divider or compensation routed under the inductor or beside the switch node;
  - low-side gate return shared with the power return.

**Boost (synchronous or diode)**
- Commutation cell: low-side switch → rectifier (diode or synchronous switch) → local **output** HF capacitor → back.
- High-dv/dt node: switch node at the inductor, switch, and rectifier junction.
- Sensing: low-side shunt in the switch source or return; output voltage.
- Trap: decoupling the input but not the output. In a boost, the output capacitor closes the hot loop.

**Inverting buck-boost**
- Commutation cell: input HF capacitor → switch → diode → output HF capacitor. Both input and output currents pulse.
- Trap: output referenced negative to ground; check the sense and feedback references.

**Four-switch buck-boost**
- Two commutation cells: the buck leg with the input capacitor, and the boost leg with the output capacitor. Keep both small.
- Sensing: often one shunt shared by both legs; route its Kelvin connections carefully.

**SEPIC and Ćuk**
- Commutation cell includes the coupling capacitor. SEPIC: switch → coupling capacitor → diode → output capacitor. Ćuk: switch → coupling capacitor → diode.
- Trap: a coupling capacitor with too much ESL, or placed away from the switch and diode.

**Multiphase (VRM, point-of-load)**
- Commutation cell per phase: input capacitors directly at each power stage's VIN and PGND pins, on the same layer.
- High-dv/dt nodes: one switch node per phase.
- Sensing: per-phase current (DCR, integrated monitor, or shunt), and differential remote sense of the output at the load.
- Traps:
  - via arrays too small for the output current;
  - remote-sense pair routed near switch nodes or not as a pair;
  - unequal phase layouts that unbalance current;
  - thermal crowding.

## Isolated DC-DC

**Flyback (RCD clamp, active clamp, quasi-resonant)**
- Primary on-state loop: input capacitor → primary winding → switch → shunt (if present) → input return.
- Turn-off: the leakage inductance keeps the winding current flowing, so the current moves from the switch to the clamp. For an RCD clamp to the input positive, the fast-changing loop is: switch drain node → clamp diode → clamp capacitor → input capacitor → switch source (through the shunt). Keep it small.
- Secondary loop: secondary winding → rectifier → output capacitor → winding return.
- High-dv/dt nodes: primary drain, secondary rectifier node, and an active-clamp switch node if present.
- Sensing: ground-referenced source shunt for peak-current control. Output feedback crosses the barrier, usually through an optocoupler or primary-side regulation.
- Traps:
  - large clamp loop;
  - drain copper coupling to a grounded heatsink;
  - Y-capacitor returns that bypass the intended path;
  - transformer interwinding capacitance as the main common-mode path.
- Quasi-resonant: the drain ringing is intentional. Valley detection and the auxiliary winding sense are noise-sensitive.

**Forward (single switch with reset, two-switch, active clamp)**
- Primary loop: input capacitor → winding → switch(es). Two-switch forward: the clamp diodes return energy to the input capacitor, so they belong in the primary hot loop.
- Secondary commutation: forward rectifier → freewheel rectifier → secondary winding. The output inductor current is continuous.
- Trap: reset winding or clamp loop placed far away.

**Push-pull**
- Two low-side switches with a centre-tapped primary. Loops: input capacitor → centre tap → half winding → switch → return, one per switch.
- Each drain sees about twice the input voltage plus leakage spikes.
- Trap: asymmetric loops or timing that unbalance the flux (flux walking).

**Half-bridge and full-bridge (hard-switched or phase-shifted)**
- Commutation cell per leg: local DC-link HF capacitor → high-side switch → low-side switch. Give every leg its own capacitor.
- High-dv/dt nodes: each leg's switch node. The transformer or load connects between them.
- Floating references: high-side gate drivers (bootstrap or isolated supply).
- Traps:
  - one shared DC-link capacitor far from one leg;
  - long bootstrap loop;
  - insufficient dv/dt immunity in high-side drivers;
  - switch nodes coupling into control traces;
  - phase-shifted bridges losing ZVS at light load and then hard switching.

**LLC and other resonant converters**
- Primary: a half- or full-bridge leg plus the resonant tank. ZVS lowers edge stress, but start-up, light load, and faults can still hard-switch.
- Secondary: synchronous rectifiers with very high di/dt. Drain-source sensing of the rectifier needs Kelvin connections at the device pins.
- Traps:
  - resonant capacitor not rated for the AC current;
  - rectifier sensing picking up loop inductance;
  - tank layout that adds stray inductance and shifts the resonance.

**Dual active bridge (DAB) and bidirectional isolated stages**
- Two bridges, each with its own DC-link HF capacitors and isolated gate drives. Isolation transformer plus series inductance.
- Traps:
  - ground pours on both sides coupling capacitively across the barrier;
  - transformer interwinding capacitance carrying common-mode current;
  - soft-switching range lost at some operating points.

## AC-DC front ends

**Mains input and rectifier**
- Fuse, inrush limiting, X and Y capacitors, common-mode choke, rectifier, bulk capacitor.
- Traps:
  - copper or parts that bypass the common-mode choke, capacitively or directly;
  - Y-capacitor connection points;
  - creepage around fuse, varistor, and bridge pins.

**CCM boost PFC (single or interleaved)**
- Commutation cell: switch → boost diode (often SiC Schottky) → local HF capacitor across the bulk capacitor. The electrolytic bulk capacitor alone is too far and too inductive.
- Sensing: shunt in the return path, often negative with respect to controller ground. Line and bulk voltage are sensed through high-value resistor chains, which need creepage along their length.
- Trap: shunt sense lines not Kelvin-connected, or crossing switch-node copper.

**Bridgeless and totem-pole PFC**
- Fast leg (SiC or GaN) switching at high frequency with its own local HF capacitor. Slow leg (MOSFETs or diodes) switching at line frequency.
- The fast switch node is referenced to the line, not to a quiet ground. Current sensing usually needs an isolated method (current transformer, Hall, isolated amplifier).
- Traps:
  - large fast switch-node copper;
  - the slow-leg node swinging at line frequency relative to control ground;
  - gate drives without enough dv/dt immunity.

## Motor drives and inverters

**Three-phase inverter (and H-bridge)**
- Commutation cell per leg: local DC-link HF capacitor → high-side → low-side switch. Bulk capacitors are shared.
- Sensing: one shunt per leg, a single DC-link shunt, or inline phase sensing. The amplifier reference and Kelvin connections to each shunt are critical.
- Floating references: bootstrap or isolated high-side drives; desaturation detection routing for IGBT or SiC.
- Traps:
  - legs laid out unequally;
  - long gate loops to a central driver;
  - shunt sense lines shared with power current;
  - motor-terminal dv/dt driving common-mode current through cable and motor-frame capacitance;
  - heatsink potential undefined.

**Grid-tied and PV inverters**
- Like the three-phase or H-bridge case, plus output filter (L, LCL), relays, and grid-voltage sensing across isolation.
- Traps: filter-capacitor return paths; relay and sensing creepage; leakage current requirements.

## Linear and protection paths

**Linear regulators, LDOs, load switches, hot-swap**
- No switching edges, but safe operating area (SOA) and heat dominate.
- Hot-swap: inrush current through the MOSFET in its linear region; check SOA, not just current rating.
- Sensing: Kelvin shunt for current limit; output sense at the load.
- Traps: copper chosen for heat that destabilizes the regulator loop; sense traces sharing load current.

**Battery and BMS power paths**
- Back-to-back charge and discharge MOSFETs, shunt, fuse, pre-charge, cable connectors. Very high fault current is available from the battery.
- Sensing: Kelvin shunt; cell-voltage sense lines; isolated communication between stacked monitors.
- Traps:
  - unequal current sharing between paralleled MOSFETs;
  - cell-sense traces beside high-current copper;
  - no TVS or snubbing for cable inductance at turn-off;
  - creepage along high-voltage stacks.

## Auxiliary and gate-drive supplies

**Isolated auxiliary and gate-drive supplies**
- Small isolated DC-DC converters feeding floating drivers. Their interwinding capacitance carries common-mode current at every switch-node edge.
- Choose low-capacitance parts for high-dv/dt nodes. Place them close to the driver they feed. Keep their primary and secondary pours apart.

**Bootstrap supplies**
- The loop includes the bootstrap diode, bootstrap capacitor, and low-side switch. Charge it fast enough at start-up and at minimum duty.
- Watch negative switch-node undershoot below the driver's ground, and the diode's reverse recovery.
