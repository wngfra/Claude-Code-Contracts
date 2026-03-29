# Claude Code Contracts — Manifest

**Self-contained production contract system for Claude Code.**

Version: 2.0
Status: Production Ready
Last Updated: March 2026
Languages: Python, TypeScript, Rust, Go, C/C++, Bash

---

## Contents (11 Files, ~100 KB)

### Entry Points
- **START-HERE.md** — Quick 2-min overview, read this first
- **README.md** — Full system overview, file guide

### Core Contracts
- **CLAUDE-CODE-CONTRACT.md** — Quality standards document (authoritative)
- **PROMPT-CLAUDE-CODE-MASTER.md** — Master prompt (in `prompts/`)

### Guides & References
- **CLAUDE-CODE-GUIDE.md** — Complete how-to with examples
- **CLAUDE-CODE-CHEATSHEET.md** — Quick copy-paste reference
- **TASK-BRIEF-TEMPLATE.md** — Copy for each project

### Standards
- **CODING-CONTEXT.md** — Language-specific rules

### Meta
- **MANIFEST.md** — This file, index of everything
- **DELIVERY-SUMMARY.md** — Delivery overview
- **FILE-STRUCTURE.txt** — ASCII directory tree

---

## What This System Does

Ensures Claude Code generates production-grade code, every time:
- 100% type hints (no `any` types)
- >=80% test coverage (all tests pass)
- Benchmarks for hot paths
- Zero TODOs (complete, shipping code)
- Zero linting warnings
- Zero type errors
- Security audit clean
- CI configuration included
- Working setup script
- Comprehensive README with tested examples (per README Generation Spec)
- CHANGELOG in Keep a Changelog format
- README + CHANGELOG always synced with code changes
- Git-ready (commit immediately)

**No iteration needed. One-shot, ship-ready.**

---

## How to Use

### Minimal (5 minutes)
```
1. Read START-HERE.md (2 min)
2. Copy TASK-BRIEF-TEMPLATE.md to your project
3. Fill with your project details
4. Attach 4 files to Claude Code:
   - CLAUDE-CODE-CONTRACT.md
   - CODING-CONTEXT.md
   - TASK-BRIEF.md (your project)
   - prompts/PROMPT-CLAUDE-CODE-MASTER.md
5. Ask: "Generate production code following CLAUDE-CODE-CONTRACT.md"
6. Verify: make test && make lint && make bench && git status
7. Ship
```

### Complete (30 minutes)
```
1. Read START-HERE.md (2 min)
2. Read CLAUDE-CODE-GUIDE.md (15 min)
3. Read CLAUDE-CODE-CHEATSHEET.md (5 min)
4. Copy TASK-BRIEF-TEMPLATE.md
5. Fill with project details
6. Attach & generate with Claude Code
7. Verify & ship
```

---

## File Guide

| File | Purpose | Read When |
|---|---|---|
| **START-HERE.md** | Entry point, quick overview | **First** |
| **README.md** | System overview, file guide | Planning |
| **CLAUDE-CODE-GUIDE.md** | Complete walkthrough, examples | First project |
| **CLAUDE-CODE-CONTRACT.md** | Quality standards, requirements | Reviewing code |
| **CLAUDE-CODE-CHEATSHEET.md** | Quick reference, templates | During project |
| **PROMPT-CLAUDE-CODE-MASTER.md** | Master prompt for Claude Code | Customizing |
| **TASK-BRIEF-TEMPLATE.md** | Copy for each project | Starting project |
| **CODING-CONTEXT.md** | Language-specific rules | Customizing |
| **MANIFEST.md** | This file, index | Navigation |
| **DELIVERY-SUMMARY.md** | Delivery overview | Understanding scope |
| **FILE-STRUCTURE.txt** | ASCII tree reference | Quick reference |

---

## Reading Paths

### Path 1: I Want to Use This Right Now
1. START-HERE.md (2 min)
2. Copy TASK-BRIEF-TEMPLATE.md
3. Go to your project
4. Attach files to Claude Code
5. Done

### Path 2: I Want to Understand the System
1. START-HERE.md (2 min)
2. README.md (5 min)
3. CLAUDE-CODE-GUIDE.md (15 min)
4. TASK-BRIEF-TEMPLATE.md (3 min)
5. Create your first project

### Path 3: I Want Quick Reference
1. CLAUDE-CODE-CHEATSHEET.md (1 min)
2. Copy TASK-BRIEF-TEMPLATE.md
3. Your project

### Path 4: I Want to Customize
1. CLAUDE-CODE-CONTRACT.md (adjust standards)
2. CODING-CONTEXT.md (adjust language rules)
3. TASK-BRIEF-TEMPLATE.md (adjust template)
4. Use customized version

---

## Quality Standards (Non-Negotiable)

Every Claude Code project must deliver:

