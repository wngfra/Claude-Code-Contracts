# Claude Code Production Contracts

**Standalone contract system for generating production-grade code with Claude Code.**

Works independently. No OpenClaw required. Just attach these files to any Claude Code session.

---

## 🚀 Quick Start

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
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md    (master prompt)
```

### 2. For Your First Project
```bash
# Copy template
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md

# Fill it out
# (name, language, 3 requirements, success criteria)

# Attach to Claude Code:
#   - CLAUDE-CODE-CONTRACT.md
#   - CODING-CONTEXT.md
#   - TASK-BRIEF.md
#   - prompts/PROMPT-CLAUDE-CODE-MASTER.md
```

### 3. Get Production Code
```bash
cd ~/my-project
./setup.sh
make test    # >= 80% coverage
make lint    # zero warnings
git status   # ready
```

---

## 📚 Files Explained

| File | Purpose | Read When |
|---|---|---|
| **START-HERE.md** | Entry point, 2-min overview | **First** |
| **CLAUDE-CODE-GUIDE.md** | Complete walkthrough with examples | Planning a project |
| **CLAUDE-CODE-CONTRACT.md** | Quality standards & requirements | Need to understand standards |
| **PROMPT-CLAUDE-CODE-MASTER.md** | What Claude Code sees (master prompt) | Want to customize |
| **CLAUDE-CODE-CHEATSHEET.md** | Quick reference, copy-paste templates | During a project |
| **TASK-BRIEF-TEMPLATE.md** | Copy this for each new project | Starting a project |
| **CODING-CONTEXT.md** | Language-specific rules (Python/TS/Go) | Customizing for your languages |

---

## 🎯 The System

```
Fill TASK-BRIEF.md (your project requirements)
        ↓
Attach all contract files to Claude Code
        ↓
Claude Code generates production code:
  • 100% type hints
  • ≥80% test coverage
  • Zero TODOs
  • Zero warnings
  • Working setup
  • README with examples
        ↓
Ship immediately (no iteration needed)
```

---

## ✅ Quality Standards (Non-Negotiable)

Every Claude Code project meets:

- ✅ 100% type hints on public APIs
- ✅ ≥80% test coverage, all tests pass
- ✅ Zero TODOs, zero stubs
- ✅ Zero linting warnings
- ✅ Zero type errors
- ✅ Working setup script
- ✅ Accurate README with examples
- ✅ Ready to git commit immediately

**Code violating these standards is rejected and rerequest.**

---

## 🏗️ Project Structure

Claude Code generates this structure:

```
my-project/
├── src/                    # Application code
│   ├── main.py
│   ├── core/               # Core logic
│   ├── models.py           # Data models
│   ├── errors.py           # Custom exceptions
│   └── utils/
├── tests/                  # Full test suite
│   ├── unit/
│   ├── integration/
│   └── fixtures.py
├── scripts/
│   └── setup.sh            # One-command setup
├── README.md               # Setup + examples
├── .env.example            # Config template
├── Makefile                # test, lint, run targets
├── pyproject.toml          # Dependencies
├── .gitignore
└── .git/                   # First commit ready
```

---

## 💬 How to Use With Claude Code

### Option A: Web Interface
1. Open Claude Code or Copilot interface
2. Attach files:
   - CLAUDE-CODE-CONTRACT.md
   - CODING-CONTEXT.md
   - TASK-BRIEF.md (your project)
   - prompts/PROMPT-CLAUDE-CODE-MASTER.md
3. Paste prompt from START-HERE.md or CLAUDE-CODE-GUIDE.md
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

### Option C: CLI (if you have Claude CLI)
```bash
claude code \
  --context CLAUDE-CODE-CONTRACT.md \
  --context CODING-CONTEXT.md \
  --context TASK-BRIEF.md \
  --context prompts/PROMPT-CLAUDE-CODE-MASTER.md \
  "Generate production code"
```

---

## 🎓 Reading Order

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

## ✨ Key Features

### One-Shot Generation
No iteration. Code is complete, tested, documented on first run.

### Language Agnostic
Works with Python, TypeScript, Go, and more.

### Portable
No dependencies on OpenClaw, tools, or specific IDEs. Just Claude Code + contracts.

### Customizable
Edit CODING-CONTEXT.md for your language rules and project standards.

### Enforceable
Clear standards. Code violating them is rejected with specific fix requests.

---

## ❌ What Gets Rejected

- Code with TODOs or stubs
- Missing type hints (even one function)
- Test coverage < 80%
- Failing tests or skipped tests
- Linting warnings or errors
- Type checking errors
- Broken setup script
- README with non-working examples
- Secrets in code (no API keys, tokens)
- Silent error handling

**Reject and rerequest with specific fix.**

---

## 📞 Usage Examples

### Example 1: Web API
```bash
cp TASK-BRIEF-TEMPLATE.md weather-api/TASK-BRIEF.md

