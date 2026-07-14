---
name: decision-records
description: Record, consult, and review the user's significant decisions in DECISIONS.md ledger files (the DECISIONS.md format). Use this skill whenever the user says "log this decision", "record this decision", "we've decided", "记决策", "记一下这个决策", "拍板"; right after a significant technical choice, architecture tradeoff, or priority call is made in conversation — even if the user doesn't mention recording; when the user overrides a model recommendation; when the user asks "why did we choose X", "what did we decide about this", "当时为什么选"; before starting significant work in a project that has a DECISIONS.md; or when the user asks for a decision review. Pure progress logging ("log today's progress") is not this skill — but offer to record any qualifying decisions at session end.
---

# Decision Records

Execution got cheap; judgment is the bottleneck. This skill turns judgment into an accumulating asset: each significant decision is recorded with its alternatives, its reasons under the constraints of the time, who decided it, and the conditions under which it should be revisited. Future sessions cite it instead of re-litigating; when the environment changes, entries trip their own alarms.

**The format is the product; this skill is just a writer.** DECISIONS.md files are plain markdown with a protocol header (see `templates/DECISIONS-template.md`) — any agent, any vendor, can maintain them without this skill. Keep the header intact whenever you edit a decisions file.

**The first risk is write-only.** A ledger nobody reads at decision time is worthless. The read-path hook below (registration line) matters more than any writing ceremony.

## Where the file lives

1. Default: `DECISIONS.md` at the repo root. **When creating it, also add one registration line to the repo's AGENTS.md (or CLAUDE.md):** "Before significant changes, read the Active section of DECISIONS.md. Follow its protocol header when editing it." Without this line, passing agents cannot discover the ledger.
2. **Defer to existing homes.** If the project already keeps decision records (an ADR directory like `docs/decisions/`, an initiative `decisions.md`), point there and follow the existing format — never create a second ledger. The useful additions are usually just the fields those formats lack: decided-by, regret-when, review.
3. **Decisions follow the project, not the folder.** Multi-repo projects keep one ledger (in a designated primary repo or an external state directory); member repos carry a one-line pointer in their instruction files.
4. If the user runs a cross-project state layer (e.g. the project-portfolio skill), honor its per-project `decisions:` pointer field, and register the monthly review as a standing trigger in its watchlist. Without a state layer, reviews are user-invoked ("review my decisions") — say so honestly when setting up.

## What counts as a decision

Record when **any** of these hold: (1) high stakes × hard to reverse; (2) it settles a question that will recur; (3) the user overrode a model recommendation — a high-signal taste sample; (4) a model default was accepted unreviewed on something consequential — mark it `model-default, unreviewed`.

Do not record: reversible implementation details, or **conventions** (commit style, formatting — those are law: they belong in the instruction file, not the ledger). The ledger stores rulings, not rules. When unsure: three months from now, would "why was it done this way?" deserve this entry as its answer?

## Entry format

```markdown
### D-260712-sqlite — Use SQLite, not Postgres, for local state
- status: active
- decided-by: user-ratified (Claude recommended)
- alternatives: Postgres (needs a server) / JSON files (corrupt under concurrent writes)
- reasoning@constraints: single machine, single user, no deploy budget, <1GB data
- regret-when: multi-device sync needed, or data >10GB
- review: 2026-10-12
```

- `status`: `active` / `superseded-by D-xxx` / `expired`. The **only** mutable field — entries are append-only; overturning a decision means a new entry plus a status change on the old one. A ledger whose history can be rewritten cannot calibrate anyone.
- `decided-by`: `user-ratified` / `model-recommended, user-accepted` / `model-default, unreviewed`. Provenance of authority — the field that instruction files and agent memory both lack.
- `regret-when`: must be checkable in one sentence at review time ("data >10GB" qualifies; "users unhappy" does not). Omit when no honest observable exists — never fake an alarm.
- `review`: a filter key for batch reviews, not a reminder.

## Workflow: record

1. **Draft from evidence.** Alternatives, reasons, constraints come from the actual conversation or project material. Never invent or backfill a reason — if it wasn't said, ask, or leave the field empty.
2. **User ratification before writing, zero exceptions.** Show the draft; write only after confirmation. In non-interactive sessions (scheduled tasks), do not touch the decisions file — leave a one-line note in the project's state file or session summary ("1 pending decision: <summary>") for the next interactive session to ratify. Other agents can't interpret unratified entries; the file must contain only what the user approved.
3. Append to the Active section; add a dated history line to the project's state file if one exists.
4. Proactivity budget: interrupt mid-session **at most once** — spend it on the highest-stakes decision point; batch the rest into a session-end offer. Alert fatigue kills recording systems faster than missed entries do.

## Workflow: consult

- Scan the Active section when starting significant work in a project that has a ledger.
- "Why did we choose X?" → answer from the entry's reasoning and constraints; check in passing whether its regret condition has since come true — if so, propose `expired` and a fresh decision.
- **About to violate an active decision? Surface and ask** — "Decision X stands because Y; this change conflicts. Does Y still hold?" Never silently comply, never silently violate. Entries are precedents, not statutes.

## Workflow: review

Monthly (via the state layer's standing trigger, or when the user asks):

1. Collect entries with `review <= today`. Score each — **right / wrong / too early to tell** — with one line of real evidence. Extend too-early entries one cycle.
2. Update statuses; move non-active entries to Archive. If Active exceeds ~20 entries, walk them down one by one (expire, supersede, or re-confirm with the user) — no merging, no rewriting.
3. Keep a running decided-by × outcome tally. **Accumulate only** — no behavioral conclusions from single-digit samples.

## Workflow: promote

A judgment recurring in **≥3 independent decisions across different projects** (restatements of one principle inside one system count once) may be promoted, with the user's approval, to their global instruction file as a single line with inline provenance: `- one source of truth; aggregate by reference (2026-07, from projA/projB/projC decisions)`. No intermediate stores, no mirrors. Demotion is manual — if a global line contradicts recent decisions, mention it once.

## Extraction from transcripts

When drafting entries from a session (or backtesting history): structured multiple-choice exchanges are pre-structured decision records — scan them first; user turns are the recall anchors, but expand context by dialogue structure, not fixed length; a user reply that overrides a recommendation carries criteria the model lacked — record it fully; silent model defaults have no user turn — find them in a separate pass; settle to end-of-session state with supersede chains, not play-by-play.

Full format details: see SPEC.md in the decision-records repository (linked from the protocol header).
