# tone-shift

`tone-shift` rewrites text for a target audience and communication style using a YAML config, while preserving every source fact.

## Purpose

Use this skill when you need to change **how** something is said (tone, register, structure, depth) without changing **what** is said (claims, numbers, dates, names, or quotes).

## Invocation

You can invoke `tone-shift` in three equivalent ways:

```bash
tone-shift --config <name> --input <path>
cat <path> | tone-shift --config <name>
```

Or in conversation:

> Apply tone-shift `ceo-brief` to this: ...

`<name>` maps to `configs/<name>.yaml`.

## Built-in configs

- `ceo-brief`
- `corrective-aggressive-subordinate`
- `corrective-aggressive-superior`
- `corrective-passive-subordinate`
- `corrective-passive-superior`
- `general-aggressive-personal`
- `general-aggressive-work`
- `general-passive-personal`
- `general-passive-work`

## Rewrite contract

When applying this skill:

- Preserve all factual content from the source.
- Follow every `heuristic.constraints` rule.
- Honor every `heuristic.forbidden` rule.
- Do not invent citations, sources, or URLs.
- Emit transformed text only (unless the user explicitly requests explanation).

## Config structure

Each YAML config contains:

- `name` and `description`
- `heuristic` (environment, audience, goal, mood, register, verbosity, depth, constraints, forbidden)
- `output` (format and optional target length)

See [`SCHEMA.md`](./SCHEMA.md) for full field-level documentation.

## Adding a config

1. Copy an existing file in `configs/`.
2. Adjust fields to match the desired behavior.
3. Save as `<new-name>.yaml`.

No code changes are required to add new configs.
