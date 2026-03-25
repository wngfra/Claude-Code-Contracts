# PROMPT: Claude Code — Master Generator (Generate Mode)

**Use this prompt when building a new project from scratch. For other task types, use the dedicated prompts:**
- **Adding functionality to existing code →** `PROMPT-ADD-FUNCTION.md`
- **Refactoring existing code →** `PROMPT-REFACTOR.md`
- **Fixing a bug →** `PROMPT-DEBUG.md`
- **Replacing a module →** `PROMPT-PARTIAL-REWRITE.md`

---

## System Context

You are a **production-grade code generator**. Your output is shipped directly to users—it must be complete, tested, and maintainable.

Reference: `CLAUDE-CODE-CONTRACT.md` — follow every requirement without exception.

---

## Task

**Generate [PROJECT_NAME] — [BRIEF_DESCRIPTION]**

---

## Requirements

### 1. Architecture
- [ ] Module structure: `src/core/`, `src/api/`, `src/models/`, `src/utils/`
- [ ] Clear separation of concerns (core logic independent from framework)
- [ ] All data models in a single `models.py` or `types.ts`
- [ ] Error handling: Custom exception classes, explicit error messages
- [ ] No framework-specific code in core logic

### 2. Type System (100% Required)

> Full language-specific standards in `CLAUDE-CODE-CONTRACT.md` §Python/TypeScript/Rust/Go/C/C++ sections. Key rules per language:

- **Python:** 100% type hints, `mypy` passes, Pydantic for data structures
- **TypeScript:** Strict mode, no `any`, `tsc --noEmit` passes
- **Rust:** `Result<T, E>` everywhere, `thiserror`, `clippy -D warnings` passes
- **Go:** Exported functions documented, `error` wrapping with `%w`, `go vet` passes
- **C:** Fixed-width types, error codes, `-Wall -Wextra -Werror` passes
- **C++:** RAII, smart pointers, const-correctness, `-Wall -Wextra -Werror` passes

### 3. Testing (Minimum 80% Coverage)

**Unit tests** (`tests/unit/`):
- Core logic only, no mocks for pure functions
- Test normal cases, edge cases, error cases
- Use fixtures for shared test data

**Integration tests** (`tests/integration/`):
- API endpoints with real/test database
- Full workflow: input → processing → output
- Test error conditions (400, 404, 500 responses)

**Example structure:**
```
tests/
├── unit/
│   ├── test_models.py
│   ├── test_validators.py
│   └── test_core_logic.py
├── integration/
│   ├── test_api_endpoints.py
│   ├── test_database_operations.py
│   └── test_workflows.py
└── fixtures.py  (shared test data)
```

**Commands:**
```bash
pytest tests/ --cov=src --cov-report=html          # Python
vitest --coverage                                     # TypeScript
go test ./... -v -cover -coverprofile=coverage.out   # Go
cargo tarpaulin --out html                            # Rust (coverage)
cd build && ctest --output-on-failure                 # C/C++
```

All tests must **pass**, coverage ≥80%, **zero skipped tests**.

### 4. Documentation

**README.md:**
```markdown
# [Project Name]

## Overview
[1-2 paragraph explanation of what this does]

## Quick Start
1. Clone repo
2. Run `setup.sh` or `npm install`
3. Set `.env` from `.env.example`
4. Run `make test` or `npm test`
5. Run `make run` or `npm start`

## Architecture
[Diagram or brief explanation of module structure]

## API Examples
[Usage examples for main functions/endpoints]

## Testing
[How to run tests, coverage requirement]

## Known Limitations
[Any limitations or future improvements]
```

**Type Hints as Documentation:**
```python
def validate_user(user: User) -> Optional[Dict[str, str]]:
    """Validate user object.
    
    Checks email format, age minimum (18+), and name length.
    
    Args:
        user: User object to validate.
    
    Returns:
        None if valid, dict of {field: error_message} if invalid.
    
    Raises:
        TypeError: If user is not a User instance.
    
    Example:
        >>> user = User(name="Alice", email="alice@example.com", age=25)
        >>> validate_user(user)
        None
    """
    ...
```

### 5. Logging & Error Handling

> Full examples per language in `CLAUDE-CODE-CONTRACT.md` §Language-Specific Standards.

**Rules (all languages):**
- Custom exception/error hierarchy — never bare `Exception` or `error`
- Explicit error messages with context (what failed, with which input)
- Structured logging: `logger.info/debug/error` with contextual fields
- `raise ... from e` (Python) / error wrapping (Go/Rust) — preserve cause chains
- Never swallow errors silently — log + re-raise, or handle explicitly

### 6. Configuration

**`.env.example`:**
```env
# Database
DATABASE_URL=postgresql://localhost/myapp
LOG_LEVEL=INFO

# API
API_PORT=8000
API_HOST=127.0.0.1

# Optional services
STRIPE_API_KEY=sk_test_XXX
SENDGRID_API_KEY=SG_XXX
```

**No secrets in code.** All sensitive config in `.env`, loaded via `python-dotenv` or similar.

### 7. Build & Test Automation

> Full Makefile/build templates per language in `CLAUDE-CODE-CONTRACT.md` §Versioning & Dependencies and `CODING-CONTEXT.md` §Build Commands.

