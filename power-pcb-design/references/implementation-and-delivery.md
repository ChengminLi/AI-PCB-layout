# Implementation and delivery

## Tool and source preflight

Inspect the existing project before selecting automation. Record the CAD version, supported CLI/API, simulator, libraries, and any generators actually available. Check installed command help and matching official documentation; do not assume a command or API signature from a different release.

This skill bundles guidance, not PCB generators, field solvers, or thermal solvers. The former example archive `EMC_demo_flyback/EMC_flyback_KiCad_project.zip` is not a required dependency and is not bundled here. If the user supplies it, inspect its scripts and assumptions before reuse. Treat its placement coordinates, dimensions, repair scripts, and component choices as example-specific. Do not claim to run an unavailable `loopL.py`, `check_spacing.py`, or other named helper.

For KiCad, use the installed version's CLI where supported for ERC/DRC and exports. Current command capabilities must be discovered; legacy KiCad 7 workarounds are not universal instructions. [KiCad CLI documentation](https://docs.kicad.org/8.0/en/cli/cli.html) demonstrates available DRC functionality in version 8; consult the version matching the installation.

Preserve the authoritative source workflow: existing CAD files for an interactive project, or generators plus input data for a generated project. Do not silently replace one with the other. If generation is appropriate, keep stable internal component identifiers separate from refdes and maintain explicit net/symbol/footprint mappings. Preserve identifiers through renaming and validate connectivity after regeneration.

## Reproducible changes

- Work on a staged copy or revision and record source hashes before editing. Check for concurrent changes immediately before replacing files.
- Respect editor locks; do not discard unsaved user work or instruct a reload that loses edits. Reconcile/save to a separate copy where needed.
- Keep project-local symbols, footprints, and models where portability requires them, subject to their licenses. Use project-relative references.
- Record units and coordinate conventions, especially rotations and bottom-side transforms. Audit pin/pad mapping independently of renderings.
- Export analysis tables from the checked result data. Record tool/model versions, operating cases, revision/hash, assumptions, and uncertainty with results.
- Prefer structural edits tied to stable objects over coordinate-based post-processing. Where a geometric patch is unavoidable, require explicit preconditions and reject stale geometry; DRC alone may not detect altered engineering intent.
- Inspect diffs and perform the checks invalidated by the change. Avoid new solvers or generic helper scripts unless their purpose and validation are clear.

## Check records

For each acceptance criterion, retain: criterion ID; applicable scope; requirement/source; limit and units; observed/predicted value; worst case; result; evidence type; artifact and input revision; uncertainty; unresolved assumptions; and follow-up measurement where needed.

Use `pass`, `fail`, `unresolved`, or `not_applicable` for result. Use `measured`, `simulated`, `calculated_estimate`, `cad_checked`, or `unavailable` for evidence type. A criterion may cite several evidence records. Missing values remain null/unknown, never zero or an invented measurement. Document the rationale for not-applicable checks and intentional DRC/ERC exclusions.

## Fabrication and assembly

When requested, export Gerbers, drills, fabrication stack-up notes, and assembly data from the same checked board revision. Inspect plotted copper and outline/drills independently of the live editor; verify dimensions, slots, layer mapping, copper weight, plating, solder-mask constraints, and critical tolerances.

Produce BOM and placement data for the selected assembler, not a hardcoded vendor. Include MPN, package variant, population/DNP status, quantity, and sourcing identifiers where relevant. Verify placement origin, units, rotation convention, board side, polarity, and hand-assembled parts. An exported CPL is not automatically assembler-correct.

Mechanical checks include connector access, component envelopes, tolerances, stand-offs, heatsink insulation, screw/tool access, and enclosure clearances. In end-to-end design, every component has a verified 3D model (or dimensioned envelope) before placement, as required in SKILL.md; for reviews and small revisions, obtain 3D assets only to the level the task needs. Do not assume a fixed model rotation from the CAD vendor alone.

Label release intent and unresolved criteria clearly. Prototype fabrication may precede hardware measurements, but this does not establish production readiness or compliance. Retain previous revisions without including stale fabrication files in the current release package.

## Change impact

| Change | Revisit at minimum |
|---|---|
| Silkscreen only | Silkscreen/pad and edge checks, plotted output, revision consistency |
| Placement or routing | Connectivity, DRC, affected return paths, insulation, copper capability, and coupling/thermal assumptions |
| Stack-up or copper | Geometry/rules, electrical parasitics, current sharing, thermal, and fabrication requirements |
| Semiconductor/driver/sensing change | Pin mapping, reference architecture, operating/stress models, protection, loss, layout, and thermal |
| Enclosure/heatsink/interface change | Mechanical fit, insulation/accessibility, capacitive coupling, and thermal boundaries |

Broaden the checks when actual dependencies require it. Final exports must correspond to the checked revision. Deliver the results and limitations, not just a statement that all checks passed.
