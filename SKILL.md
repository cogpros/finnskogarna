---
name: finnskogarna
description: 'Master accordion: fold N parallel Claude Code sessions when they overwhelm — AND compress one finished topic mid-session ("park it"). Multi-session: compress each, diff, wake ONE, seal or HELD the rest (HELD = quarantined-but-present, surfaced as a daily count heartbeat). Never merge. Single-topic mode ("park it" / accordion mode): compress one closed thread into a memory-ready record, then clear the deck. Use when (1) "finnskogarna" / "fold the sessions"; (2) N≥3 sessions pile up; (3) overwhelmed by sessions; (4) pre-bed reconcile; (5) a thread burns and should be held, not cut; (6) "park it" / one finished topic needs compressing mid-session while the session continues. NOT for session close (/closing-time-facts), reorientation (/whats-next), one-line captures (Dross "save that").'
license: MIT
compatibility: Requires Claude Code. Depends on session-fact-sheet.py extractor at the operator's private scripts directory and interactive-html-report skill. Tested on macOS.
user-invocable: true
metadata:
  version: "0.7.0"
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
  - "park it"
  - "accordion"
  - "compress this thread"
---

# Finnskogarna — The Master Accordion

The master accordion. When N parallel Claude Code sessions converge into operator overwhelm, `/finnskogarna` runs a two-level fold: compress each session independently, diff the compressions, surface ONE wake-up thread, seal the rest cold-resumable. Never merge contexts. Always pick.

Named for *Livet i Finnskogarna* (Life in the Finn Forests) — the Carl Jularbo accordion waltz that the operator's great-grandfather played. The song is about Finnish settlers who built lives in distinct clearings out in the Swedish woods, each clearing keeping its own shape. Each parallel session is a settlement. The skill is the operator standing above the woods, hearing each clearing's signal, deciding which one breathes next.

Three-count: **compress · compare · choose** — and on arc-shaped folds, **weigh** (the owls) drops in before the choice.

The multi-session fold is the headline. But the same accordion also plays one note: **single-topic mode** compresses ONE finished thread mid-session, then clears the deck — no diff, no wake-pick, no seal pipeline. One settlement, parked. (Absorbed from `/accordion`, primitive 16, 2026-06-10. finnskogarna now owns both the N-session fold and the one-topic "park it".)

---

## Single-topic mode (accordion mode — "park it")

The operator closed ONE thread but the session continues. No N-session overwhelm, no diff, no wake-pick. Just: compress this topic into a durable memory record so any future cold session can pick it up, then clear the deck for the next thing.

Triggered by: `/finnskogarna` with a single live thread, `"park it"`, Dross `"park it"`, `"accordion"`, `"compress this thread"`, or any mid-session close of a finished topic. Run AFTER `/loose-ends` when the sweep surfaces items worth preserving before compression.

This mode skips the five-phase pipeline entirely. It runs this:

```
You just closed a thread. Compress it and save it so it survives this session.

1. IDENTIFY THE THREAD
   What topic/work was just active? Name it in one line.
   If two topics overlapped, ASK which one to compress. Do not guess.
   If the conversation produced no decisions, work, or state, it is a ramble,
   not a thread — say so and stop. Do not invent structure.

2. COMPRESS — memory-ready summary, three tight sections, facts only:
   - **What happened:** Decisions, discoveries, work done.
   - **Current state:** Where it stands now. Built/not built, blocked/unblocked,
     open questions.
   - **Next steps:** What picks up from here. Numbered. Specific enough that a
     cold session can act on them.
   Format for scanning: bullets over paragraphs. Include exact paths for any
   code/files/configs/URLs touched ("the script we built" is not pickup-ready).

3. SAVE
   Search the MEMORY.md index first (`qmd search "<topic>"`).
   - If a detail file exists: merge into it. Never create a second file for the
     same topic. Update the index line only if state changed; don't duplicate it.
   - If none: create a new detail file with frontmatter (`name`, `description`,
     `type:` project/feedback/reference). Files without frontmatter break qmd.
   New MEMORY.md index entries must pass the memory write gate: line under 150
   chars, Detail pointer present, no duplicate, no payload in the index.

4. CONFIRM — one line: what was saved, where it went. Then clear the deck.
```

**Cold-pickup test:** could a different agent in a different session read this detail file and know exactly where to pick up? If not, add what's missing.

**Boundaries.** This mode is mid-session, end-of-thread. It is NOT end-of-session closure — if the operator is closing the session, route to `/closing-time-facts` and let it handle thread capture. Never double-fire with `/closing-time-facts` in the same close.

