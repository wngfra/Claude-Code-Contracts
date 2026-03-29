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
**Language:** Python / TypeScript / Rust / Go / C++
**Type:** Library / CLI / API / Service
**Problem:** [1 sentence describing what you're building]

**Must have:**
- [ ] Feature A
- [ ] Feature B  
- [ ] Feature C

**Performance:** [e.g., "<100ms p99" or "not critical"]
**Security:** [e.g., "Validate all inputs" or "internal tool, minimal"]

**Success:** Tests pass (≥80%), benchmarks exist, setup works first try, README complete with tested examples, CHANGELOG present

**Constraints:** [Any limits or dependencies]
```

That's it. Claude Code handles the rest.

---

## 2. What Claude Code Delivers

```
project/
├── src/                 # All source code
├── tests/              # Full test suite (unit + integration)
├── benches/            # Performance benchmarks
├── .github/workflows/  # CI configuration
├── scripts/
│   └── setup.sh        # One-command setup
├── README.md           # Full README (per README Generation Spec)
├── CHANGELOG.md        # Version history (Keep a Changelog format)
├── .env.example        # Config template
├── Makefile            # test, lint, bench, run targets
├── [build file]        # pyproject.toml / Cargo.toml / go.mod / etc.
└── .gitignore

Status:
✅ make test     → 45 passed, 87% coverage
✅ make lint     → zero warnings
✅ make bench    → benchmarks run
✅ make audit    → no known vulnerabilities
✅ git status    → nothing to commit
```

---

## 3. Verification (5 min)

```bash
cd project/

# Install
./setup.sh

# Test (must pass + show coverage)
make test

# Lint (must have zero output = success)
make lint

# Benchmarks (must compile and run)
make bench

# Security audit (if available)
make audit  # or pip audit / npm audit / cargo audit

# Run (must start without errors)
make run

# Git (must be clean)
git status
```

If all green → **Done. Ship it.**

---

## 4. If Something's Wrong

Don't accept broken code. Request specific fix:

```
"The fetch_user() function is missing error handling for DatabaseError.
Regenerate src/api.py with explicit try/except for all database calls."

"Test coverage is 73%, contract requires >=80%.
Add integration tests for error conditions in tests/integration/."

"function validate_email() on line 47 of src/validators.py is missing
return type hint. Regenerate src/validators.py with 100% type coverage."

"No benchmarks present. Add benches/bench_core.rs with criterion benchmarks
for process_items() and fetch_users()."
```

Be specific. Rerun with the exact issue stated.

---

## 5. Language-Specific Templates

### Python

```markdown
# TASK-BRIEF: [Name]

**Language:** Python  
**Framework:** FastAPI / Flask / None
**Type:** CLI / API / Library

**Must have:**
- [ ] Type hints: 100%, mypy --strict passes
- [ ] Error handling: Custom exception hierarchy
- [ ] Logging: structlog (structured JSON)
- [ ] Tests: pytest (unit + integration, >=80% coverage)
- [ ] Benchmarks: pytest-benchmark for hot paths
- [ ] Linting: ruff + black + isort

**Performance:** [targets or "not critical"]
**Security:** [requirements or "input validation at boundaries"]

**Success Criteria:**
```bash
uv sync                    # or pip install -e ".[dev]"
make test                  # >=80% coverage
make lint                  # ruff + mypy pass
pytest --benchmark-only   # benchmarks run
make run                   # starts without errors
```
```

### TypeScript

```markdown
# TASK-BRIEF: [Name]

**Language:** TypeScript  
**Framework:** Express / Fastify / Hono / None
**Type:** CLI / API / Library

**Must have:**
- [ ] Strict TypeScript (zero `any`)
- [ ] Error handling: Custom error classes extending AppError
- [ ] Logging: pino (structured JSON)
- [ ] Tests: vitest (unit + integration, >=80% coverage)
- [ ] Benchmarks: vitest bench for hot paths
- [ ] Linting: eslint + prettier + tsc --noEmit

**Performance:** [targets or "not critical"]
**Security:** [requirements or "input validation at boundaries"]

**Success Criteria:**
```bash
npm ci
npm test                  # >=80% coverage
npm run lint              # eslint + tsc pass
npm run bench             # benchmarks run
npm start                 # starts on configured port
```
```

### Rust

```markdown
# TASK-BRIEF: [Name]

**Language:** Rust
**Framework:** Axum / Actix / None
**Type:** CLI / API / Library

**Must have:**
- [ ] Error handling: thiserror (library) / anyhow (application)
- [ ] Logging: tracing with structured spans
- [ ] Tests: cargo test (unit + integration, >=80% coverage)
- [ ] Benchmarks: criterion for hot paths
- [ ] Linting: clippy -D warnings
- [ ] Docs: rustdoc on all public items
- [ ] No unsafe without justification

**Performance:** [targets or "not critical"]
**Security:** [requirements or "no unsafe, input validation at boundaries"]

**Success Criteria:**
```bash
cargo build
cargo test --all-targets       # all pass
cargo clippy -- -D warnings    # zero warnings
cargo bench                    # benchmarks run
cargo run -- [args]            # runs successfully
```
```

### Go

```markdown
# TASK-BRIEF: [Name]

**Language:** Go
**Framework:** Gin / Echo / Chi / stdlib
**Type:** CLI / API / Library

**Must have:**
- [ ] Error handling: error wrapping with %w, sentinel errors
- [ ] Logging: log/slog (structured)
- [ ] Tests: go test -race (unit + integration, >=80% coverage)
- [ ] Benchmarks: go test -bench for hot paths
- [ ] Linting: go vet + staticcheck
- [ ] Race detection: go test -race clean

**Performance:** [targets or "not critical"]
**Security:** [requirements or "input validation at boundaries"]

**Success Criteria:**
```bash
go mod download
go test -race ./...            # all pass, no races
go vet ./...                   # zero warnings
go test -bench=. -benchmem ./... # benchmarks run
go run ./cmd/...               # runs successfully
```
```

### C/C++

```markdown
# TASK-BRIEF: [Name]

**Language:** C++ (C++20)
**Framework:** None / [specify]
**Type:** CLI / Library / Embedded

**Must have:**
- [ ] Build: CMake 3.20+ with Ninja
- [ ] Memory: RAII only, no raw new/delete
- [ ] Error handling: std::expected or custom exceptions
- [ ] Logging: spdlog
- [ ] Tests: Google Test (unit + integration, >=80% coverage)
- [ ] Benchmarks: Google Benchmark for hot paths
- [ ] Linting: clang-tidy + clang-format
- [ ] Sanitizers: ASan + UBSan in Debug builds
- [ ] Const correctness throughout

**Performance:** [targets or "not critical"]
**Security:** [requirements or "ASan clean, no leaks"]

**Success Criteria:**
```bash
cmake -B build -G Ninja -DSANITIZE=ON
cmake --build build
cd build && ctest --output-on-failure   # all pass
./build/benchmarks                       # benchmarks run
./build/myapp                            # runs successfully
valgrind --leak-check=full ./build/myapp # zero leaks
```
```

---

## 6. Quality Checklist (Auto-Rejected if Fails)

```
Before returning:

Source Code:
☐ All files present (no placeholders)
☐ No TODOs or comments like "// TODO:"
☐ Clear, descriptive variable names (no `x`, `temp`, `thing`)
☐ Proper error handling (no silent failures)
☐ No unsafe code without SAFETY justification

Type System:
☐ Python: 100% type hints (mypy --strict passes)
☐ TypeScript: Strict mode (tsc --noEmit passes, zero `any`)
☐ Rust: All public items documented (cargo doc)
☐ Go: All exported funcs documented (godoc)
☐ C/C++: const correctness, [[nodiscard]] on pure functions

Tests:
☐ Tests exist (tests/ directory)
☐ All tests pass (zero failures)
☐ Coverage >=80% (checked with --cov / --coverage flag)
☐ No skipped tests (@skip, t.Skip, #[ignore])
☐ Unit + integration tests present
☐ Parametrized/table-driven tests for multiple inputs

Benchmarks:
☐ benches/ directory exists
☐ Hot paths have benchmarks
☐ Benchmarks compile and run clean

Security:
☐ No secrets in code (checked .env.example)
☐ All external inputs validated
☐ Dependency audit clean (pip audit / npm audit / cargo audit)
☐ No raw memory operations without bounds checking (C/C++)

Tooling:
☐ Makefile / build scripts working
☐ Linting configured and passing (zero warnings)
☐ Type checking configured and passing
☐ .env.example present with all vars
☐ setup.sh / install script present
☐ .github/workflows/ci.yml present

Documentation:
☐ README.md has all required sections (Overview, Quick Start, Installation, Usage, Architecture, Configuration, Testing, Performance, Troubleshooting, Known Limitations, Changelog link)
☐ All README examples actually work (tested)
☐ CHANGELOG.md present with all changes under [Unreleased]
☐ README and CHANGELOG synced with current code
☐ Performance numbers are from actual benchmark runs

Git:
☐ .gitignore correct (language-appropriate)
☐ Ready to git add . && git commit -m "feat: initial [project]"
```

---

## 7. Anti-Patterns (Never Accept)

| Pattern | Why It Fails |
|---|---|
| **Code with TODOs** | Incomplete work |
| **Missing type hints** | Unmaintainable, poor IDE support |
| **Tests that skip** | False positive coverage |
| **No benchmarks** | Performance is unknown |
| **Cryptic names** | Unmaintainable |
| **Silent error handling** | Debugging nightmare |
| **Setup requires manual steps** | Breaks first-run experience |
| **README examples don't work** | Unusable documentation |
| **README missing required sections** | Incomplete documentation |
| **Missing or stale CHANGELOG** | No change history |
| **Code changed without doc sync** | Docs drift from reality |
| **Linting warnings** | Quality debt |
| **Coverage <80%** | Untested code |
| **Secrets in code** | Security issue |
| **No CI config** | Regressions undetected |
| **Unsafe without justification** | Memory safety risk |
| **Unbounded queues** | OOM under load |

---

## 8. Common Issues & Fixes

### "Code generation is slow"
- **Cause:** Large project in one go
- **Fix:** Split into smaller TASK-BRIEFs (core, then API, then tests)

### "Tests don't pass"
- **Cause:** Missing fixtures or imports
- **Fix:** Request specific error message, regenerate with explicit test setup

### "Type hints incomplete"
- **Cause:** Claude Code wasn't strict enough
- **Fix:** Add to TASK-BRIEF: "Strict type hints required. Run `mypy --strict` before returning."

### "No benchmarks"
- **Cause:** Not specified in TASK-BRIEF
- **Fix:** Add "Benchmarks for hot paths required" + list the hot paths

### "Setup fails"
- **Cause:** Missing dependencies or wrong commands
- **Fix:** Request working setup.sh that installs all deps and runs tests

### "Unsafe code in Rust/C++ without justification"
- **Cause:** Claude used unsafe as shortcut
- **Fix:** Request safe rewrite or explicit SAFETY comments with invariant documentation

### "Race conditions in Go/Rust"
- **Cause:** Shared mutable state without synchronization
- **Fix:** Request `go test -race` clean version, or Rust version with proper `Arc<Mutex<T>>`

---

## 9. Pro Tips

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

### Tip 4: Specify Performance Budgets Upfront
```
"Process 10k items in <100ms" is better than "make it fast"
Claude can optimize toward a measurable target.
```

### Tip 5: Request Safe Patterns Explicitly
```
For Rust: "No unsafe. Use safe abstractions only."
For C++: "RAII only. No raw new/delete. Smart pointers for heap."
For Go: "Always run tests with -race flag."
```

### Tip 6: Document Trade-Offs
```python
def cache_users(ttl: int = 300):
    """Cache users in memory.
    
    Trade-off: Fast, but not distributed.
    For multi-server, use Redis instead.
    """
```

---

## 10. Final Sanity Check

Before you ship, ask yourself:

```
☐ Would I be proud to show this to other engineers?
☐ Could someone clone this and run it immediately?
☐ Are all error cases handled explicitly?
☐ Is the code easy to modify 6 months from now?
☐ Did I test the examples in the README?
☐ Does the README have all required sections?
☐ Is the CHANGELOG up to date with all changes?
☐ Do the README and CHANGELOG match the actual code?
☐ Does setup work on a fresh machine?
☐ Are there benchmarks for the critical paths?
☐ Would this pass a security review?
```

If any answer is "no", it's not ready.

---

## Remember

> **Quality is not about perfection. It's about clarity, completeness, and care.**

Use this cheat sheet to enforce that. Every time.

---

**Last Updated:** March 2026  
**Version:** 2.0
