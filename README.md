# skills-library

A repository of reusable skills for structured prompt workflows.

## Repository layout

- `tone-shift/` — rewrite text to a target audience, tone, and depth while preserving all source facts.

## Available skills

| Skill | Purpose | Key files |
|---|---|---|
| `tone-shift` | Applies config-driven rewriting heuristics to transform style and emphasis without changing factual content. | `tone-shift/SKILL.md`, `tone-shift/README.md`, `tone-shift/SCHEMA.md`, `tone-shift/configs/*.yaml` |

## Working with a skill

1. Open the skill's `SKILL.md` for invocation and workflow rules.
2. Read the skill-specific `README.md` for examples and quick reference.
3. Review `SCHEMA.md` (if present) for config field definitions.
4. Use or extend configs in the skill's `configs/` directory.
