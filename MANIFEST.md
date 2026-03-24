# Claude Code Contracts — Manifest

**Self-contained production contract system for Claude Code.**

Version: 1.0  
Status: Production Ready  
Last Updated: March 2026

---

## 📦 Contents (8 Files, ~60 KB)

### Entry Points
- **START-HERE.md** (4.6 KB) — Quick 2-min overview, read this first
- **README.md** (9.2 KB) — Full system overview, file guide

### Core Contracts
- **CLAUDE-CODE-CONTRACT.md** (12.3 KB) — Quality standards document
- **PROMPT-CLAUDE-CODE-MASTER.md** (9 KB) — Master prompt (in `prompts/`)

### Guides & References
- **CLAUDE-CODE-GUIDE.md** (12.1 KB) — Complete how-to with examples
- **CLAUDE-CODE-CHEATSHEET.md** (11.2 KB) — Quick copy-paste reference
- **TASK-BRIEF-TEMPLATE.md** (4.8 KB) — Copy for each project

### Standards
- **CODING-CONTEXT.md** (4.3 KB) — Language-specific rules

### This File
- **MANIFEST.md** — Index of everything

---

## 🎯 What This System Does

Ensures Claude Code generates production-grade code, every time:
- ✅ 100% type hints (no `any` types)
- ✅ ≥80% test coverage (all tests pass)
- ✅ Zero TODOs (complete, shipping code)
- ✅ Zero linting warnings
- ✅ Zero type errors
- ✅ Working setup script
- ✅ Accurate README with examples
- ✅ Git-ready (commit immediately)

**No iteration needed. One-shot, ship-ready.**

---

## 🚀 How to Use

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
6. Verify: make test && make lint && git status
7. Ship
```

### Complete (30 minutes)
```
1. Read START-HERE.md (2 min)
2. Read CLAUDE-CODE-GUIDE.md (15 min)
3. Read CLAUDE-CODE-CHEATSHEET.md (5 min)
4. Copy TASK-BRIEF-TEMPLATE.md
5. Fill with project details
6. Attach & spawn Claude Code
7. Verify & ship
```

---

## 📖 File Guide

| File | Purpose | Size | Read When |
|---|---|---|---|
| **START-HERE.md** | Entry point, quick overview | 4.6 KB | **First** |
| **README.md** | System overview, file guide | 9.2 KB | Planning |
| **CLAUDE-CODE-GUIDE.md** | Complete walkthrough, examples | 12.1 KB | First project |
| **CLAUDE-CODE-CONTRACT.md** | Quality standards, requirements | 12.3 KB | Reviewing code |
| **CLAUDE-CODE-CHEATSHEET.md** | Quick reference, templates | 11.2 KB | During project |
| **PROMPT-CLAUDE-CODE-MASTER.md** | Master prompt for Claude Code | 9 KB | Customizing |
| **TASK-BRIEF-TEMPLATE.md** | Copy for each project | 4.8 KB | Starting project |
| **CODING-CONTEXT.md** | Language-specific rules | 4.3 KB | Customizing |
| **MANIFEST.md** | This file, index | — | Navigation |

---

## 🎓 Reading Paths

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

## ✅ Quality Standards (Non-Negotiable)

Every Claude Code project must deliver:

```
☑ Type Hints: 100% on public APIs
☑ Tests: ≥80% coverage, all pass, no skips
☑ Code: Zero TODOs, zero stubs
☑ Linting: Zero warnings, zero errors
☑ Types: Type checking passes (mypy/tsc)
☑ Setup: Works first time (setup.sh && make test)
☑ Docs: README accurate, examples tested
☑ Errors: Explicit handling, never silent failures
☑ Config: .env.example complete, no secrets
☑ Git: Ready to commit immediately
```

**Code violating any standard is rejected until fixed.**

---

## 🛠️ How to Apply

### To a Web API Project
```bash
# 1. Fill TASK-BRIEF.md with:
#    - Framework: Express / FastAPI / Fastify
#    - Endpoints: /api/users, /api/posts
#    - Authentication: JWT / OAuth
#    - Database: PostgreSQL / SQLite

# 2. Attach contracts & spawn Claude Code

# 3. Get back:
#    - Express/FastAPI server
#    - Models & schemas
#    - Error handling
#    - Tests (unit + integration)
#    - Setup script & Makefile
#    - Working README
```

### To a CLI Tool
```bash
# 1. Fill TASK-BRIEF.md with:
#    - Language: Python / TypeScript / Go
#    - Commands: triage, report, sync
#    - Input/Output format
#    - Error handling

# 2. Attach & spawn

# 3. Get back:
#    - Full CLI implementation
#    - Argument parsing
#    - Error handling
#    - Tests
#    - Setup script
```

### To a Library
```bash
# 1. Fill TASK-BRIEF.md with:
#    - Core functions
#    - Data structures
#    - Edge cases to handle
#    - Public API

# 2. Attach & spawn

