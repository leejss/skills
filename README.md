# Agent Skills

[![recap on skills.sh](https://img.shields.io/badge/skills.sh-recap-111827)](https://skills.sh/leejss/skills/recap)
[![think-it-through on skills.sh](https://img.shields.io/badge/skills.sh-think--it--through-111827)](https://skills.sh/leejss/skills/think-it-through)
[![tracer-bullet on skills.sh](https://img.shields.io/badge/skills.sh-tracer--bullet-111827)](https://skills.sh/leejss/skills/tracer-bullet)

Reusable agent workflows for focused thinking, durable conversation records, and verified end-to-end exploration.

## Available skills

| Skill | Purpose |
| --- | --- |
| `recap` | Turn a conversation into a focused learning, project, handoff, or archive document. |
| `think-it-through` | Clarify a vague request through focused, opinionated dialogue. |
| `tracer-bullet` | Trace or build the smallest verified end-to-end path that resolves a key uncertainty. |

## Install

List the skills available in this repository:

```bash
npx skills add leejss/skills --list
```

Install one skill globally for Codex:

```bash
npx skills add leejss/skills --skill recap --agent codex --global
```

Install every skill globally for Codex:

```bash
npx skills add leejss/skills --skill '*' --agent codex --global
```

Replace `recap` with `think-it-through` or `tracer-bullet` to install a different skill.

## Update

Update installed skills to their latest versions:

```bash
npx skills update --global
```

## Documentation

See [Effective Skill Design](docs/effective-skill-design.md) for the design principles used in this repository.
