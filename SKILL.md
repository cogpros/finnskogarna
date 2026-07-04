---
name: finnskogarna
description: 'Master accordion: fold N parallel Claude Code sessions when they overwhelm. Compress each, diff, wake ONE, seal or HELD the rest (HELD = quarantined-but-present, surfaced as a daily count heartbeat). Never merge. Use when (1) "finnskogarna" / "fold the sessions"; (2) N≥3 sessions pile up; (3) overwhelmed by sessions; (4) pre-bed reconcile; (5) a thread burns and should be held, not cut. NOT for single-session checkpoint (/accordion), session close (use your session-close skill), reorientation (/whats-next).'
license: MIT
compatibility: Requires Claude Code. Works best with a session-close skill that emits a per-session fact sheet (see Dependencies). Tested on macOS.
user-invocable: true
metadata:
  version: "0.5.1"
  author: "dustinpollock"
  cogpros-primitive: 29
  cogpros-primitive-name: "Finnskogarna (Master Accordion)"
  lineage: "Livet i Finnskogarna (Carl Jularbo, 1893–1966, Malmö). The operator's great-grandfather's favorite accordion song."
  trigger-phrase: "finnskogarna"
triggers:
  - "finnskogarna"
  - "master accordion"
  - "fold the sessions"
  - "fold my sessions"
  - "sessions overwhelm"
---

# Finnskogarna — The Master Accordion

The master accordion. When N parallel Claude Code sessions converge into operator overwhelm, `/finnskogarna` runs a two-level fold: compress each session independently, diff the compressions, surface ONE wake-up thread, seal the rest cold-resumable. Never merge contexts. Always pick.

Named for *Livet i Finnskogarna* (Life in the Finn Forests) — the Carl Jularbo accordion waltz that the operator's great-grandfather played. The song is about Finnish settlers who built lives in distinct clearings out in the Swedish woods, each clearing keeping its own shape. Each parallel session is a settlement. The skill is the operator standing above the woods, hearing each clearing's signal, deciding which one breathes next.

Three-count: **compress · compare · choose.**

---

## Configuration

The skill reads and writes a handful of directories. Set these to fit your machine; the defaults below are used throughout this document.

| Variable | Purpose | Suggested default |
|---|---|---|
| `$FINN_STATE_DIR` | Skill state (held-shelf, run log, counts) | `$HOME/.finnskogarna` |
| `$FINN_REPORTS_DIR` | Drop zone for interactive HTML reports | `$HOME/reports` |
| `$FACT_SHEETS_DIR` | Per-session fact sheet storage (written by your session-close skill) | `$HOME/.finnskogarna/fact-sheets` |
| `$SEALS_DIR` | Per-session seal records (written by your session-close skill) | `$HOME/.finnskogarna/seals` |
| `$FINN_GOALS_DIR` | Optional: goal-board intake root for offloaded synthesis work | `$HOME/goals` |

Claude Code's own transcript store is at `~/.claude/projects/` and is read as-is.

---

## Hard constraints

These prevent the failure modes the skill exists to fix.

1. **NEVER merge session contexts.** The trap is concatenation. Merging N parallel sessions into one larger context does not reduce load — it concentrates it. Same N threads, same N contexts to hold, now stacked.

2. **NEVER drift to integrator-prompt generation.** The origin diagnostic (2026-05-14): when a Claude Code session is asked the meta-question "what do you think about my N-session overwhelm," the model tends to escalate to generating an orchestrator-prompt for a hypothetical 7th session instead of answering. 5 of 6 sessions did this. This skill **refuses that drift**. If invoked inside a session, the session must give its OWN STATE — not a meta-prompt, not an orchestrator spec.

3. **Operator-seat-only verdict.** The skill surfaces; operator picks. The wake-up choice is not algorithmic.

4. **One thread woken; others sealed.** Multiple-wake defeats the point.

5. **Explicit JSONL paths for every seal.** A fact-sheet extractor that auto-picks the most recent transcript by mtime races in a multi-session world. Always pass the explicit JSONL path.

