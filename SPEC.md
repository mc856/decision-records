# DECISIONS.md — Format Specification

**Version: 0.1-draft** · Pre-release; breaking changes possible. Feedback via issues.

## 1. Purpose and scope

A `DECISIONS.md` file records the **ratified decisions** of one project: what was chosen, what was rejected, the reasons under the constraints of the time, who decided, and the conditions under which the decision should be revisited or considered void.

It is the third memory layer for human–agent collaboration — **case law** — complementing instruction files (**law**: what to do, no why) and agent memory (**impressions**: unratified observations).

Scope rule: feature-level choices belong to existing repo conventions, or nowhere. Project-level decisions belong here. Universal preferences belong in your global instruction file — promoted only from repeated, independent decisions (§8).

## 2. File location and discovery

- Default: `DECISIONS.md` at the repository root.
- **Registration line (required for discovery)** in the repo's agent instruction file (AGENTS.md / CLAUDE.md):

  > Before significant changes, read the Active section of DECISIONS.md. Follow its protocol header when editing it.

  Without this line, passing agents have no way to know the ledger exists. A ledger that is written but never read has no value.
- **Deference rule**: if the project already has a decision-record home (an ADR directory such as `docs/decisions/`, an initiative `decisions.md`), keep that home canonical — do not create a second ledger. Adopt this spec's extra fields (decided-by, regret-when, review) inside the existing format if useful.
- Projects without a repository, or spanning several, keep the file in an external state directory. Decisions follow the **project**, not the folder; member repos carry a one-line pointer in their instruction files.

## 3. Protocol header (normative)

Every file begins with this header. Agents keep it intact.

```markdown
<!--
DECISIONS.md · protocol (any agent editing this file follows these rules — vendor-neutral)
Spec: v0.1-draft · <repo-url-here>
1. Entries are append-only; the only mutable field is "status".
   Overturning a decision = add a new entry + set the old one to superseded-by D-xxx.
2. Status values: active / superseded-by D-xxx / expired.
   This file contains only entries the user has ratified.
3. Entry fields: status, decided-by, alternatives, reasoning@constraints, regret-when, review.
   regret-when must be checkable in one sentence at review time; omit it otherwise.
4. Reasons must come from real conversation or material — never invent or backfill them.
5. When citing this file for a new decision: surface it to the user and ask.
   Never silently reuse an old decision for a new choice.
6. Reviews are handled in a monthly batch; this file carries no reminders of its own.
7. Non-active entries move to the Archive section. Active stays readable in one screen.
-->
```

## 4. Entry schema

Full form, ID pattern `D-YYMMDD-slug`:

```markdown
### D-260712-sqlite — Use SQLite, not Postgres, for local state
- status: active
- decided-by: user-ratified (Claude recommended)
- alternatives: Postgres (needs a server) / JSON files (corrupt under concurrent writes)
- reasoning@constraints: single machine, single user, no deploy budget, <1GB data
- regret-when: multi-device sync needed, or data >10GB
- review: 2026-10-12
```

Field semantics:

| Field | Meaning |
|---|---|
| `status` | See §5. The only field that may ever change. |
| `decided-by` | Provenance of authority. One of: `user-ratified` (optionally noting who recommended), `model-recommended, user-accepted`, `model-default, unreviewed` — the last marks consequential choices that slipped through without human review; they are first in line at review time. |
| `alternatives` | What was rejected, each with a word on why. Alternatives are what make the reasoning legible later. |
| `reasoning@constraints` | The why, **under the constraints of the time**. Constraints are what tell a future reader whether the reasoning still transfers. |
| `regret-when` | Falsifiability clause: one sentence checkable at review time ("data >10GB" qualifies; "users unhappy" does not). Omit when no honest observable exists — never fake an alarm. |
| `review` | Date used as a **filter key** by the monthly review batch (§8). Not a reminder. |

## 5. Status machine

```
active ──> superseded-by D-xxx   (a newer entry overturns this one)
active ──> expired               (regret condition observed true)
```

Append-only discipline: corrections and reversals are **new entries**; the old entry only changes status. There is no merge, no rewrite, no delete. A ledger whose history can be edited after the fact cannot be used for calibration — human memory already flatters its past beliefs; the file must not do the same.

## 6. Recording filter

A decision earns an entry when **any** of these hold:

1. High stakes × hard to reverse;
2. It settles a question that will recur;
3. The user overrode a model recommendation (high-signal for taste);
4. A model default was accepted unreviewed on something consequential (mark it so).

Not recorded: reversible implementation details; **conventions** (commit language, code style — those are law: put them in the instruction file). The ledger stores rulings, not rules. When unsure, ask: three months from now, would "why was it done this way?" deserve this entry as its answer?

## 7. Reading discipline (normative)

- Consult the Active section before work that could conflict with it.
- On conflict, **surface and ask**: "Decision X stands, because Y. This change conflicts. Does Y still hold?" — never silently comply, never silently violate.
- Never auto-apply an old decision to a new choice without asking. Entries are precedents, not statutes.

## 8. Review and promotion (non-normative)

- **Monthly batch**: collect entries with `review <= today`; score each — *right / wrong / too early to tell* — with one line of real evidence; update statuses; move non-active entries to Archive; extend the review date of *too-early* entries one cycle.
- **Calibration tally**: accumulate decided-by × outcome counts. Accumulate only — no behavioral conclusions from single-digit samples.
- **Promotion**: a judgment recurring in ≥3 **independent** decisions across different projects (restatements of one principle inside one system count once) may be promoted, with the user's approval, to the global instruction file as a single line with inline provenance: `- one-source-of-truth; aggregate by reference, never copy (2026-07, from projA/projB/projC decisions)`. No intermediate store, no mirrors — every extra synchronized layer is another discipline-dependent drift point.

## Appendix A — extraction hints (non-normative)

For agents drafting entries from live sessions or transcripts:

- Structured Q&A exchanges (multiple-choice question tools) are pre-structured decision records: question → title, options → alternatives, recommended-vs-chosen → decided-by and taste signal. Highest fidelity; scan first.
- User turns are the recall anchors — decisions are only ratified there — but expand context by dialogue structure, not fixed length: alternatives and reasons usually live in the preceding assistant turn; ratification may arrive several turns later.
- Disagreement outweighs agreement: a reply that overrides or amends a recommendation carries criteria the model lacked — record it fully. Rubber-stamp approvals matter for governance, not for taste.
- Silent model defaults have no user turn; find them with a separate pass over assistant-side choices that were never surfaced.
- Settle to end-of-session state: a decision overturned within the session is recorded once, with its supersede chain — the ledger stores rulings, not play-by-play.
