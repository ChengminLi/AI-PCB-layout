# power-pcb-design

The agent skill itself. Copy this whole folder into your agent's skills folder to install it; see the [main README](../README.md#quick-start) for instructions.

| File | Purpose |
|---|---|
| [SKILL.md](SKILL.md) | Entry point read by the agent: scopes, human review gates, working rules, and the nine-step workflow |
| [references/](references/) | Detailed guidance the agent loads only when a step needs it |

When editing the skill, keep `SKILL.md` focused on the workflow and put detailed technical material in `references/`. Before publishing a substantial change, walk through the scenarios in [behavioral-evaluation.md](references/behavioral-evaluation.md).
