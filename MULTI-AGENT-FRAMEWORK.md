# Multi-Agent Framework

A fifth task mode — **Orchestrate** — for projects too large to ship in a single Claude Code session. The Orchestrator decomposes a `TASK-BRIEF.md` into ordered phases, spawns Implementer subagents per phase via the `Task` tool, and gates every phase merge through an independent Reviewer subagent that drives a fix loop until the contract is satisfied.

This layer **does not replace** the existing Generate / Refactor / Debug / Partial-Rewrite prompts. It composes them: each phase ultimately runs one of those prompts, scoped to its slice of the work.

---

## When to use

Use Orchestrate when **any** of:
- Estimated > ~1500 LOC across > 3 modules
- Multiple independent subsystems (API + worker + schema + client)
- Cross-cutting refactor touching many files
- Mixed-mode work (new module + bug fix + rewrite of an old one)
- A single session would blow the context window or lose coherence

Otherwise, stay with the single-mode prompts. Orchestration has overhead — don't pay it for small tasks.

---

## Roles

| Role | Spawned by | Tools | Responsibility |
|---|---|---|---|
| **Orchestrator** | User (top-level session) | Task, Read, Write, Edit, Bash, Grep, Glob, TodoWrite | Owns the brief, plans phases, dispatches subagents, manages the review loop, merges phase outputs, keeps `STATE.md` current. **Does not write feature code itself.** |
| **Architect** (optional) | Orchestrator | Read, Write, Grep, Glob, WebFetch | Produces `ARCHITECTURE.md` and the phase DAG once, before any implementation. Used only for novel/complex projects. Write access is scoped to `ARCHITECTURE.md` only — no source files. |
| **Implementer** | Orchestrator (one per phase, possibly in parallel) | Full tool set | Executes a single `PHASE-BRIEF.md` against the contract, using the matching base prompt (Generate/Refactor/Debug/Partial-Rewrite). Returns a phase report. |
| **Reviewer** | Orchestrator (after each Implementer finishes) | Read, Grep, Glob, Bash (read-only: tests, lint, type-check) | Independent validation. Has **never seen** the Implementer's reasoning. Runs the Validation Gate from `CLAUDE-CODE-CONTRACT.md` and emits a structured `REVIEW-REPORT.md`: PASS or list of blocking issues. |
| **Fixer** | Orchestrator (only if Reviewer fails) | Full tool set | A fresh subagent given the phase code + the Reviewer's issue list. Fixes only the listed issues. Output goes back to the Reviewer. Loop until PASS or `max_review_iterations` (default 3). |

The Orchestrator is the **only** persistent agent. All others are short-lived subagents launched via the `Task` tool with self-contained prompts.

---

## Phase pipeline

```
TASK-BRIEF.md
     │
     ▼
┌────────────────┐
│  Orchestrator  │  ── (optional) ──▶  Architect ──▶ ARCHITECTURE.md + phase DAG
└────────────────┘
     │
     │  for each phase in topological order:
     ▼
┌────────────────┐         ┌──────────┐         ┌──────────┐
│  Implementer   │ ──────▶ │ Reviewer │ ──FAIL─▶│  Fixer   │──┐
└────────────────┘         └──────────┘         └──────────┘  │
                                │                              │
                                │ PASS                         │
                                ▼                              │
                         merge phase, update STATE.md          │
                                │                              │
                                └──────────────────────────────┘
                                       (loop, max 3 iters)
```

Phases that share no files may be dispatched **in parallel** (single message, multiple `Task` calls). Phases with dependencies run sequentially in topological order.

---

## State handoff

The Orchestrator maintains a small set of files in the project root that subagents read instead of seeing prior conversation:

| File | Owner | Purpose |
|---|---|---|
| `TASK-BRIEF.md` | User | Top-level requirements (existing template). |
| `ARCHITECTURE.md` | Architect or Orchestrator | Module map, public interfaces, phase DAG. Frozen after phase 1. |
| `PHASES/PHASE-NN-<slug>.md` | Orchestrator | Per-phase brief (see `PHASE-BRIEF-TEMPLATE.md`). |
| `PHASES/PHASE-NN-REVIEW.md` | Reviewer | Latest review report for that phase. |
| `STATE.md` | Orchestrator | Current pipeline status: phase, status (pending/in-progress/in-review/passed/blocked), per-phase review-loop iteration count, owning subagent, pre-phase git checkpoint SHA. Updated after every state transition. |
| `PHASES/PHASE-NN-DESIGN-NOTE.md` | Implementer (only if gate required) | Output of the Design Thinking Gate for that phase. One per phase, never shared. Standardized path eliminates collisions across parallel phases. |
| `PHASES/PHASE-NN-CHANGELOG.md` | Implementer/Fixer | Per-phase CHANGELOG fragment. The Implementer writes its phase entry here, never to the root `CHANGELOG.md`. |
| `CHANGELOG.md` | Orchestrator only | Root changelog. Orchestrator merges per-phase fragments at final integration time. This is the only writer — Implementers never touch it directly. Eliminates the parallel-phase append race. |
| `PHASES/PHASE-NN-ESCALATION.md` | Implementer or Reviewer | Created when a phase cannot proceed without an out-of-scope decision (interface wrong, brief wrong, contract genuinely impossible). Contains: reason, evidence, proposed change, downstream impact. The Orchestrator picks this up and decides. |

