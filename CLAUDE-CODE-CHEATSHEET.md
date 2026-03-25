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
| **Add Function** (extend existing code) | `prompts/PROMPT-ADD-FUNCTION.md` |
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

### Add Function (Extend Existing Code)

```markdown
# TASK-BRIEF: Add [feature] to [project]

**Task Type:** Add Function
**Language:** Python / TypeScript / Go / Rust / C / C++
**Existing project:** [repo path or URL]

**What to add:** [1 sentence describing the new functionality]
**Where it fits:** [which existing files/modules to extend]

**Must have:**
- [ ] Feature A
- [ ] Feature B
- [ ] Feature C

**Constraints:**
- Must follow existing code conventions
- All existing tests must pass
- New code needs tests (>=80% coverage)

**Success:** New functionality works, existing tests pass, new tests pass, docs updated
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

# Add Function (extend existing code)
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-ADD-FUNCTION.md @TASK-BRIEF.md
  Add the new functionality following the contract and task brief.

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

### Add Function Mode
```
❌ "The new feature doesn't work right"
✅ "The new export_data() function uses print() for logging, but the rest of
    the codebase uses structured logging with slog. Rewrite to follow existing
    conventions. Also missing unit tests for the error path."
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

> **Full examples for all 6 languages** (Python, TypeScript, Rust, Go, C, C++) are in `CLAUDE-CODE-CONTRACT.md` §Language-Specific Standards. Each includes:
> - Error handling with custom exception hierarchy
> - Structured logging patterns
> - Testing examples (unit + integration)
> - Type hint / type safety patterns
>
> **Refer to the contract instead of duplicating here.** This saves ~4,000 tokens per session.

### Quick Pattern Reference (all languages)

| Pattern | Rule |
|---------|------|
| **Error hierarchy** | Base error → domain-specific errors (Validation, NotFound, Database) |
| **Error messages** | Include what failed + which input: `f"Failed to fetch user {user_id}: {e}"` |
| **Error chaining** | Python: `raise X from e` / Go: `fmt.Errorf("...: %w", err)` / Rust: `#[from]` |
| **Logging** | `info` for operations, `debug` for data, `error` for failures with context |
| **Never** | Empty catch/except, bare `raise`, silent error swallowing |

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

## 13. Token Efficiency Tips

### Minimal Attachment Strategy

Not every session needs all files. Choose the minimum context:

| Session Type | Attach | Skip |
|---|---|---|
| **Quick generate** (small project) | CONTRACT + matching PROMPT + BRIEF | GUIDE, CHEATSHEET, THINKING-FRAMEWORKS |
| **Complex generate** (novel domain) | CONTRACT + PROMPT + BRIEF + THINKING-FRAMEWORKS | GUIDE, CHEATSHEET |
| **Add Function / Refactor / Debug** | CONTRACT + matching PROMPT + BRIEF | CHEATSHEET, THINKING-FRAMEWORKS, CODING-CONTEXT |
| **Reference lookup** | CHEATSHEET only | Everything else |

### Reduce Prompt Size

- Use **task brief** for specifics — don't repeat what's in the contract
- For repeat projects in the same language, drop other language sections from CONTRACT
- Use CHEATSHEET for quick jobs, full CONTRACT for production work

### Session Splitting for Large Projects

```
Session 1: "Generate core types and business logic" (src/core/, src/models/)
Session 2: "Generate API layer using the core from Session 1" (src/api/)
Session 3: "Generate test suite for existing code" (tests/)
```

Each session is smaller, more focused, and uses fewer tokens than one massive generation.

---

## 14. Handling Complex Tasks

For non-trivial problems (algorithm design, unfamiliar domains, architectural decisions):

→ Attach `THINKING-FRAMEWORKS.md` alongside the contract and prompt.

It provides:
- Problem decomposition (invariants → core subproblem → solution shape)
- Algorithm design principles (data structure selection, complexity contracts)
- Novel domain handling (vocabulary → constraints → operations → analogies)
- Design decision documentation template
- Incremental complexity management (build in layers, verify each)

---

## Remember

> **Quality is not about perfection. It's about clarity, completeness, and care.**

Use this cheat sheet to enforce that. Every time.

---

**Last Updated:** March 2026
**Version:** 1.1
