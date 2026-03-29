# Claude Code Contracts — Delivery Summary

**You have a complete, self-contained production contract system that works with any Claude Code interface.**

---

## What You Got

A folder with everything needed to generate production-grade code using Claude Code — no dependencies, just files you attach to Claude Code.

```
claude-code-contracts/
├── README.md                          — System overview
├── START-HERE.md                      — Quick start guide
├── MANIFEST.md                        — File index & guide
│
├── CLAUDE-CODE-CONTRACT.md            — Quality standards (authoritative)
├── CLAUDE-CODE-GUIDE.md               — Complete how-to
├── CLAUDE-CODE-CHEATSHEET.md          — Quick reference
│
├── TASK-BRIEF-TEMPLATE.md             — Project template
├── CODING-CONTEXT.md                  — Language rules
│
├── DELIVERY-SUMMARY.md                — This file
├── FILE-STRUCTURE.txt                 — Directory tree reference
│
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md   — Master prompt

Total: ~100 KB, fully self-contained, zero dependencies
```

---

## How to Use It

### Right Now (5 Minutes)
```bash
# 1. Read quick start
cat START-HERE.md

# 2. Copy template to your project
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md

# 3. Edit your project brief
# (name, language, type, requirements, performance budget)

# 4. Attach to Claude Code:
#    - CLAUDE-CODE-CONTRACT.md
#    - CODING-CONTEXT.md
#    - TASK-BRIEF.md (your project)
#    - prompts/PROMPT-CLAUDE-CODE-MASTER.md

# 5. Prompt: "Generate production code following CLAUDE-CODE-CONTRACT.md"

# 6. Verify
cd ~/my-project
./setup.sh
make test      # All pass? >= 80% coverage?
make lint      # Zero warnings?
make bench     # Benchmarks run?
```

### Complete Usage (30 Minutes)
```
1. Read START-HERE.md (2 min)
2. Read CLAUDE-CODE-GUIDE.md (15 min)
3. Read CLAUDE-CODE-CHEATSHEET.md (5 min)
4. Copy TASK-BRIEF-TEMPLATE.md to your project
5. Fill with requirements + performance budget
6. Attach 4 files to Claude Code
7. Get code back
8. Verify & ship
```

---

## What Claude Code Will Generate

Every project comes with:

```
my-project/
├── src/                    # All source code (100% typed)
├── tests/                  # Full test suite (>=80% coverage)
├── benches/                # Performance benchmarks
├── .github/workflows/      # CI configuration
├── scripts/setup.sh        # One-command install
├── README.md               # Setup + working examples
├── Makefile                # test, lint, bench, run targets
├── .env.example            # Configuration template
├── [build file]            # pyproject.toml / Cargo.toml / go.mod / etc.
├── .gitignore
└── .git/                   # First commit ready
```

**Quality guaranteed:**
- 100% type hints on public APIs
- >=80% test coverage (all tests pass)
- Benchmarks for hot paths
- Zero TODOs (complete code)
- Zero warnings (lint + type check clean)
- Security audit clean
- CI configuration present
- Working setup (`./setup.sh` succeeds)
- Accurate README (examples tested)
- Git-ready (commit immediately)

---

## Supported Languages

| Language | Type System | Testing | Linting | Logging | Benchmarks |
|---|---|---|---|---|---|
| **Python** | mypy --strict | pytest --cov | ruff + black | structlog | pytest-benchmark |
| **TypeScript** | tsc --noEmit | vitest --coverage | eslint + prettier | pino | vitest bench |
| **Rust** | cargo clippy -D | cargo test | clippy + rustfmt | tracing | criterion |
| **Go** | go vet | go test -race | go vet + staticcheck | slog | go test -bench |
| **C/C++** | -Wall -Wextra -Werror | gtest | clang-tidy | spdlog | google benchmark |
| **Bash** | — | bats | shellcheck | — | — |

---

## Key Features

### One-Shot Generation
No iteration. Code is complete, tested, benchmarked, documented on first generation.

### Multi-Language
Full support for Python, TypeScript, Rust, Go, C/C++, Bash with language-specific quality patterns.

### Performance-Aware
Benchmarks required for hot paths. Performance budgets defined per project.

### Security-First
Input validation at boundaries. Dependency auditing. No secrets in code. Unsafe code requires justification.

### CI-Ready
GitHub Actions workflows generated per language.

### Self-Contained
All files are here. No external dependencies or setup required.

### Portable
Works with any Claude Code interface: Claude.ai web, IDE extensions, Claude API, any LLM.

### Customizable
Adjust standards in CLAUDE-CODE-CONTRACT.md and CODING-CONTEXT.md.

### Enforceable
Clear standards. Code violating them is rejected until fixed.

---

## What Gets Rejected

**Code that has:**
- TODOs or incomplete sections
- Missing type hints
- Test coverage < 80%
- No benchmarks for hot paths
- Failing or skipped tests
- Linting warnings
- Type errors
- Security audit failures
- No CI configuration
- Unsafe code without justification
- Broken setup
- Non-working README examples

**You reject it and ask for a specific fix. Re-run until it passes.**

Example:
> "Coverage is 73%, contract requires >=80%. Add integration tests for error conditions. Re-run and verify `pytest --cov` shows >=80% before returning."

---

## Quick Checklist

### Before Spawning Claude Code
- [ ] Read START-HERE.md
- [ ] Filled TASK-BRIEF.md (including performance budget if applicable)
- [ ] Have all 4 files ready to attach
- [ ] Know the prompt to use

### After Claude Code Returns Code
- [ ] `./setup.sh` succeeded
- [ ] `make test` passes with >=80% coverage
- [ ] `make lint` shows zero warnings
- [ ] `make bench` benchmarks run (if applicable)
- [ ] `git status` is clean
- [ ] README examples work
- [ ] No TODOs in code
- [ ] All type hints present
- [ ] CI workflow present

### Before Shipping
- [ ] Code passes all checks
- [ ] Team reviewed and approved
- [ ] Documentation is accurate
- [ ] Setup works on fresh machine

---

## System at a Glance

```
Your Requirements + Performance Budget
        ↓
TASK-BRIEF.md (you fill this)
        ↓
Claude Code + 4 Contract Files
        ↓
Claude Code Generates:
  - 100% type hints
  - >=80% tests (all pass)
  - Benchmarks for hot paths
  - Zero TODOs
  - Zero warnings
  - Security audit clean
  - CI configured
  - Working setup
  - README with examples
        ↓
make test && make lint && make bench && git status
        ↓
SHIP (no further work)
```

---

## Version Info

- **Version:** 2.0
- **Status:** Production Ready
- **Last Updated:** March 2026
- **Languages:** Python, TypeScript, Rust, Go, C/C++, Bash
- **Format:** Markdown (plain text, version-controllable)
- **Size:** ~100 KB (very portable)
- **Dependencies:** None

---

## Next Steps

1. **Right now:** Open START-HERE.md
2. **In 5 min:** Copy TASK-BRIEF-TEMPLATE.md to your project
3. **In 10 min:** Attach 4 files to Claude Code
4. **In 20 min:** Have production code
5. **Done:** Ship it

---

**Everything is ready. Start with START-HERE.md.**