Subagents are briefed by passing **file paths**, not pasted content. Each subagent reads only the files it needs via its own filesystem tools (Read/Grep/Glob). This is what keeps context bounded as the project grows. Subagent prompts therefore stay small (≈ the role prompt + a few path references), not size-limited by document length.

**Brief immutability.** Once the Orchestrator dispatches an Implementer for a phase, the corresponding `PHASES/PHASE-NN-<slug>.md` is **frozen for the duration of that phase's review loop**. Amendments require: (a) halting the phase, (b) updating the brief, (c) discarding the phase's git checkpoint, (d) restarting the phase from iteration 0 with a fresh Implementer. This prevents the Reviewer from being silently re-targeted mid-loop.

---

## The review loop

The fix loop is the core correctness mechanism. Rules:

1. **Reviewer is independent.** It must be a fresh `Task` invocation with no prior conversation. Its prompt contains: phase brief path, contract path, code paths to inspect, and the explicit instruction *"You did not write this code. Validate it adversarially."*
2. **Reviewer output is structured.** Either `STATUS: PASS` or `STATUS: FAIL` followed by a numbered issue list. Each issue has: `severity` (blocker/major/minor), `location` (`file:line`), `rule_violated` (cite the contract section), `evidence` (failing command output or quoted code), `required_fix` (one sentence).
3. **Fixer sees only the issue list**, not the Reviewer's full reasoning. The Reviewer's structured issue format (see `PROMPT-REVIEWER.md`) is therefore self-contained: each issue includes location, the violated rule **quoted in full** (not just cited by section number), command-output evidence, and the required fix. The Fixer never needs to read the contract to understand an issue.
4. **Loop cap.** `max_review_iterations` defaults to **3, per phase**, tracked independently in `STATE.md` per phase ID. It is **not** a global counter and does **not** share state with the Implementer's contract-level validation-retry cap (also 3, but internal to a single Implementer run). Effective worst case per phase is therefore: 1 Implementer attempt × 3 internal validation retries, then up to 3 review-loop iterations × (1 Fixer × 3 internal validation retries each). After review-loop iteration 3 fails, the Orchestrator stops, writes `BLOCKED.md`, and escalates. Never silently downgrade severity. The cap is set in `TASK-BRIEF.md` (top-level field `max_review_iterations`); it is not overridable per-phase, so the user always knows the worst case.
5. **Reviewer runs commands, doesn't trust claims.** The Reviewer must execute `make test`, `make lint`, type-check, and any phase-specific validation listed in the phase brief. Implementer claims of "tests pass" are not evidence — command output is.
6. **Minor issues do not block** unless the phase brief escalates them. Blockers and majors block.

---

## Git checkpoints

The Reviewer's `Files Owned` boundary check requires a known pre-phase tree state. The Orchestrator establishes this explicitly:

1. **Before dispatching the Implementer for phase N**, the Orchestrator runs `git add -A && git commit --allow-empty -m "checkpoint: pre PHASE-NN"` and records the resulting SHA in `STATE.md` as `PHASE-NN.checkpoint_sha`.
2. **The Reviewer diffs against that SHA**, not against `HEAD~1` or any guess: `git diff --name-only <checkpoint_sha>..HEAD`. Any file in the diff that is not in `Files Owned` is a blocker.
3. **On phase PASS**, the Orchestrator squashes the phase's commits into a single `phase: PHASE-NN-<slug>` commit on top of the checkpoint, then advances. The checkpoint SHA is retained in `STATE.md` until the project is complete.
4. **On phase BLOCKED**, the Orchestrator does **not** auto-revert. The user inspects the checkpoint and decides whether to roll back or amend.
5. **On brief amendment** (immutability break), the Orchestrator hard-resets the working tree to the checkpoint SHA before restarting the phase.

This makes the `Files Owned` enforcement deterministic and gives the user a clean rollback boundary at every phase.

---

## Parallelism rules

The Orchestrator may run phases in parallel **only when**:
- Phases edit disjoint file sets (declared in each `PHASE-BRIEF.md` under `Files Owned`).
- No phase reads another's in-progress output.
- Total parallel subagents ≤ 4 (avoid context-pool exhaustion).

Reviewer and Implementer for the *same* phase are always sequential. Reviewers for *different* completed phases may run in parallel.

---

## Failure handling

