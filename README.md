# Claude Code Production Contracts

A contract system that ensures Claude generates production-grade code: fully typed, tested, benchmarked, documented, and ready to ship.

Supports **eight task scenarios** across five prompt modes:

| Scenario | Prompt Mode | Use When |
|---|---|---|
| **Generate** | Master Generator | Building a new project or module from scratch |
| **Debug / Fix** | Debug | Finding and fixing bugs in existing code |
| **Refactor** | Refactor | Restructuring existing code without changing behavior |
| **Partial Rewrite** | Partial Rewrite | Replacing a module with a new implementation |
| **Full Rewrite** | Master Generator | Rebuilding an entire codebase from scratch |
| **Add Function** | Master Generator | Adding new functions/endpoints/commands to existing code |
| **Remove Function** | Master Generator | Removing functions and all references |
| **Modify Behavior** | Master Generator | Changing how existing code works |
| **Orchestrate** | Orchestrator | Large multi-phase projects with independent review/validation loops |

Works with **Claude Code CLI**, **claude.ai**, or the **Claude API**. No external dependencies. Just Markdown files.

---

## Quick Start

### 1. Copy and fill the task brief

```bash
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md
# Edit: name, language, type, requirements, performance budget
```

### 2. Attach files to your Claude Code session

```bash
cd ~/my-project
claude --add-dir /path/to/claude-code-contracts

# Reference the contract + matching prompt:
> @CLAUDE-CODE-CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-CLAUDE-CODE-MASTER.md @TASK-BRIEF.md
  Generate production code following the contract and task brief.
```

Also works with **claude.ai** (attach files) or the **Claude API** (pass as system prompt + user message).

### 3. Verify and ship

```bash
cd ~/my-project
./setup.sh
make test    # >= 80% coverage
make lint    # zero warnings
make bench   # benchmarks run
git status   # ready to commit
```

If something fails, tell Claude exactly what's wrong and ask for a fix.

---

## Files

```
claude-code-contracts/
├── README.md                              ← you are here
├── CHANGELOG.md                           ← version history (Keep a Changelog format)
├── CLAUDE-CODE-CONTRACT.md                ← quality standards (attach to every session)
├── CLAUDE-CODE-GUIDE.md                   ← detailed walkthrough with examples
├── CLAUDE-CODE-CHEATSHEET.md              ← quick reference & copy-paste templates
├── THINKING-FRAMEWORKS.md                 ← problem decomposition & algorithm design
├── MULTI-AGENT-FRAMEWORK.md               ← Orchestrator/Implementer/Reviewer pipeline
├── TASK-BRIEF-TEMPLATE.md                 ← copy this for each task
├── PHASE-BRIEF-TEMPLATE.md                ← per-phase brief (multi-agent mode)
├── CODING-CONTEXT.md                      ← language-specific rules
└── prompts/
    ├── PROMPT-CLAUDE-CODE-MASTER.md       ← Generate mode (new projects)
    ├── PROMPT-REFACTOR.md                 ← Refactor mode (restructure existing code)
    ├── PROMPT-DEBUG.md                    ← Debug mode (find and fix bugs)
    ├── PROMPT-PARTIAL-REWRITE.md          ← Partial Rewrite mode (replace a module)
    ├── PROMPT-ORCHESTRATOR.md             ← Orchestrate mode (lead agent)
    ├── PROMPT-IMPLEMENTER.md              ← phase worker subagent
    └── PROMPT-REVIEWER.md                 ← independent review subagent
```

| Task | Prompt to Attach |
|---|---|
| Build something new | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| Add/remove/modify functions | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| Full rewrite | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| Restructure existing code | `prompts/PROMPT-REFACTOR.md` |
| Fix a specific bug | `prompts/PROMPT-DEBUG.md` |
| Replace a module/component | `prompts/PROMPT-PARTIAL-REWRITE.md` |
| Large multi-phase project | `prompts/PROMPT-ORCHESTRATOR.md` + `MULTI-AGENT-FRAMEWORK.md` |

---

## Customization

- **Adjust quality bar:** Edit `CLAUDE-CODE-CONTRACT.md` (e.g., raise coverage to 90%)
- **Add language rules:** Edit `CODING-CONTEXT.md`
- **Change project template:** Edit `TASK-BRIEF-TEMPLATE.md`
- **Modify mode prompts:** Edit files in `prompts/`

---

## Token Efficiency

Not every session needs all files. Attach only what your task requires:

| Session Type | Attach | Can Skip |
|---|---|---|
| Quick generate | CONTRACT + PROMPT + BRIEF | GUIDE, CHEATSHEET, THINKING-FRAMEWORKS |
| Complex/novel generate | CONTRACT + PROMPT + BRIEF + THINKING-FRAMEWORKS | GUIDE, CHEATSHEET |
| Refactor / Debug / Rewrite | CONTRACT + matching PROMPT + BRIEF | CHEATSHEET, THINKING-FRAMEWORKS |

