# CAD and analysis tools

Use this file in step 1 (tool inventory and version match), steps 4–7 (implementing groups, rules, and routing), and step 8 (choosing analysis tools). Commands and features change between releases. **Check the installed version's help (`--help`, the manual for that version) before relying on any command or option listed here.**

## Where tools run and version match

- Record for each tool: name, exact version, and where it runs (user's computer or the agent's workspace).
- Design files carry a version stamp (in KiCad, the `(version …)` date in the file header). An older tool generally cannot open files saved by a newer one, and opening an older file in a newer tool converts it when saved.
- If the agent's tool is older than the user's: obtain the matching version; or work read-only (parse the text files, or read reports the user exports from their tool); or have the user make the edits. Never write the user's files with the older version.
- If the workspace cannot download the needed version because of network restrictions, say so, and name which sites an administrator would need to allow.

## Concept mapping

| Concept | KiCad 9 | Altium Designer | Other tools |
|---|---|---|---|
| Group parts by function (step 6) | Hierarchical sheets; **component classes** assigned in the schematic; groups in the PCB editor. `A.hasComponentClass('…')` matches footprints only; for copper items use a courtyard condition such as `A.intersectsCourtyard('REF')` | Rooms (from sheets or component classes); component classes | Allegro: rooms and groups. Xpedition/PADS: clusters, groups. Fusion: groups |
| Net classes and rules | Net classes (patterns allowed) in board setup; custom rules file `.kicad_dru` with conditions such as `A.hasNetclass('…')` | Net classes; design rules with scope queries (for example `InNetClass('…')`) | Allegro/OrCAD: Constraint Manager. Xpedition/PADS: constraint editor. Fusion: net classes and design rules |
| Creepage check | `(constraint creepage (min …))` rule between net classes, new in 9.0 (see notes) | Creepage distance rule | Check the tool's documentation; otherwise verify with a separate geometry check |
| Copper areas for power nets | Zones with priorities; zone manager (9.0) | Polygon pours with pour order | Shapes or planes with priority |
| Intentional net joins (Kelvin, star points) | Net-tie footprints | Net-tie components | Net-tie or star-point features |
| Repeat identical channels (phases, legs) | Multi-channel tool (repeat layout between channels) | Multi-channel design with room format copy | Reuse or replicate features |
| Reusable blocks | Design blocks are **schematic-only** in 9.0; for layout reuse, use the multi-channel tool or copy from a reference board | Snippets; reuse blocks; device sheets | Reuse modules or blocks |
| Rule check from the command line | `kicad-cli pcb drc`, `kicad-cli sch erc` | Not usually from the command line; run DRC in the application and export the report | Tool-specific batch modes |
| Fabrication outputs | `kicad-cli pcb export gerbers / drill / pos / step / ipc2581 / odb`; jobsets (9.0) | Output job files | Tool-specific |
| Scripting | Python: SWIG `pcbnew` module (still present, being replaced); IPC API (9.0, needs a running KiCad) | DelphiScript and other scripting | Allegro: SKILL. Fusion: ULP |

## KiCad 9 notes

**Command-line checks**
```
kicad-cli pcb drc --format json --severity-all --schematic-parity --exit-code-violations -o drc.json board.kicad_pcb
kicad-cli sch erc -o erc.rpt project.kicad_sch
```
- `--exit-code-violations` returns 5 when violations exist. JSON output lets the agent group violations by type and location.
- Check `kicad-cli sch erc --help` for its options in the installed version.

**Creepage rule**
```
(version 1)
(rule pri_sec_creepage
  (constraint creepage (min 5mm))
  (condition "A.hasNetclass('PRI') && B.hasNetclass('SEC')"))
```
- The value shown is a placeholder. Derive the real value in step 5.
- Use net classes in the condition, not net names. Avoid unconditional creepage rules: they can be slow and inconsistent.
- The check runs per copper layer. Early 9.0 builds checked only the front layer; use a current 9.0.x release and confirm violations appear on every copper layer.
- The rule checks board-surface paths between copper. It does not check through-air clearance to parts, hardware, or enclosures, or solid insulation. Keep the independent checks of step 8.

**Other features**
- Component classes link the step 6 functional groups to selections and footprint rules.
- The multi-channel tool repeats a routed channel (for example identical phases or legs).
- Jobsets store output steps (plots, exports, DRC) for reuse.
- Zone refill from Python: `pcbnew.ZONE_FILLER(board).Fill(board.Zones())`.

## Autorouter

- **Freerouting** works through the Specctra format: export a `.dsn`, route, import the `.ses` session.
  - Documented options: `-de <file.dsn>` (input), `-do <file.ses>` (output), `-inc <net classes>` (net classes to ignore).
  - `-mp <n>` sets the maximum number of passes. Other options (threads, headless mode, optimization) differ by version; check `-help`.
  - Some versions need a display; on a server, run under a virtual display (for example `xvfb-run`).
- Other autorouters: Altium Situs, Allegro PCB Router, and others. The same rules apply: route only low-current signal nets, lock critical nets first, add keep-outs over isolation gaps and commutation loops, and set a pass or time limit.
- Use net-class widths and clearances from step 5 in the exported design, so the autorouter starts from the right rules.

## Making the agent's routing fast (modes A and C)

These implement the step 7b list.
- **Persistent session:** load the board once in a long-running Python process. Apply an edit, refill only the affected zones, and run checks on the changed area. Save at the end of a block, not after each edit.
- **Pad-based helpers:** write small functions such as "connect pad to pad on layer L with width W", "add the return path directly underneath", "add a via array between two pads", "create a zone from these pads with priority P". Re-run them when placement changes instead of editing coordinates.
- **Machine-readable checks:** parse DRC output (JSON where available) into a table with type, nets, location, and block. Fix all violations of one cause together.
- **Focused images:** crop renders to the changed block at high resolution, overlay DRC markers, and show full-board renders only at the gate.
- **Variants in parallel:** when using an autorouter, run a few variants at once (different pass limits or net orders) and keep the best by violations, then length.
- **Budget:** count iterations per block. When the budget runs out, stop and offer mode B or C.

## Analysis tools by question

| Question | Typical tools |
|---|---|
| Switching waveforms, stresses, losses with parasitics | SPICE simulators: LTspice, ngspice, SIMetrix/SIMPLIS, PSpice, QSPICE |
| Control loop, system behaviour, many switching cycles | PLECS, PSIM, Simulink/Simscape; SIMPLIS for averaged and loop analysis |
| Loop inductance and mutual inductance from layout | FastHenry (magnetoquasistatic), Ansys Q3D, other field solvers; analytical estimates only for screening |
| Full-wave and radiated behaviour | openEMS and other FDTD or FEM solvers |
| DC voltage drop and current density in copper | Altium Power Analyzer by Keysight (formerly PDN Analyzer), Cadence Sigrity PowerDC, or a custom resistive-network model of the copper |
| Thermal | Ansys Icepak, Simcenter Flotherm, Elmer, or a custom finite-difference model with stated boundary conditions |
| Magnetic components | FEMM, Ansys Maxwell, vendor design tools |

Record the model sources and their limits (see analysis-and-measurement). Vendor models may be encrypted or tied to one simulator; check compatibility in step 1.