| Failure | Orchestrator action |
|---|---|
| Implementer subagent errors out | Re-dispatch once with the error appended to the phase brief. If it fails again, escalate. |
| Reviewer FAIL, iters < cap | Spawn Fixer with issue list. Increment iter counter in `STATE.md`. |
| Reviewer FAIL, iters == cap | Stop. Write `BLOCKED.md` summarizing the deadlock and pointing at the latest review report. Surface to user. |
| Reviewer disagrees with itself across iterations | Reviewer is dispatched as a fresh `Task` every iteration with the **same** prompt and **no** memory of prior runs. Drift is impossible by construction. |
| Implementer or Reviewer writes `PHASES/PHASE-NN-ESCALATION.md` | Orchestrator halts the phase, reads the escalation, decides: (a) amend brief and restart phase (hard reset to checkpoint, fresh Implementer, iter counter back to 0; **increment `PHASE-NN.brief_amendments` in `STATE.md`**), (b) amend `ARCHITECTURE.md` and re-open downstream phases that depended on the changed interface (mark them `pending` again, walk the phase DAG), or (c) reject the escalation and re-dispatch with `escalation_rejected: <reason>` appended to the brief. |
| `PHASE-NN.brief_amendments` ≥ 2 | The brief is fundamentally wrong, not merely buggy. Amending again resets the iter counter and would mask the loop from churn detection. Halt the phase, write `BLOCKED.md` citing "brief malformation loop", and require the **user** to rewrite the brief from scratch — not the Orchestrator, not another amendment. The user must explicitly delete `PHASES/PHASE-NN-*.md` and re-plan that phase before the Orchestrator may retry. This is the brief-loop equivalent of the contract's churn-detection rule and closes the only remaining deadlock path. |
| Downstream phase reveals an upstream phase's interface was wrong | Same as escalation case (b). Increment a top-level `architecture_revisions` counter in `STATE.md`; if it exceeds 2, halt and force the user to re-plan — repeated reopens mean the original decomposition was wrong. |
| Final integration review FAIL | Treat issue list as a synthetic phase `PHASE-FINAL`, dispatch a Fixer, re-run the integration review. Uses its own review-loop counter, also capped at `max_review_iterations`. If exhausted, `BLOCKED.md`. Never silently accept a failing integration review. |

---

## Minimal example

```
User: @CLAUDE-CODE-CONTRACT.md @MULTI-AGENT-FRAMEWORK.md
      @prompts/PROMPT-ORCHESTRATOR.md @TASK-BRIEF.md
      Orchestrate this project.

Orchestrator:
  1. Reads TASK-BRIEF.md, decides project is large enough for orchestration.
  2. (Optional) Spawns Architect → ARCHITECTURE.md + 5-phase DAG.
  3. Writes PHASES/PHASE-01-schema.md … PHASE-05-cli.md.
  4. Dispatches Implementer for PHASE-01 (Task tool, prompt = PROMPT-IMPLEMENTER.md + phase brief path).
  5. On return, dispatches Reviewer (Task tool, prompt = PROMPT-REVIEWER.md + phase brief path).
  6. Reviewer FAIL with 2 blockers → Orchestrator dispatches Fixer with issue list.
  7. Reviewer PASS → Orchestrator updates STATE.md, dispatches PHASE-02 and PHASE-03 in parallel (disjoint files).
  8. Continues until all phases pass or escalation.
  9. Runs cross-phase integration tests (full suite + e2e + visual/a11y for UI).
  10. Final step: dispatches a whole-project Reviewer against the full contract.
```

---

## Cross-phase integration testing

Phase-local PASS does not imply project-level PASS. After all phases pass and before the integration Reviewer, the Orchestrator must verify that phases compose correctly:

1. **Run the full project test suite** at the project root — per-phase tests only covered individual modules. The full suite catches interface mismatches, import errors, and integration gaps.
2. **Run end-to-end tests** if they exist (`e2e/`, `tests/e2e/`). If the project should have e2e tests (multi-module, frontend + backend, API + client) but doesn't, the Orchestrator dispatches an Implementer for a synthetic `PHASE-E2E` phase to create them.
3. **For UI projects:** run visual regression tests and accessibility audit at the project level. One phase's CSS can break another phase's layout — this is only caught by whole-project visual tests.
4. **If cross-phase tests fail**, the Orchestrator dispatches a Fixer whose `Files Owned` is the union of all phase files (it may edit across phase boundaries). After the Fixer returns, re-run all cross-phase tests before proceeding to the integration Reviewer.

This step is what separates orchestrated projects from "N independent scripts run in sequence."

---

## Files added by this layer

- `MULTI-AGENT-FRAMEWORK.md` (this file)
- `PHASE-BRIEF-TEMPLATE.md`
- `prompts/PROMPT-ORCHESTRATOR.md`
- `prompts/PROMPT-IMPLEMENTER.md`
- `prompts/PROMPT-REVIEWER.md`

Nothing in the existing contract changes. The base prompts (`PROMPT-CLAUDE-CODE-MASTER.md`, `PROMPT-REFACTOR.md`, `PROMPT-DEBUG.md`, `PROMPT-PARTIAL-REWRITE.md`) are reused unchanged by Implementers.
