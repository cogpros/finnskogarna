# HISTORY: How Finnskogarna Grew an Eye

This is the evolution narrative behind finnskogarna's version line. The SKILL.md is the spec; this file is why the spec looks the way it does: the failures that forced each addition, and the convergence that gave the skill its most recent organ.

---

## Origin: primitive 16, `/accordion` (pre-0.1.0)

Before finnskogarna existed, `/accordion` did one thing: compress a single finished thread mid-session so the operator could clear the deck without losing it. One note, one clearing in the woods.

## v0.1-0.3: the multi-session fold

The real overwhelm wasn't one thread. It was N parallel Claude Code sessions piling up faster than any one operator-seat could hold. Finnskogarna was built as the two-level extension: compress each session independently, diff the compressions, wake exactly one, seal or hold the rest. **Never merge** is the hard constraint that exists because merging N contexts into one doesn't reduce load, it concentrates it.

**Convergence proof (2026-05-14).** Six parallel sessions, asked the same meta-question ("what do you think about my overwhelm?"). Five of six escalated into generating an orchestrator-prompt for a hypothetical seventh session instead of reporting their own state. That 5-of-6 pattern is what canonicalized finnskogarna's hardest constraint: a session asked to fold must give ITS OWN STATE, never a meta-prompt. The failure proved the skill was necessary before the skill had a name.

## v0.4.0 (2026-06-09): Fable 5 review hardening

Two field-tested fixes from external review:
- **Pre-set route verdicts.** First real fold (2026-06-07) surfaced 13 wake/seal/HELD verdicts and the operator set 1, deferred 12. Blank-slate decision volume exceeded what a depleted decision faculty can spend in one sitting, so route toggles now ship pre-set to the machine-proposed verdict, and the operator flips exceptions instead of building from zero.
- **Deadline override.** HELD's avoidance-quieting is a positive feedback loop by design (quieter, less reopened, quieter), correct for a pure emotional burn, wrong for a thread carrying a statutory deadline. Deadline proximity now sets a volume floor that overrides avoidance-quieting, because deadlines don't track readiness.

## v0.5.0-0.5.1: the consume-convention and Mode C

Discovered 2026-06-19: reports that get answered *inside* the report (paste-back, localStorage) leave no disk trace, so the same "open" report re-surfaces in every future fold even after the operator did the work. Twelve open reports, several already worked, none consume-marked. Fixed by requiring a `<slug>-verdict.md` written next to every consumed report; the loop only closes when an answered-marker lands on disk. Mode C (open-report reconciliation) exists specifically to enforce this convention.

## v0.6.0 (2026-06-19): Mode B, the historical fold

Not every overwhelm is live. Mode B lets finnskogarna reconstruct what's in flight across a time window of already-closed sessions, compressing from transcripts instead of probing live state.

## v0.7.0 (2026-07-08): Two Birds folds in as Phase 3.5

**The gap.** Five phases (probe, seal, harvest, offload, verdict) compress and route, but none of them *evaluate*. Wake/seal/HELD is a logistics decision. Nothing in the pipeline asked whether the work actually cohered, or whether this arc was a repeat of a fight already fought.

**The convergence.** Two Birds (a separate blind-owls-plus-rift arc evaluator) already existed as a standalone daily ritual: two owls read an arc blind, one for this-arc coherence, one for all-time recurrence, and a rift step surfaces where they diverge. Running alongside it, unconnected, finnskogarna's own HELD mechanism had an `avoidance_score`: a number that auto-routes a thread to quarantine when the operator keeps not reopening it.

The recognition that closed the arc: **these are the same organ, reached two different ways.** The all-time recurrence owl's read is a *voice* naming "you've fought this before." The `avoidance_score` is a *number* detecting the same avoidance pattern from behavior instead of language. Rift is why-to-HELD. Score is that-to-HELD. One faculty, two representations.

**The re-cut that made the fold safe.** Two Birds originally ran sequential hand-off (one owl reads, then hands to the next), a design that let the second owl anchor on the first owl's framing instead of reading blind. Before folding it in, the design was re-cut to **blind-parallel plus rift**: both owls read the arc independently, and only the rift step compares. Merge-free, no anchoring contamination, the same "never merge" discipline finnskogarna already enforces on sessions, now applied to the owls reading them.

**The build.** Two Birds slotted into the existing pre-set-to-commit seam between Harvest (Phase 3) and Verdict (Phase 5) as **Phase 3.5: Evaluation**, conditional: it fires only on arc-shaped folds (an `--arc-label`, or explicit operator call), never on routine housekeeping. The gate itself follows Axiom 0: the arc-label auto-fires from the fold's own shape rather than waiting on the operator to remember to summon the owls, because gating a prosthetic on the initiation deficit it exists to bypass defeats the point.

**What this means going forward.** Two Birds is no longer a standalone daily ritual competing for a separate slot in the operator's day. It's the evaluation faculty finnskogarna was always missing; the owls live in the woods now, not beside them. The original standalone sequential-hand-off tool is retired in favor of this fold; the name and lineage carry forward here, not as a parallel project.

---

## Screenshot slots

*(Reserved for the public promotion pass. Drop images here when captured.)*

1. **Phase 3.5 gate in action**: an arc-labeled fold firing Two Birds vs. a routine fold skipping it silently.
2. **The rift dashboard**: a rendered rift showing genuine owl divergence with the ruling pending.
3. **Rift-to-HELD reconcile**: a Phase 3 route pre-set flipped to HELD by a converging owl-read and elevated `avoidance_score`.

---

## Version line at a glance

| Version | Date | What landed |
|---|---|---|
| pre-0.1.0 | n/a | `/accordion`: single-topic compress, primitive 16 |
| 0.1-0.3 | n/a | Multi-session fold (compress, compare, choose); never-merge constraint |
| 0.4.0 | 2026-06-09 | Pre-set route verdicts; deadline override on HELD |
| 0.5.0-0.5.1 | 2026-06-19 | Consume-convention; Mode C (open-report reconciliation) |
| 0.6.0 | 2026-06-19 | Mode B (historical fold from transcripts); `/accordion` absorbed |
| 0.7.0 | 2026-07-08 | Phase 3.5: Two Birds (blind owls + rift) as the evaluation faculty |
