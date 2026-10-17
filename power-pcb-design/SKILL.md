---
name: power-pcb-design
description: Review, create, or improve power-electronics PCBs in any CAD tool, including converters, inverters, motor drives, gate drivers, power supplies, and battery power paths. Covers commutation and gate loops, sensing, insulation, EMC, copper capability, thermal, and fabrication. Works in steps, with a human review gate after each step.
---

# Power-electronics PCB design

Develop a power PCB whose electrical, mechanical, and manufacturing decisions trace to requirements and evidence. Applies to any topology, device technology, voltage/current regime, application, stack-up, and CAD or analysis tool. Preserve the user's tools, existing design, and requested deliverables.

Work in steps. After each step, stop at a **human review gate** and continue only after the reviewer approves.

## Select the scope

Agree on the scope in step 1 and run only the steps it needs.

| Scope | Steps and gates |
|---|---|
| **Review** of an existing design, no file changes | 1 → 2 → findings gate (prioritized findings with location, mechanism, correction, missing evidence). Run step 8 checks only where requested. |
| **Small revision** (label, footprint swap, local reroute) | One gate *before* editing: proposed change, affected items, checks to rerun. Then edit, check, and report. |
| **Create or revise a layout** for an approved circuit | 1 → 2 → 4 (footprints/3D as needed) → 5 → 6 → 7 → 8 → 9 |
| **End-to-end design** | 1 → 9 |

Enclosure design, formal reports, and ordering are separate deliverables unless requested.

## Human review gates

1. End every step with a step report (template below) and an explicit request for one of: **approve**, **approve with changes**, **redo** (with reasons), or **stop**.
2. Do not start any work belonging to the next step before approval, including files, simulations, or exports. Read-only preparation for the reviewer's questions is allowed.
3. Treat approved items as the baseline. If a later step requires changing an approved decision, stop, explain the conflict and options, and reopen that gate. Never change an approved decision silently.
4. Apply "approve with changes" fully. Show the reviewer what changed before continuing to the next step.
5. If the session is unattended or the reviewer is unavailable, finish the current step, deliver its report, and wait. Never treat silence as approval.
6. Merge or skip gates only when the reviewer explicitly says so (for example "run steps 6–8 without stopping"). Record which gates were waived, and still stop before step 9 delivery unless that gate was waived too.
7. Keep a gate log (step, date, decision, reviewer changes) with the project records.

### Step report template

```
Step N — <name>: ready for review
Done:          what was produced (files, figures, tables)
Decisions:     key choices and their reasons
Assumptions:   provisional values and their source; what would change them
Results:       criterion — pass / fail / unresolved — evidence type
Open items:    questions for the reviewer, missing inputs
Changed files: list, with revision
Next step:     what step N+1 will do, and its inputs
Please reply: approve / approve with changes / redo / stop
```

Keep the report short. Link figures and tables rather than repeating them.

## Working rules

1. Separate **requirements**, **calculated limits**, and **heuristics**. Give every numerical limit its units, source, assumptions, and operating cases. Example values are not defaults.
2. Derive high-di/dt paths from switching states and high-dv/dt coupling from physical geometry. Do not add capacitors or move ground references to satisfy a generic rule.
3. Preserve driver and current-sense reference potentials. A dedicated Kelvin-source pin, a PCB Kelvin trace, and controller signal ground are different connections.
4. Derive clearance, creepage, and solid insulation from the applicable standard and inputs. Never reduce a required spacing to fit a package.
5. Distinguish CAD check, analytical estimate, simulation, and measurement. None alone establishes product safety or EMC compliance.
6. Missing models, tools, or standard tables remain missing evidence. Do not fabricate outputs or claim a check ran.
7. Map every concept to the user's actual tools (CAD, simulator, solver, fabricator) before using it. Do not assume a specific vendor, version, or script. Never edit the user's design files with an older tool version than the one they use, and never convert them to another version without approval.

## Workflow

### 1. Requirements and inputs
Inspect supplied files and datasheets first. Ask only for information that materially changes decisions; label provisional assumptions.
Record: application class and applicable standards; topology and operating modes; input range and transients; outputs and load range; switching frequency and edge rates; device technology; isolation function and environment (pollution degree, altitude, overvoltage category); ambient and cooling; stack-up, board technology, copper and plating; outline, connectors, and enclosure; fabricator and assembler capabilities; CAD and analysis tools available; deliverables and release stage.
For each tool, record where it runs (user's computer or the agent's workspace) and its exact version. Compare the user's CAD version with the one the agent can run, and with the version stamp in the design files. If the agent's version is older:
- obtain the matching version, or
- work read-only, by parsing the files or using reports the user exports from their tool (DRC, netlist, BOM), or
- have the user make the edits.