# 3. Get back:
#    - Library code (100% typed)
#    - Tests
#    - API documentation
#    - Examples
```

---

## 🚫 What Gets Rejected

Code that has:
- TODOs or incomplete sections
- Missing type hints (even one function)
- Test coverage < 80%
- Failing or skipped tests
- Linting warnings
- Type checking errors
- Broken setup script
- Non-working README examples
- Secrets in code

**Request specific fix and rerun.**

---

## 🎁 Package Contents

```
claude-code-contracts/
├── README.md                          Main overview
├── START-HERE.md                      Quick start
├── MANIFEST.md                        This file
│
├── CLAUDE-CODE-CONTRACT.md            Standards (12.3 KB)
├── CLAUDE-CODE-GUIDE.md               How-to (12.1 KB)
├── CLAUDE-CODE-CHEATSHEET.md          Quick ref (11.2 KB)
├── TASK-BRIEF-TEMPLATE.md             Template (4.8 KB)
├── CODING-CONTEXT.md                  Language rules (4.3 KB)
│
└── prompts/
    └── PROMPT-CLAUDE-CODE-MASTER.md   Master prompt (9 KB)

Total: ~60 KB, self-contained, no dependencies
```

---

## 💡 Key Principles

1. **One-shot, no iteration** — Code ships on first generation
2. **Quality non-negotiable** — Standards are enforced, not suggested
3. **Clear contracts** — No ambiguity about what's expected
4. **Portable** — Works with any Claude Code interface
5. **Customizable** — Adapt for your needs
6. **Complete** — Everything included (tests, docs, setup)

---

## 🤝 Compatible With

- Claude.ai web interface
- Copilot IDE extensions
- Claude API
- Any LLM accepting Claude as model
- OpenAI APIs (same contract system)

**Just attach files and use the prompts provided.**

---

## 📊 System Overview

```
Your Project Requirements
        ↓
Fill TASK-BRIEF.md
        ↓
Attach Contract Files to Claude Code
        ↓
Claude Code Reads Contract
        ↓
Generates Production Code:
  ✓ 100% type hints
  ✓ 80%+ tests
  ✓ Zero TODOs
  ✓ Working setup
        ↓
You Verify (make test, make lint)
        ↓
Ship (git commit immediately)
```

---

## ✨ What Makes This Different

### Before (No Contract)
- Claude Code generates incomplete code
- Missing type hints, tests, documentation
- Requires iteration to fix
- Manual quality checks
- Unpredictable quality

### After (With Contract)
- Claude Code reads contract upfront
- Knows exact requirements
- Generates complete code
- All standards met automatically
- Predictable quality
- No iteration needed

---

## 🚀 Getting Started Right Now

**Option 1: This Minute (5 min)**
```bash
1. Open START-HERE.md
2. Follow 5-step quick start
3. Done
```

**Option 2: Properly (30 min)**
```bash
1. Read README.md
2. Read CLAUDE-CODE-GUIDE.md
3. Read CLAUDE-CODE-CHEATSHEET.md
4. Create your first project
```

**Option 3: Reference Needed Later**
```bash
1. Bookmark CLAUDE-CODE-CHEATSHEET.md
2. Bookmark CODING-CONTEXT.md
3. Keep TASK-BRIEF-TEMPLATE.md handy
```

---

## 📞 Support

### "How do I use this?"
→ Read START-HERE.md

### "What are the quality standards?"
→ Read CLAUDE-CODE-CONTRACT.md

### "Can I customize it?"
→ Edit CODING-CONTEXT.md

### "What language does it support?"
→ Check CODING-CONTEXT.md (Python/TypeScript/Go, more available)

### "How do I reject bad code?"
→ See CLAUDE-CODE-GUIDE.md section on rejection protocol

---

## 📋 Quick Checklist

Before using with Claude Code:
- [ ] Read START-HERE.md
- [ ] Have all 8 files in this folder
- [ ] Understand the quality standards
- [ ] Know how to fill TASK-BRIEF.md
- [ ] Know how to attach files to Claude Code
- [ ] Understand rejection protocol

Before shipping generated code:
- [ ] `make test` passes with ≥80% coverage
- [ ] `make lint` shows zero warnings
- [ ] `git status` shows ready to commit
- [ ] README examples actually work
- [ ] ./setup.sh worked on fresh machine
- [ ] No TODOs in code
- [ ] No type hints missing
- [ ] All tests passing (no skips)

---

## 🎯 Success Metrics

**You've won if:**
- Generated code passes all checks first try
- No iteration needed
- Team is happy with code quality
- Can ship immediately without changes
- Zero technical debt from generation

---

## 📝 File Dependencies

```
START-HERE.md ← Read this first
        ↓
CLAUDE-CODE-GUIDE.md ← Complete walkthrough
        ↓
All other files reference CLAUDE-CODE-CONTRACT.md standards
        ↓
TASK-BRIEF-TEMPLATE.md ← Copy for your project
        ↓
PROMPT-CLAUDE-CODE-MASTER.md ← Attach with contract
        ↓
CODING-CONTEXT.md ← Customize as needed
```

---

## 🏁 Final Notes

- **No external dependencies** — All files are self-contained
- **No licensing restrictions** — Use freely for any project
- **Open for modification** — Customize for your needs
- **Proven system** — Used in production for multiple projects
- **Always improving** — Update CODING-CONTEXT.md as you learn

---

## 🎓 Examples Included

- Weather API (TypeScript/Express)
- GitHub Issue Triage (Python/FastAPI)
- Slack Bot (Python/Flask)
- CLI tools, libraries, scripts

See CLAUDE-CODE-GUIDE.md and CLAUDE-CODE-CHEATSHEET.md for examples.

---

**Ready to build?** Start with **START-HERE.md** →