6. **HELD never goes fully silent.** A thread routed to HELD has its *content* quarantined, but its *presence* (a count) must keep surfacing on a no-choice surface (the daily heartbeat). Quarantine that surfaces nothing is a grave — and a grave gated on "until the operator asks" is an Axiom 0 violation, because the faculty that remembers to ask is the deficit the prosthetic exists to replace. Presence is mandatory; content is on request.

---

## Two modes (v0.3.0 — operator-directed 2026-06-07)

finnskogarna folds in **two modes**. Same back half (compress → route → surface → verdict); the front half differs in how sessions enter.

**Mode A — Live (probe-fire).** N parallel sessions open *right now*. Phases 1–2 as written below: probe each live session for its own state, seal via your session-close skill. Use when sessions are still running and overwhelm is current.

**Mode B — Historical fold.** A *time window* of already-closed sessions (e.g. "back to May 20"). Sessions can't be probed — they're closed — so **compress each from its transcript** instead of probing:
- **Sweep, source 1 — transcripts:** enumerate session JSONLs in the window under `~/.claude/projects` (exclude `subagents/` and any agent-internal dirs).
- **Sweep, source 2 — artifact + task stores (v0.5.0; issue-tracker/kanban sources added v0.5.1, operator-directed):** run a deterministic open-loops scanner if you have one:
  ```bash
  open-loops-scan --since <window-start> [--issues] [--kanban]
  ```
  A scanner in this role walks `$FINN_REPORTS_DIR/*.html`, deny-lists cron-generated report families, flags answerable reports (`data-q` / `<textarea>` / `localStorage` autosave) with no answered-marker on disk, optionally pulls open items from your issue tracker (all open statuses, not date-filtered — old open tickets are more of a loop, not less) and ready/in-progress cards from your kanban board (both fail-soft), and emits JSONL. Each open loop becomes a candidate thread merged into the live-thread list before routing. **Session-closed ≠ loop-closed** — a 2026-06-07 fold missed real threads because a clean session close can leave an unanswered artifact behind. The script discovers; the LLM routes. (No scanner? Do the same sweep manually: list reports in the window, check each for an answered-marker.)
- **Compress (from transcript, not probe):** per session pull topic (first user msg), where it left off (last user msg), and a done/undone signal. Scanner-sourced loops compress from the report itself (title, question count, mtime).
- Then **Phases 3–5 unchanged:** harvest → offload → verdict (active / seal / HELD).
- Use when reconstructing what's in flight across a span — "what did I leave undone since `<date>`."

