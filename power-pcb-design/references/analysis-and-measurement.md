# Analysis, optimization, and measurement

## Match analysis to the decision

For a review, use available waveforms/calculations and identify missing evidence; do not require a new simulation project merely to identify an obvious routing defect. For designs relying on switching-stress predictions, include relevant parasitics and bound uncertainty. Use one suitable simulator unless a second format is a requested deliverable or serves a defined cross-check.

Record model origin/version, supported simulator, temperature dependence, and validated operating range. Maintain a parasitic inventory: physical element, value/range, units, source, and whether already included in a device/subcircuit. Avoid double counting package inductance, nonlinear capacitances, diode recovery, or transformer leakage.

Use native branch-current outputs where available; insert zero-volt sensing sources only where needed and verify polarity and solver behavior. Map schematic, netlist, and PCB identities explicitly. Stable net names help but do not replace this mapping.

## Switching and control checks

Bound input, load, temperature, component/model spread, driver supply, and plausible parasitics. Include relevant start-up, shutdown, mode transitions, dead time, current limiting, and faults that the models can represent. Mark unsupported fault behavior unresolved.

Evaluate Vds/Vce and positive/negative Vgs/Vge at the relevant device terminals, diode stresses, peak/RMS currents, clamp/snubber losses, and driver supply droop. Choose margins from device/application requirements and uncertainty, not a fixed universal percentage.

The linear series-RLC condition R_total approximately >= 2*sqrt(L/C) is only a gate-damping screening estimate. Nonlinear capacitances, driver impedance, and switching state limit its validity. Likewise Cgd*dv/dt and Lcs*di/dt are screening terms: sign, coupling, frequency response, and device behavior matter. Check unwanted conduction/shoot-through and device-specific gate limits; a datasheet threshold-voltage comparison alone is insufficient.

Model common-source impedance where both power and gate loops actually share it. Separate-driver Kelvin returns reduce shared external impedance but do not guarantee zero common-source inductance. Do not give Lcs a fixed independent percentage of switching loss when nonlinear interactions prevent that decomposition; report the defined comparison case instead.

For loss integration, document voltage/current reference direction, integration windows, timestep sensitivity, and treatment of stored energy. Terminal v*i during an edge can include reversible capacitive energy. Do not add Eoss or recovery loss a second time if already captured by the model. Check complete-cycle energy balance and steady-state convergence; burst-mode averages must cover the slower envelope.

Control-loop acceptance needs defined stability criteria and an appropriate model/measurement across relevant operating points. Load-step results supplement, not replace, loop analysis. Account for topology-specific dynamics and controller behavior where applicable.

## Parasitic extraction and optimization

Use geometry-based loop estimates for screening only within their assumptions. A centreline Neumann-integral calculation requires a justified conductor representation and return path; a fixed effective radius is not generally valid for wide pours, planes, and via arrays.

Record method, geometry/stack-up, ports, frequency range, return paths, discretization, and uncertainty. Where accuracy affects acceptance, compare with suitable field extraction or measurement. Self/mutual terms must use consistent references and signs; the assembled inductance model must be physically valid. Segment contributions are not generally independent because of mutual coupling.

Optimize against the dominant stress or coupling mechanism. Compare capacitor placement/rotation, return geometry, package choice, gate reference, layer spacing, and switching-speed tradeoffs as applicable. Evaluate changed capacitance and thermal behavior as well as inductance. Preserve schematic connectivity and insulation rules in every variant.

Back-annotate credible parameter ranges rather than unjustified exact values. Rerun affected stress, loss, and thermal cases. State the iteration budget and decision resolution before a large sweep; do not claim a meaningful improvement smaller than model uncertainty. Report the chosen geometry and why it meets the stated criteria. Low inductance and damped ringing do not establish EMC compliance.

## Thermal analysis

Use the specified ambient, enclosure, orientation, airflow, mounting, and duty cycle. Include device, magnetic, shunt, clamp, capacitor, and copper losses as relevant. Iterate temperature-dependent losses where material.

Use thermal resistances or compact models only under matching assumptions; datasheet theta_JA is not a universal board-independent parameter. Represent interface materials, heatsinks, copper layers, vias, convection, and radiation to the fidelity needed for the decision.

For numerical solvers, verify boundary conditions, energy balance, and mesh sensitivity. Do not conceal a disconnected domain or missing boundary condition by adding arbitrary tiny conductances. If regularization is mathematically justified, disclose it and show negligible sensitivity. A fine mesh cannot compensate for unknown heat-transfer coefficients.

Report junction and component/board temperatures with uncertainty and rating margins. Hardware thermal validation remains pending until measured.

## Bring-up and EMC measurements

Plan staged energization, current limiting, discharge verification, functional/protection checks, and waveform/temperature measurements appropriate to the voltage and stored energy. Provide physically accessible measurement points without needlessly enlarging hot loops.

Measure gate voltage relative to the actual source/emitter reference. An earth-referenced passive probe is suitable only when its ground connection is valid for that circuit. For floating/high-side nodes, select an appropriate differential or isolated measurement arrangement with adequate differential/common-mode ratings, bandwidth, CMRR at the edge frequencies, and probe loading. Do not defeat oscilloscope protective earth. For measured switching energy, deskew voltage/current channels and quantify measurement error.

Specify the relevant EMC standard, frequency range, detector, bandwidth, limits, LISN/network, load, cable geometry, reference plane, and bonding arrangement. Follow the applicable setup rather than prescribing one universal PE bond.

When separating modes mathematically, define calibrated conductor-to-reference voltages V1 and V2: Vcm=(V1+V2)/2. Two differential-mode conventions are in use: the conductor-to-conductor voltage V1-V2, and the per-line value (V1-V2)/2 that many CM/DM separators and LISN-based setups report (6 dB lower). State the convention on every figure, scope set-up, and result table, and keep it consistent within a project. Require matched termination, amplitude/phase calibration, and correction factors. Oscilloscope sums/differences alone are not a compliance receiver measurement.

## Technical references

- [FastHenry2](https://www.fastfieldsolvers.com/fasthenry2.htm): frequency-dependent resistance and self/mutual inductance extraction within a magnetoquasistatic approximation.
- [Tektronix power-converter probing](https://www.tek.com/en/documents/application-note/probing-techniques-accurate-voltage-measurements-power-converters-oscillos): measurement references and probe arrangements.
- [Analog Devices EMI mitigation](https://www.analog.com/en/resources/analog-dialogue/raqs/raq-issue-188.html): common-mode coupling and practical measurement/layout tradeoffs.
