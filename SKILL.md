---
name: deep-planning
version: "0.1.0"
description: Mode-based router for any complex planning exercise prior to executing. Three modes (Quick / Build / Strategic) calibrate ordering, skill selection, and Compound Engineering integration to the intent and complexity of the work. Replaces /spec-writer (hard-retired 2026-05-08). Use when the user says "spec out X", "design the system for X", "let's plan X properly", "scope this sub-project", "evaluate", "explore", "from scratch", "rethink", or returns to an in-progress /project-create with non-trivial complexity.
user-invocable: true
argument-hint: "topic description (short sentence of what to plan)"
---

# Deep Planning — Mode-Based Composite Pipeline for Complex Planning

Routes a planning assignment through one of three modes, each encoding ordering, skill selection, and CE patterns appropriate to its intent. Terminal output is an implementation plan or engagement plan ready for execution (`/ce:work` for code, manual handoff for non-code).

## When to use

Use when the user is about to start serious work on a non-trivial task and wants structured thinking before touching execution. Triggers: "spec out", "design the system for", "let's plan X properly", "scope this", "think through X before building", "evaluate", "explore", "first principles", "should we", "rethink", "from scratch."

**Also valid for non-code engagements** — career coaching, multi-month consulting, structured guidance programs, AIAC participant cases, friend-mentorship engagements, anything where Stage 4 produces an *engagement/operating plan* rather than an *implementation plan*. Validated 2026-05-02 with [[JV Career Reboot]] (run under the predecessor `/spec-writer`) — full pipeline produced research → brainstorm → plan → project-create with no skill modifications. Stage 4 plan structure adapts naturally (Outcome / Engagement Model / Workstream Architecture / Milestone Arc / Component Build / Tool Integration / Meeting Template / Pre-M0 Deliverables / Success Metrics / Failure Modes / Privacy / Budget / Artifacts / Risks / Cross-Pollination / Sources). Trigger phrases for non-code use: "design a 6-month engagement for X", "plan a structured advisory for X", "scope a coaching program for X".

Do NOT use for:
- Simple bug fixes or single-file changes (overkill — go straight to implementation)
- Brainstorming-only sessions where no plan output is wanted (use `/ce-brainstorm` directly)
- Emergency hotfixes (time cost too high)
- Single-conversation deliverables (a one-off email draft, a single document — pipeline overhead exceeds value)

## Modes

Three modes partition the planning intent space. Each has its own chain, ordering rule, and default skill choices.

| Mode | Intent | Order rule | Terminal artifact | Typical runtime |
|---|---|---|---|---|
| **Quick** | Small, well-scoped feature/decision; user wants fast structured output | Skip research stages | Implementation plan or decision note | 15–30 min |
| **Build** | Internal/operational sub-project; the org's own context dominates | Recency-first: `/last30days` → `/deep-research` → brainstorm → plan | Implementation plan | 40–90 min |
| **Strategic** | Novel/external/exploratory work; first-principles framing precedes recency | Strategy-first then research-first: `/ce-strategy` → `/deep-research` → `/last30days` → ideate → brainstorm → plan → audit | Implementation/engagement plan + multi-lens audit | 90–180 min |

### Mode chains

#### Quick mode

```
superpowers:brainstorming  →  /ce:plan
```

- No research stages — Quick assumes context is already clear.
- **Brainstorm = `superpowers:brainstorming`** (see Brainstorm skill selection below). Design-doc shape is sufficient at small scope. Manual handoff override required (`writing-plans` → `ce:plan`) — same pattern as the predecessor.
- Output: implementation plan in `00 HUB/00 INBOX/`.

**Use when:** scope is small, direction is clear, user wants minimum structured-thinking overhead. Below this threshold, don't invoke `/deep-planning` at all — just prompt directly or call `/ce-brainstorm` + `/ce:plan` standalone.

#### Build mode

```
/last30days  →  /deep-research  →  [opt-in /find-repos]  →  [ce-brainstorm | superpowers:brainstorming]  →  /ce:plan  →  [opt-in /ce-doc-review]
```

