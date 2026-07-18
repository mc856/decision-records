# decision-records

> Your agents follow instructions. Give them case law.

**Status: pre-release draft.** Spec v0.1-draft — names, fields and wording may change. Feedback via issues is welcome, especially "here's how I'd use this" stories.

## The problem

You work with coding agents every day. Decisions get made constantly — some by you, many by the model. Then:

- A settled choice gets **re-litigated** three sessions later, because nothing tells the next agent it was already decided — and why.
- The model picks a library, an architecture, a tradeoff — and nobody notices a decision was even made. Six months later: *"why is it built this way?"*
- Your instruction files say **what** to do, stripped of the why. Your agent's memory holds impressions nobody ratified. Neither can tell you **when a decision stops being valid.**

Agent harnesses have converged on two memory layers: instructions (law) written by you, and auto-memory (impressions) written by the agent. Claude Code has CLAUDE.md + auto memory; Codex has AGENTS.md + `~/.codex/memories/`. **Both are missing the third layer: case law.**

|                        | Instructions      | Agent memory     | **DECISIONS.md**                        |
| ---------------------- | ----------------- | ---------------- | --------------------------------------- |
| Written by             | you               | the agent        | agent drafts, **you ratify**            |
| Carries reasons        | no                | sometimes        | **always, with constraints-at-the-time** |
| Provenance (who decided) | —               | —                | **user-ratified vs model-default**      |
| Expiry                 | rots silently     | rots silently    | **regret conditions + review dates**    |

## The format

One `DECISIONS.md` per project. Entries are dated, ratified, append-only:

```markdown
### D-260712-sqlite — Use SQLite, not Postgres, for local state
- status: active
- decided-by: user-ratified (Claude recommended)
- alternatives: Postgres (needs a server) / JSON files (corrupt under concurrent writes)
- reasoning@constraints: single machine, single user, no deploy budget, <1GB data
- regret-when: multi-device sync needed, or data >10GB
- review: 2026-10-12
```

Three rules make it a ledger, not a wiki:

1. **Append-only.** The only mutable field is `status` (`active` → `superseded-by D-xxx` / `expired`). History never gets rewritten — hindsight loves to edit its own past.
2. **Ratified-only.** Nothing lands without human sign-off. Agents draft; you decide.
3. **Falsifiable.** An entry may carry a regret condition — one checkable sentence that says, in advance, what would invalidate it.

Every file carries a protocol header, so any agent — any vendor, no install — can maintain it correctly. See **[SPEC.md](SPEC.md)**.

## Why not ADR?

This *is* the decision-record tradition (ADR, MADR), extended for the delegation era. What's new: **decided-by** (was this ratified, or did a model default slip through?), **regret conditions** (decisions that can expire), **review dates** (decisions that get scored). It coexists with ADRs — if your repo already has `docs/decisions/`, keep it canonical; this format defers to existing homes rather than founding a second one.

## Why not memory?

Memory is written by the agent: unratified, no provenance, no expiry. Fine for impressions; not fine for commitments. A preference in memory is an opinion. An entry in DECISIONS.md is a ruling.

## Adoption tiers

- **Tier 0 — just the format.** Add one registration line to your AGENTS.md / CLAUDE.md ("Before significant changes, read the Active section of DECISIONS.md"). Any agent maintains the file by following its protocol header. Zero install.
- **Tier 1 — the skill.** [`skills/decision-records/`](skills/decision-records/) for any agent supporting the Agent Skills standard (Claude Code, Codex, Cursor, Cowork, …): record (draft-from-conversation, then ratify), consult (surface before violating, never silently obey), review (monthly scoring and gardening).
- **Tier 2 — with a state layer.** Pairs with a cross-project tracker for monthly review triggers and promotion of repeated judgments into your global instruction file.

## Install

**Any agent (recommended)** — auto-detects Claude Code / Codex / Cursor and installs to each:

```bash
npx skills add mc856/decision-records
```

**Claude Code plugin marketplace:**

```
/plugin marketplace add mc856/decision-records
/plugin install decision-records
```

**Codex (manual):**

```bash
git clone https://github.com/mc856/decision-records /tmp/dr && cp -r /tmp/dr/skills/decision-records ~/.agents/skills/
```

**Claude Cowork:** download `decision-records.skill` from [Releases](https://github.com/mc856/decision-records/releases) and drop it into a chat — it installs via the "Save skill" button.

**Tier 0 (no install):** copy the protocol header from [SPEC.md §3](SPEC.md) into a `DECISIONS.md`, add the registration line to your AGENTS.md, done.

## Dogfood

This repo keeps its own [DECISIONS.md](DECISIONS.md) — the design decisions of the format, recorded in the format, including two decisions that were overturned by an independent design audit. The supersede chain is the demo.

## License

MIT
