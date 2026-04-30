# tone-shift

A configurable rewrite skill that changes **tone, style, and depth** while preserving all source facts.

This skill applies a named YAML heuristic from `configs/` and rewrites input text to match the target audience and communication goals.

## What it does

- Rewrites wording, structure, and emphasis to match a target voice.
- Preserves factual content (claims, names, numbers, dates, and direct quotes).
- Enforces config-defined requirements (`constraints`) and prohibitions (`forbidden`).
- Supports multiple communication contexts (work/personal), audiences, and registers.

## Usage

From the skill directory, invoke in any of these ways:

```bash
tone-shift --config <name> --input <path>
cat <path> | tone-shift --config <name>
```

Or in chat:

> Apply tone-shift `ceo-brief` to this: ...

If you run without a config, the skill should list available config names.

## Available configs

- `ceo-brief`
- `corrective-aggressive-subordinate`
- `corrective-aggressive-superior`
- `corrective-passive-subordinate`
- `corrective-passive-superior`
- `general-aggressive-personal`
- `general-aggressive-work`
- `general-passive-personal`
- `general-passive-work`

## Config model

Each config is a YAML file with:

- Top-level metadata (`name`, `description`)
- `heuristic` (environment, audience, mood, register, verbosity, depth, constraints/forbidden)
- `output` (format and optional length target)

See `SCHEMA.md` for full field definitions and examples.

## Safety and fidelity rules

- Never change the underlying facts.
- Never invent citations or URLs.
- `forbidden` rules override stylistic instincts.
- `constraints` must all be followed.
- Output should be the transformed text only (unless explanation is explicitly requested).

## Add a new config

1. Copy an existing YAML file in `configs/`.
2. Update fields for your target communication style.
3. Save as `<name>.yaml`.

No code changes are required to add configs.
