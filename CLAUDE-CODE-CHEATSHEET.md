# Claude Code Cheat Sheet

**Copy-paste templates for instant high-quality code generation.**

---

## TL;DR Formula

```
Specific TASK-BRIEF + CLAUDE-CODE-CONTRACT.md + PROMPT-CLAUDE-CODE-MASTER.md
= Production-ready code, first try
```

---

## 1. Quick TASK-BRIEF (2 min)

```markdown
# TASK-BRIEF: [Project]

**Name:** [name]
**Language:** Python / TypeScript / Go
**Problem:** [1 sentence describing what you're building]

**Must have:**
- [ ] Feature A
- [ ] Feature B  
- [ ] Feature C

**Success:** Tests pass (≥80%), setup works first try, README has examples

**Constraints:** [Any limits or dependencies]
```

That's it. Claude Code handles the rest.

---

## 2. Command to Spawn Claude Code

### Option A: Direct (if ACP configured)
```bash
openclaw acp --session claude-code << 'EOF'
Generate production code per TASK-BRIEF.md following CLAUDE-CODE-CONTRACT.md

Must include:
- Source code (src/)
- Tests (tests/) with ≥80% coverage
- Type hints (100%)
- Setup script & Makefile
- README with examples
- .env.example
- Ready to git commit

Quality: Zero TODOs, zero warnings, all tests pass.
EOF
```

### Option B: Via Python
```python
spawn_session(
    runtime="acp",
    agentId="claude-code",
    mode="session",
    task="Generate production code following CLAUDE-CODE-CONTRACT.md",
    attachments=[
        ("CLAUDE-CODE-CONTRACT.md", read("contracts/CLAUDE-CODE-CONTRACT.md")),
        ("CODING-CONTEXT.md", read("contracts/CODING-CONTEXT.md")),
        ("TASK-BRIEF.md", read("TASK-BRIEF.md")),
        ("PROMPT-CLAUDE-CODE-MASTER.md", read("contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md")),
    ]
)
```

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

```
❌ "The code has bugs"
✅ "The fetch_user() function is missing error handling for DatabaseError. 
    Regenerate src/api.py with explicit try/except for all database calls."

❌ "Tests don't work"
✅ "Test coverage is 73%, contract requires ≥80%. 
    Add integration tests for error conditions in tests/integration/."

❌ "It's not type hinted"
✅ "function validate_email() on line 47 of src/validators.py is missing 
    return type hint. Regenerate src/validators.py with 100% type coverage."
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
☐ Go: All exported funcs documented

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

## 11. When to Use This vs When to Use Sonnet

| Task | Use Claude Code | Use Sonnet |
|---|---|---|
| Generate new project | ✅ Claude Code | — |
| Fix specific bug | — | ✅ Sonnet |
| Add feature to existing code | ✅ Claude Code | ✅ Sonnet |
| Review architecture | — | ✅ Sonnet |
| Optimize performance | — | ✅ Sonnet |
| Write tests for existing code | — | ✅ Sonnet |
| Refactor large module | ✅ Claude Code | ✅ Sonnet |
| One-off script | — | ✅ Sonnet |

---

## 12. Boilerplate Code Snippets

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

---

## 13. Final Sanity Check

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
