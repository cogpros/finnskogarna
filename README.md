# Finnskogarna

> The master accordion. N parallel sessions → compress each → diff the compressions → wake one, seal the rest.

A cogpros primitive (#29) for operators running multiple parallel Claude Code sessions on related work. When the parallel load passes what one mind can hold, `/finnskogarna` runs a two-level fold that never merges contexts. Every settlement keeps its own shape; the operator stands above the woods and picks which one breathes next.

Three-count: **compress · compare · choose.**

---

## Why this exists

Most operators (and most LLMs) respond to multi-session overwhelm by trying to merge contexts into one bigger window. That move concentrates load — it doesn't reduce it.

The opposite move works: seal each session independently, diff the compressions, wake one thread, send the others to cold-resumable storage. The output is a **decision** (which thread breathes), not a merged context.

This skill codifies the opposite move so it can not be re-discovered every time.

---

## When to use

Trigger conditions:

- Operator types `/finnskogarna` or says "master accordion" or "fold the sessions"
- N ≥ 3 parallel sessions accumulating without reconciliation
- "I'm overwhelmed by X sessions" with X ≥ 3
- Pre-bed reconciliation when operator can not hold all parallel threads in head
- After a long sprint where multiple sessions converged on shared substrate

---

## When NOT to use

- Single-session checkpoint → `/accordion`
- End-of-session close → your session-close skill
- Mid-session reorientation → `/whats-next` or equivalent
- One stalled session → just resume it
- Sessions that have not yet been sealed via your session-close skill

---

## Quickstart

```
/finnskogarna
```

Or with explicit session IDs:

```
/finnskogarna aaaa1111,bbbb2222,cccc3333,dddd4444,eeee5555,ffff6666
```

The skill walks five phases. Each phase must complete before the next.

---

## Configuration

The skill reads and writes a handful of directories, referenced throughout as variables. Set them to fit your machine:

| Variable | Purpose | Suggested default |
|---|---|---|
| `$FINN_STATE_DIR` | Skill state (held-shelf, run log, counts) | `$HOME/.finnskogarna` |
| `$FINN_REPORTS_DIR` | Drop zone for interactive HTML reports | `$HOME/reports` |
| `$FACT_SHEETS_DIR` | Per-session fact sheet storage | `$HOME/.finnskogarna/fact-sheets` |
| `$SEALS_DIR` | Per-session seal records | `$HOME/.finnskogarna/seals` |
| `$FINN_GOALS_DIR` | Optional goal-board intake root | `$HOME/goals` |

---

## The five phases

### 1. Probe-fire
Operator picks a probe prompt. Default: `"audit yourself — produce your own session-state-dump in fact-sheet shape."`

Operator pastes the prompt into each session. Each session responds with **its own state**, not a meta-prompt for a hypothetical 7th session.

If a session responds with an orchestrator-prompt, that's integrator-drift. Re-prompt with state-dump demand.

### 2. Seal
Run your session-close skill on each session, with explicit JSONL path to avoid mtime race:

```bash
python3 session-fact-sheet.py \
  ~/.claude/projects/<project-dir>/<session-id>.jsonl \
  > $FACT_SHEETS_DIR/<session-id>-<TS>.md
```

Each session writes its own fact sheet plus a cold-handover audit MD.

### 3. Harvest
Build an interactive harvest HTML at `$FINN_REPORTS_DIR/finnskogarna-<DATE>.html` (interactive-report pattern: textareas with `data-q`, action bar, autosave).

One card per session: probe-response, fact-sheet excerpt, work-shipped list, operator-take textarea. Cross-cutting cards for convergences, contradictions, drifts, and integrity issues. Autosave to `localStorage`. Export to `.md` or `.html`.

### 4. Offload
Optionally emit a goal intake at `$FINN_GOALS_DIR/finnskogarna-<DATE>/` and queue an issue-tracker chain for the comparative read.

The operator-seat session does **not** chew through the inputs. The substrate does. The operator-seat picks the verdict.

### 5. Verdict
Surface:
- Convergences (2+ sessions extracting the same skill / pattern / decision → real primitive candidate)
- Contradictions (opposing decisions on the same artifact)
- Integrator-drifts (too-meta sessions)
- Fact-sheet integrity issues (missing seals, cross-wires)

Operator picks ONE thread to wake. Others go cold-resumable.

---

## Inputs

- N session IDs (or `--auto` for last-N-by-mtime, best-effort)
- Probe prompt (optional, default provided)
- Output directory (default `$FINN_REPORTS_DIR`)
- Date stamp (default today, operator's local timezone)

---

## Outputs

| Path | Purpose |
|------|---------|
| `$FINN_REPORTS_DIR/finnskogarna-YYYY-MM-DD.html` | Interactive harvest harness |
| `$FINN_REPORTS_DIR/finnskogarna-YYYY-MM-DD-verdict.md` | Synthesis output |
| `$FINN_GOALS_DIR/finnskogarna-YYYY-MM-DD/` | Optional goal board for synthesis work |
| Issue-tracker chain | Optional synthesis pass |

---

## Hard constraints

1. **NEVER merge session contexts.** The trap is concatenation.
2. **NEVER drift to integrator-prompt generation.** If invoked inside a session, demand state-dump.
3. **Operator-seat-only verdict.** Skill surfaces; operator picks.
4. **One thread woken; others sealed.**
5. **Explicit JSONL paths for every seal** (mtime race protection).

---

## Anti-patterns

- "Bring it all into one session to move forward from" — the merge trap. Reject.
- Generating an orchestrator-prompt instead of running this skill — the integrator-drift. The origin diagnostic for the 6-session cascade: 5 of 6 sessions did this.
- Sealing without explicit JSONL path — mtime cross-wire.
- Waking multiple threads at once — defeats the point.

---

## Pairs with

- `/accordion` — single-topic compress (primitive 16). This is the multi-session generalization.
- Your session-close skill — per-session seal (Phase 2).
- Goal board + issue tracker — synthesis offload (Phase 4, optional).
- Interactive-HTML-report pattern — harness (Phase 3).
- A skill-audit skill — audit this skill before publication.

---

## Lineage

Cogpros primitive #29 candidate. Two-level extension of `/accordion`.

**Convergence proof:** 2026-05-14 six-session cascade. Five of six sessions independently generated the integrator-prompt shape this skill canonicalizes. Two of six independently birthed the interactive-HTML-report pattern that Phase 3 uses. The convergence proves the skill is real and needed.

**Named for** *Livet i Finnskogarna* — "Life in the Finn Forests" — accordion waltz by Carl Jularbo (1893–1966, Malmö). The song is about Finnish settlers who built lives in distinct clearings out in the Swedish woods. The operator's great-grandfather played it. The skill remembers the man who played it and the man who wrote it.

---

## Install

### Claude Code

```bash
git clone https://github.com/cogpros/finnskogarna.git \
  ~/.claude/skills/finnskogarna
```

### Other agent runtimes

Clone into the runtime's user-level skills directory:

```bash
git clone https://github.com/cogpros/finnskogarna.git \
  <RUNTIME_SKILLS_DIR>/finnskogarna
```

Verify the skill loaded — open a session and check the skills registry. The skill name `finnskogarna` should appear with the trigger phrases listed.

---

## Setup

1. Pick your directories (see Configuration) and confirm they exist:
   ```bash
   mkdir -p $FINN_STATE_DIR $FINN_REPORTS_DIR $FACT_SHEETS_DIR $SEALS_DIR
   ```
2. Confirm you have a fact-sheet extractor (any script that turns a session JSONL into a one-page fact sheet and accepts an explicit path).
3. Confirm companion skills are installed: a session-close skill and an interactive-HTML-report pattern.
4. (Optional) Wire a dashboard auto-index so harvest HTML registers on drop.

---

## Usage

### Natural language

> "fold the sessions"
> "i'm overwhelmed by six sessions"
> "/finnskogarna for last 5"
> "run master accordion on these session ids: a1b2c3, d4e5f6, ..."

### Commands table

| Invocation | Effect |
|---|---|
| `/finnskogarna` | Run with last-N-by-mtime auto-detect (best-effort) |
| `/finnskogarna <sid1,sid2,...>` | Run on explicit session IDs |
| `/finnskogarna --probe "<text>"` | Override probe prompt |
| `/finnskogarna --output <dir>` | Override output directory |
| `/finnskogarna --date YYYY-MM-DD` | Override date stamp |

---

## File structure

```
finnskogarna/
├── SKILL.md          # Skill instructions + frontmatter
├── README.md          # This file
├── LICENSE.txt        # MIT license
└── .gitignore         # .DS_Store, node_modules/, editor files
```

No scripts — workflow-orchestration skill. Phases 2-4 delegate to companion skills.

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Phase 2 seal but no fact sheet appears | mtime race on multi-session machine | Re-run extractor with explicit JSONL path |
| Fact sheet has metrics from wrong session | Operator-fields cross-wired from another loaded context | Regenerate with explicit JSONL; preserve original as `-original.md` |
| 5/6 sessions return orchestrator-prompts instead of state | Integrator-drift (known failure mode) | Re-prompt each drifted session: "no orchestrator prompt. give me YOUR session state in fact-sheet shape." |
| Harvest HTML opens empty | localStorage from a prior run masking content | Hit Clear button in the HTML or use a fresh browser profile |
| `npx skills-ref validate` fails | Frontmatter shape mismatch | Check name field matches dir name, description under 1024 chars |

---

## Security

- This skill writes files to `$FINN_REPORTS_DIR`, `$FINN_GOALS_DIR`, and `$FINN_STATE_DIR`. Confirm those paths are writable.
- The harvest HTML uses `localStorage` for autosave on the operator's browser. No remote calls.
- No network access required for Phases 1, 3, 5. Phases 2 and 4 may touch local services depending on your session-close skill and issue tracker.
- No secrets read. No credentials handled.

---

## Limitations

- `--auto` session detection is best-effort by mtime — falls back to a warning if ambiguous.
- Phase 5 verdict is **operator-only**. The skill never auto-picks which thread to wake.
- Designed for Claude Code session topology. Other runtimes need adapted Phase 2 sealers.
- Not a chat-thread closer (use your session-close skill for that).

---

## License

MIT. See LICENSE.txt.

---

## Status

`v0.5.1` — five field runs behind the design (see version notes in SKILL.md). Skill-doctor audited 2026-05-14 (9.0/11, 82%).

## Acknowledgments

- *Livet i Finnskogarna* — Carl Jularbo (1893–1966), accordion composer, Malmö.
- The operator's great-grandfather, who played the song. The skill carries the song he played.