- **Order = recency-first.** Internal operational context (Bufalinda blockers, recent decisions, in-flight work) shapes what's worth researching externally. If `/last30days` surfaces a blocker at a data source, the research stage pivots from "framework Y" to "workarounds for that data source." Putting research first risks burning the research budget on the wrong questions. (Codified from a 2026-04-20 retro on the predecessor `/spec-writer`.)
- **Stage 2.5 — `/find-repos` (opt-in, auto-suggested for code work).** After `/deep-research` surfaces state-of-the-art, `/find-repos` materializes that into a scored shortlist of actual GitHub repos (6-dimension scoring: maintenance, security, license, community, fit, hygiene). Makes the adopt-vs-build check explicit before brainstorming. Auto-suggest the run when `/deep-research` surfaces multiple existing tools/approaches OR when the work is clearly code-implementation-bound. Skip when work is non-code (career engagements, business strategy, internal-data-unique automations) or when reuse has already been ruled out.
- **Brainstorm = conditional on work properties** (see Brainstorm skill selection below). Default for typical Build work = `ce-brainstorm` (clean native handoff to `ce-plan`, no manual handoff override). Switches to `superpowers:brainstorming` when triggers fire.
- **Stage 5 audit = opt-in.** At end of Stage 4, ask: "Run multi-lens audit (`/ce-doc-review`)? Recommended when plan crosses 500+ LOC, modules, or teams." Default suggestion for Build = no.
- **Output paths** — all artifacts to `00 HUB/00 INBOX/`.

**Use when:** sub-project inside an existing operational context (Bufalinda, an Area you already manage, an AIAC participant case, an in-flight Program). This is the predecessor `/spec-writer` chain with two changes: brainstorm-skill swap (default to `ce-brainstorm` for clean handoff) and explicit `/find-repos` reuse check.

#### Strategic mode

```
/ce-strategy  →  [optional /ce-ideate]  →  /deep-research  →  /last30days  →  superpowers:brainstorming  →  /ce:plan  →  [opt-in /ce-doc-review]
```

- **Order = strategy-first, then research-first.** `/ce-strategy` writes/refines the strategic anchor; `/deep-research` then anchors first-principles framing; `/last30days` enters third, scoped by what research surfaced.
- **Stage 0 — `/ce-strategy` (always-on).** Per CE methodology, `/ce-strategy` is the upstream anchor read by ce-ideate, ce-brainstorm, and ce-plan as grounding. Creates or refines the strategic doc for this work. **Vault adaptation:** in CE the artifact is `STRATEGY.md` at repo root; in vault context, the natural strategic anchor is the project brief, Area MOC, or wiki page. Stage 0 logic:
  - (a) if a relevant project brief / area MOC exists, refine its strategic framing section in place;
  - (b) if no anchor exists for this work, create a new strategic context doc at the appropriate level (`01 PROJECTS/<topic>/<topic>.md` or `02 AREAS/<area>/STRATEGY.md`);
  - (c) load the resulting anchor as context for subsequent stages.
- **Stage 0.5 — `/ce-ideate` (optional).** When the user can't yet name what they're building even after strategy framing, generate and adversarially filter directions before sinking research budget into one. Skip when intent is already locked by Stage 0.
- **Brainstorm = `superpowers:brainstorming`** by default. Strategic mode meets the "strategic in nature" trigger by definition; the visual companion phase is also valuable for novel-domain exploration. Multi-approach proposal + design-spec output suit exploratory work better than `ce-brainstorm`'s requirements-doc framing. Tradeoff: requires the manual handoff override (`writing-plans` → `ce:plan`).
- **Stage 5 audit = opt-in (recommended).** At end of Stage 4, ask: "Run multi-lens audit (`/ce-doc-review`)? Recommended for Strategic scope — 7 parallel persona reviewers (coherence, feasibility, product, design, security, scope-guardian, adversarial) tend to surface category-level gaps a linear pipeline misses." Default suggestion for Strategic = yes; user can decline.
- **Output paths** — all artifacts to `00 HUB/00 INBOX/`.

**Use when:** entering a novel domain, evaluating a strategic pivot, scoping a new program, exploring "should we" before "how do we." Examples: a new investment thesis, a new line of business, a structural rework, a domain you don't yet have a wiki for.

### Mode selection (Step 1)

Step 1 (Parse + confirm scope) proposes a mode based on triggers in the topic description, then asks the user to confirm or override before any stage runs. Triggers:

- "spec out", "design the system for", "scope this sub-project" → **Build**
- "evaluate", "explore", "first principles", "should we", "rethink", "from scratch", external/novel domain → **Strategic**
- "quick plan", "small feature", "just give me a plan" → **Quick**
- Default when ambiguous → **Build** (preserves predecessor behavior)

