# Claude Code Guide — How to Use the Production Contract

Complete walkthrough for generating production-grade code with Claude using the contract system.

---

## The Contract System

### Core Files

| File | Purpose |
|---|---|
| **CLAUDE-CODE-CONTRACT.md** | Quality standards (100% type hints, 80%+ tests, etc.) |
| **CODING-CONTEXT.md** | Language-specific rules and project context |
| **TASK-BRIEF-TEMPLATE.md** | Your project requirements — copy and fill for each task |

### Task Mode Prompts

| Prompt | When to Use |
|---|---|
| **prompts/PROMPT-CLAUDE-CODE-MASTER.md** | Building a new project from scratch |
| **prompts/PROMPT-REFACTOR.md** | Restructuring existing code without changing behavior |
| **prompts/PROMPT-DEBUG.md** | Finding and fixing a specific bug |
| **prompts/PROMPT-PARTIAL-REWRITE.md** | Replacing a module with a new implementation |

### The Flow

```
1. Pick your task type (generate / refactor / debug / rewrite)
        ↓
2. Fill TASK-BRIEF.md (your requirements + task-type-specific sections)
        ↓
3. Give Claude: contract + context + matching prompt + brief
        ↓
4. Claude reads the contract and produces output
        ↓
5. Code meets 100% of mode-specific standards
        ↓
6. Verify (make test, make lint) → Ship
```

---

## Step 1: Pick Your Task Type & Fill TASK-BRIEF.md

Copy the template and fill it with your project details. **Start by checking the task type** that matches what you're doing.

```bash
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md
```

### Example: Generate — GitHub Issue Triage System

```markdown
# TASK-BRIEF: GitHub Issue Triage AI

**Task Type:** Generate

## Project
Name: github-issue-triage
Language: Python
Framework: FastAPI

## Problem Statement
Build a system that reads GitHub issues, uses Claude AI to categorize them
(priority/type), extract stakeholders, and suggest labels. CLI + REST API.

## Functional Requirements
**Must have:**
- [ ] Read issues from GitHub API (public + private repos)
- [ ] Classify by priority (critical/major/minor/low)
- [ ] Extract stakeholder mentions (@username)
- [ ] Generate 100-word summary using Claude
- [ ] Output JSON (API) or formatted table (CLI)

**Nice to have:**
- [ ] Batch processing (50+ issues)
- [ ] Custom classification rules (YAML config)

## Constraints
- No external databases (SQLite OK)
- Python 3.10+

## Success Criteria
- [ ] All functional requirements working
- [ ] Tests: >= 80% coverage, all pass
- [ ] Setup works: `setup.sh && make test` first try
- [ ] README has working examples
- [ ] Type hints: 100% on public APIs
```

### Example: Refactor — Clean Up Auth Module

```markdown
# TASK-BRIEF: Refactor auth module

**Task Type:** Refactor

## Project
Language: Python
Files in scope: src/auth.py (1200 lines)

## Problem Statement
The auth module has grown into a monolith with duplicated validation,
deep nesting, and mixed concerns. Split into focused sub-modules
without changing the public API.

## Requirements
- [ ] Split into src/auth/tokens.py, passwords.py, sessions.py, middleware.py
- [ ] src/auth/__init__.py re-exports all current public functions
- [ ] Reduce max function length to 30 lines
- [ ] Eliminate duplicated validation logic

## Existing Code Context
Tests: tests/unit/test_auth.py (42 tests, all passing)
Coverage: 84%

## Success Criteria
- [ ] All 42 existing tests pass unchanged
- [ ] Coverage >= 84% (maintained or improved)
- [ ] Public API identical (same imports work)
- [ ] REFACTOR-PLAN included
```

### Example: Debug — Email Validation Bug

```markdown
# TASK-BRIEF: Fix email validation accepting "user@"

**Task Type:** Debug

## Project
Language: Python
Broken function: src/validators.py:validate_email

## Bug Report
**Observed:** validate_email("user@") returns True
**Expected:** Should raise ValidationError
**Reproduction:** Call validate_email("user@") — returns True instead of raising

## Error Output
No error — that's the bug. Invalid emails reach the database.

## Success Criteria
- [ ] Regression test: test_validate_email_rejects_missing_domain
- [ ] Root cause documented
- [ ] Minimal fix (change regex or add check)
- [ ] All existing tests pass
```

