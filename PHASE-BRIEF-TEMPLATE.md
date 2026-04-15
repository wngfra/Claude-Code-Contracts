# Phase Brief — PHASE-NN-<slug>

> Filled by the Orchestrator. One file per phase, lives under `PHASES/`.
> Subagents read this file instead of being told the plan in chat.

## Identity

- **Phase ID:** PHASE-NN
- **Slug:** short-kebab-case
- **Mode:** Generate | Refactor | Debug | Partial-Rewrite
- **Base prompt:** `prompts/PROMPT-CLAUDE-CODE-MASTER.md` (or matching mode prompt)
- **Depends on phases:** [PHASE-IDs, or "none"]
- **Blocks phases:** [PHASE-IDs]
- **Design Thinking Gate:** required | not required   ← decided by the Orchestrator at planning time; Implementer and Reviewer obey this, never re-decide.

(`max_review_iterations` is a top-level setting in `TASK-BRIEF.md`, not per-phase.)

## Goal

One paragraph. What does this phase deliver? Why does it exist as its own phase (not merged into another)?

## Scope — IN

Concrete, bounded list of what this phase MUST produce. Bullet list of files / functions / behaviors.

## Scope — OUT

What this phase MUST NOT touch. Anything not listed here that the Implementer wants to change requires escalation to the Orchestrator, not silent expansion.

## Files Owned

Exact list of file paths this phase may create or modify. Used by the Orchestrator to detect parallelism conflicts. Anything outside this list is read-only for the Implementer.

The root `CHANGELOG.md` and `README.md` must **never** appear here. Phases write per-phase fragments at `PHASES/PHASE-NN-CHANGELOG.md` (auto-included; no need to list) and the Orchestrator merges them at final integration. Same for any other shared root file.

```
src/foo/bar.py
src/foo/__init__.py
tests/foo/test_bar.py
```

## Files Read-Only

Files the Implementer needs to read for context but may not modify.

## Public Interface (frozen)

The exact signatures / types / endpoints this phase must expose. Other phases will be written against these — they cannot drift. If the Implementer believes the interface is wrong, escalate before changing it.

```python
def parse_event(payload: bytes) -> Event: ...
```

## Acceptance Criteria

Checklist the Reviewer will verify, in addition to the standard contract validation gate. **Every item must be a verifiable assertion**: a shell command exit code, a grep result, a numeric threshold, a file existence check, or a quoted output substring. Anything that requires human taste to evaluate is rejected outright by the Reviewer as a malformed brief.

**Format rule:** each criterion must include a `verify:` clause showing exactly how it will be checked. No `verify:` clause → the Reviewer treats the criterion as malformed and FAILs the phase before even running tests, with the issue routed back to the Orchestrator as `PHASES/PHASE-NN-ESCALATION.md` (brief is wrong).

Forbidden patterns (auto-reject):
- "code is clean / readable / maintainable / well-structured"
- "tests are meaningful / comprehensive"
- "performance is good / acceptable"
- "follows best practices"
- any adjective without a measurement

Allowed patterns:

```
- [ ] tests pass
      verify: `make test` exits 0; coverage report shows ≥ 85% on src/foo/
- [ ] parse_event rejects malformed payloads
      verify: `pytest tests/foo/test_bar.py::test_malformed -q` exits 0
- [ ] hot path stays under 50µs
      verify: `make bench` output contains `parse_event` and reports < 50000 ns/op
- [ ] no edits outside Files Owned
      verify: `git diff --name-only <checkpoint_sha>..HEAD` ⊆ Files Owned
- [ ] phase changelog fragment present
      verify: file `PHASES/PHASE-NN-CHANGELOG.md` exists and is non-empty
- [ ] no TODO comments introduced
      verify: `git diff <checkpoint_sha>..HEAD` contains zero added lines matching `TODO|FIXME|XXX`
- [ ] cyclomatic complexity ≤ 10 on changed functions
      verify: `radon cc src/foo/ -a -nc` reports max grade ≤ B
- [ ] visual regression tests pass (UI phases only)
      verify: `npx playwright test --project=visual` exits 0
- [ ] accessibility audit clean (UI phases only)
      verify: `npx playwright test --grep @a11y` exits 0; zero violations in output
- [ ] responsive at breakpoints (UI phases only)
      verify: `npx playwright test --grep @responsive` exits 0
```

## Validation Commands

Exact shell commands the Reviewer must execute. Implementer claims do not count.

```
make test
make lint
make typecheck
pytest tests/foo/ -q
```

## Design Thinking Gate

(See the `Design Thinking Gate` field under `Identity` above. The Orchestrator decided this; Implementer/Reviewer obey.)

If the field is `required`:
- Implementer produces `PHASES/PHASE-NN-DESIGN-NOTE.md` (exact path) using `THINKING-FRAMEWORKS.md`: invariants, core subproblem + canonical shape, chosen data structure with rejected alternatives, trade-off, ≤10-line pseudocode, edge cases.
- Tests in this phase must cover every edge case listed in the design note. Reviewer enforces this as a blocker.

If the field is `not required`:
- No design note expected. Reviewer skips the design check entirely.

## Risk / Notes

Anything the Implementer should know: gotchas, prior incidents, deliberate non-goals, open questions.