# Edit TASK-BRIEF.md:
# Name: weather-api
# Language: TypeScript
# Must have:
#   - GET /weather?city=London
#   - 7-day forecast
#   - Cache results

# Attach to Claude Code:
# - CLAUDE-CODE-CONTRACT.md
# - CODING-CONTEXT.md
# - TASK-BRIEF.md
# - prompts/PROMPT-CLAUDE-CODE-MASTER.md

# Get back: Complete Express API with tests
cd weather-api
npm install
npm test      # 87% coverage
npm start     # Server running
```

### Example 2: CLI Tool
```bash
cp TASK-BRIEF-TEMPLATE.md github-triage/TASK-BRIEF.md

# Edit TASK-BRIEF.md:
# Name: github-triage
# Language: Python
# Must have:
#   - Read issues from GitHub API
#   - Classify by priority
#   - Generate summary with Claude

# Get back: Complete CLI with tests
cd github-triage
./setup.sh
make test      # 82% coverage
python -m src.main --help
```

---

## 🔧 Customization

### Add Language Rules
Edit `CODING-CONTEXT.md`:
```markdown
## Your Project Standards

- Error codes: Use HTTP status + custom codes (ERR_RATE_LIMITED)
- Logging: Always include request_id
- Async: async/await only, no callbacks
- Testing: Smoke tests for all API calls
```

### Adjust Quality Bar
Edit sections in `CLAUDE-CODE-CONTRACT.md`:
```markdown
## Coverage Requirement

- Default: ≥80%
- Your project: ≥90% (more critical code)
```

### Change Project Structure
Update template sections in `TASK-BRIEF-TEMPLATE.md`.

---

## 🎁 What's Included

```
claude-code-contracts/ (self-contained)
├── README.md
├── START-HERE.md
├── CLAUDE-CODE-CONTRACT.md         (12 KB)
├── CLAUDE-CODE-GUIDE.md             (12 KB)
├── CLAUDE-CODE-CHEATSHEET.md        (11 KB)
├── TASK-BRIEF-TEMPLATE.md           (5 KB)
├── CODING-CONTEXT.md                (varies)
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md (9 KB)

Total: ~50 KB, self-contained, no dependencies
```

---

## 🚀 Your First Project (Right Now)

```bash
# 1. Read (2 min)
cat START-HERE.md

# 2. Copy template (1 min)
mkdir my-awesome-project
cp TASK-BRIEF-TEMPLATE.md my-awesome-project/TASK-BRIEF.md
cd my-awesome-project

# 3. Fill it out (5 min)
# Edit TASK-BRIEF.md with your project details

# 4. Attach to Claude Code
# - CLAUDE-CODE-CONTRACT.md
# - CODING-CONTEXT.md
# - TASK-BRIEF.md
# - prompts/PROMPT-CLAUDE-CODE-MASTER.md
#
# Prompt: "Generate production code following CLAUDE-CODE-CONTRACT.md"

# 5. Verify (5 min)
./setup.sh
make test
make lint
git status

# 6. Ship 🎉
```

---

## 📖 Files Are Self-Documenting

Each file has:
- Clear purpose at the top
- Table of contents or sections
- Examples
- Quick reference

**Everything is here.** No external documentation needed.

---

## 💡 Key Principle

> **One-shot, ship-ready code. Quality is non-negotiable.**

If Claude Code generates code that violates the contract, reject it with a specific fix request. Rerun. Repeat until it meets standards.

This is not optional. This is how you maintain code quality.

---

## 🤝 Use This With Any Claude Code Interface

- Claude.ai web interface
- Copilot IDE extensions
- Claude API
- Any LLM that accepts Claude as model

Just attach the files and use the prompts provided.

---

## ✅ Ready to Go

Everything you need is in this folder. No dependencies. No setup required.

Start with `START-HERE.md` and build something great.

---

**Version:** 1.0
**Status:** Production Ready
**Last Updated:** March 2026