### Example: Partial Rewrite — SQLite to PostgreSQL

```markdown
# TASK-BRIEF: Rewrite storage layer from SQLite to PostgreSQL

**Task Type:** Partial Rewrite

## Project
Language: Python
Module to replace: src/database/sqlite_store.py
Replace with: src/database/postgres_store.py (asyncpg)

## Why not refactor?
SQLite doesn't support concurrent writes. The Store interface is fine
but the implementation needs to be async + PostgreSQL from the ground up.

## Must preserve
- [ ] Store interface (src/database/store.py — unchanged)
- [ ] All methods: get_user, save_user, list_users, delete_user
- [ ] Error types: StoreError, NotFoundError
- [ ] Config via DATABASE_URL env var

## Success Criteria
- [ ] All integration tests pass with PostgreSQL backend
- [ ] New unit tests for postgres_store.py
- [ ] Migration guide for .env changes
- [ ] Rollback: sqlite_store.py still works if reverted
```

---

## Step 2: Give Claude the Contract Files + Matching Prompt

Choose the method that matches how you use Claude. **Always use the prompt that matches your task type.**

| Task Type | Prompt File |
|---|---|
| Generate | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| Refactor | `prompts/PROMPT-REFACTOR.md` |
| Debug | `prompts/PROMPT-DEBUG.md` |
| Partial Rewrite | `prompts/PROMPT-PARTIAL-REWRITE.md` |

### Claude Code CLI

```bash
cd ~/my-project
claude --add-dir /path/to/claude-code-contracts

# Use @ references with the matching prompt. Examples:

# Generate
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-CLAUDE-CODE-MASTER.md @TASK-BRIEF.md
  Generate production code following the contract and task brief.

# Refactor
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-REFACTOR.md @TASK-BRIEF.md
  Refactor the code following the contract and task brief.

# Debug
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-DEBUG.md @TASK-BRIEF.md
  Debug and fix the issue following the contract and task brief.

# Partial Rewrite
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-PARTIAL-REWRITE.md @TASK-BRIEF.md
  Rewrite the module following the contract and task brief.
```

Or pipe everything in one shot:

```bash
# Swap the prompt file to match your task type
cat CLAUDE-CODE-CONTRACT.md CODING-CONTEXT.md prompts/PROMPT-REFACTOR.md TASK-BRIEF.md \
  | claude --print "Refactor the code following the attached contract and task brief."
```

### Claude.ai (web)

1. Start a new conversation
2. Attach: `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `TASK-BRIEF.md`, and the **matching prompt file**
3. Type your request matching the task type

### Claude API

```python
import anthropic

client = anthropic.Anthropic()

contract = open("CLAUDE-CODE-CONTRACT.md").read()
context = open("CODING-CONTEXT.md").read()
task_brief = open("TASK-BRIEF.md").read()

# Pick the prompt that matches your task type
prompt_file = "prompts/PROMPT-DEBUG.md"  # or MASTER, REFACTOR, PARTIAL-REWRITE
mode_prompt = open(prompt_file).read()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=8192,
    system=mode_prompt,
    messages=[{
        "role": "user",
        "content": f"{contract}\n\n{context}\n\n{task_brief}\n\nFollow the contract and task brief above."
    }],
)
```

---

## Step 3: Claude Produces Output

What Claude delivers depends on the task mode:

### Generate Mode
- Complete source code (no TODOs)
- Full test suite (>= 80% coverage, all pass)
- Type hints (100% on public APIs)
- Documentation (README with examples)
- Configuration (`.env.example`)
- Build tools (Makefile, setup script)
- Git ready (`.gitignore`, first commit)

### Refactor Mode
- Modified source files (restructured, renamed, extracted)
- REFACTOR-PLAN documenting scope and changes
- All existing tests passing unchanged
- Coverage maintained or improved

### Debug Mode
- Regression test (fails before, passes after)
- Minimal fix (fewest lines changed)
- DEBUG-REPORT with root cause analysis
- All existing tests passing

### Partial Rewrite Mode
- New implementation of the targeted module
- New unit tests for the rewritten code
- REWRITE-PLAN documenting scope and boundary contracts
- Migration guide (if API changed)
- All integration tests passing

---

## Step 4: Verify & Ship

```bash
cd ~/my-project

