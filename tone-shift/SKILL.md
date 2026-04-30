---
name: tone-shift
description: Rewrite text for a target audience and mood using a config-defined heuristic. Loads tone, style, and depth rules from configs/<name>.yaml and applies them while preserving every fact in the source.
---

# tone-shift

Rewrites a piece of text so its tone, style, and depth match a heuristic defined in a YAML config. Facts in the source are immutable — only how they're presented changes.

## Invocation

Three equivalent ways to invoke:

1. **Config + file path**
   ```
   tone-shift --config <name> --input <path>
   ```
2. **Config + stdin**
   ```
   cat <path> | tone-shift --config <name>
   ```
3. **Free-form, in conversation**
   > "Apply tone-shift `ceo-brief` to this:" followed by the pasted text.

`<name>` is the basename of a file in `configs/` (without the `.yaml`). Run with no config to see what's available.

## Workflow

When invoked, follow these steps in order.

### 1. Resolve the config

- Look for `configs/<name>.yaml` inside this skill's directory.
- If the requested config doesn't exist, list every `configs/*.yaml` basename and ask the user which one they meant. Do not invent a config or guess.

### 2. Load the input text

- If `--input <path>` was given, read that file with the `Read` tool.
- If text was piped or pasted, use it as-is.
- Never modify the source file. Output goes to stdout only, unless the user explicitly asks you to write somewhere.

### 3. Read the heuristic

Open the resolved config and load every field. The schema is documented in `SCHEMA.md` next to this file. Every field is meaningful — don't skim.

Pay particular attention to:
- `heuristic.constraints` — must-do rules. Apply all of them.
- `heuristic.forbidden` — must-not-do rules. Honor all of them.
- `heuristic.context` — situational facts about the recipient. Use these to shape what to call out, not to change the underlying facts.
- `output.format` and `output.target_length` — shape and size of the result.

### 4. Rewrite

Produce the transformed text. Hard rules that override anything else:

- **Preserve every factual claim, number, name, date, and direct quote** from the input. You may reorder, compress, or expand prose — never change what is asserted.
- **Never invent citations, sources, or URLs.** If `output.format` calls for a citations footer, only include URLs that already appear in the source. If the source has none, omit the footer.
- **Honor `forbidden` strictly.** A `forbidden: [profanity]` rule means zero profanity, even if `register: aggressive`.
- **Honor `constraints` fully.** If a constraint conflicts with a stylistic instinct, the constraint wins.
- **Match `mood` and `register` together.** They're orthogonal — `mood: inspiring` + `register: aggressive` means high-intensity language pushing the reader to do better, not warm encouragement and not pure hostility.

### 5. Output

Print only the transformed text. No preamble like "Here's your rewrite:", no trailing commentary, no diff. If the user asked for explanation alongside the rewrite, put the rewrite first, then a short note clearly separated by a blank line and a heading.
