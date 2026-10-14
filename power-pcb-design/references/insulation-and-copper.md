# Insulation and conductor capability

## Determine insulation requirements

Before declaring spacing adequate, record:
- Applicable product standard, edition, jurisdiction/use context, and any referenced insulation-coordination standard.
- The two conductors/domains and insulation function: functional, basic, supplementary, double, or reinforced as applicable.
- Working voltage with the metric required by the standard; repetitive peak, temporary overvoltage, and impulse stresses as applicable. Low output voltage alone does not define a primary-secondary barrier.
- Overvoltage category where applicable, pollution degree, altitude, PCB material group/CTI, environmental exposure, and intended coating/encapsulation process if credited.
- Requirements for solid insulation, components crossing the boundary, accessible parts, chassis/PE, and mechanical hardware.

Use the applicable authoritative tables and their conditions. Manufacturer summaries help interpret requirements but do not replace the standard. If the needed standard/table or an input is unavailable, identify it and mark the affected criterion unresolved. Continue unrelated work. Never invent table entries from memory or reuse an example project's spacing as a verified requirement.

Derive clearance (through air) and creepage (along insulating surfaces) separately. Even when their geometric lengths coincide on a flat board, their required values can differ. Evaluate solid insulation through layers separately; interlayer dielectric thickness is not surface creepage. High-frequency voltage may require additional provisions under the applicable standard.

Do not assume solder mask provides the insulation credit of a qualified coating. Credit coatings or encapsulation only under an applicable process and standard. IPC conductor-spacing guidance and fabricator minimums do not by themselves establish safety insulation.

## Implement and inspect the geometry

Record the required distance and actual minimum path for each relevant conductor pair. Include copper and exposed pads, vias/barrels, all layers as relevant, slots, board edges, soldered leads, components, heatsinks, screws, and accessible metal. A same-layer copper-distance script is not a complete creepage or assembled-clearance check.

Apply the derived rules to tracks, pads, vias, and filled zones. Check rule precedence and evaluate representative objects to ensure the intended rules apply. Do not create broad courtyard or zone exclusions to silence violations. Each exception needs a specific object pair, technical basis, and evidence; a package with inadequate spacing can require a different part.

Only intentional, appropriately rated components may bridge an isolation boundary. Chassis/PE routes require their own spacing analysis and must not silently bridge domains. Slots increase some surface paths only if their geometry meets the standard's conditions; they do not automatically solve through-air spacing.

## Copper, vias, and fault stress

Record finished copper thickness, layer position, board/nearby copper geometry, ambient and neighboring heat sources, permitted temperature rise, and current waveform. Size each branch for a design current above its worst-case RMS current; state the margin and its basis (model and tolerance uncertainty, overload or current-limit operation, ageing, hot-spot proximity), together with the permitted temperature rise. This applies to every branch, including capacitor and clamp paths. Do not use voltage or current thresholds to omit fast-edge or sensitive circuits from review.

Use IPC-2152 guidance within its documented assumptions, a suitable thermal/electrical model, or measurements. Legacy IPC-2221 empirical equations are preliminary estimates whose assumptions and limitations must be recorded. Fabricator minimum track width is a manufacturing limit, not a current rating.

Specify barrel plating and via geometry; assess current distribution, entry/exit necks, connection inductance, and temperature. Multiple nearby vias may not share current equally. Neither a fixed current-per-via rule nor a mandatory minimum via count establishes adequacy.

For pulses/faults, consider available energy, duration, repetition, initial temperature, and protective-device clearing behavior. An adiabatic fusing estimate is not evidence of acceptable operating temperature or survival under every fault. Check component pads, connectors, shunts, and solder joints as well as tracks.

Map circuit branch currents to physical conductors explicitly. For parallel tracks/pours, document a conservative sharing assumption or solve the distribution. Check narrow pours, thermal spokes, pad entries, and layer transitions on the filled, final board. DC resistance alone may not describe high-frequency sharing and losses.

## Sources to consult

- The project's applicable product standard and edition; IEC 60664-1 and other referenced parts only as applicable.
- Applicable IPC-2152 conductor guidance and the fabricator's actual stack-up/plating capabilities.
- [TI creepage and clearance explanation](https://www.ti.com/video/6146024211001): roles of insulation rating, altitude, pollution degree, and material group.

No normative spacing table is bundled with this skill. Acquire the applicable source before making a normative pass claim.