./setup.sh          # install dependencies
make test           # should pass with >= 80% coverage
make lint           # should show zero warnings
mypy src/           # type check (Python) — or tsc --noEmit (TS)
make run            # should start without errors
git status          # should be clean
```

If all pass, **ship it**.

---

## When Claude Falls Short

Don't accept broken code. Be specific about what's wrong:

```
Bad:  "The code has bugs"
Good: "fetch_user() is missing error handling for DatabaseError.
       Regenerate src/api.py with explicit try/except for all database calls."

Bad:  "Tests don't work"
Good: "Test coverage is 73%, contract requires >= 80%.
       Add integration tests for error conditions in tests/integration/."
```

### Rejection Protocol

1. **Identify the issue:** "Coverage is 65%, contract requires 80%"
2. **Request specific fix:** "Add integration tests for error paths in `tests/integration/test_api.py`"
3. **Rerun gate:** "Run `make test` before returning"

---

## Example: Full Workflow

### 1. Create brief

```bash
mkdir ~/weather-api && cd ~/weather-api
cp /path/to/claude-code-contracts/TASK-BRIEF-TEMPLATE.md TASK-BRIEF.md
# Edit TASK-BRIEF.md with your requirements
```

### 2. Start Claude Code

```bash
claude --add-dir /path/to/claude-code-contracts

> @/path/to/claude-code-contracts/CLAUDE-CODE-CONTRACT.md @/path/to/claude-code-contracts/CODING-CONTEXT.md @/path/to/claude-code-contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md @TASK-BRIEF.md Generate a production-grade weather API per the contract and task brief.
```

### 3. Verify

```bash
npm install
npm test        # 87% coverage
npm run lint    # zero warnings
npm start       # server on port 3000
```

### 4. Ship

```bash
git add .
git commit -m "feat: initial weather API implementation"
```

---

## Customization

### Add your own language rules

Edit `CODING-CONTEXT.md`:

```markdown
## Project-Specific Standards
- Error codes: HTTP status + custom codes (ERR_RATE_LIMITED)
- Logging: Always include request_id
- Async: async/await only, no callbacks
```

### Adjust quality bar

Edit `CLAUDE-CODE-CONTRACT.md`:

```markdown
## Coverage Requirement
- Default: >= 80%
- Your project: >= 90%
```

### Modify the master prompt

Edit `prompts/PROMPT-CLAUDE-CODE-MASTER.md` to change the architecture patterns, testing requirements, or delivery format Claude follows.

---

## Choosing the Right Mode

A common mistake is using the wrong mode:

| Symptom | Wrong Mode | Right Mode |
|---|---|---|
| "I refactored but tests broke" | Refactor (behavior changed) | Partial Rewrite (new design) |
| "My bug fix turned into a rewrite" | Debug (scope crept) | Debug first, then Refactor |
| "I need to fix a bug AND clean up" | Trying both at once | Debug first, then Refactor (two tasks) |
| "The whole thing needs a redo" | Partial Rewrite | Generate (start fresh) |

**Rule of thumb:** If in doubt, start with the narrower mode. You can always follow up with a broader one.

---

## Final Notes

### Quality Is Not Negotiable

This contract exists because "I'll iterate later" projects never do. Build right the first time.

### One Task, One Mode

Don't mix modes in a single session. Fix the bug first (Debug mode), then clean up the code (Refactor mode), then replace the module if needed (Partial Rewrite mode). Each gets its own TASK-BRIEF and prompt.

### Scale the Brief, Not the Standards

Whether it's a 2-hour CLI tool or a 40-hour platform, the contract applies the same. Write a more detailed TASK-BRIEF for bigger projects.
