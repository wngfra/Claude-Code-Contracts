# Claude Code Cheat Sheet

**Copy-paste templates for instant high-quality code generation.**

---

## TL;DR Formula

```
TASK-BRIEF (with task type) + CONTRACT + matching PROMPT
= Production-ready code, first try
```

| Task Type | Prompt to Attach |
|---|---|
| **Generate** (new project) | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| **Refactor** (restructure) | `prompts/PROMPT-REFACTOR.md` |
| **Debug** (fix bug) | `prompts/PROMPT-DEBUG.md` |
| **Partial Rewrite** (replace module) | `prompts/PROMPT-PARTIAL-REWRITE.md` |

---

## 1. Quick TASK-BRIEFs (2 min each)

### Generate (New Project)

```markdown
# TASK-BRIEF: [Project]

**Task Type:** Generate
**Name:** [name]
**Language:** Python / TypeScript / Go / Rust / C / C++
**Problem:** [1 sentence describing what you're building]

**Must have:**
- [ ] Feature A
- [ ] Feature B
- [ ] Feature C

**Success:** Tests pass (≥80%), setup works first try, README has examples
**Constraints:** [Any limits or dependencies]
```

### Refactor (Existing Code)

```markdown
# TASK-BRIEF: Refactor [module]

**Task Type:** Refactor
**Language:** [language]
**Files in scope:** [list files/modules to restructure]

**Problem:** [what's wrong with the current structure]
**Goal:** [what the code should look like after]

**Constraints:**
- Existing tests must pass unchanged
- Public API must not change
- No bug fixes mixed in

**Success:** Same behavior, cleaner code, tests still green
```

### Debug (Fix a Bug)

```markdown
# TASK-BRIEF: Fix [bug description]

**Task Type:** Debug
**Language:** [language]
**Broken function/module:** [file:function]

**Bug:** [what happens vs what should happen]
**Reproduction:** [exact steps or input to trigger it]
**Error output:** [paste error message/stack trace]

**Constraints:**
- Minimal fix only
- Must include regression test
- No refactoring

**Success:** Regression test passes, all existing tests pass, root cause documented
```

### Partial Rewrite (Replace Module)

```markdown
# TASK-BRIEF: Rewrite [module]

**Task Type:** Partial Rewrite
**Language:** [language]
**Module to replace:** [file paths]
**Replace with:** [new technology/approach]

**Why not refactor:** [why incremental changes aren't enough]

**Must preserve:**
- [ ] [Public function A]
- [ ] [Public function B]
- [ ] [Error types X, Y]

**Success:** Callers unchanged, new tests pass, migration guide if API changed
```

That's it. Claude Code handles the rest.

---

## 2. Start Claude Code

### Claude Code CLI (recommended)

```bash
cd ~/my-project
claude --add-dir /path/to/claude-code-contracts
```

Then reference the **right prompt for your task type:**

```bash
# Generate (new project)
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-CLAUDE-CODE-MASTER.md @TASK-BRIEF.md
  Generate production code following the contract and task brief.

# Refactor (restructure existing code)
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-REFACTOR.md @TASK-BRIEF.md
  Refactor the code following the contract and task brief.

# Debug (fix a bug)
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-DEBUG.md @TASK-BRIEF.md
  Debug and fix the issue following the contract and task brief.

# Partial Rewrite (replace a module)
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-PARTIAL-REWRITE.md @TASK-BRIEF.md
  Rewrite the module following the contract and task brief.
```

(Use full paths like `@/path/to/claude-code-contracts/CLAUDE-CODE-CONTRACT.md` if not using `--add-dir`)

### One-shot (pipe mode)

```bash
# Adjust the prompt file for your task type
cat CLAUDE-CODE-CONTRACT.md CODING-CONTEXT.md prompts/PROMPT-DEBUG.md TASK-BRIEF.md \
  | claude --print "Debug and fix the issue following the attached contract and task brief."
```

### Claude.ai (web)

1. Start a new conversation
2. Attach: `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `TASK-BRIEF.md`, and the **matching prompt file**
3. Type your request matching the task type

---

## 3. What Claude Code Delivers

```
project/
├── src/                 # All source code
│   ├── __init__.py
│   ├── main.py         # Entry point
│   ├── core/           # Core logic
│   ├── models.py       # Data models
│   ├── errors.py       # Custom exceptions
│   └── utils/          # Utilities
├── tests/              # Full test suite
│   ├── unit/
│   ├── integration/
│   └── conftest.py
├── scripts/
│   └── setup.sh        # One-command setup
├── README.md           # Setup + examples
├── .env.example        # Config template
├── Makefile            # test, lint, run targets
├── pyproject.toml      # Dependencies
├── .gitignore
└── .git/               # First commit ready

