# Claude Code Production Contracts

**Standalone contract system for generating production-grade code with Claude Code.**

Supports: Python, TypeScript, Rust, Go, C/C++, Bash. No external dependencies. Just attach these files to any Claude Code session.

---

## Quick Start

### 1. Get These Files
```
claude-code-contracts/
├── README.md                           (this file)
├── START-HERE.md                       (read this first)
├── CLAUDE-CODE-CONTRACT.md             (quality standards)
├── CLAUDE-CODE-GUIDE.md                (how to use)
├── CLAUDE-CODE-CHEATSHEET.md           (quick reference)
├── TASK-BRIEF-TEMPLATE.md              (copy for each project)
├── CODING-CONTEXT.md                   (language rules)
├── MANIFEST.md                         (file index)
├── DELIVERY-SUMMARY.md                 (delivery overview)
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md    (master prompt)
```

### 2. For Your First Project
```bash
# Copy template
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md

# Fill it out
# (name, language, type, requirements, performance budget)

# Attach to Claude Code:
#   - CLAUDE-CODE-CONTRACT.md
#   - CODING-CONTEXT.md
#   - TASK-BRIEF.md
#   - prompts/PROMPT-CLAUDE-CODE-MASTER.md

# Prompt: "Generate production code following CLAUDE-CODE-CONTRACT.md"
```

### 3. Verify & Ship
```bash
cd ~/my-project
./setup.sh
make test    # >= 80% coverage
make lint    # zero warnings
make bench   # benchmarks run
git status   # ready to commit
```

---

## Files Explained

| File | Purpose | Read When |
|---|---|---|
| **START-HERE.md** | Entry point, 2-min overview | **First** |
| **CLAUDE-CODE-CONTRACT.md** | Quality standards & requirements (authoritative) | Need to understand standards |
| **CLAUDE-CODE-GUIDE.md** | Complete walkthrough with examples | Planning a project |
| **PROMPT-CLAUDE-CODE-MASTER.md** | What Claude Code sees (master prompt) | Want to customize |
| **CLAUDE-CODE-CHEATSHEET.md** | Quick reference, copy-paste templates | During a project |
| **TASK-BRIEF-TEMPLATE.md** | Copy this for each new project | Starting a project |
| **CODING-CONTEXT.md** | Language-specific rules | Customizing for your languages |

---

## The System

```
Fill TASK-BRIEF.md (your project requirements + performance budget)
        ↓
Attach all contract files to Claude Code
        ↓
Claude Code generates production code:
  - 100% type hints
  - >=80% test coverage
  - Benchmarks for hot paths
  - Security audit clean
  - CI configuration included
  - Zero TODOs, zero warnings
  - Working setup, README with examples
  - CHANGELOG in Keep a Changelog format
  - README + CHANGELOG always synced with code
        ↓
Verify: ./setup.sh && make test && make lint && make bench
        ↓
Ship immediately (no iteration needed)
```

---

## Quality Standards (Non-Negotiable)

Every Claude Code project meets:

- 100% type hints on public APIs
- >=80% test coverage, all tests pass
- Benchmarks for hot paths
- Zero TODOs, zero stubs
- Zero linting warnings
- Zero type errors
- Security audit clean (no known CVEs)
- CI configuration present
- Working setup script
- Comprehensive README with tested examples (see README Generation Spec)
- CHANGELOG.md in Keep a Changelog format
- README and CHANGELOG always synced with code changes
- Ready to git commit immediately

**Code violating these standards is rejected and re-requested.**

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

## Project Structure

Claude Code generates this structure (adapted per project type):

```
my-project/
├── src/                    # Application code
│   ├── core/               # Core logic (framework-independent)
│   ├── api/                # API/routing layer
│   ├── models/             # Data types
│   └── utils/              # Pure utilities
├── tests/
│   ├── unit/               # Unit tests
│   ├── integration/        # Integration tests
│   └── fixtures/           # Shared test data
├── benches/                # Performance benchmarks
├── .github/workflows/      # CI configuration
├── scripts/
│   └── setup.sh            # One-command setup
├── README.md               # Full README (per README Generation Spec)
├── CHANGELOG.md            # Version history (Keep a Changelog format)
├── .env.example            # Config template
├── Makefile                # test, lint, bench, run targets
├── [build file]            # pyproject.toml / Cargo.toml / go.mod / etc.
├── .gitignore
└── .git/                   # First commit ready
```

---

## How to Use

### Option A: Claude.ai Web Interface
1. Open Claude Code
2. Attach files: CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, TASK-BRIEF.md, PROMPT-CLAUDE-CODE-MASTER.md
3. Prompt: "Generate production code following CLAUDE-CODE-CONTRACT.md"
4. Claude generates code