**Observability.** Bus emit when the deck is cleared, fire-and-forget:

```bash
the event-bus emitter script (operator-private automation, not in this repo) finnskogarna accordion_park "<thread topic>" "lines_compressed=<n> memory_file=<path>" cogpros
```

`accordion_park` — single thread compressed and cleared. Subject: thread topic (one line). Body: `lines_compressed=<n> memory_file=<path>`.

The rest of this SKILL.md is the **multi-session fold**. Skip to it only when N parallel sessions pile up.

---

## Hard constraints

These prevent the failure modes the skill exists to fix.

1. **NEVER merge session contexts.** The trap is concatenation. Merging N parallel sessions into one larger context does not reduce load — it concentrates it. Same N threads, same N contexts to hold, now stacked.

2. **NEVER drift to integrator-prompt generation.** Tonight's diagnostic (2026-05-14): when EOM (Claude Code) is asked the meta-question "what do you think about my N-session overwhelm," the model tends to escalate to generating an orchestrator-prompt for a hypothetical 7th session instead of answering. 5 of 6 sessions did this. This skill **refuses that drift**. If invoked inside a session, the session must give its OWN STATE — not a meta-prompt, not an orchestrator spec.

3. **Operator-seat-only verdict.** The skill surfaces; operator picks. The wake-up choice is not algorithmic.

4. **One thread woken; others sealed.** Multiple-wake defeats the point.

5. **Explicit JSONL paths for every seal.** The session-fact-sheet.py mtime-pick races in a multi-session world. Always pass the explicit JSONL path.

6. **HELD never goes fully silent.** A thread routed to HELD has its *content* quarantined, but its *presence* (a count) must keep surfacing on a no-choice surface (the daily heartbeat). Quarantine that surfaces nothing is a grave — and a grave gated on "until the operator asks" is an Axiom 0 violation, because the faculty that remembers to ask is the deficit the prosthetic exists to replace. Presence is mandatory; content is on request.

---

## Three modes (A+B v0.3.0; Mode C added v0.6.0, operator-directed 2026-06-19)

finnskogarna folds in **three modes**. Same back half (compress → route → surface → verdict); the front half differs in what enters. A folds live sessions, B folds closed sessions, **C folds the open reports themselves** — the unit finnskogarna folds is *contexts that pile up*, and parked reports are the third kind.

**Mode A — Live (probe-fire).** N parallel sessions open *right now*. Phases 1–2 as written below: probe each live session for its own state, seal via `/closing-time-facts`. Use when sessions are still running and overwhelm is current.

**Mode B — Historical fold.** A *time window* of already-closed sessions (e.g. "back to May 20"). Sessions can't be probed — they're closed — so **compress each from its transcript** instead of probing:
- **Sweep, source 1 — transcripts:** enumerate session JSONLs in the window under `~/.claude/projects` (exclude `subagents/` and agent-internal dirs like `paperclip-instances`).
- **Sweep, source 2 — artifact + task stores (v0.5.0; paperclip/kanban added v0.5.1, operator-directed):** run the deterministic scanner:
  ```bash
  the open-loops scanner script (operator-private automation, not in this repo) --since <window-start> --paperclip --kanban --jspace
  ```
  It walks `the operator's private reports directory*.html`, deny-lists cron families (`wake-up-plan-*`, `what-we-built-*`, `index.html`), flags answerable reports (`data-q` / `<textarea>` / `localStorage` autosave) with no answered-marker on disk, pulls open Paperclip issues (local API, all open statuses, not date-filtered — old open tickets are more of a loop, not less) and Hermes kanban ready/in-progress cards across all boards, and (`--jspace`, 2026-07-07) open **session-uncertainty** loose ends from chr-jspace — open Q2 known-unknowns + J-space reasoning-unknowns from `signal:true` `uncertainty.json` objects (`signal:false` skipped at the sink, Axiom 0) — all fail-soft, and emits JSONL. Each open loop becomes a candidate thread merged into the live-thread list before routing. **Session-closed ≠ loop-closed** — the 2026-06-07 fold missed real threads because a clean session close can leave an unanswered artifact behind (`feedback_finnskogarna-scope-gap-artifact-loops.md`). The script discovers; the LLM routes.
- **Compress (from transcript, not probe):** per session pull topic (first user msg), where it left off (last user msg), and a done/undone signal. Scanner-sourced loops compress from the report itself (title, question count, mtime).
- Then **Phases 3–5 unchanged:** harvest → offload → verdict (active / seal / HELD).
- Use when reconstructing what's in flight across a span — "what did I leave undone since `<date>`."

