# agent-skills

Personal Cursor agent skills. Each top-level folder is one skill with a `SKILL.md`.

| Skill | Description |
|---|---|
| [power-pcb-design](power-pcb-design/) | Review, create, or improve power-electronics PCBs with human review gates |

## Install or reload on a machine

Cursor loads personal skills from `%USERPROFILE%\.cursor\skills`, so clone this repo there:

```powershell
git clone https://github.com/ChengminLi/agent-skills.git "$env:USERPROFILE\.cursor\skills"
```

To reload the latest version later:

```powershell
git -C "$env:USERPROFILE\.cursor\skills" pull
```

## Publish changes

```powershell
cd "$env:USERPROFILE\.cursor\skills"
git add -A
git commit -m "Describe the change"
git push
```

To add a skill, create a new folder next to `power-pcb-design` containing a `SKILL.md`.
