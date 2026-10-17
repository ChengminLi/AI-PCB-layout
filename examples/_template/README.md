# Example title

One or two sentences: what the board is, and what you asked the agent to do.

![Board overview](images/overview.png)

## Design at a glance

| Item | Value |
|---|---|
| Topology | e.g. synchronous buck, half-bridge, flyback |
| Input / output | e.g. 48 V in, 12 V / 10 A out |
| Switching frequency | |
| Devices | e.g. SiC MOSFETs, part numbers |
| Board | layers, copper weight, size |
| CAD tool and version | e.g. KiCad 9.0.x |
| Agent and model | e.g. Cursor with Claude |
| Scope | review / small revision / create or revise layout / end-to-end |

## What the agent did

Summarize each step that ran and the decision at its review gate.

| Step | Result | Gate decision | Your changes |
|---|---|---|---|
| 1. Requirements and inputs | | approve | |
| 2. Architecture and limits | | | |

## Results and evidence

| Criterion | Result | Evidence type |
|---|---|---|
| e.g. DRC | pass, 0 violations | CAD check |
| e.g. commutation loop inductance | 3.2 nH | field-solver estimate |
| e.g. switch-node overshoot | | measurement |

## Lessons learned

What worked well, where the agent needed correction, and anything you would change in the skill.

## Files

- `design/` — CAD project
- `outputs/` — fabrication files and BOM
- `images/` — renders and photos