**Mode C — Open-report reconciliation (v0.6.0 — operator-directed 2026-06-19).** The thing piling up isn't sessions — it's the *open field reports* the operator works off. When N reports sit open (operator drops a set, or the dropzone accumulates), fold the reports themselves:
- **Sweep:** enumerate the open reports (operator-supplied list, or `open-loops-scan` over the dropzone).
- **Compress = one reader (subagent) per report, fanned out.** Each reader judges DONE-vs-OPEN by **real-world disk evidence** — verdict/answers markers, downstream artifacts, recent commits — **NOT by the report's checkboxes.** Answers live in browser `localStorage` and are invisible on disk; reading the HTML tells you what the report *asks*, never what the operator *did*. Each returns: verdict (keep-open / revise / close-stale), the genuinely-open actions, and an IRL/online tag.
- Then **Phases 3–5 unchanged:** render the board (one report per row), surface convergences/diamonds, operator picks.
- **Mode C exists to enforce the consume-convention (below).** For every report the operator confirms done or abandoned, write its `<slug>-verdict.md`. This is the mode's reason to be — it closes the localStorage blind spot where work done *inside* a report never closes its loop, so the same reports re-surface as "open" in every future fold (proven 2026-06-19: 12 open reports, several already worked, none consume-marked). A report that **moved workflows** (stale-but-not-dead — e.g. a sandbox superseded by a lessons flow) gets a verdict that *redirects to its successor*, not a bare close.
- Use when open reports accumulate faster than they're consumed — "reconcile my open reports — what's actually done?"

