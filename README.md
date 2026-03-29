# Claude Code Production Contracts

A contract system that ensures Claude generates production-grade code: fully typed, tested, benchmarked, documented, and ready to ship. Supports **four task modes** — generating new projects, refactoring, debugging, and partial rewrites.

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
├── CLAUDE-CODE-CONTRACT.md                ← quality standards (attach to every session)
├── CLAUDE-CODE-GUIDE.md                   ← detailed walkthrough with examples
├── CLAUDE-CODE-CHEATSHEET.md              ← quick reference & copy-paste templates
├── THINKING-FRAMEWORKS.md                 ← problem decomposition & algorithm design
├── TASK-BRIEF-TEMPLATE.md                 ← copy this for each task
├── CODING-CONTEXT.md                      ← language-specific rules
└── prompts/
    ├── PROMPT-CLAUDE-CODE-MASTER.md       ← Generate mode (new projects)
    ├── PROMPT-REFACTOR.md                 ← Refactor mode (restructure existing code)
    ├── PROMPT-DEBUG.md                    ← Debug mode (find and fix bugs)
    └── PROMPT-PARTIAL-REWRITE.md          ← Partial Rewrite mode (replace a module)
```

| Task | Prompt to Attach |
|---|---|
| Build something new | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| Restructure existing code | `prompts/PROMPT-REFACTOR.md` |
| Fix a specific bug | `prompts/PROMPT-DEBUG.md` |
| Replace a module/component | `prompts/PROMPT-PARTIAL-REWRITE.md` |

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

## Quality Standards

Defined in `CLAUDE-CODE-CONTRACT.md`. Non-negotiable:

- 100% type hints on public APIs
- >= 80% test coverage, all tests pass, zero skipped
- Benchmarks for hot paths
- Zero TODOs, zero lint/type warnings
- Security audit clean, no secrets in code
- Working setup script, CI configuration
- Comprehensive README with tested examples
- CHANGELOG.md in Keep a Changelog format
- README and CHANGELOG always synced with code changes
- Ready to git commit immediately

Code that violates any standard is rejected with a specific fix request.

---

## Further Reading

| File | When to read |
|---|---|
| `CLAUDE-CODE-GUIDE.md` | First time — full walkthrough with examples for all 4 modes |
| `CLAUDE-CODE-CHEATSHEET.md` | During a project — quick reference & copy-paste templates |
| `CLAUDE-CODE-CONTRACT.md` | When reviewing output against standards |
| `THINKING-FRAMEWORKS.md` | Complex tasks — problem decomposition, algorithms, novel domains |
| `CODING-CONTEXT.md` | When customizing for your language/project |

---

**Version:** 2.0 | **Languages:** Python, TypeScript, Rust, Go, C/C++, Bash