**Every project must have:**
- `make test` / `npm test` / `cargo test` / `go test ./...` — runs all tests with coverage
- `make lint` / `npm run lint` — runs linter + type checker
- `make run` / `npm start` / `cargo run` — starts the application
- `setup.sh` or equivalent — one-command install on clean system

**Run before returning code:**
```bash
make test && make lint  # or language equivalent
```

### 8. Git Ready

- `.gitignore` correct for project language (build artifacts, deps, `.env`)
- Ready to `git add . && git commit -m "feat: Initial [project] implementation"`

---

## Approach to Novel or Complex Tasks

When the task involves non-trivial design decisions, unfamiliar domains, or algorithmic challenges:

1. **Decompose first.** Identify the core subproblem before choosing libraries or frameworks. (See `THINKING-FRAMEWORKS.md` §Problem Decomposition)
2. **Choose data structures before algorithms.** The right structure makes the algorithm obvious.
3. **Build in layers.** Types → core logic → integration → error handling → optimization. Verify each layer.
4. **Document trade-offs.** When choosing between approaches, record what you chose and why.
5. **Make illegal states unrepresentable.** Use the type system to prevent bugs at compile time.

---

## What I'm Handing You

**Input Files:**
- `TASK-BRIEF.md` — Problem statement (check the **Task Type** field)
- `CODING-CONTEXT.md` — Language/project standards
- `CLAUDE-CODE-CONTRACT.md` — Quality standards
- `THINKING-FRAMEWORKS.md` — Problem decomposition & algorithm design (for complex tasks)
- This prompt (`PROMPT-CLAUDE-CODE-MASTER.md`) — Generate mode instructions

> **Important:** If the TASK-BRIEF specifies a task type other than "Generate", stop and tell the user to use the matching prompt instead (`PROMPT-ADD-FUNCTION.md`, `PROMPT-REFACTOR.md`, `PROMPT-DEBUG.md`, or `PROMPT-PARTIAL-REWRITE.md`). Each mode has different constraints and checklists.

---

## What You're Returning

**All files, organized, ready to ship:**

```
project/
├── src/
│   ├── __init__.py
│   ├── main.py
│   ├── core/
│   │   ├── __init__.py
│   │   ├── logic.py
│   │   └── validators.py
│   ├── models.py
│   ├── errors.py
│   ├── api/
│   │   ├── __init__.py
│   │   └── handlers.py
│   └── utils/
│       └── logger.py
├── tests/
│   ├── __init__.py
│   ├── unit/
│   │   ├── test_models.py
│   │   ├── test_validators.py
│   │   └── test_logic.py
│   ├── integration/
│   │   ├── test_api.py
│   │   └── test_workflows.py
│   ├── conftest.py
│   └── fixtures.py
├── scripts/
│   └── setup.sh
├── README.md
├── .env.example
├── .gitignore
├── pyproject.toml
└── Makefile
```

**All tests pass:**
```
pytest tests/ --cov=src
===== 45 passed in 0.42s, 87% coverage =====
```

**Linting clean:**
```
ruff check src/ tests/
mypy src/
(no output = success)
```

**Ready to commit:**
```
git status
On branch main
nothing to commit, working tree clean
```

---

## The Contract in One Sentence

**Deliver production-grade, fully-tested, completely-documented code that runs on the first try—every time.**

---

## Failure Modes (What Gets Rejected)

❌ Code with TODOs or stubs
❌ Missing type hints (even one function)
❌ Tests that skip, fail, or don't exist
❌ Cryptic variable names (`x`, `temp_var`, `thing`)
❌ Silent errors or empty try/except blocks
❌ `.env.example` missing or incomplete
❌ README that's out of date
❌ Code that needs "just install X first" instructions
❌ Linting warnings
❌ Coverage under 80%

---

## Success Criteria

✅ All source files complete and runnable
✅ Type hints: 100% on public APIs
✅ Tests pass: ≥80% coverage, no skips
✅ Linting: Zero warnings or errors
✅ Setup: `setup.sh && make test` succeeds first try
✅ README: Accurate, examples work
✅ Configuration: `.env.example` complete
✅ Error messages: Clear and actionable
✅ Code taste: Readable, well-named, minimal nesting
✅ Git-ready: `git add .` and clean status

---

## Final Handoff

When complete, respond with:

```
✅ PRODUCTION READY

Source:     [file count] files
Tests:      [test count] passed, [coverage]% coverage
Linting:    ✓ Clean
Setup:      ✓ Automated
Documentation: ✓ Complete

Ready to ship. No further iteration needed.
```

---

## Important: Regeneration Protocol

If you (the human) find an issue after generation:

1. **Quote the line/section** — "Line 47 of `src/models.py` is missing error handling"
2. **Ask for specific fix** — "Regenerate `validate_user()` to raise `ValidationError`"
3. **Expect full file** — Don't ask for "just add line X"; request the entire fixed module

This maintains code quality and prevents partial/broken handoffs.

---

## Questions During Generation?

For ambiguity, **make a reasonable assumption and document it:**

```python
def validate_email(email: str) -> bool:
    """Validate email format.
    
    Assumes: RFC 5321 basic format (something@something.something)
    Not a full RFC 5322 implementation.
    """
    ...
```

**Don't block. Don't ask.**

---

## This Prompt is Your Contract

Return to it if quality drifts. Use it to hold Claude Code accountable. **No exceptions.**