**The consume-convention (v0.5.0).** A report's loop closes only when an answered-marker lands on disk. The session that consumes a report's answers (paste-back, export, or verdict discussion) **must write `<slug>-verdict.md` next to the report** in `the operator's private reports directory`. Paste-back answers leave no disk artifact otherwise — proven 2026-06-09 when the Fable 5 review scanned UNANSWERED an hour after it was answered. Accepted markers, any one closes the loop: `<slug>-verdict.md` or `<slug>-answers.md` beside the report, or `~/Downloads/<slug>.md` / `~/Downloads/<slug>-answers.md` (the report's own download export). Writing the verdict file is part of consuming, not optional bookkeeping.

**Data reach (both modes):** finn reads what's on disk — Claude Code / CLI transcripts, the reports drop zone, open Paperclip issues, and Hermes kanban cards (all via open-loops-scan). **Cowork (VM-sealed) and Chat (cloud) are out of reach** until a push-from-inside export drops their entries into `~/1HUGR` (see `references/four-pieces-system-design.md`). Audit packages in `the operator's private audit-package store` are not swept directly; instead `/system-audit` now drops an actionable field-report into the reports dropzone on completion (Step 8.5), so audit queues surface through the dropzone source finn already covers. The old `the operator's (retired) private handoffs directory` baton dir was retired 2026-06-16 (dead since May; live handoffs run through `the operator's private handoff queue`, cron-consumed, non-accumulating). Remaining unwired source: `~/Desktop/Documents/` undelivered drafts. Flag uncovered surfaces; never imply coverage.

**Session-origin typing (2026-07-01, ARC t_11bcf8a0).** Every `session_stop` bus event now carries `sentinel=` (`found` / `missing` / `exempt` / `untyped`) and, where known, `origin=` (`operator` / `machine` / `subagent`) — see `the session-origin sidecar writer (operator-private automation, not in this repo)`. Before computing ANY stops-vs-seals statistic (e.g. "N stops / 0 seals"), whether from a live `bus.jsonl` grep or a Mode B sweep: **machine-origin sessions (`sentinel=exempt`) are never counted as a skipped close** — cron `claude -p` probes and worker scripts don't run closing-time and aren't supposed to; report them separately as "machine sessions," not as operator deferral. **Sessions with no origin sidecar (`sentinel=untyped`, pre-2026-07-01 or hook failure) report as "untyped," never as operator failures** — absence of a sidecar is not evidence of a skip. **A seal filed after the SessionEnd hook fired still counts as sealed** — re-check `the operator's private seal-record state store<session-id>.json` for a late arrival before attributing deferral; don't take the point-in-time `sentinel=missing` tag as a final verdict. Origin: the 2026-06-30 "57 stops / 0 seals" fold stat was later shown to be mostly machine/untyped sessions, not operator skips (verdict: `an internal verdict record in the operator's private dashboards store`).

---

## Pipeline — five phases

The phases below are written for **Mode A**. **Mode B** replaces Phase 1 (probe-fire) and the live-seal half of Phase 2 with the sweep+compress above, then runs Phases 3–5 identically. **Phase 3.5 (Evaluation — the owls)** is conditional across all modes: it fires only on arc-shaped folds (see the gate in that phase) and is otherwise absent.

### Phase 1 — Probe-fire

- Operator chooses a probe prompt. Defaults: `"what do you think? go deep"` or `"audit yourself — produce your own session-state-dump in the closing-time-facts shape."`
- Operator pastes the prompt into each of the N sessions.
- **Each session must respond with ITS OWN STATE** — not a meta-prompt for a hypothetical 7th session, not an orchestrator spec.
- If a session responds with an orchestrator-prompt: flag it. That's integrator-drift. Session is too meta to trust as state-source. Operator must re-prompt that session with: "no orchestrator prompt. give me YOUR session state in closing-time-facts shape."

### Phase 2 — Seal

- `/closing-time-facts` on each session in turn.
- **Pass explicit JSONL path** to the fact-sheet extractor:
  ```bash
  python3 the fact-sheet extractor script (operator-private automation, not in this repo) \
    ~/.claude/projects/-Users-$USER/<session-id>.jsonl \
    > the operator's private fact-sheet state store<session-id>-<TS>.md
  ```
- Each session also writes its own cold-handover audit MD in `~/.claude/projects/-Users-$USER/memory/session-YYYY-MM-DD-<topic>-audit.md`.
- Verify each seal: fact sheet + audit MD + seal record in `the operator's private seal-record state store`.

### Phase 3 — Harvest

- Build the harvest harness as a single interactive HTML at `a fold-dated file in the operator's private reports directory, named finnskogarna-<DATE>.html`. Use the `interactive-html-report` skill pattern (textareas with `data-q`, copy/download action bar, autosave to localStorage).
- **MANDATORY — "DO THIS TOMORROW" banner at the very top.** Every finnskogarna board opens with the `#tomorrowBell` banner (field-report `{TOMORROW_BANNER_PLACEHOLDER}`) naming the **single** highest-relief next action — the cheapest or most time-critical open item the board surfaces (read it off the board's own clock / send-block). EOM proposes the default; operator picks or swaps. One action only — it's the anti-drift nudge, not a list. (Origin: operator 2026-06-24.)
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
- **Pre-set route verdicts (v0.4.0 — Fable 5 review F5, 2026-06-09).** Every per-thread route toggle (Wake/Seal/HELD) ships **pre-set to the machine-proposed verdict**; the operator scans and flips exceptions instead of setting N verdicts from blank. Empirical basis: first real fold (2026-06-07) surfaced 13 verdicts, operator set 1 and deferred 12 — N blank decisions exceeds one sitting's decision budget, and decision throughput is part of the deficit (Axiom 0: the verdict layer can't be gated on the depleted faculty). **The wake-pick is the exception: it always ships blank.** Constraint #3 (operator-seat-only verdict) governs the wake; route proposals are pre-routing, not the wake.

### Phase 3.5 — Evaluation (the owls; conditional)

Harvest *defines* the arc; this phase *judges* it. Finnsko's five phases compress and route but never **evaluate** — wake/seal/HELD is a logistics verdict, not a read on whether the work cohered. Phase 3.5 is the missing eye: **Two Birds** (`~/1HUGR/bin/two-birds-run.py`) reads the harvested board and returns two blind reads plus **the rift** — where the this-arc owl (Archimedes) and the all-time owl (Wan Shi Tong) diverge. The operator rules on the rift; the ruling flows into the Phase 5 verdict. (Two Birds folded in as finnsko's evaluation phase, 2026-07-08.)

**Fires ONLY on arc-shaped folds.** Two Birds is end-of-arc, not daily (its own hard constraint). Finnsko runs often — pre-bed reconcile, N≥3 overwhelm, accordion-park — and an eye held open on every routine fold habituates into a rubber-stamp, cheapening the one ritual whose value is scarcity. Gate:
- **FIRE** when the fold carries an `--arc-label` (a genuine working arc is closing) OR the operator explicitly calls for the owls.
- **SKIP** single-topic "park it" mode and routine housekeeping folds with no arc-label. When skipped, Phase 3 → Phase 4 run unchanged — this phase simply isn't there.
- **Axiom 0 — the arc-label AUTO-fires; it does not wait on the operator to remember.** The label is set when a fold is launched to close a working arc (the `--arc-label` input finnsko already takes), so the fire is read from the fold's own shape, not the operator's initiative. Explicit operator call is a *supplemental* trigger, never the only one — gating the owls solely on the operator remembering to summon them would gate a prosthetic on the initiation deficit it exists to bypass. Default-SKIP applies to *unlabeled* housekeeping folds; it is not an opt-in requirement. An eye that opens on arcs and closes on chores — and the arc opens it, not the hand.

