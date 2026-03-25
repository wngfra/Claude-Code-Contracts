# Claude Code Production Contracts

A contract system that ensures Claude produces production-grade code: fully typed, tested, documented, and ready to ship. Supports **four task modes** — generating new projects, refactoring, debugging, and partial rewrites.

Works with the **Claude Code CLI**, **claude.ai web**, or the **Claude API**. No other tools required.

---

## Files

```
claude-code-contracts/
├── README.md                              ← you are here
├── CLAUDE-CODE-CONTRACT.md                ← quality standards (attach to every session)
├── CLAUDE-CODE-GUIDE.md                   ← detailed walkthrough with examples
├── CLAUDE-CODE-CHEATSHEET.md              ← quick reference & copy-paste templates
├── TASK-BRIEF-TEMPLATE.md                ← copy this for each task
├── CODING-CONTEXT.md                      ← language-specific rules
└── prompts/
    ├── PROMPT-CLAUDE-CODE-MASTER.md       ← Generate mode (new projects)
    ├── PROMPT-REFACTOR.md                 ← Refactor mode (restructure existing code)
    ├── PROMPT-DEBUG.md                    ← Debug mode (find and fix bugs)
    └── PROMPT-PARTIAL-REWRITE.md          ← Partial Rewrite mode (replace a module)
```

### What Are the Prompt Files?

Each prompt is a **system prompt** — instructions that tell Claude *how* to approach a specific type of task. They define the constraints, checklists, and delivery format for that mode.

**You don't read them yourself.** You give the matching one to Claude as context so it knows what mode to operate in and what quality bar to hit.

| Task | Prompt to Attach |
|---|---|
| Build something new | `prompts/PROMPT-CLAUDE-CODE-MASTER.md` |
| Restructure existing code | `prompts/PROMPT-REFACTOR.md` |
| Fix a specific bug | `prompts/PROMPT-DEBUG.md` |
| Replace a module/component | `prompts/PROMPT-PARTIAL-REWRITE.md` |

---

## Quick Start

### 1. Pick your task type and prepare a brief

```bash
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md
# Edit it: check the task type, fill project details, add mode-specific sections
```

### 2. Give everything to Claude with the matching prompt

```bash
cd ~/my-project
claude --add-dir /path/to/claude-code-contracts

# Use @ to reference the contract + matching prompt. Examples:

# Generate (new project)
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-CLAUDE-CODE-MASTER.md @TASK-BRIEF.md
  Generate production code following the contract and task brief.

# Refactor
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-REFACTOR.md @TASK-BRIEF.md
  Refactor the code following the contract and task brief.

# Debug
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-DEBUG.md @TASK-BRIEF.md
  Debug and fix the issue following the contract and task brief.

# Partial Rewrite
> @CONTRACT.md @CODING-CONTEXT.md @prompts/PROMPT-PARTIAL-REWRITE.md @TASK-BRIEF.md
  Rewrite the module following the contract and task brief.
```

Also works with **claude.ai** (attach the files) or the **Claude API** (pass as system prompt + user message). See `CLAUDE-CODE-GUIDE.md` for full examples.

### 3. Verify and ship

```bash
cd ~/my-project
make test          # must pass with >= 80% coverage
make lint          # must show zero warnings
git status         # should be ready to commit
```

If something fails, tell Claude exactly what's wrong and ask for a fix. See the cheatsheet for mode-specific rejection templates.

---

## Quality Standards

Every generated project must meet these (defined in `CLAUDE-CODE-CONTRACT.md`):

- **100% type hints** on public APIs
- **>= 80% test coverage**, all tests pass, zero skipped
- **Zero TODOs** or stubs — complete code only
- **Zero lint/type warnings**
- **Working setup** — `setup.sh && make test` succeeds first try
- **Accurate README** with tested examples
- **No secrets in code** — config via `.env.example`

Code that violates any standard should be rejected with a specific fix request.

---

## Typical Output

Claude generates a complete project structure like this:

```
my-project/
├── src/                    # application code (100% typed)
├── tests/
│   ├── unit/               # core logic tests
│   └── integration/        # API/workflow tests
├── scripts/setup.sh        # one-command install
├── README.md               # setup + working examples
├── Makefile                # test, lint, run targets
├── .env.example            # config template
├── pyproject.toml          # (or package.json, Cargo.toml, go.mod, CMakeLists.txt)
├── .gitignore
└── .git/                   # first commit ready
```

---

## Supported Languages

Python, TypeScript, Go, Rust, C, and C++. Language-specific rules live in `CODING-CONTEXT.md` — edit it to add your own standards.

---

## When to Reject Output

### All Modes
- TODOs, stubs, or incomplete sections
- Missing type hints (even one function)
- Test coverage below 80%, or skipped/failing tests
- Lint or type-check warnings

### Mode-Specific Rejections

**Refactor:** "Existing tests broke — you changed behavior, not just structure."
**Debug:** "Fix is too broad — you refactored 50 lines but only 1 line was the bug."
**Partial Rewrite:** "Callers are broken — provide a migration guide or preserve the old API."

See `CLAUDE-CODE-CHEATSHEET.md` for copy-paste rejection templates.

---

## Customization

- **Adjust quality bar:** Edit `CLAUDE-CODE-CONTRACT.md` (e.g., raise coverage to 90%)
- **Add language rules:** Edit `CODING-CONTEXT.md`
- **Change project template:** Edit `TASK-BRIEF-TEMPLATE.md`
- **Modify mode prompts:** Edit files in `prompts/`
- **Add a new mode:** Create a new `prompts/PROMPT-*.md` following the pattern of existing ones

---

## Further Reading

| File | When to read |
|---|---|
| `CLAUDE-CODE-GUIDE.md` | First time — full walkthrough with examples for all 4 modes |
| `CLAUDE-CODE-CHEATSHEET.md` | During a project — quick reference & copy-paste templates |
| `CLAUDE-CODE-CONTRACT.md` | When reviewing output against standards |
| `CODING-CONTEXT.md` | When customizing for your language/project |
| `prompts/PROMPT-REFACTOR.md` | Understanding refactor mode constraints |
| `prompts/PROMPT-DEBUG.md` | Understanding debug mode constraints |
| `prompts/PROMPT-PARTIAL-REWRITE.md` | Understanding rewrite mode constraints |