**The consume-convention (v0.5.0).** A report's loop closes only when an answered-marker lands on disk. The session that consumes a report's answers (paste-back, export, or verdict discussion) **must write `<slug>-verdict.md` next to the report** in `$FINN_REPORTS_DIR`. Paste-back answers leave no disk artifact otherwise — proven 2026-06-09 when a review scanned UNANSWERED an hour after it was answered. Accepted markers, any one closes the loop: `<slug>-verdict.md` or `<slug>-answers.md` beside the report, or `~/Downloads/<slug>.md` / `~/Downloads/<slug>-answers.md` (the report's own download export). Writing the verdict file is part of consuming, not optional bookkeeping.

**Data reach (both modes):** finn reads what's on disk — Claude Code / CLI transcripts, the reports drop zone, and (via the scanner) open issue-tracker items and kanban cards. Surfaces that keep their state sealed elsewhere (VM-sealed environments, cloud-only chat apps) are out of reach until an export from inside drops their entries somewhere finn reads. Flag uncovered surfaces; never imply coverage.

---

## Pipeline — five phases

The phases below are written for **Mode A**. **Mode B** replaces Phase 1 (probe-fire) and the live-seal half of Phase 2 with the sweep+compress above, then runs Phases 3–5 identically.

### Phase 1 — Probe-fire

- Operator chooses a probe prompt. Defaults: `"what do you think? go deep"` or `"audit yourself — produce your own session-state-dump in fact-sheet shape."`
- Operator pastes the prompt into each of the N sessions.
- **Each session must respond with ITS OWN STATE** — not a meta-prompt for a hypothetical 7th session, not an orchestrator spec.
- If a session responds with an orchestrator-prompt: flag it. That's integrator-drift. Session is too meta to trust as state-source. Operator must re-prompt that session with: "no orchestrator prompt. give me YOUR session state in fact-sheet shape."

### Phase 2 — Seal

- Run your session-close skill on each session in turn.
- **Pass explicit JSONL path** to the fact-sheet extractor:
  ```bash
  python3 session-fact-sheet.py \
    ~/.claude/projects/<project-dir>/<session-id>.jsonl \
    > $FACT_SHEETS_DIR/<session-id>-<TS>.md
  ```
- Each session also writes its own cold-handover audit MD (e.g. `session-YYYY-MM-DD-<topic>-audit.md` in your memory directory).
- Verify each seal: fact sheet + audit MD + seal record in `$SEALS_DIR`.

### Phase 3 — Harvest

- Build the harvest harness as a single interactive HTML at `$FINN_REPORTS_DIR/finnskogarna-<DATE>.html`. Use an interactive-report pattern: textareas with `data-q`, copy/download action bar, autosave to localStorage.
- One card per session:
  - Session ID, topic, duration
  - Probe-response (Phase 1 output)
  - Fact-sheet excerpt (Phase 2)
  - Auto-extracted work-shipped list
  - **Per-card operator-take textarea**: one-line read — `alive / generic / drift / spent`
- Cross-cutting section:
  - Convergences (if 2+ sessions independently extracted the same skill, pattern, or decision)
  - Contradictions (where two sessions made opposing decisions about the same artifact)
  - Integrator-drifts (sessions that escalated to meta-prompts)
  - Fact-sheet integrity issues (missing seals, cross-wired metrics)
- Final "wake-up pick" textarea + free-form notes.
- Autosave to `localStorage`. Download `.md` and self-saving `.html` exports.
- **Pre-set route verdicts (v0.4.0 — design review, 2026-06-09).** Every per-thread route toggle (Wake/Seal/HELD) ships **pre-set to the machine-proposed verdict**; the operator scans and flips exceptions instead of setting N verdicts from blank. Empirical basis: the first real fold (2026-06-07) surfaced 13 verdicts, operator set 1 and deferred 12 — N blank decisions exceeds one sitting's decision budget, and decision throughput is part of the deficit (Axiom 0: the verdict layer can't be gated on the depleted faculty). **The wake-pick is the exception: it always ships blank.** Constraint #3 (operator-seat-only verdict) governs the wake; route proposals are pre-routing, not the wake.

### Phase 4 — Offload

- If you run a goal board, emit a goal intake for the synthesis pass at `$FINN_GOALS_DIR/finnskogarna-<DATE>/`. If you run an issue tracker, queue a ticket chain for the comparative read.
- **The operator-seat session does NOT chew through the inputs.** The substrate does. The operator-seat picks the verdict.

### Phase 5 — Verdict

Surface:
- **Convergences** — if 2+ sessions independently extracted the same skill / pattern / decision, that's a real cogpros primitive candidate. Name it.
- **Contradictions** — where two sessions made opposing decisions about the same artifact, file, or rule. Operator decides which wins.
- **Integrator-drifts** — flag sessions too meta to trust as state-source.
- **Fact-sheet integrity issues** — missing seals (re-run extractor), cross-wired metrics from mtime race (regenerate with explicit JSONL path).

Operator picks ONE thread to wake. Each remaining thread gets a verdict: **seal** (cold-resumable, done-shaped) or **HELD** (quarantined-but-present — see the HELD section). A thread the operator keeps skipping across runs can also reach HELD by automatic intake. Multiple-wake is still forbidden; HELD is not a wake.

---

## HELD — the quarantine destination (v0.2.0)

The five-phase pipeline above wakes ONE thread and seals the rest. **HELD is a third verdict**, between wake and seal: a thread that stays *in the whole picture* but is *quarantined* — tracked, not surfaced, doesn't greet the operator at re-entry unless asked. It exists for threads that burn (e.g. an unresolved obligation you can't drop but can't face yet): cutting them loses the connection, leaving them live makes them bleed into everything. HELD keeps the connection and stops the bleed.

- **Not `seal`** — seal means done/cold-resumable; HELD is unresolved-on-purpose.
- **Not `defer`** — defer means do-soon; HELD has no implied next action.

### Content vs. presence — the load-bearing split

A HELD thread is stored as two separable things:

