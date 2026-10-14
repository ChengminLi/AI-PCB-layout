# Operating regimes and standards

Use this file in step 1 to find which concerns dominate and which documents to obtain, and in step 5 to know which inputs the rules need. It names documents and inputs; it gives no normative values. **Always confirm the current edition, the regional adoption (EN, UL, CSA, GB, and others), and whether the product falls inside each standard's scope.** Where the user's organization or customer specifies requirements, those come first.

## Operating regimes

**Low voltage, high current (for example core supplies, point-of-load, battery-powered drives)**
- Dominant concerns: conduction loss in copper and vias, power delivery impedance, current sharing, thermal crowding, remote sensing.
- Inputs: load current and its steps, allowed voltage drop and ripple at the load, copper weight and layer count.
- Layout consequences: wide pours on several layers, via arrays at every layer change, differential remote sense, input capacitors directly at power stages.

**Extra-low voltage power (for example 12–60 V DC industrial, telecom, automotive)**
- Dominant concerns: switching loops, EMC, transients on the supply, reverse polarity, load dump (automotive).
- Inputs: supply transient specification, required immunity tests, functional insulation only or also safety insulation.
- Layout consequences: input protection placed at the connector, filter before the converter, short hot loops.

**Mains-connected (AC-DC, PFC, offline flyback, drives on the grid)**
- Dominant concerns: safety insulation (basic, double, reinforced), protective earth, fuse and inrush, surge, X/Y capacitors and leakage current, conducted emissions, harmonics.
- Inputs: product safety standard, overvoltage category, pollution degree, altitude, material group, protection class, earth connection, accessible parts.
- Layout consequences: primary–secondary barrier with required clearance, creepage, and solid insulation; slots where they help; components rated for the barrier; creepage along resistor chains and fuse/varistor pins.

**High-voltage DC link (roughly 400 V to 1500 V: EV, PV, industrial drives)**
- Dominant concerns: insulation for high working voltage, partial discharge, surface contamination, very high dv/dt, stored energy and discharge.
- Inputs: working and transient voltages, partial-discharge requirement, coating or potting, pollution degree, altitude.
- Layout consequences: large spacings between potentials, guard against field concentration at sharp copper corners, slots, coating qualified for the stress, discharge resistors and their creepage.

**High current (tens to hundreds of amperes)**
- Dominant concerns: copper temperature, connector and terminal ratings, mechanical strength, current sharing.
- Inputs: continuous and peak current, allowed temperature rise, cooling.
- Layout consequences: heavy copper, busbars or press-fit terminals, multiple layers in parallel, thermal analysis with real copper.

**High frequency (hundreds of kHz to MHz, GaN, resonant)**
- Dominant concerns: loop inductance, skin and proximity effects, magnetic-component losses, radiated emissions, measurement bandwidth.
- Layout consequences: vertical (stacked) power loops on thin dielectric, minimal gate loops, shielding layers, careful magnetic placement.

**Harsh environments (automotive, outdoor, rail, aerospace)**
- Dominant concerns: temperature range and cycling, vibration and shock, humidity and condensation, contamination.
- Inputs: environmental profile, qualification standard, lifetime.
- Layout consequences: component qualification, coating, mechanical support for heavy parts, solder-joint reliability for large packages.

## Application → standards map

Use this to know which documents to obtain. Several often apply together.

| Application | Safety (examples) | EMC (examples) | Other (examples) |
|---|---|---|---|
| IT, audio/video, consumer electronics, many external power supplies | IEC 62368-1 | CISPR 32 (emissions), CISPR 35 (immunity) | IEC 61000-3-2 (harmonics, mains-connected) |
| Household appliances | IEC 60335-1 and part 2 for the appliance | CISPR 14-1, CISPR 14-2 | IEC 61000-3-2 |
| Laboratory, measurement, control equipment | IEC 61010-1 | IEC 61326-1 | — |
| Industrial equipment (general) | Product-specific; IEC 62477-1 for power electronic converter systems | CISPR 11; IEC 61000-6-2 and -6-4 (industrial generic) | — |
| Variable-speed drives | IEC 61800-5-1 | IEC 61800-3 | — |
| Transformers and power supply units | IEC 61558 series | per end product | — |
| Medical electrical equipment | IEC 60601-1 (basic safety and essential performance; defines MOOP and MOPP) | IEC 60601-1-2 | — |
| Lighting control gear (LED drivers) | IEC 61347 series | CISPR 15 | IEC 61000-3-2 |
| PV inverters and grid-tied converters | IEC 62109-1, IEC 62109-2 | per product and region | Grid connection codes |
| EV charging equipment | IEC 61851 series | per product | — |
| Automotive electronics | ISO 6469-3 for high-voltage electrical safety | CISPR 25 (emissions), ISO 11452 (immunity) | ISO 7637-2 and ISO 16750-2 (supply transients); AEC-Q100/101/200 (component qualification); ISO 26262 if functional safety applies |
| Railway electronics | EN 50124-1 (insulation coordination) | EN 50121-3-2 | EN 50155 |
| Aerospace and defence | Program-specific | RTCA DO-160 (also environment and power input), MIL-STD-461 | MIL-STD-704 (aircraft power quality) |
| Batteries and battery systems | IEC 62619, IEC 62133, UL 1973 as applicable | per product | — |

Cross-cutting documents:
- **Insulation coordination:** IEC 60664-1 (basis for many product standards); IEC 60664-3 (coatings and potting); IEC 60664-4 (high-frequency voltage). UL 840 in North America.
- **Immunity tests:** IEC 61000-4-2 (ESD), -4-4 (fast transients), -4-5 (surge), -4-6 (conducted RF), as called up by the product or generic standard.
- **PCB and assembly:** IPC-2221 (generic design, conductor spacing for function, not safety), IPC-2152 (current capability), IPC-7352 (land patterns; replaced IPC-7351B), IPC-6012 (board qualification), IPC-A-610 and J-STD-001 (assembly).

## Inputs the standards will ask for

Collect these in step 1 so step 5 can derive the rules:
- product category and the standard's scope decision;
- protection class and earthing arrangement;
- working voltages between every pair of domains, including transients and repetitive peaks;
- overvoltage category (mains-connected) and surge levels;
- pollution degree and environment (indoor, outdoor, condensation);
- altitude;
- PCB material group (CTI) and any coating or potting process;
- insulation function required for each barrier (functional, basic, supplementary, double, reinforced; or MOOP/MOPP for medical);
- accessible parts and their required protection;
- leakage or touch-current limits (which constrain Y-capacitors);
- emission and immunity test levels and classes.

If any of these is unknown, the affected rule stays unresolved at the step 5 gate.