Status:
✅ make test     → 45 passed, 87% coverage
✅ make lint     → zero warnings
✅ git status    → nothing to commit
```

---

## 4. Verification (5 min)

```bash
cd project/

# Install
./setup.sh

# Test (must pass + show coverage)
make test

# Lint (must have zero output = success)
make lint

# Type check (must pass)
mypy src/  # or `tsc --noEmit` for TS

# Run (must start without errors)
make run

# Git (must be clean)
git status
```

If all green → **Done. Ship it.**

---

## 5. If Something's Wrong

Don't accept broken code. Request specific fix:

### Generate Mode
```
❌ "The code has bugs"
✅ "The fetch_user() function is missing error handling for DatabaseError.
    Regenerate src/api.py with explicit try/except for all database calls."
```

### Refactor Mode
```
❌ "The refactoring broke something"
✅ "test_validate_email fails after refactoring. The function now returns
    lowercase but callers expect original case. Revert the behavior change
    — refactor structure only, not behavior."
```

### Debug Mode
```
❌ "Your fix is too big"
✅ "Lines 45-78 are cosmetic changes unrelated to the bug. Keep only the
    fix on line 52 and the regression test. Remove all other changes."
```

### Partial Rewrite Mode
```
❌ "Callers are broken"
✅ "fetch_user() now raises NotFoundError but callers in src/api/handlers.py
    check for None. Either preserve the old return type or update all 3 callers
    and add a migration guide."
```

Be specific. Rerun with the exact issue stated.

---

## 6. Language-Specific Templates

### Python

```markdown
# TASK-BRIEF: [Name]

**Language:** Python  
**Framework:** FastAPI / Flask / None

**Must have:**
- [ ] Database models (Pydantic dataclasses)
- [ ] API endpoints with validation
- [ ] Error handling with custom exceptions
- [ ] Logging (INFO/DEBUG/ERROR levels)
- [ ] Tests with pytest (unit + integration)

**Success Criteria:**
```bash
pip install -e ".[dev]"
make test      # ≥80% coverage
make lint      # ruff + mypy pass
make run       # Starts without errors
```
```

### TypeScript/Node

```markdown
# TASK-BRIEF: [Name]

**Language:** TypeScript  
**Framework:** Express / Fastify / None

**Must have:**
- [ ] Strict TypeScript (no `any`)
- [ ] Async error handling
- [ ] Request validation with Zod/io-ts
- [ ] Structured logging (pino/winston)
- [ ] Tests with vitest (unit + integration)

**Success Criteria:**
```bash
npm install
npm test       # ≥80% coverage
npm run lint   # eslint + tsc pass
npm start      # Starts on port 3000
```
```

### Go

```markdown
# TASK-BRIEF: [Name]

**Language:** Go  
**Framework:** Gin / Echo / stdlib

**Must have:**
- [ ] Typed structs with documentation
- [ ] Error wrapping with context
- [ ] Structured logging (slog/zap)
- [ ] Tests with go test (unit + integration)
- [ ] CLI friendly (flags, return codes)

**Success Criteria:**
```bash
go mod download
go test ./... -v -cover      # ≥80% coverage
go vet ./...                  # Zero warnings
go run ./cmd/main.go          # Runs successfully
```
```

### Rust

```markdown
# TASK-BRIEF: [Name]

**Language:** Rust
**Framework:** Actix-web / Axum / Tokio / None

**Must have:**
- [ ] Strongly typed data models (structs + enums)
- [ ] Error handling with `thiserror` + `Result<T, E>`
- [ ] Structured logging (`tracing`)
- [ ] Tests with `cargo test` (unit + integration)
- [ ] No `unsafe` without documented justification

**Success Criteria:**
```bash
cargo build --release
cargo test                         # ≥80% coverage
cargo clippy -- -D warnings        # zero warnings
cargo run                          # Runs successfully
```
```

### C

```markdown
# TASK-BRIEF: [Name]

**Language:** C
**Standard:** C11 / C17
**Build:** CMake / Make

**Must have:**
- [ ] Clean header/implementation separation (.h/.c)
- [ ] Error codes for all fallible operations
- [ ] No memory leaks (valgrind clean)
- [ ] Tests with CUnit/Unity/CMocka
- [ ] Documented public API (doxygen-style comments)

