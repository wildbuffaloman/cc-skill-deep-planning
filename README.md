# spec-writer

> Structured spec-writing pipeline for complex sub-projects or architectural decisions — chains /last30days → /deep-research → /brainstorming → /ce:plan in order, passing context forward between stages. Use when the user says "spec out X", "design the system for X", "let's plan X properly", "scope this sub-project", or returns to an in-progress /project-create with non-trivial complexity.

**Version:** 0.1.0

## Installation

```bash
cd ~/.claude/skills
git clone https://github.com/wildbuffaloman/cc-skill-spec-writer.git spec-writer
```

## Usage

Invoke in Claude Code:

```
/spec-writer topic description (short sentence of what to spec)
```

## Files

- `SKILL.md`