State the choice at the gate.
Identify authoritative sources and revisions.
**Gate:** requirement list, applicable standards and their missing inputs, tool inventory (location and version, version-match decision), scope and step plan.

### 2. Architecture and limits
For each switching transition, trace current paths before, during, and after the edge. Identify commutation cells, gate charge/discharge loops, sensing references, and common-mode paths with pins and voltage domains.
Build the design record:
- **Electrical net table** (template in [electrical net table](references/electrical-net-table.md)): for each key node, reference, working and peak voltage, and dv/dt; for each branch and loop, RMS and peak current, di/dt, and fault current; for each sensitive net, its threshold, noise margin, and aggressors. Give every value its source case and evidence type, and the layout constraint it drives.
- **Loop:** transition, ordered path, closing element, return geometry, dominant uncertainty.
- **Criterion:** limit, source, margin, verification method.

Use simulation bounding cases when available: maximum input, start-up, and transients for voltage; minimum input, full load, current limit, and overload for current. Take the worst case of each quantity across all runs, not the nominal run. Do not assign one current to every branch of a net. Where a simulation of the circuit already exists, sweep the parasitics that drive the layout to set their budgets here; in end-to-end work this happens in step 3.
**Gate:** loop map, electrical net table with constraints, acceptance criteria.

### 3. Circuit and models (end-to-end only)
Size the power stage, magnetics, clamps/snubbers, sensing, protection, and control. Select parts with sourcing data. Simulate with the parasitics that matter for the decision, including commutation-loop and gate-loop inductance, common-source inductance, capacitor ESL/ESR, device and magnetic parasitics, and mechanically created capacitances. Avoid double counting what device models already contain. Sweep the parasitics that drive the layout (loop inductances, switch-node capacitance) to set their budgets, and update the electrical net table from the simulation results.
**Gate:** calculations, part list, simulated stresses and losses against criteria, model limitations; updated electrical net table with parasitic budgets.

### 4. Schematic, footprints, and 3D
Create or update the schematic. Give every part its online links in the symbol fields, so they carry into the BOM:
- datasheet URL from the manufacturer, for the exact part number;
- manufacturer product page;
- distributor or assembler page with the order code (for example Digi-Key, Mouser, Farnell, LCSC).

Check that each link opens and matches the part number, package variant, and ratings used in the design. Record the access date. Mark any part without a working link as unresolved instead of guessing a URL.

Audit symbol-to-pad mapping, package variant, polarity, exposed pads, land patterns, and drills against the linked datasheet. Where mechanics constrain the board, and always in end-to-end work, assign a 3D model or dimensioned envelope to every part. Use, in order: manufacturer model, then library model, then datasheet envelope. Verify pin 1, offset, rotation, and height. Use functional reference designators for test points and jumpers where the assembly flow accepts them.
**Gate:** ERC result, component link table (reference, part number, datasheet, product page, order page, link checked), footprint audit, 3D/mechanical envelope check, part heights.

### 5. Design rules and stack-up
Set stack-up and board technology. Assign voltage classes from the working and peak voltages in the electrical net table. Derive per domain pair the clearance, creepage, and solid insulation, citing the standard, table, and inputs. Derive conductor and via capability per branch from the design current (worst-case RMS from the net table plus a stated margin) and the permitted temperature rise. Implement net classes, rule scopes, and keep-outs in the CAD tool. Carry the remaining net-table constraints (copper-area limits, parasitic budgets, keep-outs from sensitive nets) into placement and routing notes. Limit each exception to a named object pair with justification.
**Gate:** rule table with sources, conductor table, list of exceptions, rules implemented in CAD, and traceability from each net-table constraint to its CAD rule or placement/routing note.

### 6. Placement
Fix connectors, holes, keep-outs, isolation boundaries, and thermal interfaces.

Group parts by function and place each group together as one cluster. Typical groups:
- power stage (commutation cell and its closing capacitors);
- gate driver;
- sensing (current and voltage);
- protection;
- controller and feedback;
- auxiliary supply;
- input and output filters;
- isolated interfaces and communication.