**Success Criteria:**
```bash
mkdir build && cd build && cmake .. && make
ctest --output-on-failure          # All tests pass
valgrind --leak-check=full ./myapp # No leaks
cppcheck --enable=all ../src/      # Zero warnings
```
```

### C++

```markdown
# TASK-BRIEF: [Name]

**Language:** C++
**Standard:** C++17 / C++20
**Build:** CMake
**Framework:** None / Boost / Qt

**Must have:**
- [ ] RAII for all resource management
- [ ] Smart pointers (no raw new/delete)
- [ ] Custom exception hierarchy
- [ ] Structured logging (spdlog)
- [ ] Tests with Google Test or Catch2

**Success Criteria:**
```bash
mkdir build && cd build && cmake .. && make
ctest --output-on-failure          # All tests pass
valgrind --leak-check=full ./myapp # No leaks
clang-tidy ../src/*.cpp            # Zero warnings
```
```

---

## 7. Quality Checklist (Auto-Rejected if Fails)

```
Before returning:

Source Code:
☐ All files present (no placeholders)
☐ No TODOs or comments like "// TODO:"
☐ Clear, descriptive variable names (no `x`, `temp`, `thing`)
☐ Proper error handling (no silent failures)

Type System:
☐ Python: 100% type hints (mypy passes)
☐ TypeScript: Strict mode (tsc --noEmit passes)
☐ Go: All exported funcs documented, go vet passes
☐ Rust: cargo check + cargo clippy -- -D warnings passes
☐ C/C++: Compiles with -Wall -Wextra -Werror, clang-tidy clean

Tests:
☐ Tests exist (tests/ directory)
☐ All tests pass (zero failures)
☐ Coverage ≥80% (checked with --cov flag)
☐ No skipped tests (@skip, @pytest.mark.skip)
☐ Unit + integration tests present

Tooling:
☐ Makefile / package.json scripts working
☐ Linting configured and passing
☐ Type checking configured and passing
☐ .env.example present with all vars
☐ setup.sh / install script present

Documentation:
☐ README.md complete with examples
☐ Examples actually work (tested)
☐ Architecture explained
☐ Known limitations listed

Git:
☐ .gitignore correct
☐ Ready to git add . && git commit -m "feat: initial [project]"
```

---

## 8. Anti-Patterns (Never Accept)

| Pattern | Why It Fails |
|---|---|
| **Code with TODOs** | Incomplete work |
| **Missing type hints** | Unmaintainable, poor IDE support |
| **Tests that skip** | False positive coverage |
| **Cryptic names** | Unmaintainable |
| **Silent error handling** | Debugging nightmare |
| **Setup requires manual steps** | Breaks first-run experience |
| **README examples don't work** | Unusable documentation |
| **Linting warnings** | Quality debt |
| **Coverage <80%** | Untested code |
| **Secrets in code** | Security issue |

---

## 9. Common Issues & Fixes

### "Code generation is slow"
- **Cause:** Large project in one go
- **Fix:** Split into smaller TASK-BRIEFs (core, then API, then tests)

### "Tests don't pass"
- **Cause:** Missing fixtures or imports
- **Fix:** Request specific error message, regenerate with explicit test setup

### "Type hints incomplete"
- **Cause:** Claude Code wasn't strict enough
- **Fix:** Add to TASK-BRIEF: "Strict type hints required. Run `mypy` before returning."

### "Setup fails"
- **Cause:** Missing dependencies or wrong commands
- **Fix:** Request working setup.sh that installs all deps and runs tests

### "README examples broken"
- **Cause:** Code changed but examples not updated
- **Fix:** Always test examples before returning

---

## 10. Pro Tips

### Tip 1: Batch Similar Tasks
```
Instead of: 3 separate spawns
Do this:    1 project with 3 modules