**Mechanism.**
1. Emit the harvested picture as a text arc-file — the per-thread compressed states + work-shipped + the cross-cutting convergences/contradictions from Phase 3 (strip the HTML chrome; the owls read markdown). Write to `a fold-dated arc file in finnskogarna's private state directory, named arc-<DATE>.md`.
2. Run: `python3 ~/1HUGR/bin/two-birds-run.py --arc-file a fold-dated arc file in finnskogarna's private state directory, named arc-<DATE>.md --slug finnsko-<DATE> --arc-label "<the fold's label>"`. Both owls read **BLIND** (2026-07-08 re-cut — no sequential hand-off); the rift renders to the Perch (`the operator's private Two Birds dashboard`) with the ruling PENDING.
3. Operator reads the Perch, gives the one-line ruling (verbatim → `ruling.verbatim`).

**The return edge — rift reconciles with `avoidance_score`.** Load-bearing wire, not decoration. Wan Shi Tong (all-time recurrence) and finnsko's `avoidance_score` (HELD auto-intake) detect the **same thing** — a pattern the operator's deficit can't self-name — one as a voice, one as a number. Where an owl's recurrence read names a thread AND that thread's `avoidance_score` is already elevated, the convergence is a **strong HELD pre-set**: the rift is the WHY, the score is the THAT. Feed the rift back into the Phase 3 route pre-sets before Phase 5 commits — the owls' read re-sets the proposed Wake/Seal/HELD toggles, and the wake-pick is offered against the rift. The seam already exists (Phase 3 stages pre-set verdicts, Phase 5 commits); the owls go in that gap. **The deadline override still governs.** A rift-driven HELD pre-set does not exempt a thread from the deadline floor (see HELD section): a thread carrying a `hard_deadline` keeps its volume floor regardless of what the owls say, and a HELD verdict on a deadline-bearing thread still requires `hard_deadline` set. The owls can route to HELD; they cannot silence a calendar.

**The return edge, part 2 — the ruling is captured, not stranded.** The operator's ruling is fold output: it lands in the fold's memory/seal record for the arc, not only on the Perch tile. The Perch stays the read surface; the seal carries the verdict forward.

**Build-scope note (v0.7.0):** the conditional gate + arc-file emit + rift→pre-set reconcile + ruling capture ship as the fold-in. Open: the exact automatic threshold for "arc-shaped" beyond arc-label/explicit-call (session-count heuristic left to the running agent for now); and whether the `avoidance_score`↔rift reconcile should eventually be a computed score rather than the agent's read. Do not expand into a scored auto-router without `/mimir` — the wake stays operator-seat (Constraint #3).

### Phase 4 — Offload

- `/goal-prep` emits a GoalBuddy intake for the synthesis pass at `~/1HUGR/docs/goals/finnskogarna-<DATE>/`.
- Paperclip RAV chain queued for the comparative read.
- **The operator-seat session does NOT chew through the inputs.** The substrate does. The operator-seat picks the verdict.

### Phase 5 — Verdict

Surface:
- **Convergences** — if 2+ sessions independently extracted the same skill / pattern / decision, that's a real cogpros primitive candidate. Name it.
- **Contradictions** — where two sessions made opposing decisions about the same artifact, file, or rule. Operator decides which wins.
- **Integrator-drifts** — flag sessions too meta to trust as state-source.
- **Fact-sheet integrity issues** — missing seals (re-run extractor), cross-wired metrics from mtime race (regenerate with explicit JSONL path). Check session-origin sidecars first (see "Session-origin typing" above) — a missing seal on a machine-origin or untyped session is not an integrity issue, it's expected.

Operator picks ONE thread to wake. Each remaining thread gets a verdict: **seal** (cold-resumable, done-shaped) or **HELD** (quarantined-but-present — see the HELD section). A thread the operator keeps skipping across runs can also reach HELD by automatic intake. Multiple-wake is still forbidden; HELD is not a wake.

