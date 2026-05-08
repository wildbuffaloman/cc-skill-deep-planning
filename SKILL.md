---
name: spec-writer
version: "0.1.0"
description: Structured spec-writing pipeline for complex sub-projects or architectural decisions — chains /last30days → /deep-research → /brainstorming → /ce:plan in order, passing context forward between stages. Use when the user says "spec out X", "design the system for X", "let's plan X properly", "scope this sub-project", or returns to an in-progress /project-create with non-trivial complexity.
user-invocable: true
argument-hint: "topic description (short sentence of what to spec)"
---

# Spec-Writer — Ordered Composite Pipeline for Architectural Specs

Routes a spec-writing assignment through four ordered stages. Each stage produces an artifact that feeds the next. Terminal output is an implementation plan ready for `/ce:work`.

## When to use

Use when the user is about to start serious work on a non-trivial sub-project or architectural change and wants a proper spec before touching code. Triggers: "spec out", "design the system for", "let's plan X properly", "scope this", "think through X before building."

**Also valid for non-code engagements** — career coaching, multi-month consulting, structured guidance programs, AIAC participant cases, friend-mentorship engagements, anything where Stage 4 produces an *engagement/operating plan* rather than an *implementation plan*. Validated 2026-05-02 with [[JV Career Reboot]] — full pipeline run produced research → brainstorm → plan → project-create with no skill modifications. Stage 4 plan structure adapts naturally (Outcome / Engagement Model / Workstream Architecture / Milestone Arc / Component Build / Tool Integration / Meeting Template / Pre-M0 Deliverables / Success Metrics / Failure Modes / Privacy / Budget / Artifacts / Risks / Cross-Pollination / Sources). Trigger phrases for this usage: "design a 6-month engagement for X", "plan a structured advisory for X", "scope a coaching program for X".

Do NOT use for:
- Simple bug fixes or single-file changes (overkill — go straight to implementation)
- Brainstorming-only sessions where no plan output is wanted
- Emergency hotfixes (time cost too high)
- Single-conversation deliverables (a one-off email draft, a single document — pipeline overhead exceeds value)

## Pipeline

```
Stage 1: /last30days       →  Recent operational context
Stage 2: /deep-research    →  External research brief
Stage 3: /brainstorming    →  Decision ledger
Stage 4: /ce:plan          →  Implementation plan
```

**Hard rule:** stages run in this order. `/last30days` first because recent operational context scopes what external research is worth doing.

## Steps

### Step 1 — Parse the topic and confirm scope

Read the user's topic description. Restate in one sentence. Ask the user to confirm before running any stage:

> "Running spec-writer pipeline for: *<topic>*. This will take ~4 stages with artifacts at each step. Proceed? [Y/n]"

If the topic is ambiguous (covers multiple independent subsystems), flag it and decompose before running the pipeline. A spec per subsystem is cleaner than one mega-spec.

### Step 2 — Stage 1: `/last30days`

Invoke `/last30days` scoped to the topic area. The skill returns recent activity (meetings, decisions, blockers, stalled work) that bear on the spec.

**Why first:** If last-30d surfaces a critical blocker at a data source, the research stage pivots from "KPI frameworks" to "data-source constraint workarounds." Putting research first risks burning the research budget on the wrong questions.

Pass forward: a summary of relevant last-30d findings as context for `/deep-research`.

### Step 3 — Stage 2: `/deep-research`

Invoke `/deep-research` with the topic PLUS the last-30d context summary from Stage 1. The skill produces a confidence-graded research brief with sources, options, and recommendations.

**Output lands in:** `00 HUB/00 INBOX/Research - <Topic> - YYYY-MM-DD.md` (skill default — already correct).

Pass forward: the brief's Recommendations section + Gaps section as context for brainstorming.

### Step 4 — Stage 3: `superpowers:brainstorming`

Invoke `superpowers:brainstorming` to pressure-test the research brief's recommendations. Formulate 5–10 open architectural questions from the brief's gaps and options. Walk the user through one at a time, multiple-choice preferred.

**Output override:** brainstorming skill defaults to `docs/superpowers/specs/` — override to `00 HUB/00 INBOX/Brainstorm - <Topic> - YYYY-MM-DD.md` per user's INBOX convention (see memory `feedback_plans_to_inbox.md`).

**Output override path 2:** the brainstorming skill's terminal state is `writing-plans` — override to `compound-engineering:ce-plan` for Stage 4.

Pass forward: the decision ledger (every question with its locked answer + one-line rationale) as context for `/ce:plan`.

#### Stage 3 variant — Framing-Document Mode (for high-context users with enumerable decisions)

When ALL of the following are true, prefer drafting a "Pre-Brainstorm Framing Questions" file in INBOX over the standard interactive question-by-question walkthrough:

- The architectural decisions are **clearly enumerable** (each has 2-4 named options, not open-ended exploration)
- The user is **high-context** (deeply familiar with the domain — no need for guided exposition)
- The user is **async-friendly** (can scan a structured document and respond in one message)

Pattern: Write `00 HUB/00 INBOX/Brainstorm - <Topic> - YYYY-MM-DD.md` as a Decision Ledger with each question presented as: question → options table → recommended default → one-line rationale → blank "Decision: _[awaiting user]_" line. User scans the whole document and confirms / corrects in a single response. Faster than the question-by-question walk for the right user; slower for low-context or open-ended exploration.