Tag each part with its group in the schematic (sheet, group, or field) so the CAD tool can select and move the group as one. Start with a block floorplan: an outline per group, positioned so the connections between groups are short and follow the signal and power flow. Then place parts inside each block.

Placement order:
1. Commutation cells and their closing capacitors.
2. The gate driver block, beside the switch it drives, with its bypass capacitors at its supply pins, to minimize gate-loop impedance.
3. Sensing and protection blocks next to the quantity they measure, keeping their reference connections.
4. Controller, feedback, and auxiliary blocks away from high-dv/dt copper and magnetic fields.

Some parts must sit at their point of use rather than inside their group's cluster. Examples: a gate resistor at the switch, a sense-filter capacitor at the controller pin, decoupling capacitors at IC supply pins, a Kelvin connection at the source pin. Loop and reference requirements take priority over grouping; record each such part as an exception. Keep each group on one side of any isolation boundary unless the part is meant to cross it.

Check component heights and enclosure fit. Check routability before the gate: total ratsnest length, number of ratsnest crossings, and congested areas, especially around the commutation cells and gate loops. Turn or move parts until high-current pads face each other and critical connections need no layer change.
**Gate:** block floorplan with group outlines, placement image, routability metrics (ratsnest length, crossings, congested areas), parts placed outside their group and why, estimated loop areas/inductances, isolation boundary, mechanical fit, open placement trade-offs.

### 7. Routing
Do not draw any copper until the reviewer has approved a routing plan and chosen a routing mode.

**7a. Routing plan and mode choice (gate before routing).** For each critical net (commutation loops, gate loops, Kelvin and sense connections, isolation crossings, high-current paths), list:
- layer(s) and width;
- path sketch and return path;
- via count and positions;
- reference or Kelvin point;
- the rules and net-table constraints that apply (for example a loop-inductance budget or a copper-area limit).

Plan the routing block by block, using the functional groups from step 6:
- **Ground and supply plan** for the whole board: pours, any splits, where returns join, and stitching. Return currents cross block borders, so this comes first.
- **Block entry points:** where each net between blocks enters and leaves each block (for example PWM in, fault out, supply in).
- **Reserved corridors** for nets between blocks and long nets, kept free while blocks are routed.
- **Block order:** dependent blocks after what they depend on. Typically: power stage, gate driver, sensing and protection, controller, communication and isolated interfaces, auxiliary supply, then the nets between blocks.

On a very small board where blocks overlap heavily, say so and propose routing as one block.

Then offer the reviewer these choices, with a short estimate of effort and risk for this board and a proposed routing budget for modes A and C:
- **A. The agent routes everything.** Critical nets by script, the rest by script or autorouter. Fully automated, but slow: every correction needs a rebuild, zone fill, DRC, and render. Suits boards where the reviewer cannot route.
- **B. Reviewer routes interactively; the agent verifies.** The reviewer routes in their CAD tool following the plan. The agent then checks DRC, insulation spacing, copper capability, return paths, and loop parasitics, and reports findings with locations. Usually the fastest for dense or 2-layer power boards.
- **C. Hybrid.** The agent routes the critical nets from the plan and locks them. The autorouter (or the reviewer) routes the remaining nets within a stated time or pass limit. The agent then checks the result.

Wait for the reviewer's choice. Do not pick a mode by default.

**7b. Route block by block, in the chosen mode.**
1. Route the ground and supply copper from the plan.
2. For each block in the planned order:
   - route the nets inside the block up to its entry points;
   - check that area (DRC, clearances, copper capability);
   - lock the block;
   - stop at a **block gate**.
3. After all blocks are approved, route the nets between blocks through the reserved corridors. Then fill pours, add stitching, and run the full check set.

A block gate shows: a close-up image of the block, its check results, deviations from the plan, and any change it forces on blocks still to come. The reviewer replies approve, approve with changes, redo, or stop, as at any gate. In mode B the reviewer routes each block and the agent reports the block gate. The reviewer may waive block gates explicitly; the step 7 gate still applies.

If a later block or connection needs a locked block changed, stop and reopen that block's gate. Never unlock and edit it silently.