```
Type Hints: 100% on public APIs
Tests: >=80% coverage, all pass, no skips
Benchmarks: present for hot paths
Code: Zero TODOs, zero stubs
Linting: Zero warnings, zero errors
Types: Type checking passes (mypy/tsc/clippy)
Security: Audit clean, no secrets in code
CI: Configuration present
Setup: Works first time (setup.sh && make test)
README: Complete (all sections per spec), examples tested
CHANGELOG: Present, all changes documented (Keep a Changelog format)
Doc Sync: README + CHANGELOG match current code state
Errors: Explicit handling, never silent failures
Config: .env.example complete, no secrets
Git: Ready to commit immediately
```

**Code violating any standard is rejected until fixed.**

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

## What Gets Rejected

Code that has:
- TODOs or incomplete sections
- Missing type hints (even one function)
- Test coverage < 80%
- No benchmarks for hot paths
- Failing or skipped tests
- Linting warnings
- Type checking errors
- Security audit failures
- No CI configuration
- Unsafe code without justification
- Broken setup script
- Non-working README examples or missing README sections
- Missing or stale CHANGELOG.md
- Code changes without corresponding doc updates
- Secrets in code

**Request specific fix and rerun.**

---

## Package Contents

```
claude-code-contracts/
├── README.md                          System overview
├── START-HERE.md                      Quick start
├── MANIFEST.md                        This file
├── DELIVERY-SUMMARY.md                Delivery overview
├── FILE-STRUCTURE.txt                 ASCII tree reference
│
├── CLAUDE-CODE-CONTRACT.md            Quality standards (authoritative)
├── CLAUDE-CODE-GUIDE.md               Complete how-to
├── CLAUDE-CODE-CHEATSHEET.md          Quick reference
├── TASK-BRIEF-TEMPLATE.md             Project template
├── CODING-CONTEXT.md                  Language rules
│
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md   Master prompt

Total: ~100 KB, self-contained, no dependencies
```

---

## Key Principles

1. **One-shot, no iteration** — Code ships on first generation
2. **Quality non-negotiable** — Standards are enforced, not suggested
3. **Performance measurable** — Benchmarks required for hot paths
4. **Security by default** — Validation at boundaries, audit clean
5. **Clear contracts** — No ambiguity about what's expected
6. **Multi-language** — Full support for Python, TS, Rust, Go, C/C++
7. **CI-ready** — Workflows generated per language
8. **Portable** — Works with any Claude Code interface
9. **Customizable** — Adapt for your needs
10. **Complete** — Everything included (tests, benchmarks, docs, setup, CI)

---

## Compatible With

- Claude.ai web interface
- IDE extensions
- Claude API
- Any LLM accepting Claude as model

**Just attach files and use the prompts provided.**

---

## System Overview

```
Your Requirements + Performance Budget
        ↓
Fill TASK-BRIEF.md
        ↓
Attach Contract Files to Claude Code
        ↓
Claude Code Reads Contract
        ↓
Generates Production Code:
  - 100% type hints
  - >=80% tests
  - Benchmarks
  - Zero TODOs
  - Security clean
  - CI configured
  - Working setup
        ↓
You Verify (make test, make lint, make bench)
        ↓
Ship (git commit immediately)
```

---

## What Makes This Different

### Before (No Contract)
- Claude Code generates incomplete code
- Missing type hints, tests, benchmarks, documentation
- Requires iteration to fix
- Manual quality checks
- Unpredictable quality

### After (With Contract)
- Claude Code reads contract upfront
- Knows exact requirements per language
- Generates complete code with benchmarks
- All standards met automatically
- Predictable quality
- No iteration needed

---

## Quick Checklist

Before using with Claude Code:
- [ ] Read START-HERE.md
- [ ] Have all files in this folder
- [ ] Understand the quality standards
- [ ] Know how to fill TASK-BRIEF.md
- [ ] Know how to attach files to Claude Code
- [ ] Understand rejection protocol

Before shipping generated code:
- [ ] `make test` passes with >=80% coverage
- [ ] `make lint` shows zero warnings
- [ ] `make bench` benchmarks run (if applicable)
- [ ] `git status` shows ready to commit
- [ ] README has all required sections and examples actually work
- [ ] CHANGELOG.md present with all changes documented
- [ ] README and CHANGELOG synced with current code
- [ ] `./setup.sh` worked on fresh machine
- [ ] No TODOs in code
- [ ] No type hints missing
- [ ] All tests passing (no skips)
- [ ] CI workflow present

---

## Success Metrics

**You've won if:**
- Generated code passes all checks first try
- No iteration needed
- Team is happy with code quality
- Can ship immediately without changes
- Zero technical debt from generation

---

## File Dependencies

```
START-HERE.md <- Read this first
        ↓
CLAUDE-CODE-GUIDE.md <- Complete walkthrough
        ↓
All files reference CLAUDE-CODE-CONTRACT.md standards
        ↓
TASK-BRIEF-TEMPLATE.md <- Copy for your project
        ↓
PROMPT-CLAUDE-CODE-MASTER.md <- Attach with contract
        ↓
CODING-CONTEXT.md <- Customize as needed
```

---

**Ready to build?** Start with **START-HERE.md.**