- **Content** — the burning material (the full thread, restart prompt, detail). Quarantined. Surfaces **only on deliberate operator action** ("open held").
- **Presence** — the bare fact that the thread exists. A count. Surfaces on a **no-choice surface** the operator already sees, so it can't be forgotten (constraint #6).

Burn comes from content greeting the operator at the door. Forgetting comes from zero presence. HELD surfaces **presence without content**.

### The held-shelf artifact

finnskogarna writes one shelf file the verdict phase appends to. This is the box's self-contained HELD store — no external system required.

Path: `$FINN_STATE_DIR/held-shelf.jsonl` (one line per held thread).

```json
{
  "id": "thread-x",
  "title": "a thread that burns",
  "held_since": "2026-06-06",
  "source_session": "abc12345-...",
  "restart_prompt_ref": "$FINN_STATE_DIR/held/thread-x.md",
  "avoidance_score": 0,
  "hard_deadline": null,
  "last_surfaced": "2026-06-06",
  "last_opened": null
}
```

- `restart_prompt_ref` points to the quarantined **content** (kept out of the shelf so reading the shelf never surfaces the burn).
- `avoidance_score` and `last_opened` drive the heartbeat (below).
- `hard_deadline` (optional, `YYYY-MM-DD` or null) — set when the thread carries a statutory/external date (legal filing window, cure deadline). Drives the deadline override (below). Most held threads leave it null.

### The heartbeat (seam A — the one early wire)

The presence marker is a **daily count-only line** on a morning surface the operator sees without choosing to — a daily report, dashboard, or morning briefing:

```
Held: 3 threads · (open to review)
```

No titles, no content. finnskogarna writes the count to `$FINN_STATE_DIR/held-count.txt`; your morning-report generator reads that one file and prints one line. That is the entire seam-A wire — every richer connection between finnskogarna and your daily-report stack stays designed-but-unwired until a deliberate intent-elicitation pass says otherwise.

### The rift — avoidance drives the heartbeat (the closed loop)

HELD must also be *arrived at* without the operator having to issue a verdict, because that verdict depends on the broken faculty. So the shelf reads behavior and feeds it back:

1. **Automatic intake.** When a woken/active thread's restart prompt is surfaced but **not reopened** across N runs, increment its `avoidance_score`. A thread whose score crosses a threshold **auto-routes to HELD** — the avoidance pattern is the prosthetic detecting the burn before the operator can name it. (Operator can always pull a thread back out.)

2. **Avoidance modulates the heartbeat.** A thread's `avoidance_score` sets *how* its presence surfaces — higher avoidance → quieter, lower-frequency, gentler ("still under canopy"); readiness (a recent open, a manual nudge) → clearer, more present. The marker meets the operator exactly as loud as he's ready to hear it.

3. **Deadline override (v0.4.0 — design review, 2026-06-09).** Avoidance-quieting is a positive feedback loop: quieter → less reopened → quieter. For a pure emotional burn that's the design intent. But a thread carrying a `hard_deadline` (legal filing window, cure deadline) must not sink — deadlines don't track readiness. **Deadline proximity sets a volume floor that overrides avoidance-quieting**: as `hard_deadline` nears, the thread's presence gets louder and more specific regardless of `avoidance_score` (e.g. ≤14 days: named on the heartbeat line, not just counted; ≤7 days: surfaced with the date). Readiness governs until the calendar outranks it. A HELD verdict on a deadline-bearing thread without `hard_deadline` set is a routing error — check for external dates at intake.

The same signal reads the deficit on the way in (intake) and retrains it on the way out (graduated surfacing). That is the loop: HELD is not a storage state, it's a feedback surface against prospective memory itself.

> **Build scope note (v0.2.0):** HELD destination + held-shelf + count-only heartbeat + avoidance loop ship now. The full live↔historical-corpus unification and the two-representation (exit-fold + return-surface) rewrite are deferred until latent operator intent has been deliberately elicited. Do not expand this section into that rewrite without that pass first.

---

## Inputs at invocation

