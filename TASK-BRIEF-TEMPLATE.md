# TASK-BRIEF Template — For Claude Code Sessions

**Fill this out before spawning Claude Code. Be specific, be complete.**

---

## Task Type

**Pick one:**

- [ ] **Generate** — Build a new project from scratch
- [ ] **Add Function** — Add new functionality to an existing codebase
- [ ] **Refactor** — Restructure existing code without changing behavior
- [ ] **Debug** — Find and fix a specific bug
- [ ] **Partial Rewrite** — Replace a module/component with a new implementation

> **Why this matters:** Each mode uses a different prompt and checklist. Generate mode produces a full project. The other four modes work on existing code and have stricter scope controls. Pick the right mode, attach the matching prompt from `prompts/`.

---

## Project

**Name:** [Project Name]
**Language:** [Python / TypeScript / Go / Rust / C / C++ / Other]
**Framework:** [FastAPI / Express / Flask / Actix-web / Axum / Gin / None / Other]
**Duration Estimate:** [2h / 4h / 8h / Full day]

---

## Problem Statement

**What are we building / fixing / improving?**

[2-3 sentences explaining the core problem and solution]

Example (Generate):
> We're building a GitHub issue triage system that uses Claude AI to automatically categorize issues, extract key details, and suggest labels. Users run it via CLI or API.

Example (Add Function):
> We need to add a `POST /api/users/{id}/preferences` endpoint that allows users to update their display theme, notification settings, and language. Should integrate with the existing user service and follow current API patterns.

Example (Refactor):
> The `src/auth/` module has grown to 1200 lines with deep nesting and duplicated validation logic. Restructure into focused sub-modules (tokens, passwords, sessions) without changing the public API.

Example (Debug):
> `validate_email("user@")` returns `True` instead of raising `ValidationError`. This allows invalid emails to reach the database, causing downstream 500 errors in the notification service.

Example (Partial Rewrite):
> Replace the SQLite storage layer (`src/database/sqlite_store.py`) with PostgreSQL. The `Store` interface and all callers must continue working. Motivated by need for concurrent writes.

---

## Requirements

### Functional

**Must have:**
- [ ] Requirement 1
- [ ] Requirement 2
- [ ] Requirement 3

**Nice to have:**
- [ ] Optional feature A
- [ ] Optional feature B

### Non-Functional

- **Performance:** [e.g., "Process 100 issues in <30s"]
- **Reliability:** [e.g., "Retry on API failure, backoff strategy"]
- **Scale:** [e.g., "Support 10k+ issues without memory issues"]
- **Logging:** [e.g., "Debug logs for every API call"]

---

## Constraints

- **Budget:** [Cost limit for API calls, compute, etc.]
- **Dependencies:** [No external services / Use only stdlib / Must use X library]
- **Compatibility:** [Python 3.10+, Node 18+, Rust 1.75+, Go 1.22+, C11/C17, C++17/C++20, etc.]
- **Deployment:** [Local CLI / Docker / Serverless / API server]

---

## User Experience

### Input
**How does a user interact with this?**

```
# CLI usage
$ my-tool --option value

# API usage
POST /api/endpoint
{ "key": "value" }
```

### Output
**What does success look like?**

```json
{
  "status": "success",
  "data": {}
}
```

---

## Architecture Hints (Optional)

**If you have strong opinions, specify them. If not, Claude Code decides.**

---

## Existing Code Context (For Add Function / Refactor / Debug / Partial Rewrite)

> **Skip this section for Generate tasks.** For all other task types, this is essential.

### Codebase Location

**Repository:** [URL or local path]
**Branch:** [branch name]
**Files in scope:** [list specific files/modules to work on]

### Current State

