---
name: tone-shift
description: Rewrite text for a target audience and mood using a config-defined heuristic. Loads tone, style, and depth rules from configs/<name>.yaml and applies them while preserving every fact in the source.
---

# tone-shift

Rewrites a piece of text so its tone, style, and depth match a heuristic defined in a YAML config. Facts in the source are immutable — only how they're presented changes.

## Invocation modes

| Mode | Trigger | Input source | Config resolution |
|---|---|---|---|
| **File** | `tone-shift --config <name> --input <path>` | Read `<path>` with the `Read` tool | `configs/<name>.yaml` (if missing, list available and ask) |
| **Stdin** | `cat <path> \| tone-shift --config <name>` | Piped or pasted text | Same as File |
| **Conversation** | "Apply tone-shift `<name>` to this: ..." + pasted text | Pasted text | Same as File |
| **Discovery** | Input text without a named config | Pasted or referenced text — load **before** resolving the config | See "Discovery mode" below |

`<name>` is the basename of a file in `configs/` (without the `.yaml`).

In every mode: never modify the source file. Output goes to stdout only, unless the user explicitly asks you to write somewhere. Never invent or guess a config name — list available options and ask.

### Discovery mode

When no config is named, do this before any rewrite:

- Load the input text first. The suggestion is based on the text.
- Read every `configs/*.yaml` — at minimum the `name`, `description`, and `heuristic` block (especially `environment`, `audience`, `register`, `mood`, and `context`).
- Analyze the input along these axes:
  - **Environment cues** — workplace markers (deadlines, deliverables, leadership, teams, formal channels) vs personal markers (friends, family, relationships, casual channels).
  - **Audience cues** — addressed downward (subordinate, report), upward (boss, exec, leadership), or peer/general; named recipients; tone of address.
  - **Current register** — passive, passive-aggressive, neutral, formal, aggressive.
  - **Intent signals** — is this feedback / correction with a gap to name (favors `corrective-*`), or general prose where only register needs to shift (favors `general-*`)?
- Rank configs by relevance and present the top 3 as a numbered multiple-choice question. For each option include a one-line "matches because…" reason citing specific cues from the text and config. Add a final option ("see all configs") so the user can override the suggestion.
- Wait for the user to pick before continuing. Do not start rewriting on your own guess.

**Fallbacks:**
- If the input is too short or empty to analyze, skip the suggestion and list all configs with their one-line descriptions.
- If no config is a plausible match (e.g., the text is about a domain none of the configs are tuned for), say so and list all configs rather than forcing a top 3.

## Workflow

After the config and input text are resolved (see invocation modes above), proceed in order.

### 1. Read the heuristic

Open the resolved config and load every field. The schema is documented in `SCHEMA.md` next to this file. Every field is meaningful — don't skim.

**If a heuristic field is unset, treat it as "preserve the source's value for this dimension" — do not invent a default.** This applies to `mood`, `register`, `verbosity`, and `depth`. Required fields (`environment`, `audience`, `goal`, `constraints`) are always present.

Pay particular attention to:
- `heuristic.constraints` — must-do rules. Apply all of them.
- `heuristic.forbidden` — must-not-do rules. Honor all of them.
- `heuristic.context` — situational facts about the recipient. Use these to shape what to call out, not to change the underlying facts.
- `output.format` and `output.target_length` — shape and size of the result.

### 2. Rewrite

Produce the transformed text. Hard rules that override anything else:

- **Preserve every factual claim, number, name, date, and direct quote** from the input. You may reorder, compress, or expand prose — never change what is asserted.
- **Never invent citations, sources, or URLs.** If `output.format` calls for a citations footer, only include URLs that already appear in the source. If the source has none, omit the footer.
- **Honor `forbidden` strictly.** A `forbidden: [profanity]` rule means zero profanity, even if `register: aggressive`.
- **Honor `constraints` fully.** If a constraint conflicts with a stylistic instinct, the constraint wins.
- **Match `mood` and `register` together.** They're orthogonal — `mood: inspiring` + `register: aggressive` means high-intensity language pushing the reader to do better, not warm encouragement and not pure hostility.

### 3. Output

Print only the transformed text. No preamble like "Here's your rewrite:", no trailing commentary, no diff. If the user asked for explanation alongside the rewrite, put the rewrite first, then a short note clearly separated by a blank line and a heading.