Validated 2026-05-02 with [[JV Career Reboot]] — Albert answered 10 framing decisions in one message AND inserted a substantive sales-first re-centering that wouldn't have surfaced cleanly in a standard interactive walk (because it touched multiple questions simultaneously, hard to introduce mid-walkthrough). The framing-document gave him a "whole map" view to reshape from.

**When to fall back to standard interactive mode:** decisions are open-ended (not multiple-choice), user wants to think through one at a time, lower-context user needs guided exposition, OR user explicitly asks for the walkthrough.

**Output:** identical to standard mode — a Decision Ledger that feeds Stage 4. The mode only changes the *interaction shape*, not the artifact.

### Step 5 — Stage 4: `compound-engineering:ce-plan`

Invoke `compound-engineering:ce-plan` with the brainstorm doc as the origin document. The skill produces a structured implementation plan with acceptance criteria, phases, risks, and sources.

**Output override:** ce-plan defaults to `docs/plans/` — override to `00 HUB/00 INBOX/YYYY-MM-DD-NNN-<type>-<name>-plan.md` per user's INBOX convention.

**Include in plan's origin chain:**
- `origin:` field → research brief from Stage 2
- `related:` field → brainstorm doc from Stage 3
- Plan's Sources & References section cites both

**Ultrathink default:** if ultrathink was enabled for the session, automatically run a deepening pass — re-invoke `compound-engineering:ce-plan` with the plan path and "deepen this plan" intent. This triggers ce-plan's Phase 5.3 Confidence Check in interactive mode (sub-agent findings reviewed individually before integration). Per memory `feedback_deepen_plan_inline_default.md`, integrate findings inline with `[DP:N]` tags by default.

### Step 6 — Present disposition options

After the plan lands, present the user with next-step options (per `compound-engineering:ce-plan` skill's option list): open plan, deepen, refine, share to Proof, start `/ce:work`, create Issue.

Do NOT auto-start `/ce:work` — that's a separate decision.

## Rules

- **Order is non-negotiable.** `/last30days` FIRST. No skipping. If the user asks to skip Stage 1, record the reason in the plan's Sources section (so future readers know context is missing).
- **All intermediate artifacts land in `00 HUB/00 INBOX/`.** Override every child skill's default output path. This is a hard user convention (see memory `feedback_plans_to_inbox.md`).
- **Spanish vs English:** match the conversation language. If the plan will be reviewed with Bufalinda team members (JC, Manuel, Ronnel, etc.), output in Venezuelan Spanish (tuteo) per Bufalinda Rule 1.
- **Context forwarding is explicit.** Each stage's prompt to the next includes a summary of the prior stage's findings. Don't rely on the subagent to infer context.
- **Pivots are first-class.** If the user adds scope mid-brainstorming (new tab, new constraint), update the decision ledger inline. Don't defer to "final review."
- **Priority-naming in recommendations.** When Stage 3 proposes options, each option names the priority it optimizes ("Option A optimizes YAGNI; Option B optimizes no-tech-debt"). Prevents the user from having to reverse-engineer the tradeoff.
- **Parallel agents in Stage 4 scale with blast radius.** For plans touching 500+ LOC or crossing module boundaries, `/ce:plan` should dispatch repo-research-analyst + learnings-researcher + spec-flow-analyzer. For greenfield single-file features, skip.

## Typical runtime

- Stage 1 (/last30days): 5–10 min
- Stage 2 (/deep-research): 15–30 min (depending on topic complexity + external research)
- Stage 3 (/brainstorming): 10–30 min (depending on how many open decisions)
- Stage 4 (/ce:plan): 10–20 min (plus ce-plan deepening pass: additional 10 min if ultrathink)

**Total: 40–90 min for a major architectural spec.** Expensive in time but cheap compared to reworking a half-specced implementation.

## Abort conditions

Stop the pipeline and surface the issue to the user if:
- Stage 1 reveals a blocker that invalidates the topic (e.g., the data source isn't accessible)
- Stage 2 research reveals the topic is already solved by an existing tool (redirect to adopt-not-build)
- Stage 3 user declines the architectural direction entirely (run brainstorming again with different framing)
- Stage 4 acceptance criteria cannot be made measurable (spec is too abstract; break into smaller specs)

## Evidence

- **2026-04-20 Reporte Ventas multi-module platform spec** — First full run of this pipeline. Produced:
  - [[Research - Plataforma Reportes Multi-Modulo + Modulo Lanzamiento Yogurt - 2026-04-20]] (20 external sources, 8 findings, 3 options)
  - [[Brainstorm - Plataforma Reportes + Lanzamiento Yogurt - 2026-04-20]] (10 locked decisions)
  - [[2026-04-20-001-feat-plataforma-reportes-multi-modulo-lanzamiento-plan]] (implementation plan with 7 phases + 16 SpecFlow gaps covered)
  - User flagged the pattern mid-session: "note this as a skill to develop for future spec writing assignments"
  - Retro observation on Stage 1 ordering baked into the pipeline's Hard rule

## Related

- **Memory:** `feedback_spec_writing_workflow.md` — collaboration preferences for this workflow
- **Related skills:** `/last30days`, `/deep-research`, `superpowers:brainstorming`, `compound-engineering:ce-plan` (creation + deepening), `/ce:work`
- **Conventions:** `feedback_plans_to_inbox.md`, `feedback_deliverables_to_inbox.md`, Bufalinda Rule 1 (Spanish)