Always present the proposed mode AND the proposed brainstorm skill with one-line rationale; user confirms or overrides.

### Brainstorm skill selection (cross-mode rule)

The brainstorm-stage skill is chosen based on **work properties**, not mode alone. Modes specify a *default*; the rule below overrides when triggers fire.

**Rule:** use `superpowers:brainstorming` when ANY of these apply:

- **Visual mockups would help** — UI/UX work, layout-heavy specs, anything where a sketched-up visual companion materially improves understanding. (`superpowers:brainstorming` has a built-in "visual companion" phase that `ce-brainstorm` lacks.)
- **Mega-scope** — extremely large, foundational, or rearchitectural — where multi-approach proposal and design-spec richness pay off more than requirements-doc rigor.
- **Strategic in nature** — novel framing, exploratory "should we" questions, entering a domain you don't yet have a wiki for.

Otherwise, use `ce-brainstorm` (default for mid-scope operational work — produces a requirements doc that `ce-plan` reads natively, no manual handoff override).

**Implied curve:** `superpowers` for small scope (Quick — design-spec is sufficient); `ce-brainstorm` for mid-scope operational work (Build default — right rigor); `superpowers` again for large/strategic/visual work (Strategic, Build exceptions — exploratory richness needed). `ce-brainstorm` sits in the middle of the curve, not at the top.

## Steps

### Step 1 — Parse the topic, propose mode + brainstorm skill, confirm scope

Read the user's topic description. Restate in one sentence. Propose mode + brainstorm skill with one-line rationale. Ask the user to confirm before running any stage:

> "Running `/deep-planning` for: *<topic>*.
> Proposed mode: **<Quick | Build | Strategic>** because <rationale>.
> Proposed brainstorm skill: **<superpowers:brainstorming | ce-brainstorm>** because <rationale>.
> Pipeline will produce artifacts at each stage. Proceed? [Y / different mode / different brainstorm / scope decomposition needed]"

If the topic is ambiguous (covers multiple independent subsystems), flag it and decompose before running the pipeline. A plan per subsystem is cleaner than one mega-plan.

### Step 2 — Run the mode chain in order

Each mode's chain runs stage-by-stage, passing context forward explicitly. See "Mode chains" above for the per-mode stage list.

**Context forwarding is explicit.** Each stage's prompt to the next includes a summary of the prior stage's findings. Don't rely on the subagent to infer context.

**Output overrides per stage:**

- `/deep-research` → already defaults to `00 HUB/00 INBOX/Research - <Topic> - YYYY-MM-DD.md` (no override needed)
- `superpowers:brainstorming` → defaults to `docs/superpowers/specs/`. Override to `00 HUB/00 INBOX/Brainstorm - <Topic> - YYYY-MM-DD.md` per `feedback_plans_to_inbox.md`. Override the terminal handoff from `writing-plans` to `compound-engineering:ce-plan`.
- `ce-brainstorm` → defaults to `docs/brainstorms/`. Override to `00 HUB/00 INBOX/Requirements - <Topic> - YYYY-MM-DD.md`.
- `compound-engineering:ce-plan` → defaults to `docs/plans/`. Override to `00 HUB/00 INBOX/YYYY-MM-DD-NNN-<type>-<name>-plan.md`.
- `/ce-doc-review` → no separate output file by default; integrate findings into the plan or surface in conversation.

### Step 3 — Brainstorm interaction shape (for any brainstorm skill choice)

The brainstorm stage has two interaction shapes; both are wrapper-level patterns that work with either underlying skill (`superpowers:brainstorming` or `ce-brainstorm`):

**Standard interactive walkthrough.** Walk the user through 5–10 open architectural questions one at a time, multiple-choice preferred. Each option names the priority it optimizes ("Option A optimizes YAGNI; Option B optimizes no-tech-debt"). Default for low-context users or open-ended exploration.

**Framing-Document Mode.** When ALL of the following are true, prefer drafting a "Pre-Brainstorm Framing Questions" file in INBOX over the standard interactive walkthrough:

- The architectural decisions are **clearly enumerable** (each has 2–4 named options, not open-ended exploration)
- The user is **high-context** (deeply familiar with the domain — no need for guided exposition)
- The user is **async-friendly** (can scan a structured document and respond in one message)