TASK-BRIEF: "Build auth system with 3 modules: JWT, RBAC, MFA"
Saves tokens & time.
```

### Tip 2: Start Simple, Add Features
```
First run:  Core functionality only
Second run: Add validation, error handling, tests
(Iteration is OK if you're adding features, not fixing broken code)
```

### Tip 3: Lock Good Code
```
After 1st successful generation:
git commit -m "feat: initial [project]"

Then any new work: git checkout main && git branch feature/...
Protects your baseline.
```

### Tip 4: Use Fixtures for Shared Data
```python
# tests/fixtures.py
@pytest.fixture
def sample_user():
    return User(id="123", name="Alice", email="alice@example.com")

# tests/unit/test_models.py
def test_user_validation(sample_user):
    assert sample_user.id == "123"
```

### Tip 5: Document Trade-Offs
```python
def cache_users(ttl: int = 300):
    """Cache users in memory.
    
    Trade-off: Fast, but not distributed.
    For multi-server, use Redis instead.
    """
```

---

## 11. Boilerplate Code Snippets

### Error Handling (Python)
```python
class ApplicationError(Exception):
    """Base error."""
    pass

class ValidationError(ApplicationError):
    pass

class NotFoundError(ApplicationError):
    pass

def safe_fetch(user_id: str) -> User:
    if not user_id:
        raise ValidationError("user_id cannot be empty")
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise NotFoundError(f"User {user_id} not found")
    return user
```

### Logging (Python)
```python
import logging

logger = logging.getLogger(__name__)

def process_user(user_id: str) -> None:
    logger.info(f"Processing user: {user_id}")
    try:
        user = fetch_user(user_id)
        logger.debug(f"Fetched: {user}")
    except ValidationError as e:
        logger.warning(f"Validation failed: {e}")
    except Exception as e:
        logger.error(f"Unexpected error: {e}", exc_info=True)
        raise
```

### Error Handling (TypeScript)
```typescript
class ApplicationError extends Error {
  constructor(public code: string, message: string) {
    super(message);
    this.name = 'ApplicationError';
  }
}

async function safeAsync<T>(fn: () => Promise<T>): Promise<T> {
  try {
    return await fn();
  } catch (error) {
    if (error instanceof ApplicationError) {
      logger.error({ code: error.code }, error.message);
    } else {
      logger.error({ error }, 'Unexpected error');
    }
    throw error;
  }
}
```

### Error Handling (Rust)
```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("validation: {0}")]
    Validation(String),
    #[error("not found: {0}")]
    NotFound(String),
    #[error("database: {source}")]
    Database { #[from] source: sqlx::Error },
}

pub fn safe_fetch(user_id: &str) -> Result<User, AppError> {
    if user_id.is_empty() {
        return Err(AppError::Validation("user_id cannot be empty".into()));
    }
    db.query_user(user_id)
        .map_err(AppError::from)?
        .ok_or_else(|| AppError::NotFound(format!("User {user_id}")))
}
```

### Error Handling (Go)
```go
import (
    "errors"
    "fmt"
)

var ErrNotFound = errors.New("not found")

func SafeFetch(ctx context.Context, userID string) (*User, error) {
    if userID == "" {
        return nil, fmt.Errorf("user_id cannot be empty: %w", ErrInvalidInput)
    }
    user, err := db.QueryUser(ctx, userID)
    if err != nil {
        return nil, fmt.Errorf("fetch user %s: %w", userID, err)
    }
    if user == nil {
        return nil, fmt.Errorf("user %s: %w", userID, ErrNotFound)
    }
    return user, nil
}
```

### Error Handling (C)
```c
typedef enum { ERR_OK = 0, ERR_INVALID = -1, ERR_NOT_FOUND = -2 } ErrorCode;

ErrorCode safe_fetch(const char *user_id, User *out) {
    if (!user_id || !user_id[0]) return ERR_INVALID;
    if (db_query_user(user_id, out) != 0) return ERR_NOT_FOUND;
    return ERR_OK;
}
```

### Error Handling (C++)
```cpp
#include <stdexcept>

class AppError : public std::runtime_error {
    using std::runtime_error::runtime_error;
};
class ValidationError : public AppError { using AppError::AppError; };
class NotFoundError : public AppError { using AppError::AppError; };

User safe_fetch(const std::string &user_id) {
    if (user_id.empty()) throw ValidationError("user_id cannot be empty");
    auto user = db.query_user(user_id);
    if (!user) throw NotFoundError("User " + user_id + " not found");
    return *user;
}
```

---

## 12. Final Sanity Check

Before you ship, ask yourself:

```
☐ Would I be proud to show this to other engineers?
☐ Could someone clone this and run it immediately?
☐ Are all error cases handled explicitly?
☐ Is the code easy to modify 6 months from now?
☐ Did I test the examples in the README?
☐ Does setup work on a fresh machine?
```

If any answer is "no", it's not ready.

---

## Remember

> **Quality is not about perfection. It's about clarity, completeness, and care.**

Use this cheat sheet to enforce that. Every time.

---

**Last Updated:** March 2026  
**Version:** 1.0