**When Phase 3.5 ran, this verdict is not blank-slate.** The rift + the operator's ruling have already re-set the Phase 3 route pre-sets (an owl's recurrence read converging with an elevated `avoidance_score` = strong HELD), and the wake-pick is offered against the rift. The operator confirms/flips against the owls' read rather than the raw board. When Phase 3.5 was skipped (routine fold), verdicts run off the machine pre-sets as before.

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

Path: `the held-shelf file in finnskogarna's private state directory` (one line per held thread).

```json
{
  "id": "thread-x",
  "title": "a thread that burns",
  "held_since": "2026-06-06",
  "source_session": "abc12345-...",
  "restart_prompt_ref": "a per-thread file in finnskogarna's private held-content store",
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

The presence marker is a **daily count-only line** on the morning `wake-up-plan` (Daily Pulse), a surface the operator sees without choosing to:

```
Held: 3 threads · (open to review)
```

No titles, no content. finnskogarna writes the count to `the held-count file in finnskogarna's private state directory`; the `wake-up-plan` generator reads that one file and prints one line. That is the entire seam-A wire — everything else connecting finnskogarna to Daily Pulse / Anamnesis / Rhonda stays designed-but-unwired until the `/mimir`-gated reconcile.

### The rift — avoidance drives the heartbeat (the closed loop)

HELD must also be *arrived at* without the operator having to issue a verdict, because that verdict depends on the broken faculty. So the shelf reads behavior and feeds it back:

1. **Automatic intake.** When a woken/active thread's restart prompt is surfaced but **not reopened** across N runs, increment its `avoidance_score`. A thread whose score crosses a threshold **auto-routes to HELD** — the avoidance pattern is the prosthetic detecting the burn before the operator can name it. (Operator can always pull a thread back out.)

2. **Avoidance modulates the heartbeat.** A thread's `avoidance_score` sets *how* its presence surfaces — higher avoidance → quieter, lower-frequency, gentler ("still under canopy"); readiness (a recent open, a manual nudge) → clearer, more present. The marker meets the operator exactly as loud as he's ready to hear it.

3. **Deadline override (v0.4.0 — Fable 5 review F3, 2026-06-09).** Avoidance-quieting is a positive feedback loop: quieter → less reopened → quieter. For a pure emotional burn that's the design intent. But a thread carrying a `hard_deadline` (legal filing window, cure deadline) must not sink — deadlines don't track readiness. **Deadline proximity sets a volume floor that overrides avoidance-quieting**: as `hard_deadline` nears, the thread's presence gets louder and more specific regardless of `avoidance_score` (e.g. ≤14 days: named on the heartbeat line, not just counted; ≤7 days: surfaced with the date). Readiness governs until the calendar outranks it. A HELD verdict on a deadline-bearing thread without `hard_deadline` set is a routing error — check for external dates at intake.

The same signal reads the deficit on the way in (intake) and retrains it on the way out (graduated surfacing). That is the loop: HELD is not a storage state, it's a feedback surface against prospective memory itself.

> **Build scope note (v0.2.0):** HELD destination + held-shelf + count-only heartbeat + avoidance loop ship now. The full live↔historical-corpus unification and the two-representation (exit-fold + return-surface) rewrite are deferred to the `/mimir`-gated reconcile — that is where unelicited operator intent still lives. Do not expand this section into that rewrite without `/mimir` first.

---

## Inputs at invocation

1. List of N session IDs — or `--auto` mode to detect the last N sessions by mtime (warn that auto-mode is best-effort).
2. Probe prompt (optional, default provided).
3. Output directory (default: `the operator's private reports directory`).
4. Date stamp (default: today, CST).

---

## Outputs

- `a per-run harvest harness file in the operator's private reports directory` — interactive harvest harness
- `a per-run verdict file in the operator's private reports directory` — synthesis output
- `~/1HUGR/docs/goals/finnskogarna-YYYY-MM-DD/` — GoalBuddy board for offloaded synthesis work
- One Paperclip RAV chain for the synthesis pass
- `the held-shelf file in finnskogarna's private state directory` — append-only HELD store (one line per held thread)
- `finnskogarna's private held-content store<id>.md` — quarantined content per held thread
- `the held-count file in finnskogarna's private state directory` — single integer the wake-up-plan heartbeat reads (seam A)
- Vedrfolnir tile auto-registered

---

## Companion primitives

| Phase | Primitive | Owned by |
|-------|-----------|----------|
| 1 — Probe-fire | operator-driven | operator |
| 2 — Seal | `/closing-time-facts` | per session |
| 3 — Harvest | `interactive-html-report` skill pattern | finnskogarna |
| 3.5 — Evaluation (conditional) | `/two-birds` (blind owls + rift) | operator rules; owls advise |
| 4 — Offload | `/goal-prep` + Paperclip | substrate |
| 5 — Verdict | operator-seat-only | operator |

