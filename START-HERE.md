# Claude Code Production Contracts — Start Here

**Standalone system for generating production-grade code with Claude Code.**

No OpenClaw required. Works with any Claude Code interface.

---

## ⚡ What This Is (30 Seconds)

A contract system that ensures Claude Code generates:
- ✅ 100% type hints (all functions documented)
- ✅ ≥80% test coverage (all tests pass)
- ✅ Zero TODOs (complete code)
- ✅ Zero warnings (clean lint/typecheck)
- ✅ Working setup (install script + Makefile)
- ✅ Ready to ship (git commit immediately)

**One-shot, no iteration.**

---

## 🚀 Quick Start (5 Minutes)

### Step 1: Read the Guide
```
Read: CLAUDE-CODE-GUIDE.md (15 minutes)
```

### Step 2: Fill Your Project Brief
```bash
# Copy template
cp TASK-BRIEF-TEMPLATE.md ~/my-project/TASK-BRIEF.md

# Edit it:
# - Project name
# - Language (Python/TypeScript/Go)
# - 3 functional requirements
# - Success criteria
```

### Step 3: Attach to Claude Code
Attach these 4 files to your Claude Code session:
1. `CLAUDE-CODE-CONTRACT.md`
2. `CODING-CONTEXT.md`
3. `TASK-BRIEF.md` (your project)
4. `prompts/PROMPT-CLAUDE-CODE-MASTER.md`

### Step 4: Get Code
Ask Claude Code: *"Generate production code following CLAUDE-CODE-CONTRACT.md"*

### Step 5: Verify & Ship
```bash
cd ~/my-project
./setup.sh
make test   # All pass? >= 80% coverage?
make lint   # Zero warnings?
git status  # Ready?
```

If yes → **Ship immediately. No further iteration.**

---

## 📁 Files in This Folder

```
claude-code-contracts/
├── README.md                          Overview (this is it)
├── START-HERE.md                      Entry point (you're reading this)
│
├── CLAUDE-CODE-GUIDE.md               How to use (read first)
├── CLAUDE-CODE-CONTRACT.md            Quality standards (reference)
├── CLAUDE-CODE-CHEATSHEET.md          Quick lookup (bookmark)
├── TASK-BRIEF-TEMPLATE.md             Copy for each project
├── CODING-CONTEXT.md                  Language-specific rules
│
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md   What Claude Code sees
```

---

## 📖 Reading Order

**First project:**
1. This file (2 min)
2. CLAUDE-CODE-GUIDE.md (15 min)
3. Copy TASK-BRIEF-TEMPLATE.md
4. Fill with your project
5. Attach & spawn

**Subsequent projects:**
1. CLAUDE-CODE-CHEATSHEET.md (1 min)
2. Copy TASK-BRIEF-TEMPLATE.md
3. Fill quickly
4. Attach & spawn

---

## ✅ What You Get Back

Every Claude Code project delivers:

```
my-project/
├── src/               ← All source code (no TODOs)
├── tests/             ← Full test suite (≥80% coverage)
├── README.md          ← Setup + working examples
├── .env.example       ← Config template
├── Makefile           ← build, test, lint, run targets
├── setup.sh           ← One-command install
└── .git/              ← Ready to commit
```

Status when returned:
- `make test` → All pass, 82% coverage
- `make lint` → Zero warnings
- `git status` → Nothing to commit

---

## 💡 The Contract

Claude Code reads the contract and knows:
- ✅ Must include 100% type hints
- ✅ Must have ≥80% test coverage
- ✅ Must have zero TODOs
- ✅ Must pass linting
- ✅ Must have working setup
- ✅ Must have accurate README

**No ambiguity. No shortcuts.**

---

## ❌ When to Reject Code

If Claude Code returns code that has:
- TODOs or incomplete sections
- Missing type hints
- Test coverage < 80%
- Failing or skipped tests
- Linting warnings
- Broken setup
- Non-working README examples

**Reject it.** Request specific fix. Rerun.

Example:
> "Test coverage is 73%, contract requires ≥80%. 
> Add integration tests for error cases in tests/integration/.
> Rerun and ensure npm test shows ≥80% coverage before returning."

---

## 🎯 It's That Simple

```
Fill brief + attach contracts 
  ↓
Claude Code generates code
  ↓
Verify (make test, make lint)
  ↓
Ship (no iteration)
```

---

## 🤔 FAQ

**Q: Do I need OpenClaw?**
A: No. These contracts work with any Claude Code interface.

**Q: Can I use this with Claude.ai?**
A: Yes. Attach files, paste prompt, get code.

**Q: What languages?**
A: Python, TypeScript, Go, and more. Edit CODING-CONTEXT.md.

**Q: How long does it take?**
A: 20-30 minutes for first project. Faster after.

**Q: What if it fails?**
A: Rerun with clearer TASK-BRIEF or specific fix request.

**Q: Can I customize standards?**
A: Yes. Edit CLAUDE-CODE-CONTRACT.md and CODING-CONTEXT.md.

---

## 🚀 Ready?

1. Open **CLAUDE-CODE-GUIDE.md**
2. Follow the steps
3. Build something great

Everything you need is in this folder. No external dependencies.

---

**Version:** 1.0  
**Status:** Production Ready  
**License:** Use freely

**Start with CLAUDE-CODE-GUIDE.md →**
