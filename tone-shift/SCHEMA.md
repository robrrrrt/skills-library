# tone-shift config schema

Every config in `configs/` is a YAML file with this shape. Required fields are marked. Free-form fields accept any string; enumerated fields list the recognized values — Claude will follow values outside the enum but the canonical ones are tested against the defaults.

## Top-level

| Field | Required | Type | Notes |
|---|---|---|---|
| `name` | yes | string | Should match the filename basename. |
| `description` | yes | string | One-line summary shown when listing configs. |
| `heuristic` | yes | object | The transformation rules. See below. |
| `output` | yes | object | Shape and size of the result. See below. |

## `heuristic`

| Field | Required | Type | Recognized values | Notes |
|---|---|---|---|---|
| `environment` | yes | string | `work`, `personal`, `academic`, `public` | Sets baseline norms. |
| `audience` | yes | string | free-form (e.g., `ceo`, `subordinate`, `peer`, `customer`, `general public`) | Who's reading. |
| `goal` | yes | string | free-form prose | What the rewrite should accomplish. |
| `context` | no | list of strings | free-form | Situational facts about the recipient. Used to shape emphasis, not to change source facts. |
| `mood` | yes | string | `neutral`, `urgent`, `inspiring`, `warm`, `somber` | Emotional valence. |
| `register` | yes | string | `formal`, `casual`, `aggressive`, `deferential` | Social formality and intensity. Orthogonal to `mood`. |
| `verbosity` | yes | string | `terse`, `standard`, `expansive` | How much prose. |
| `depth` | yes | string | `surface`, `detailed`, `exhaustive` | How much analysis or detail. |
| `constraints` | yes | list of strings | free-form rules | Must-do. Apply every one. |
| `forbidden` | no | list of strings | free-form rules | Must-not-do. Honor every one. Overrides instincts. |

### How `mood` and `register` combine

They're independent axes. Some examples:
- `neutral` + `formal` → standard executive memo voice.
- `inspiring` + `aggressive` → high-intensity push: name the gap, demand better, raise the bar. Not warm. Not hostile.
- `warm` + `casual` → encouraging note to a peer.
- `urgent` + `formal` → escalation to a stakeholder.

If a config lists a `forbidden` rule that contradicts a register (e.g., `register: aggressive` plus `forbidden: [profanity, personal attacks]`), the `forbidden` rule wins.

## `output`

| Field | Required | Type | Notes |
|---|---|---|---|
| `format` | yes | string | e.g., `prose`, `prose-with-citations-footer`, `bullets`, `numbered-list`. Free-form — Claude interprets sensibly. |
| `target_length` | no | string | Relative (`~40% of input`, `100-130% of input`) or absolute (`under 200 words`). Treat as a target, not a hard cap. |

## Authoring tips

- Start by copying one of the shipped defaults and changing fields one at a time.
- `constraints` and `forbidden` are where most of the actual voice lives — be specific. "cut hedges" beats "be confident". "name the specific gap" beats "give feedback".
- If a config grows past ~25 constraint+forbidden rules, it's probably trying to be two configs. Split it.
- The `name` field should match the filename so listings and lookups stay consistent.
