# Claude Code Production Contracts

A contract system that ensures Claude generates production-grade code: fully typed, tested, documented, and ready to ship on first run.

Works with the **Claude Code CLI**, **claude.ai web**, or the **Claude API**. No other tools required.

---

## Files

```
claude-code-contracts/
├── README.md                           ← you are here
├── CLAUDE-CODE-CONTRACT.md             ← quality standards (attach to every session)
├── CLAUDE-CODE-GUIDE.md                ← detailed walkthrough with examples
├── CLAUDE-CODE-CHEATSHEET.md           ← quick reference & copy-paste templates
├── TASK-BRIEF-TEMPLATE.md             ← copy this for each project
├── CODING-CONTEXT.md                   ← language-specific rules
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md    ← master prompt (see below)
```

### What is PROMPT-CLAUDE-CODE-MASTER.md?

It is a **system prompt** — instructions that tell Claude *how* to generate code. It defines the architecture, type system requirements, testing standards, and delivery format Claude must follow.

**You don't read it yourself.** You give it to Claude as context so it knows exactly what quality bar to hit. Think of it as the "contract enforcer" that Claude reads before writing any code.

---

## Quick Start

### 1. Prepare your project brief

```bash
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md
# Edit it: project name, language, 3+ requirements, success criteria
```

### 2. Give everything to Claude

How you do this depends on your interface:

#### Claude Code CLI (recommended)

Start Claude Code in your project directory and reference the contract files with `@` in your prompt:

```bash
cd ~/my-project
claude

# Inside the session, reference files with @ and type your prompt:
> Read @/path/to/claude-code-contracts/CLAUDE-CODE-CONTRACT.md @/path/to/claude-code-contracts/CODING-CONTEXT.md @/path/to/claude-code-contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md and @TASK-BRIEF.md then generate production code following the contract and task brief.
```

Or pipe everything in non-interactive mode:

```bash
cat CLAUDE-CODE-CONTRACT.md CODING-CONTEXT.md prompts/PROMPT-CLAUDE-CODE-MASTER.md TASK-BRIEF.md \
  | claude -p "Generate production code following the attached contract and task brief."
```

#### Claude.ai (web)

1. Start a new conversation
2. Attach these files: `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `TASK-BRIEF.md`, `prompts/PROMPT-CLAUDE-CODE-MASTER.md`
3. Type: *"Generate production code following CLAUDE-CODE-CONTRACT.md and TASK-BRIEF.md"*

#### Claude API

```python
import anthropic

client = anthropic.Anthropic()

# Read contract files
contract = open("CLAUDE-CODE-CONTRACT.md").read()
context = open("CODING-CONTEXT.md").read()
master_prompt = open("prompts/PROMPT-CLAUDE-CODE-MASTER.md").read()
task_brief = open("TASK-BRIEF.md").read()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=8192,
    system=master_prompt,
    messages=[{
        "role": "user",
        "content": f"{contract}\n\n{context}\n\n{task_brief}\n\nGenerate production code following the contract and task brief above."
    }],
)
```

### 3. Verify and ship

```bash
cd ~/my-project
./setup.sh        # one-command install
make test          # must pass with >= 80% coverage
make lint          # must show zero warnings
git status         # should be ready to commit
```

If all checks pass, ship it. If something fails, tell Claude exactly what's wrong and ask for a fix.

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

## When to Reject Generated Code

If Claude returns code with any of these, **reject it and request a specific fix**:

- TODOs, stubs, or incomplete sections
- Missing type hints (even one function)
- Test coverage below 80%, or skipped/failing tests
- Lint or type-check warnings
- Broken setup script
- README with non-working examples

Example rejection:
> "Test coverage is 73%, contract requires >= 80%. Add integration tests for error cases in `tests/integration/`. Rerun and verify coverage before returning."

---

## Customization

- **Adjust quality bar:** Edit `CLAUDE-CODE-CONTRACT.md` (e.g., raise coverage to 90%)
- **Add language rules:** Edit `CODING-CONTEXT.md`
- **Change project template:** Edit `TASK-BRIEF-TEMPLATE.md`
- **Modify the master prompt:** Edit `prompts/PROMPT-CLAUDE-CODE-MASTER.md`

---

## Further Reading

| File | When to read |
|---|---|
| `CLAUDE-CODE-GUIDE.md` | First time — full walkthrough with examples |
| `CLAUDE-CODE-CHEATSHEET.md` | During a project — quick reference |
| `CLAUDE-CODE-CONTRACT.md` | When reviewing generated code against standards |
| `CODING-CONTEXT.md` | When customizing for your language/project |