---

## Lineage

- **Cogpros primitive 29 candidate.**
- **Absorbed primitive 16 (`/accordion`) on 2026-06-10.** finnskogarna began as the two-level extension of accordion — where accordion compressed one topic in one session, finnskogarna compressed across N sealed sessions. The single-topic compress now lives here as the single-topic mode above, and accordion is retired. One skill, two scales: one note or the whole woods.
- **Folded in Two Birds as Phase 3.5 (Evaluation) on 2026-07-08 (v0.7.0).** The fold gained an eye. Finnsko compressed and routed but never evaluated; Two Birds (blind owls + rift) is the missing judgment faculty, dropped into the existing pre-set→commit seam between Harvest and Verdict, conditional on arc-shaped folds. The load-bearing recognition: the all-time recurrence owl and the HELD `avoidance_score` were already the same organ — pattern-detection the deficit can't self-run — one a voice, one a number; the fold reconciles them (rift = why-to-HELD, score = that-to-HELD). Operator-directed, this session.
- **Convergence proof:** 2026-05-14 six-session cascade. 5 of 6 sessions independently generated the integrator-prompt shape this skill canonicalizes. The convergence proves the skill is real and needed.
- **Named for** *Livet i Finnskogarna* (Carl Jularbo, 1893–1966, Malmö). The operator's great-grandfather's favorite accordion song.

---

## Known Limitations & Gotchas

### Anti-patterns

- "Bring it all into one session to move forward from" — the merge trap. Reject.
- Generating an orchestrator-prompt instead of running this skill — the integrator-drift.
- Sealing without explicit JSONL path — mtime cross-wire.
- Waking multiple threads at once — defeats the point. One breathes, others sleep.
- Treating this as a Discord-thread close — different scale. Use `/closing-time-facts` for that.
- Letting the operator-seat session chew through all inputs — that's substrate work. Offload to GoalBuddy + Paperclip.

### Failure modes observed in the field

- **Integrator-drift (2026-05-14):** When EOM sessions are asked the meta-question "what do you think about my N-session overwhelm," 5 of 6 sessions escalated to generating orchestrator-prompts instead of responding with their own state. Counter by demanding explicit state-dump in closing-time-facts shape.
- **mtime cross-wire:** session-fact-sheet.py auto-picks most-recent JSONL by mtime. In a multi-session world this races. Always pass explicit JSONL path per session.
- **Partial seals:** A seal record can land in `the operator's private seal-record state store` without a corresponding fact sheet in `the operator's private fact-sheet state store`. Verify both exist per session before harvest.
- **Operator-fields cross-contamination:** Operator-filled fields in auto-mode can pull metrics from a different session if context was loaded from that session. Re-extract from explicit JSONL path if values look wrong.

## Examples

### Worked example — 2026-05-14 six-session cascade

**Setup.** Operator was running six parallel Claude Code sessions over a 30-hour window. Topics: Soapbox voice substrate, verify-citation library build, ghost-hours regime audit, jeremyknows iMessage harvest, EME Tenfold redesign, sick-clothing-co website prompt.

**Phase 1 (probe-fire).** Operator manually pasted `"i am overwhelmed by 6 sessions... what do you think? go deep"` into each session. Outcome: 5 sessions generated integrator-prompts for a hypothetical 7th session. 1 session (Soapbox) gave actual state. Integrator-drift detected in 5 of 6.

**Phase 2 (seal).** `/closing-time-facts` run per session. 4 sessions sealed cleanly. 1 (EME) wrote seal record but no fact sheet — failure caught at harvest. 1 (sick-clothing-co) wrote cross-wired operator fields with ghost-hours metrics — mtime race caught at harvest.

**Phase 3 (harvest).** HTML harness built at `a per-fold HTML harness in the operator's private reports directory` (operator-facing) and `tonight-2026-05-14-what-we-built.html` (cross-cutting inventory). Per-session probe-response + fact-sheet excerpts + work-shipped lists. Operator-take textareas surfaced S1 as the only alive thread, S2-S6 as integrator-drifted.

**Phase 4 (offload).** GoalBuddy intake drafted at `~/1HUGR/docs/goals/finnskogarna/`. Paperclip RAV chain for synthesis.