Pattern: Write `00 HUB/00 INBOX/Brainstorm - <Topic> - YYYY-MM-DD.md` as a Decision Ledger with each question presented as: question → options table → recommended default → one-line rationale → blank "Decision: _[awaiting user]_" line. User scans the whole document and confirms / corrects in a single response. Faster than the question-by-question walk for the right user; slower for low-context or open-ended exploration.

Validated 2026-05-02 with [[JV Career Reboot]] — Albert answered 10 framing decisions in one message AND inserted a substantive sales-first re-centering that wouldn't have surfaced cleanly in a standard interactive walk (because it touched multiple questions simultaneously, hard to introduce mid-walkthrough). The framing-document gave him a "whole map" view to reshape from.

**When to fall back to standard interactive mode:** decisions are open-ended (not multiple-choice), user wants to think through one at a time, lower-context user needs guided exposition, OR user explicitly asks for the walkthrough.

**Output:** identical to standard mode — a Decision Ledger that feeds Stage 4. The mode only changes the *interaction shape*, not the artifact.

### Step 4 — Plan stage (`compound-engineering:ce-plan`)

Invoke `compound-engineering:ce-plan` with the brainstorm doc as the origin document. The skill produces a structured implementation plan with acceptance criteria, phases, risks, and sources.

**Include in plan's origin chain:**
- `origin:` field → research brief from earlier stage
- `related:` field → brainstorm doc from Stage 3 (or Strategic-mode strategic anchor)
- Plan's Sources & References section cites all upstream artifacts

**Ultrathink default:** if ultrathink was enabled for the session, automatically run a deepening pass — re-invoke `compound-engineering:ce-plan` with the plan path and "deepen this plan" intent. This triggers ce-plan's Phase 5.3 Confidence Check in interactive mode. Per memory `feedback_deepen_plan_inline_default.md`, integrate findings inline with `[DP:N]` tags by default.

### Step 5 — Audit stage (opt-in `/ce-doc-review`)

At end of Stage 4, ask the user about audit per the mode default:

- **Strategic:** "Strategic-scope plan complete. Run multi-lens audit (`/ce-doc-review`)? 7 parallel persona reviewers (coherence, feasibility, product, design, security, scope-guardian, adversarial) tend to surface category-level gaps a linear pipeline misses. **Recommended for Strategic.** [Y/n]"
- **Build:** "Build-scope plan complete. Run multi-lens audit (`/ce-doc-review`)? Recommended only when plan crosses 500+ LOC, modules, or teams. [N/y]"
- **Quick:** Audit not suggested. (User can opt in via explicit ask.)

If user opts in, dispatch `/ce-doc-review` with the plan path. Integrate auto-tier findings into the plan; surface manual-tier findings in conversation for user decision.

### Step 6 — Terminal disposition (enriched menu)

After the plan lands (and audit if run), present a richer post-execution menu than the predecessor's disposition list. Surface skills that are commonly relevant immediately after planning, grouped by intent. **This is a *disposition menu*, not auto-invocation.** The skill presents options with one-line rationale; the user picks.

**Execute the plan**
- `/ce:work` — execute the plan against its guardrails (canonical Compound Engineering loop terminus)

**Refine the plan further**
- `/ce-doc-review` — multi-lens parallel audit (7 personas), if not already run in Stage 5
- `/ce:plan` deepening pass — re-invoke ce-plan with "deepen this plan" intent for Phase 5.3 confidence check
- Open in Proof for human-in-the-loop review (`/ce-proof`)

**Reuse before building**
- `/find-repos` — if not already run in Stage 2.5, surface as a reuse-check option

**After execution starts** (mention but do NOT auto-invoke)
- `/simplify` — once code is written, run as a focused pass for clarity/efficiency/reuse
- `/debug` or `/ce-debug` or `superpowers:systematic-debugging` — if bugs surface during execution
- `/code-review` or `/ce-code-review` — for PR-grade review before merge
- `/compile` or `/migrate-lessons` — fold execution learnings back into vault wikis (closes the compound loop)

**Defer**
- Leave plan in INBOX for later
- Create a Project brief from this plan (`/project-create`)
- Create an Issue tracker entry

**Note on cross-session skill surfacing.** Suggesting these skills proactively at `/session-close` or via continuation prompts is **out of scope for `/deep-planning`** — that capability lives in the separate [[Skill Surfacing Layer]] project (incubating).

## Rules

