<!--
DECISIONS.md · protocol (any agent editing this file follows these rules — vendor-neutral)
Spec: v0.1-draft · https://github.com/mc856/decision-records
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

# DECISIONS.md: this format itself

The design decisions of this format, recorded in this format. Two entries below were
overturned by an independent design audit — the supersede chains are left visible on purpose.

## Active

### D-260713-repo-name — Repo named decision-records; case-law metaphor demoted to tagline
- status: active
- decided-by: user-decided (overrode the model's recommendation twice — first "caselaw", then the "agent-" prefix)
- alternatives: caselaw (vivid but opaque — requires common-law literacy to decode; co-designer of the concept still found it unrelated, and half the audience comes from civil-law countries) / agent-decision-records (category prefix redundant when every distribution channel already supplies the category — the "requests, not python-requests" principle) / decisions-md / who-decided
- reasoning@constraints: names get three seconds on a list scan, so the name must be transparent and the metaphor can ride in the tagline; dropping the prefix aligns repo = skill dir = frontmatter into one name; bare "decision-records" verified unoccupied on GitHub
- regret-when: cold-search traffic proves too weak because "decision records" queries resolve to the ADR tradition instead
- review: 2026-10-13

### D-260712-hybrid-routing — Decisions follow the project, not the folder
- status: active
- decided-by: user-ratified (Claude recommended, after five-dimension tradeoff analysis)
- alternatives: all files centralized in a state directory (loses cross-vendor visibility in repos) / all files in repos (no home for non-repo or multi-repo projects, privacy leaks in public repos)
- reasoning@constraints: owned single repos want in-repo files for agent visibility and commit atomicity; OSS contributions and non-repo projects cannot place files in others' repositories; public repos leak sensitive constraints
- regret-when: the routing pointer is frequently missing or wrong in practice, so lookups fail
- review: 2026-08-12

### D-260713-single-tier — One entry tier; the filter decides record-or-not
- status: active
- decided-by: user-ratified (independent audit recommended)
- alternatives: two tiers, with one-line "quick" entries for medium decisions
- reasoning@constraints: quick entries had no ID, no status, no lifecycle — orphans that only accumulate; the tier's own example failed the recording filter; conventions belong in instruction files anyway
- regret-when: recurring urge to record sub-threshold decisions with no place to put them
- review: 2026-09-13

### D-260713-monthly-review — Review dates are filter keys; one standing monthly trigger
- status: active
- decided-by: user-ratified (independent audit, finding H3)
- alternatives: one reminder trigger per entry
- reasoning@constraints: per-entry triggers flood the shared watchlist with scattered "too early to tell" reminders, training the user to ignore alerts — alert fatigue poisons the host system
- regret-when: a high-stakes decision misses its window because monthly cadence was too coarse
- review: 2026-09-13

### D-260713-ratified-only-on-disk — Non-interactive sessions never write the ledger
- status: active
- decided-by: user-ratified (independent audit, finding H4)
- alternatives: write entries with a "draft" status for later approval
- reasoning@constraints: other agents reading the file cannot interpret unratified entries — a label does not change what landing on disk means; draft debt accumulates awaiting a ratification ritual that may never come
- regret-when: asynchronously captured decisions are repeatedly lost before ratification
- review: 2026-09-13

### D-260713-promotion-single-layer — Promotion writes one line, inline provenance, directly to the global instruction file
- status: active
- decided-by: user-ratified (independent audit, finding M3; overturns D-260712-preferences-mirror)
- alternatives: intermediate preferences store + mirror line with backlink / no promotion mechanism at all
- reasoning@constraints: every synchronized layer is a discipline-dependent drift point; promotion frequency is single-digit per quarter, so manual demotion suffices; inline date + project names are enough to trace back to ledger entries
- regret-when: promoted lines outgrow one line, or provenance questions become frequent
- review: 2026-10-13

### D-260712-interrupt-budget — Proactive capture limited to one mid-session interruption
- status: active
- decided-by: user-ratified (Claude recommended)
- alternatives: record only when told to / propose whenever a decision is noticed
- reasoning@constraints: interruption cost is superlinear — alert fatigue then uninstall is the first cause of death for recording systems; zero proactivity misses the highest-value samples (model defaults slipping through unratified)
- regret-when: within two weeks, proposal acceptance rate <1/3, or clear misses of significant decisions
- review: 2026-07-26

### D-260712-backtest-over-evals — Real-transcript backtesting instead of synthetic eval cases
- status: active
- decided-by: user-decided (overrode the model's plan: 4 synthetic cases + control group + review page)
- alternatives: synthetic eval loop per the skill-creator workflow
- reasoning@constraints: historical transcripts are free real data whose extraction quality is directly human-checkable; synthetic cases have poor cost-benefit for a single-user private skill
- regret-when: the skill is distributed to other users (reproducible regression tests become valuable again) — fired 2026-07-13: the repo was published; assess at review
- review: 2026-07-20

### D-260718-oneshot-distribution — Distribution capped at one-shot channel setup, not sustained growth
- status: active
- decided-by: user-ratified (Claude recommended)
- alternatives: defer all promotion until the probe review (probes never get the minimum exposure needed to produce readings) / sustained growth operations (violates the pre-validation no-growth-investment discipline recorded in the private ledger)
- reasoning@constraints: a demand probe needs minimum exposure to read anything; scope fixed to one round of channel setup (skills.sh telemetry seeding, awesome-list PRs, one post per channel) plus feedback collection; metric verdicts stay with the monthly probe batch and the three-probe review
- regret-when: a second round of proactive distribution effort is spent before the 2026-08-15 review
- review: 2026-08-15

## Archive

### D-260712-two-tier-entries — Full entries plus one-line quick entries
- status: superseded-by D-260713-single-tier
- decided-by: user-ratified (Claude recommended)
- alternatives: single tier
- reasoning@constraints: writing cost seemed worth lowering for medium decisions
- regret-when: —
- review: —

### D-260712-preferences-mirror — Preferences store + CLAUDE.md mirror line
- status: superseded-by D-260713-promotion-single-layer
- decided-by: user-ratified (Claude recommended: "ledger for correctness, cache for the hot path")
- alternatives: direct write to global instruction file / no promotion
- reasoning@constraints: wanted full provenance chain and demotion mechanism for the highest-stakes memory tier
- regret-when: mirror and store drift out of sync
- review: —
