# deep-planning

> Mode-based router for any complex planning exercise prior to executing. Three modes (Quick / Build / Strategic) calibrate ordering, skill selection, and Compound Engineering integration to the intent and complexity of the work. Replaces `/spec-writer` (hard-retired 2026-05-08).

**Version:** 0.1.0

## Installation

```bash
cd ~/.claude/skills
git clone https://github.com/wildbuffaloman/cc-skill-deep-planning.git deep-planning
```

> **Note:** GitHub repo rename pending — until that completes, the repo URL is `https://github.com/wildbuffaloman/cc-skill-spec-writer.git`. After the GitHub-side rename, run `git remote set-url origin https://github.com/wildbuffaloman/cc-skill-deep-planning.git` inside the cloned skill folder.

## Usage

Invoke in Claude Code:

```
/deep-planning topic description (short sentence of what to plan)
```

The skill proposes a mode (Quick / Build / Strategic) based on triggers in the topic and asks the user to confirm before running any stage.

## Files

- `SKILL.md` — full skill specification including mode taxonomy, per-mode chains, brainstorm-skill selection rule, and step-by-step pipeline

## Migration from `/spec-writer`

`/spec-writer` was hard-retired on 2026-05-08. If you typed `/spec-writer`, retype `/deep-planning`. Build mode is the closest equivalent to the predecessor's behavior.
