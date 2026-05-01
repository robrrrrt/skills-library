# AGENTS.md

This repo is a library of Claude Code skills. Each skill lives in its own kebab-case directory containing a `SKILL.md` (frontmatter + instructions Claude follows) plus any supporting configs, schemas, or scripts.

## Post-change evaluation

After completing a requested change — files written, validated, committed — run a brief evaluation pass before reporting back. Look at:

- **Skill prompts** (`SKILL.md`). Is the new instruction consistent with existing rules? Did the change implicitly weaken or contradict prior guidance? Is there ambiguity the model executing the skill could trip on?
- **Schemas** (`SCHEMA.md`, config files). Did the change require a workaround because the schema couldn't natively express what was needed? Are field semantics still consistent across configs in the same family?
- **Code** (helper scripts, if any). Anything brittle, redundant, or now-unused after the change?
- **Cross-skill / cross-config duplication.** Did this change repeat content (constraints, forbidden lists, prompt fragments, helper logic) that already exists elsewhere? If the same block now lives in 3+ places, flag it as a candidate for a shared snippet, schema field, or refactor.
- **Fit to the requested change.** Did this actually solve the user's stated need, or just the surface of it? Are there edge cases or downstream implications that weren't addressed?
- **Future scalability / extensibility.** Will this generalize to similar future asks? Did the change lock out variations? Are there structural improvements that would pay back the next 2-3 similar changes?

Surface findings as a short list of suggested follow-ups at the end of the response. **Do not implement them** — they are for the user to triage. If nothing meaningful surfaces, say so explicitly rather than padding with cosmetic suggestions.

**Skip the evaluation pass entirely** for trivial changes: typo fixes, single-line edits, comment-only changes, mechanical renames where the intent is unambiguous. The pass is for substantive changes where structural feedback could matter.
