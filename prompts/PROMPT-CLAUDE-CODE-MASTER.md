# PROMPT: Claude Code — Master Generator (Generate Mode)

**Use this prompt when building a new project from scratch. For other task types, use the dedicated prompts:**
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
- **Python:** Full type hints on all public functions + classes
  - Use `from typing import` for complex types
  - Use Pydantic `@dataclass` or `BaseModel` for data structures
  - Run `mypy` — must pass with zero errors

- **TypeScript:** Strict mode enabled, full type coverage
  - All function parameters and returns typed
  - No `any` types except where explicitly documented
  - Run `tsc --noEmit` — must pass with zero errors

- **Rust:** Full type annotations on all public functions
  - Use `Result<T, E>` for all fallible operations
  - Use `thiserror` for custom error types
  - Run `cargo check` and `cargo clippy -- -D warnings` — must pass with zero errors

- **Go:** All exported functions documented with godoc comments
  - Return `error` as last return value for fallible operations
  - Use `fmt.Errorf("...: %w", err)` for error wrapping
  - Run `go vet ./...` — must pass with zero errors

- **C:** Consistent function signatures with documented parameters
  - Use fixed-width types (`stdint.h`) for cross-platform safety
  - Return error codes (0 = success, negative = error)
  - Compile with `-Wall -Wextra -Werror` — must pass with zero warnings

- **C++:** Full const-correctness, RAII for all resources
  - Use `std::optional`, `std::variant`, `std::expected` where appropriate
  - Smart pointers over raw `new`/`delete`
  - Compile with `-Wall -Wextra -Werror` — must pass with zero warnings

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

**Logging:**
```python
import logging

logger = logging.getLogger(__name__)

def process_user(user_id: str) -> User:
    logger.info(f"Processing user: {user_id}")
    try:
        user = fetch_user(user_id)
        logger.debug(f"Fetched user: {user.name}")
        return user
    except DatabaseError as e:
        logger.error(f"Database error for user {user_id}: {e}", exc_info=True)
        raise
```

**Error Handling:**
```python
class ApplicationError(Exception):
    """Base error."""
    pass

class ValidationError(ApplicationError):
    """User input validation failed."""
    pass

class DatabaseError(ApplicationError):
    """Database operation failed."""
    pass

def fetch_user(user_id: str) -> User:
    if not user_id:
        raise ValidationError("user_id cannot be empty")
    try:
        return db.query(User).filter(User.id == user_id).first()
    except Exception as e:
        raise DatabaseError(f"Failed to fetch user {user_id}: {e}") from e
```

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

**Makefile (Python):**
```makefile
.PHONY: install test lint format run clean

install:
	pip install -e ".[dev]"

test:
	pytest tests/ --cov=src --cov-report=html

lint:
	ruff check src/ tests/
	mypy src/

format:
	black src/ tests/
	isort src/ tests/

run:
	python -m src.main

clean:
	find . -type d -name __pycache__ -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete
	rm -rf .pytest_cache/ htmlcov/
```

**package.json scripts (TypeScript):**
```json
{
  "scripts": {
    "install": "npm install",
    "build": "tsc",
    "test": "vitest --coverage",
    "lint": "eslint src/ --fix && tsc --noEmit",
    "dev": "tsx watch src/index.ts",
    "start": "node dist/index.js"
  }
}
```

**Makefile (Rust):**
```makefile
.PHONY: build test lint format run clean

build:
	cargo build --release

test:
	cargo test -- --nocapture
	cargo tarpaulin --out html

lint:
	cargo clippy -- -D warnings
	cargo fmt -- --check

format:
	cargo fmt

run:
	cargo run

clean:
	cargo clean
```

**Makefile (Go):**
```makefile
.PHONY: build test lint format run clean

build:
	go build -o bin/app ./cmd/...

test:
	go test ./... -v -cover -coverprofile=coverage.out
	go tool cover -html=coverage.out -o coverage.html

lint:
	go vet ./...
	golangci-lint run

format:
	gofmt -w .
	goimports -w .

run:
	go run ./cmd/main.go

clean:
	rm -rf bin/ coverage.out coverage.html
```

**Makefile (C/C++):**
```makefile
.PHONY: build test lint run clean

build:
	mkdir -p build && cd build && cmake .. && make

test:
	cd build && ctest --output-on-failure

lint:
	clang-tidy src/*.c src/*.cpp -- -std=c17 -std=c++17
	cppcheck --enable=all --error-exitcode=1 src/

run:
	./build/myapp

clean:
	rm -rf build/
```

Run these before returning code:
```bash
make test && make lint
# or
npm test && npm run lint
# or
cargo test && cargo clippy -- -D warnings
# or
go test ./... && go vet ./...
```

### 8. Git Ready

**.gitignore:**
```
# Python
__pycache__/
*.pyc
.pytest_cache/
htmlcov/

# TypeScript/Node
dist/
node_modules/

# Rust
target/

# Go
bin/
coverage.out

# C/C++
build/
*.o
*.a
*.so
*.dylib

# Common
.env
```

After generation:
```bash
git add .
git commit -m "feat: Initial [project] implementation"
git log --oneline  # Show last commit
```

---

## What I'm Handing You

**Input Files:**
- `TASK-BRIEF.md` — Problem statement (check the **Task Type** field)
- `CODING-CONTEXT.md` — Language/project standards
- `CLAUDE-CODE-CONTRACT.md` — Quality standards
- This prompt (`PROMPT-CLAUDE-CODE-MASTER.md`) — Generate mode instructions

> **Important:** If the TASK-BRIEF specifies a task type other than "Generate", stop and tell the user to use the matching prompt instead (`PROMPT-REFACTOR.md`, `PROMPT-DEBUG.md`, or `PROMPT-PARTIAL-REWRITE.md`). Each mode has different constraints and checklists.

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