**Phase 5 (verdict).**
- Convergence detected: 2 sessions (ghost-hours + EME) independently extracted `interactive-html-report` skill. Skill confirmed real.
- Contradictions: none load-bearing.
- Integrator-drifts: 5 of 6 sessions. Pattern named and encoded as constraint #2 above.
- Fact-sheet integrity issues: 2 (EME missing, sick-clothing-co cross-wired). Both recovered with explicit JSONL paths.
- Wake-up pick: deferred — operator chose to ship `/finnskogarna` skill itself as the synthesis output, since the convergence proved the skill was real.

**Output.** This skill. Cogpros primitive 29.

## Dependencies

### Required scripts and tools
- `the fact-sheet extractor script (operator-private automation, not in this repo)` — fact sheet extractor; must support explicit JSONL path argument
- `the open-loops scanner script (operator-private automation, not in this repo)` — deterministic artifact + task-store sweep for Mode B (source 2); emits JSONL of unanswered answerable reports + open Paperclip issues + kanban cards + session-uncertainty loose ends (`--paperclip --kanban --jspace`)
- `the operator's private reports directory` — drop zone for HTML harness (auto-indexed via Vedrfolnir)
- `the operator's private fact-sheet state store` — per-session fact sheet storage
- `the operator's private seal-record state store` — per-session seal records
- `~/1HUGR/docs/goals/` — GoalBuddy goal directory root

### Required skills
- `/closing-time-facts` — Phase 2 seal per session
- `interactive-html-report` — Phase 3 harness pattern (textareas with `data-q`, action bar, autosave)
- `/goal-prep` — Phase 4 GoalBuddy intake
- `/skill-doctor` — audit this skill before publication

### Optional integrations
- Paperclip API — synthesis pass RAV chain (Phase 4)
- Vedrfolnir tile registration — auto-indexed on drop
- gbrain — for downstream memory writes after verdict (Phase 5)

## Verification

After Phase 5 verdict, confirm the artifacts landed:

```bash
# 1. Each session has both a seal record AND a fact sheet
for SID in <session-ids>; do
  test -f the operator's private seal-record state store${SID}.json || echo "MISSING seal: $SID"
  ls the operator's private fact-sheet state store${SID}-*.md >/dev/null 2>&1 || echo "MISSING fact sheet: $SID"
done

# 2. Harvest HTML exists and is non-empty
test -s a fold-dated file in the operator's private reports directory, named finnskogarna-$(date +%Y-%m-%d).html

# 3. Verdict MD written
test -s a fold-dated file in the operator's private reports directory, named finnskogarna-$(date +%Y-%m-%d)-verdict.md

# 4. GoalBuddy intake exists
test -d ~/1HUGR/docs/goals/finnskogarna-$(date +%Y-%m-%d)
```

If any check fails, the verdict has not actually landed. Re-run the failing phase before declaring done.

## Observability

Emit a bus event on verdict landing so downstream agents and dashboards can react:

```bash
bash the event-bus emitter script (operator-private automation, not in this repo) \
  agent finnskogarna_verdict \
  "N=<n> sessions folded. Wake-up: <session-topic>. Convergences: <count>. Integrator-drifts: <count>." \
  "" "finnskogarna"
```

Log the run summary to `the run-log file in finnskogarna's private state directory` (one line per invocation) so the pattern is queryable later. Schema:

```json
{
  "ts": "2026-05-14T04:54:00Z",
  "n_sessions": 6,
  "wake_up_pick": "soapbox-resurrection",
  "sessions_sealed_cleanly": 4,
  "sessions_with_integrity_issues": 2,
  "integrator_drifts": 5,
  "convergences_detected": 1,
  "skills_birthed_from_convergence": ["interactive-html-report"]
}
```

---

## Pairs with

- `/loose-ends` — surfaces what to compress before single-topic mode or the multi-session fold.
- `/closing-time-facts` — per-session seal in Phase 2.
- `/goal-prep` + Paperclip — synthesis offload in Phase 4.
- `interactive-html-report` — harness pattern in Phase 3.
- `/skill-doctor` — audit this skill itself before publication.

---

## NOT FOR

- End-of-session close (use `/closing-time-facts`) — never double-fire with it in the same close
- Mid-session reorientation (use `/whats-next`)
- One-line memory captures (use Dross `"save that"`)
- One stalled session (use `/whats-next` or just resume)
- Sessions that have not yet been sealed via `/closing-time-facts` (multi-session fold only)

(Single-topic checkpoint / "park it" used to route to `/accordion` — that is now the single-topic mode in this skill.)