- **Mode is selected explicitly per run.** No silent defaults beyond the trigger-based proposal in Step 1. User confirms before any stage runs.
- **Order within a mode is non-negotiable.** Build = recency-first; Strategic = strategy-first then research-first; Quick = no research. If the user asks to skip a stage, record the reason in the plan's Sources section (so future readers know context is missing).
- **All intermediate artifacts land in `00 HUB/00 INBOX/`.** Override every child skill's default output path. This is a hard user convention (see `feedback_plans_to_inbox.md`).
- **Spanish vs English:** match the conversation language. If the plan will be reviewed with Bufalinda team members (JC, Manuel, Ronnel, etc.), output in Venezuelan Spanish (tuteo) per Bufalinda Rule 1.
- **Context forwarding is explicit.** Each stage's prompt to the next includes a summary of the prior stage's findings. Don't rely on the subagent to infer context.
- **Pivots are first-class.** If the user adds scope mid-brainstorming (new tab, new constraint), update the decision ledger inline. Don't defer to "final review."
- **Priority-naming in recommendations.** When the brainstorm proposes options, each option names the priority it optimizes ("Option A optimizes YAGNI; Option B optimizes no-tech-debt"). Prevents the user from having to reverse-engineer the tradeoff.
- **Parallel agents in Stage 4 scale with blast radius.** For plans touching 500+ LOC or crossing module boundaries, `/ce:plan` should dispatch repo-research-analyst + learnings-researcher + spec-flow-analyzer. For greenfield single-file features, skip.
- **Step 6 disposition is a menu, not auto-invocation.** Always wait for user pick. The richness of the menu reflects the breadth of useful next-step skills, not a recommendation that all of them should fire.

## Typical runtime

| Mode | Stages | Total |
|---|---|---|
| Quick | brainstorm 10–20 + plan 10 | 15–30 min |
| Build | last30d 5–10 + research 15–30 + [find-repos 10–20] + brainstorm 10–30 + plan 10–20 + [audit 15–30] | 40–90 min (≈115 with all opt-ins) |
| Strategic | strategy 10 + [ideate 15] + research 15–30 + last30d 5–10 + brainstorm 15–30 + plan 10–20 + [audit 15–30] | 90–180 min |

**Expensive in time but cheap compared to reworking a half-specced implementation.**

## Abort conditions