### Option B: API
```python
client.messages.create(
    model="claude-opus-4-1",
    system="You are a production code generator. Follow CLAUDE-CODE-CONTRACT.md completely.",
    messages=[
        {
            "role": "user",
            "content": "Generate production code per TASK-BRIEF.md and CLAUDE-CODE-CONTRACT.md"
        }
    ],
    # Attach files as context...
)
```

### Option C: CLI
```bash
claude code \
  --context CLAUDE-CODE-CONTRACT.md \
  --context CODING-CONTEXT.md \
  --context TASK-BRIEF.md \
  --context prompts/PROMPT-CLAUDE-CODE-MASTER.md \
  "Generate production code"
```

---

## Reading Order

### First Time
1. **START-HERE.md** (2 min)
2. **CLAUDE-CODE-GUIDE.md** (15 min)
3. **TASK-BRIEF-TEMPLATE.md** (3 min to copy)
4. Fill TASK-BRIEF.md for your project
5. Spawn Claude Code

### Subsequent Times
1. **CLAUDE-CODE-CHEATSHEET.md** (quick lookup)
2. Copy TASK-BRIEF-TEMPLATE.md
3. Fill and spawn

### Reference
- **CLAUDE-CODE-CONTRACT.md** (when reviewing code)
- **CODING-CONTEXT.md** (when customizing for your languages)
- **PROMPT-CLAUDE-CODE-MASTER.md** (when understanding what Claude sees)

---

## Key Features

### One-Shot Generation
No iteration. Code is complete, tested, benchmarked, documented on first run.

### Multi-Language
Full support for Python, TypeScript, Rust, Go, C/C++, Bash with language-specific quality patterns.

### Performance-Aware
Benchmarks required for hot paths. Performance budgets defined per project.

### Security-First
Input validation at boundaries. Dependency auditing. No secrets in code. Unsafe code requires justification.

### CI-Ready
GitHub Actions workflows generated per language. Lint, test, and build in CI.

### Portable
No external dependencies. Just Markdown files attached to Claude Code sessions.

### Customizable
Edit CODING-CONTEXT.md for your language rules. Edit CLAUDE-CODE-CONTRACT.md to adjust the quality bar.

### Enforceable
Clear standards. Code violating them is rejected with specific fix requests.

---

## What Gets Rejected

- Code with TODOs or stubs
- Missing type hints (even one function)
- Test coverage < 80%
- Failing tests or skipped tests
- No benchmarks for hot paths
- Linting warnings or errors
- Type checking errors
- Broken setup script
- README with non-working examples or missing sections
- Missing or stale CHANGELOG.md
- Code changes without README/CHANGELOG updates
- Secrets in code (no API keys, tokens)
- Silent error handling
- No CI configuration
- Unsafe code without justification
- Unbounded concurrency without limits

**Reject and re-request with specific fix.**

---

## Customization

### Add Language Rules
Edit `CODING-CONTEXT.md`:
```markdown
## Your Project Standards
- Error codes: Use HTTP status + custom codes (ERR_RATE_LIMITED)
- Logging: Always include request_id
- Testing: Smoke tests for all API calls
```

### Adjust Quality Bar
Edit sections in `CLAUDE-CODE-CONTRACT.md`:
```markdown
- Default: >=80% coverage
- Your project: >=90% (more critical code)
```

### Change Project Structure
Update template sections in `TASK-BRIEF-TEMPLATE.md`.

---

## Your First Project (Right Now)

```bash
# 1. Read (2 min)
cat START-HERE.md

# 2. Copy template (1 min)
mkdir my-awesome-project
cp TASK-BRIEF-TEMPLATE.md my-awesome-project/TASK-BRIEF.md
cd my-awesome-project

# 3. Fill it out (5 min)
# Edit TASK-BRIEF.md with your project details

# 4. Attach to Claude Code and prompt:
# "Generate production code following CLAUDE-CODE-CONTRACT.md"

# 5. Verify (5 min)
./setup.sh
make test
make lint
make bench
git status

# 6. Ship
```

---

## Files Are Self-Documenting

Each file has:
- Clear purpose at the top
- Sections with examples
- Quick reference tables

**Everything is here.** No external documentation needed.

---

## Key Principle

> **One-shot, ship-ready code. Quality is non-negotiable.**

If Claude Code generates code that violates the contract, reject it with a specific fix request. Re-run. Repeat until it meets standards.

---

**Version:** 2.0
**Status:** Production Ready
**Last Updated:** March 2026
**Languages:** Python, TypeScript, Rust, Go, C/C++, Bash