**What works:** [describe what's currently functioning]
**What's broken/problematic:** [describe the specific issue]
**Related tests:** [list test files that cover this code]

### Existing Test Suite

```bash
# How to run existing tests
make test                    # or: npm test, cargo test, go test ./...

# Current test status
# [N] passing, [N] failing, [coverage]%
```

### Code Samples (If Relevant)

```
# Paste the specific function, class, or module that needs work.
# Include enough context for Claude to understand the surrounding code.
```

---

## Feature Scope (For Add Function Tasks Only)

> **Skip this section unless task type is Add Function.**

### What's Being Added
[Name the new functions, endpoints, classes, or modules]

### Where It Fits
[Which existing files/modules will be modified or extended]

### New Files Needed
[Any new files to create — or "none, extending existing files"]

### Interface Contract
```
# Function signatures, endpoint definitions, or class interfaces
# Example:
# POST /api/users/{id}/preferences → UserPreferences
# def export_data(format: str, output: Path) -> ExportResult
```

### Dependencies
- [ ] New libraries: [list, or "none"]
- [ ] New services: [list, or "none"]
- [ ] New configuration: [env vars, or "none"]

---

## Bug Report (For Debug Tasks Only)

> **Skip this section unless task type is Debug.**

### Observed Behavior
[What happens now — be specific]

### Expected Behavior
[What should happen instead]

### Reproduction Steps
```bash
# Exact steps to trigger the bug
1. ...
2. ...
3. Observe: [error message or wrong behavior]
```

### Error Output
```
# Paste the full error message, stack trace, or wrong output
```

### Environment
- OS: [e.g., Ubuntu 22.04, macOS 14]
- Language version: [e.g., Python 3.12, Node 20]
- Relevant dependency versions: [e.g., FastAPI 0.110, React 18.3]

---

## Rewrite Scope (For Partial Rewrite Tasks Only)

> **Skip this section unless task type is Partial Rewrite.**

### What's Being Replaced
[Module/component name and file paths]

### Why Not Refactor?
[Explain why incremental refactoring isn't sufficient]

### New Technology/Approach
[What replaces the old implementation — new library, pattern, architecture]

### Must Preserve
- [ ] Public API: [list functions/endpoints that callers depend on]
- [ ] Error types: [list error classes/codes that callers handle]
- [ ] Configuration: [list env vars or config keys the module reads]

### Acceptable Breaking Changes
- [ ] [List any API changes you're willing to accept, if any]
- [ ] [Or: "None — zero breaking changes"]

---

## Success Criteria

**The code is done when:**

### All Task Types
- [ ] Tests pass with ≥80% coverage
- [ ] Zero lint/type warnings
- [ ] Error handling is explicit (no silent failures)
- [ ] Code is readable and well-documented

### Add Function Only
- [ ] New functionality works as specified
- [ ] All existing tests pass unchanged
- [ ] New tests cover all new code (>=80% coverage)
- [ ] New code follows existing codebase conventions
- [ ] Documentation updated (docstrings, README, `.env.example`)
- [ ] FEATURE-PLAN included

### Generate Only
- [ ] All functional requirements working
- [ ] Setup script works (`setup.sh && make test`)
- [ ] README has working examples
- [ ] No console errors or warnings

### Refactor Only
- [ ] All existing tests pass unchanged
- [ ] Public API unchanged
- [ ] Coverage maintained or improved
- [ ] REFACTOR-PLAN included

### Debug Only
- [ ] Regression test exists (fails before fix, passes after)
- [ ] Root cause documented
- [ ] Minimal fix (fewest lines changed)
- [ ] DEBUG-REPORT included

### Partial Rewrite Only
- [ ] All callers of rewritten module work
- [ ] Migration guide provided (if API changed)
- [ ] New tests cover rewritten module
- [ ] REWRITE-PLAN included
- [ ] Rollback path documented

---

## Known Unknowns

**What might come up?**

```
- Q: [Question about ambiguity]
- A: [Your decision or preference]
```

---

## Notes

[Any additional context, preferences, or gotchas]

---

## Ready to Start?

Copy this template, fill it out, and give it to Claude along with the appropriate files:

| Task Type | Attach These Files |
|---|---|
| **Generate** | `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `prompts/PROMPT-CLAUDE-CODE-MASTER.md`, `TASK-BRIEF.md` |
| **Add Function** | `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `prompts/PROMPT-ADD-FUNCTION.md`, `TASK-BRIEF.md` |
| **Refactor** | `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `prompts/PROMPT-REFACTOR.md`, `TASK-BRIEF.md` |
| **Debug** | `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `prompts/PROMPT-DEBUG.md`, `TASK-BRIEF.md` |
| **Partial Rewrite** | `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `prompts/PROMPT-PARTIAL-REWRITE.md`, `TASK-BRIEF.md` |

See `README.md` for how to do this with the CLI, web, or API.