Stop the pipeline and surface the issue to the user if:
- A stage reveals a blocker that invalidates the topic (e.g., the data source isn't accessible)
- Research reveals the topic is already solved by an existing tool (redirect to adopt-not-build; trigger `/find-repos` if not already run)
- Brainstorm: user declines the architectural direction entirely (run brainstorming again with different framing)
- Plan: acceptance criteria cannot be made measurable (plan is too abstract; break into smaller plans)
- Audit (Strategic mode): persona-reviewer flags a fundamental scope problem (re-run brainstorm with audit findings)

## Evidence

Pre-rename evidence (under predecessor `/spec-writer`):

- **2026-04-20 Reporte Ventas multi-module platform spec** — first full pipeline run. Produced:
  - [[Research - Plataforma Reportes Multi-Modulo + Modulo Lanzamiento Yogurt - 2026-04-20]] (20 external sources, 8 findings, 3 options)
  - [[Brainstorm - Plataforma Reportes + Lanzamiento Yogurt - 2026-04-20]] (10 locked decisions)
  - [[2026-04-20-001-feat-plataforma-reportes-multi-modulo-lanzamiento-plan]] (implementation plan with 7 phases + 16 SpecFlow gaps covered)
  - User flagged the pattern mid-session: "note this as a skill to develop for future spec writing assignments"
  - Retro on stage ordering codified into the Build mode hard-rule

- **2026-05-02 [[JV Career Reboot]]** — non-code engagement plan validation. Demonstrated:
  - Pipeline works for non-code work (engagement plans, not just code specs)
  - Framing-document Stage 3 variant validated for high-context async users (10 framing decisions answered in one message)
  - User inserted a substantive sales-first re-centering mid-document that touched multiple questions simultaneously — would have been hard to introduce in standard interactive walkthrough

Post-rename evidence: see commit log for `/deep-planning` runs from 2026-05-08 forward.

## Dependencies

### Required
- `/last30days` — Stage 1 of Build, Stage 3 of Strategic. Recent-activity research across Reddit, X, web.
- `/deep-research` — Stage 2 of Build, Stage 2 of Strategic. Two-phase external research with sources and confidence grading.
- `superpowers:brainstorming` — default brainstorm skill for Quick and Strategic modes; conditional brainstorm for Build mode (when work meets visual / mega-scope / strategic-in-nature triggers).
- `compound-engineering:ce-brainstorm` — default brainstorm skill for Build mode. Produces a requirements doc that `ce-plan` reads natively.
- `compound-engineering:ce-plan` — Stage 4 in all modes. Structured plan creation with sub-agent decomposition; provides Phase 5.3 deepening pass invoked by ultrathink default.
- `compound-engineering:ce-strategy` — Stage 0 in Strategic mode. Writes/refines the strategic anchor.

### Optional
- `/find-repos` — Stage 2.5 in Build mode (opt-in; auto-suggested when work is code-implementation-bound and `/deep-research` surfaced multiple existing tools).
- `compound-engineering:ce-ideate` — Stage 0.5 in Strategic mode (optional; invoked when direction remains unclear after `/ce-strategy`).
- `compound-engineering:ce-doc-review` — Stage 5 in any mode (opt-in everywhere; default suggestion is yes for Strategic, no for Build, not suggested for Quick).
- `superpowers:writing-plans` — present in `superpowers:brainstorming`'s native handoff chain; this skill OVERRIDES that handoff to route to `ce-plan` instead.

### Vault Conventions
- [[Inbox Routing]] — all stage artifacts land in `00 HUB/00 INBOX/`
- [[Brief Template Compliance]] — when terminal output is a Project brief
- [[Plan Sync Protocol]] — when the resulting plan touches a `sync_status: production` document
- [[Vault Source-of-Truth]] — this skill itself lives in vault, symlinked to `~/.claude/skills/`
- [[Skill Dependencies Declaration]] — this section
- `feedback_plans_to_inbox.md` — output-path override convention
- `feedback_deepen_plan_inline_default.md` — ultrathink deepening pass behavior
- `feedback_deep_planning_workflow.md` — collaboration preferences (rewritten from prior `feedback_spec_writing_workflow.md`)
- Bufalinda Rule 1 (Venezuelan Spanish, tuteo) — when output reviewed with Bufalinda team

### Does NOT Require
- `/ce:work` — terminal disposition mentions this as a next-step option, but this skill does not invoke it
- `/simplify`, `/debug`, `/code-review`, `/compile`, `/migrate-lessons` — terminal disposition mentions these for after-execution use only; not part of any mode chain
- Cross-session skill surfacing infrastructure — that capability lives in the separate [[Skill Surfacing Layer]] project (incubating)

## Related

- **Memory:** `feedback_deep_planning_workflow.md` — collaboration preferences for this workflow (rewritten from prior `feedback_spec_writing_workflow.md`)
- **Predecessor:** `/spec-writer` (hard-retired 2026-05-08; this skill replaces it)
- **Compound Engineering core loop:** `/ce-strategy → /ce-ideate → /ce-brainstorm → /ce-plan → /ce-work → /ce-compound` — `/deep-planning` orchestrates the planning subset (strategy → brainstorm → plan); execution and compounding are downstream
- **Spinoff:** [[Skill Surfacing Layer]] — incubating project for cross-session skill awareness via `/session-close`, `/retro`, and continuation prompts
- **Stage skills:** `/last30days`, `/deep-research`, `/find-repos`, `superpowers:brainstorming`, `compound-engineering:ce-brainstorm`, `compound-engineering:ce-strategy`, `compound-engineering:ce-ideate`, `compound-engineering:ce-plan`, `compound-engineering:ce-doc-review`, `/ce:work`
- **Conventions:** `feedback_plans_to_inbox.md`, `feedback_deliverables_to_inbox.md`, `feedback_deepen_plan_inline_default.md`, Bufalinda Rule 1 (Spanish), [[Vault Source-of-Truth]]

## Migration note (2026-05-08)

This skill replaces `/spec-writer`. The rename is hard — no alias retained. If you typed `/spec-writer`, retype `/deep-planning` and select Build mode for the closest equivalent to the predecessor's behavior.

GitHub repo rename pending: `wildbuffaloman/cc-skill-spec-writer` → `wildbuffaloman/cc-skill-deep-planning`. Until that rename happens, `git push` from this skill folder targets the old repo URL — update via `git remote set-url origin <new>` after the GitHub rename.
