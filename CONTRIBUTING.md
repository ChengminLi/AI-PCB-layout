# Contributing

Thanks for helping improve this skill. There are three ways to contribute.

## Share a design example

Real boards are the most useful contribution: they show how the skill behaves on actual designs. Follow the steps in [examples/README.md](examples/README.md).

## Report a problem

[Open an issue](https://github.com/ChengminLi/AI-PCB-layout/issues/new) if the agent gave wrong engineering advice, skipped a review gate, invented a result, or misunderstood your CAD tool. Please include:

- the agent and model you used, and your CAD tool and version;
- what you asked for and what step the agent was in;
- what it did, and what it should have done.

## Improve the skill

1. Fork this repository and create a branch.
2. Make your change:
   - Keep `power-pcb-design/SKILL.md` focused on the workflow, gates, and rules. Put detailed technical material in the matching file in `power-pcb-design/references/`.
   - Give every numerical limit its units, source, and assumptions. Example values must not read as defaults.
   - Keep the guidance tool-neutral; tool-specific details belong in `references/cad-and-tools.md`.
3. For substantial changes, walk through the scenarios in [behavioral-evaluation.md](power-pcb-design/references/behavioral-evaluation.md) and note the results in your pull request.
4. Open a pull request describing what changed and why.