In modes A and C, work so that each correction is cheap:
- **Copper areas for power nets.** Implement high-current and switching nets (switch node, input, power ground, output, clamp) as prioritized copper areas built from their pads. The zone filler then applies the clearance rules. Draw tracks only where the shape matters, such as gate pairs, Kelvin and sense lines, and signals.
- **Routes defined from pads.** Define routes by pad, net, layer, and width ("pad to pad, return directly underneath, via array here") rather than by typed coordinates, so routes follow parts when placement changes. Lock critical nets once routed.
- **Incremental loop.** Keep the board open in one CAD or scripting session. After each change, refill only the affected zones and rerun DRC and checks on the changed area. Run the full set before the gate. Avoid rebuilding the whole board for each correction.
- **Batched fixes.** Read all violations at once (prefer machine-readable DRC output), group them by cause, and fix each group in one pass.
- **Limited autorouter.** Give the autorouter only low-current signal nets, with critical nets locked and keep-outs over isolation gaps and commutation loops. Set a pass or time limit. Where useful, run a few variants and keep the best by rule-check result and length.
- **Focused review images.** Render the changed area with DRC markers overlaid. Render full-board images only for the gate.
- **Local working copy.** Route on a working copy. Copy to the user's folder only at the gate, never while their CAD tool has the files open.
- **Routing budget.** State an iteration or time budget in 7a. If it runs out, stop and offer mode B or C for the remaining nets instead of continuing.

In every mode:
- Prefer structural edits tied to nets and pads over coordinate patches.
- Fill zones, then inspect return paths, necks, thermal spokes, layer changes, and filter bypass coupling.
- If routing cannot meet the plan or an approved rule, stop and reopen the plan instead of relaxing the rule.

Tool-specific methods (DRC command line, autorouter settings, reusable layout blocks) are in [CAD and tools](references/cad-and-tools.md).

**Gate:** routing mode used, block gates passed or waived, routed images per layer, DRC result (zero unexplained violations), connectivity, deviations from the plan and why, return-path notes.

### 8. Verification and optimization
Check measured insulation geometry against rules, including pads, vias, edges, slots, hardware, and accessible metal. Check branch copper capability at bottlenecks against the design current. Extract loop parasitics at a fidelity matching the decision, back-annotate, and re-simulate stresses and losses. Update the electrical net table with the re-simulated values, and check every net-table constraint against the routed board and its parasitic budget. Run thermal analysis with the real copper. Compare a small set of variants against the dominant problem within a stated iteration budget. Record every criterion as result plus evidence type.
**Gate:** check record, updated electrical net table with each constraint met or unresolved, variants compared and chosen, remaining unresolved criteria, measurements still required.

### 9. Delivery
Export fabrication and assembly data for the selected vendors from the checked revision. Include BOM (with the component links from step 4), placement, drawings, 3D, and requested reports, with numbers taken from the recorded results. State release stage and unresolved criteria. Do not label the board compliant or validated without matching evidence. Do not order or publish without explicit permission.
**Gate:** final package contents shown before export, commit, or sharing.

For a revision, rerun the checks invalidated by the change (see the change-impact table in implementation-and-delivery) and gate only the affected steps.

## References

Read only what the current step needs:
- [Electrical net table](references/electrical-net-table.md): template for node voltages and dv/dt, branch currents and di/dt, and sensitive nets; extraction from simulation; turning each value into a layout constraint.
- [Topologies](references/topologies.md): commutation cells, hot nodes, sensing references, and layout traps per topology.
- [Devices](references/devices.md): Si, IGBT, SiC, GaN, diodes, modules; gate windows, Kelvin connections, dv/dt immunity.
- [Regimes and standards](references/regimes-and-standards.md): low-voltage/high-current, mains, high-voltage, high-current regimes; application-to-standard map and required inputs.
- [CAD and tools](references/cad-and-tools.md): concept mapping across CAD tools; simulator, solver, and thermal tool selection.
- [Electrical layout](references/electrical-layout.md): switching paths, gate drive, sensing, placement, routing, EMC.
- [Insulation and copper](references/insulation-and-copper.md): insulation requirements, geometry checks, current capability, fault stress.
- [Analysis and measurement](references/analysis-and-measurement.md): model fidelity, parasitics, simulation, thermal, optimization, bench and EMC measurement.
- [Implementation and delivery](references/implementation-and-delivery.md): reproducible changes, check records, reliability, DFM/DFT, fabrication, change impact.
- [Behavioral evaluation](references/behavioral-evaluation.md): test scenarios for maintaining this skill.