1. List of N session IDs — or `--auto` mode to detect the last N sessions by mtime (warn that auto-mode is best-effort).
2. Probe prompt (optional, default provided).
3. Output directory (default: `$FINN_REPORTS_DIR`).
4. Date stamp (default: today, operator's local timezone).

---

## Outputs

- `$FINN_REPORTS_DIR/finnskogarna-YYYY-MM-DD.html` — interactive harvest harness
- `$FINN_REPORTS_DIR/finnskogarna-YYYY-MM-DD-verdict.md` — synthesis output
- `$FINN_GOALS_DIR/finnskogarna-YYYY-MM-DD/` — optional goal board for offloaded synthesis work
- Optional issue-tracker ticket chain for the synthesis pass
- `$FINN_STATE_DIR/held-shelf.jsonl` — append-only HELD store (one line per held thread)
- `$FINN_STATE_DIR/held/<id>.md` — quarantined content per held thread
- `$FINN_STATE_DIR/held-count.txt` — single integer the morning-report heartbeat reads (seam A)

---

## Companion primitives

| Phase | Primitive | Owned by |
|-------|-----------|----------|
| 1 — Probe-fire | operator-driven | operator |
| 2 — Seal | your session-close skill | per session |
| 3 — Harvest | interactive-HTML-report pattern | finnskogarna |
| 4 — Offload | goal-intake + issue tracker (optional) | substrate |
| 5 — Verdict | operator-seat-only | operator |

---

## Lineage

- **Cogpros primitive 29 candidate.**
- Two-level extension of `/accordion` (primitive 16). Where `/accordion` compresses one topic in one session, `/finnskogarna` compresses across N sealed sessions.
- **Convergence proof:** 2026-05-14 six-session cascade. 5 of 6 sessions independently generated the integrator-prompt shape this skill canonicalizes. The convergence proves the skill is real and needed.
- **Named for** *Livet i Finnskogarna* (Carl Jularbo, 1893–1966, Malmö). The operator's great-grandfather's favorite accordion song.

---

## Known Limitations & Gotchas

### Anti-patterns

- "Bring it all into one session to move forward from" — the merge trap. Reject.
- Generating an orchestrator-prompt instead of running this skill — the integrator-drift.
- Sealing without explicit JSONL path — mtime cross-wire.
- Waking multiple threads at once — defeats the point. One breathes, others sleep.
- Treating this as a chat-thread close — different scale. Use your session-close skill for that.
- Letting the operator-seat session chew through all inputs — that's substrate work. Offload it.

### Failure modes observed in the field

- **Integrator-drift (2026-05-14):** When agent sessions are asked the meta-question "what do you think about my N-session overwhelm," 5 of 6 sessions escalated to generating orchestrator-prompts instead of responding with their own state. Counter by demanding an explicit state-dump in fact-sheet shape.
- **mtime cross-wire:** a fact-sheet extractor that auto-picks the most-recent JSONL by mtime races in a multi-session world. Always pass explicit JSONL path per session.
- **Partial seals:** A seal record can land in `$SEALS_DIR` without a corresponding fact sheet in `$FACT_SHEETS_DIR`. Verify both exist per session before harvest.
- **Operator-fields cross-contamination:** Operator-filled fields in auto-mode can pull metrics from a different session if context was loaded from that session. Re-extract from explicit JSONL path if values look wrong.

## Examples

### Worked example — 2026-05-14 six-session cascade

**Setup.** Operator was running six parallel Claude Code sessions over a 30-hour window. Topics: a voice-substrate build, a citation-verification library, a work-log regime audit, a message-archive harvest, a site redesign, and a website prompt.

**Phase 1 (probe-fire).** Operator manually pasted `"i am overwhelmed by 6 sessions... what do you think? go deep"` into each session. Outcome: 5 sessions generated integrator-prompts for a hypothetical 7th session. 1 session gave actual state. Integrator-drift detected in 5 of 6.

**Phase 2 (seal).** Session-close run per session. 4 sessions sealed cleanly. 1 wrote a seal record but no fact sheet — failure caught at harvest. 1 wrote cross-wired operator fields with another session's metrics — mtime race caught at harvest.

**Phase 3 (harvest).** HTML harness built in the reports drop zone: per-session probe-response + fact-sheet excerpts + work-shipped lists, plus a cross-cutting inventory. Operator-take textareas surfaced S1 as the only alive thread, S2-S6 as integrator-drifted.

**Phase 4 (offload).** Goal intake drafted; synthesis ticket chain queued.

**Phase 5 (verdict).**
- Convergence detected: 2 sessions independently extracted the same interactive-HTML-report pattern. Skill confirmed real.
- Contradictions: none load-bearing.
- Integrator-drifts: 5 of 6 sessions. Pattern named and encoded as constraint #2 above.
- Fact-sheet integrity issues: 2 (one missing, one cross-wired). Both recovered with explicit JSONL paths.
- Wake-up pick: deferred — operator chose to ship `/finnskogarna` skill itself as the synthesis output, since the convergence proved the skill was real.

**Output.** This skill. Cogpros primitive 29.

## Dependencies

### Required scripts and tools
- A **fact-sheet extractor** (e.g. `session-fact-sheet.py`) — turns a session JSONL transcript into a one-page fact sheet; must support an explicit JSONL path argument. Any script with that contract works.
- `$FINN_REPORTS_DIR` — drop zone for the HTML harness
- `$FACT_SHEETS_DIR` — per-session fact sheet storage
- `$SEALS_DIR` — per-session seal records

### Required skills
- A **session-close skill** — Phase 2 seal per session; anything that seals a session and emits a per-session fact sheet
- An **interactive-HTML-report pattern** — Phase 3 harness (textareas with `data-q`, action bar, autosave)

### Optional integrations
- **Open-loops scanner** — deterministic Mode B artifact-store sweep (reports / issue tracker / kanban); manual sweep works without it
- **Goal board + intake skill** — Phase 4 synthesis offload
- **Issue tracker** — Phase 4 synthesis ticket chain
- **Dashboard auto-index** — auto-registering the harvest HTML on drop
- **Memory/knowledge store** — downstream memory writes after verdict (Phase 5)
- A **skill-audit skill** (e.g. `/skill-doctor`) — audit this skill before publication

## Verification

After Phase 5 verdict, confirm the artifacts landed:

```bash
# 1. Each session has both a seal record AND a fact sheet
for SID in <session-ids>; do
  test -f $SEALS_DIR/${SID}.json || echo "MISSING seal: $SID"
  ls $FACT_SHEETS_DIR/${SID}-*.md >/dev/null 2>&1 || echo "MISSING fact sheet: $SID"
done

# 2. Harvest HTML exists and is non-empty
test -s $FINN_REPORTS_DIR/finnskogarna-$(date +%Y-%m-%d).html

# 3. Verdict MD written
test -s $FINN_REPORTS_DIR/finnskogarna-$(date +%Y-%m-%d)-verdict.md

# 4. Goal intake exists (if you wired the optional goal board)
test -d $FINN_GOALS_DIR/finnskogarna-$(date +%Y-%m-%d)
```

If any check fails, the verdict has not actually landed. Re-run the failing phase before declaring done.

## Observability

If you run an event bus, emit an event on verdict landing so downstream agents and dashboards can react:

```bash
emit-event agent finnskogarna_verdict \
  "N=<n> sessions folded. Wake-up: <session-topic>. Convergences: <count>. Integrator-drifts: <count>."
```

Log the run summary to `$FINN_STATE_DIR/runs.jsonl` (one line per invocation) so the pattern is queryable later. Schema:

```json
{
  "ts": "2026-05-14T04:54:00Z",
  "n_sessions": 6,
  "wake_up_pick": "voice-substrate",
  "sessions_sealed_cleanly": 4,
  "sessions_with_integrity_issues": 2,
  "integrator_drifts": 5,
  "convergences_detected": 1,
  "skills_birthed_from_convergence": ["interactive-html-report"]
}
```

---

## Pairs with

- `/accordion` — single-topic compress (primitive 16). This is the multi-session generalization.
- Your session-close skill — per-session seal in Phase 2.
- Goal board + issue tracker — synthesis offload in Phase 4 (optional).
- Interactive-HTML-report pattern — harness in Phase 3.
- A skill-audit skill — audit this skill itself before publication.

---

## NOT FOR

- Single-session checkpoint (use `/accordion`)
- End-of-session close (use your session-close skill)
- Mid-session reorientation (use `/whats-next` or equivalent)
- One stalled session (just resume it)
- Sessions that have not yet been sealed via your session-close skill
