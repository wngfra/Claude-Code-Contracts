# Claude Code Guide — How to Use the Production Contract

Complete walkthrough for generating production-grade code with Claude using the contract system.

---

## The Contract System

### Three Parts

| File | Purpose |
|---|---|
| **CLAUDE-CODE-CONTRACT.md** | Quality standards (100% type hints, 80%+ tests, etc.) |
| **prompts/PROMPT-CLAUDE-CODE-MASTER.md** | Master prompt — instructions Claude reads to know *how* to generate code |
| **TASK-BRIEF-TEMPLATE.md** | Your project requirements — copy and fill for each project |

### The Flow

```
You fill TASK-BRIEF.md (your requirements)
        ↓
Give Claude the contract files + your brief
        ↓
Claude reads the contract and generates code
        ↓
Code meets 100% of standards
        ↓
Verify (make test, make lint) → Ship
```

---

## Step 1: Fill Out TASK-BRIEF.md

Copy the template and fill it with your project details:

```bash
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md
```

**Example: GitHub Issue Triage System**

```markdown
# TASK-BRIEF: GitHub Issue Triage AI

## Project
Name: github-issue-triage
Language: Python
Framework: FastAPI

## Problem Statement
Build a system that reads GitHub issues, uses Claude AI to categorize them
(priority/type), extract stakeholders, and suggest labels. CLI + REST API.

## Functional Requirements

**Must have:**
- [ ] Read issues from GitHub API (public + private repos)
- [ ] Classify by priority (critical/major/minor/low)
- [ ] Extract stakeholder mentions (@username)
- [ ] Generate 100-word summary using Claude
- [ ] Output JSON (API) or formatted table (CLI)

**Nice to have:**
- [ ] Batch processing (50+ issues)
- [ ] Custom classification rules (YAML config)

## Constraints
- No external databases (SQLite OK)
- Python 3.10+

## Success Criteria
- [ ] All functional requirements working
- [ ] Tests: >= 80% coverage, all pass
- [ ] Setup works: `setup.sh && make test` first try
- [ ] README has working examples
- [ ] Type hints: 100% on public APIs
```

---

## Step 2: Give Claude the Contract Files

Choose the method that matches how you use Claude:

### Claude Code CLI

Start `claude` in your project directory, load the contracts with `/add`, then type your request:

```bash
cd ~/my-project
claude

# Inside the CLI session:
> /add /path/to/claude-code-contracts/CLAUDE-CODE-CONTRACT.md
> /add /path/to/claude-code-contracts/CODING-CONTEXT.md
> /add /path/to/claude-code-contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md
> /add TASK-BRIEF.md

> Generate production code following CLAUDE-CODE-CONTRACT.md and TASK-BRIEF.md
```

Or pipe everything in one shot:

```bash
cat CLAUDE-CODE-CONTRACT.md CODING-CONTEXT.md prompts/PROMPT-CLAUDE-CODE-MASTER.md TASK-BRIEF.md \
  | claude --print "Generate production code following the attached contract and task brief."
```

### Claude.ai (web)

1. Start a new conversation
2. Attach: `CLAUDE-CODE-CONTRACT.md`, `CODING-CONTEXT.md`, `TASK-BRIEF.md`, `prompts/PROMPT-CLAUDE-CODE-MASTER.md`
3. Type: *"Generate production code following CLAUDE-CODE-CONTRACT.md and TASK-BRIEF.md"*

### Claude API

```python
import anthropic

client = anthropic.Anthropic()

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

---

## Step 3: Claude Generates Code

Claude reads the contract and generates:

- Complete source code (no TODOs)
- Full test suite (>= 80% coverage, all pass)
- Type hints (100% on public APIs)
- Documentation (README with examples)
- Configuration (`.env.example`)
- Build tools (Makefile, setup script)
- Git ready (`.gitignore`, first commit)

---

## Step 4: Verify & Ship

```bash
cd ~/my-project

./setup.sh          # install dependencies
make test           # should pass with >= 80% coverage
make lint           # should show zero warnings
mypy src/           # type check (Python) — or tsc --noEmit (TS)
make run            # should start without errors
git status          # should be clean
```

If all pass, **ship it**.

---

## When Claude Falls Short

Don't accept broken code. Be specific about what's wrong:

```
Bad:  "The code has bugs"
Good: "fetch_user() is missing error handling for DatabaseError.
       Regenerate src/api.py with explicit try/except for all database calls."

Bad:  "Tests don't work"
Good: "Test coverage is 73%, contract requires >= 80%.
       Add integration tests for error conditions in tests/integration/."
```

### Rejection Protocol

1. **Identify the issue:** "Coverage is 65%, contract requires 80%"
2. **Request specific fix:** "Add integration tests for error paths in `tests/integration/test_api.py`"
3. **Rerun gate:** "Run `make test` before returning"

---

## Example: Full Workflow

### 1. Create brief

```bash
mkdir ~/weather-api && cd ~/weather-api
cp /path/to/claude-code-contracts/TASK-BRIEF-TEMPLATE.md TASK-BRIEF.md
# Edit TASK-BRIEF.md with your requirements
```

### 2. Start Claude Code

```bash
claude

> /add /path/to/claude-code-contracts/CLAUDE-CODE-CONTRACT.md
> /add /path/to/claude-code-contracts/CODING-CONTEXT.md
> /add /path/to/claude-code-contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md
> /add TASK-BRIEF.md
> Generate a production-grade weather API per the contract and task brief.
```

### 3. Verify

```bash
npm install
npm test        # 87% coverage
npm run lint    # zero warnings
npm start       # server on port 3000
```

### 4. Ship

```bash
git add .
git commit -m "feat: initial weather API implementation"
```

---

## Customization

### Add your own language rules

Edit `CODING-CONTEXT.md`:

```markdown
## Project-Specific Standards
- Error codes: HTTP status + custom codes (ERR_RATE_LIMITED)
- Logging: Always include request_id
- Async: async/await only, no callbacks
```

### Adjust quality bar

Edit `CLAUDE-CODE-CONTRACT.md`:

```markdown
## Coverage Requirement
- Default: >= 80%
- Your project: >= 90%
```

### Modify the master prompt

Edit `prompts/PROMPT-CLAUDE-CODE-MASTER.md` to change the architecture patterns, testing requirements, or delivery format Claude follows.

---

## Final Notes

### Quality Is Not Negotiable

This contract exists because "I'll iterate later" projects never do. Build right the first time.

### One-Shot Generation

If you follow the contract, you should not need to iterate. If you do, the TASK-BRIEF wasn't specific enough or the contract wasn't clear.

### Scale the Brief, Not the Standards

Whether it's a 2-hour CLI tool or a 40-hour platform, the contract applies the same. Write a more detailed TASK-BRIEF for bigger projects.