For large projects, split across sessions: core types → API layer → test suite. See `CLAUDE-CODE-GUIDE.md` for examples.

---

## Contract Workflow

Every code change follows this pipeline:

1. **Design Thinking Gate** — For non-trivial logic, algorithms, data structure choices, or design trade-offs, produce a `DESIGN-NOTE.md` before writing any test. The agent decides whether the gate is required or optional and states the decision explicitly. Required criteria take priority over optional criteria when they overlap.
2. **TDD** — RED (write failing test) → GREEN (write minimal code) → REFACTOR (clean up) → VALIDATE → RETRY (max 3 attempts).
3. **Validation** — Tests (>=80% coverage on changed files), lint (zero warnings), type-checking, security audit, benchmarks (if applicable per mode). UI projects add: visual regression tests, accessibility audit.
4. **Documentation Sync** — README and CHANGELOG updates required for behavior-changing code changes. Internal refactors and test-only changes are exempt.

---

## Documentation Sync Contract

Every code change affecting behavior, APIs, configuration, or architecture **must** include corresponding README and CHANGELOG updates. Stale docs are treated as bugs with the same severity as failing tests.

| Code Change | README Update | CHANGELOG Update |
|---|---|---|
| New function/endpoint/command | Add to Usage / API Reference | `### Added` |
| Changed function signature/behavior | Update examples | `### Changed` |
| Removed function/endpoint | Remove from docs | `### Removed` |
| New config option / env var | Add to Configuration table | `### Added` |
| Performance improvement | Update Performance numbers | `### Changed` |
| Bug fix | Update Troubleshooting if relevant | `### Fixed` |
| Architecture change | Update Architecture diagram | `### Changed` |
| Security fix | Update relevant sections | `### Security` |
| Setup process change | Update Quick Start / Installation | `### Changed` |

CHANGELOG format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) with [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## Quality Standards

Defined in `CLAUDE-CODE-CONTRACT.md`. Deliverables scale by mode (not every session requires a full project):

**Universal (all modes):**
- 100% type hints on public APIs in changed code
- >= 80% test coverage on changed files, all tests pass, zero skipped
- Zero TODOs, zero lint/type warnings
- No secrets in code
- Ready to git commit immediately

**Generate / Full Rewrite (new projects):**
- Benchmarks for hot paths
- Security audit clean
- Working setup script, CI configuration
- Comprehensive README with tested examples
- CHANGELOG.md in Keep a Changelog format
- CLAUDE.md with project-specific coding contracts for incremental work

**UI projects (all applicable modes):**
- Visual regression tests (screenshot baselines)
- Accessibility audit (WCAG 2.1 AA, Lighthouse ≥90)
- Responsive at specified breakpoints
- Keyboard navigable, semantic HTML

**Doc sync:** README and CHANGELOG updated for behavior-changing code changes. Internal refactors and test-only changes are exempt.

Code that violates any applicable standard is rejected with a specific fix request.

---

## CLAUDE.md — From Generation to Incremental Work

The contract system is designed for large-scale code generation. Once the project is generated, developers switch to Claude Code for incremental modifications (bug fixes, feature additions, refactors). The generated `CLAUDE.md` bridges this transition:

1. **During generation** — the contract system produces a project-specific `CLAUDE.md` as a mandatory deliverable (Generate / Full Rewrite modes).
2. **After generation** — developers use Claude Code normally. The `CLAUDE.md` is automatically loaded by Claude Code and ensures the same quality standards without re-attaching the full contract system.
3. **During incremental work** — if a modification changes project conventions, validation commands, or architecture, the developer updates `CLAUDE.md` as part of that change.

The `CLAUDE.md` contains: distilled quality standards, the project's exact validation commands, language-specific conventions, project structure, documentation sync rules, error handling patterns, and security requirements. See `CLAUDE-CODE-CONTRACT.md § CLAUDE.md Generation Spec` for the full specification.

---

## Further Reading

| File | When to read |
|---|---|
| `CLAUDE-CODE-GUIDE.md` | First time — full walkthrough with examples for all 5 modes |
| `CLAUDE-CODE-CHEATSHEET.md` | During a project — quick reference & copy-paste templates |
| `CLAUDE-CODE-CONTRACT.md` | When reviewing output against standards |
| `THINKING-FRAMEWORKS.md` | Complex tasks — problem decomposition, algorithms, novel domains |
| `MULTI-AGENT-FRAMEWORK.md` | Large projects — orchestrator/implementer/reviewer pipeline |
| `CODING-CONTEXT.md` | When customizing for your language/project |

---

**Version:** 2.1 | **Languages:** Python, TypeScript, Rust, Go, C/C++, Swift, Bash
