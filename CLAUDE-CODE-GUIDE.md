# Claude Code Guide — How to Use the Production Contract

**Standalone guide for generating production-grade code with Claude Code.**

Works independently. No OpenClaw required. Use with any Claude Code interface.

---

## Quick Start (TL;DR)

1. **Fill `TASK-BRIEF.md`** — Be specific about what you want
2. **Spawn Claude Code** with this context:
   ```
   runtime: "acp"
   agentId: "claude-code" (or your configured default)
   task: "[Your TASK-BRIEF content]"
   attachments: [CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, TASK-BRIEF.md]
   ```
3. **Claude Code generates** — Full project, no iteration needed
4. **Review deliverables** — Tests pass? Linting clean? Ship it.

---

## The Contract System

### Three Parts

| File | Purpose | When |
|---|---|---|
| **CLAUDE-CODE-CONTRACT.md** | Quality standards (100% type hints, 80%+ tests, etc.) | Every session |
| **PROMPT-CLAUDE-CODE-MASTER.md** | The master prompt (copy its structure) | Claude Code session |
| **TASK-BRIEF-TEMPLATE.md** | What you're building | Fill before spawning |

### The Flow

```
You fill TASK-BRIEF.md
        ↓
Attach all 3 files to Claude Code
        ↓
Claude Code reads CONTRACT and generates code
        ↓
Code meets 100% of standards in CONTRACT
        ↓
Ship immediately (no iteration)
```

---

## Step 1: Fill Out TASK-BRIEF.md

Save to: `/Users/alex/.openclaw/workspace/projects/[project-name]/TASK-BRIEF.md`

**Example: GitHub Issue Triage System**

```markdown
# TASK-BRIEF: GitHub Issue Triage AI

## Project
Name: github-issue-triage
Language: Python
Framework: FastAPI
Duration: 4h

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
- [ ] Webhook for new issues

## Non-Functional
- Performance: Process 100 issues in <60s
- Reliability: Retry on transient API failures (max 3 attempts)
- Logging: DEBUG level for API calls, ERROR for failures

## Constraints
- No external databases (SQLite OK)
- Use Claude API (not other LLMs)
- Runnable on M1 Mac and Ubuntu 22.04
- Python 3.10+

## Input/Output

### Input (CLI)
```bash
$ issue-triage --repo owner/repo --github-token $GITHUB_TOKEN --model opus
```

### Output
```json
{
  "issue_id": 123,
  "title": "Login fails with special characters",
  "priority": "major",
  "category": "bug",
  "stakeholders": ["@alice", "@bob"],
  "summary": "Users with @ or & in password cannot login. Critical auth bug.",
  "labels": ["bug", "auth", "urgent"],
  "effort": "4h"
}
```

## Success Criteria
- [ ] All functional requirements working
- [ ] Tests: ≥80% coverage, all pass
- [ ] Setup works: `setup.sh && make test` ← first try
- [ ] README has working examples
- [ ] No console warnings/errors
- [ ] Type hints: 100% on public APIs

## Known Unknowns
- Q: Rate limits from GitHub?
  - A: Exponential backoff, max 3 retries
- Q: What if Claude API is down?
  - A: Return cached result or error
- Q: Private repos?
  - A: Yes, if user provides GitHub token

## Notes
This will be open-sourced, so code quality is critical.
Type hints required (Copilot compatibility).
```

---

## Step 2: Spawn Claude Code Session

Use this command (adjust paths/agentId as needed):

```bash
openclaw acp --session agent:main:claude-code << 'EOF'
{
  "action": "spawn",
  "runtime": "acp",
  "agentId": "claude-code",
  "mode": "session",
  "task": "Generate production-grade GitHub issue triage system per TASK-BRIEF.md",
  "attachments": [
    {
      "name": "CLAUDE-CODE-CONTRACT.md",
      "mimeType": "text/markdown",
      "path": "/Users/alex/.openclaw/workspace/openclaw-configs/contracts/CLAUDE-CODE-CONTRACT.md"
    },
    {
      "name": "CODING-CONTEXT.md",
      "mimeType": "text/markdown",
      "path": "/Users/alex/.openclaw/workspace/openclaw-configs/contracts/CODING-CONTEXT.md"
    },
    {
      "name": "TASK-BRIEF.md",
      "mimeType": "text/markdown",
      "path": "/Users/alex/.openclaw/workspace/projects/[project-name]/TASK-BRIEF.md"
    },
    {
      "name": "PROMPT-CLAUDE-CODE-MASTER.md",
      "mimeType": "text/markdown",
      "path": "/Users/alex/.openclaw/workspace/openclaw-configs/contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md"
    }
  ]
}
EOF
```

**Or simpler:** Use `sessions_spawn` in your agent code:

```python
sessions_spawn(
    runtime="acp",
    agentId="claude-code",
    mode="session",
    task="Generate production-grade code per TASK-BRIEF.md and CLAUDE-CODE-CONTRACT.md",
    attachments=[
        {
            "name": "CLAUDE-CODE-CONTRACT.md",
            "mimeType": "text/markdown",
            "content": read("openclaw-configs/contracts/CLAUDE-CODE-CONTRACT.md")
        },
        {
            "name": "CODING-CONTEXT.md",
            "mimeType": "text/markdown",
            "content": read("openclaw-configs/contracts/CODING-CONTEXT.md")
        },
        {
            "name": "TASK-BRIEF.md",
            "mimeType": "text/markdown",
            "content": read(f"projects/{project_name}/TASK-BRIEF.md")
        },
        {
            "name": "PROMPT-CLAUDE-CODE-MASTER.md",
            "mimeType": "text/markdown",
            "content": read("openclaw-configs/contracts/prompts/PROMPT-CLAUDE-CODE-MASTER.md")
        }
    ]
)
```

---

## Step 3: Claude Code Generates

Claude Code reads the contract and generates:

✅ Complete source code (no TODOs)
✅ Full test suite (≥80% coverage, all pass)
✅ Type hints (100% on public APIs)
✅ Documentation (README with examples)
✅ Configuration (`.env.example`)
✅ Build tools (Makefile, setup script)
✅ Git ready (`.gitignore`, first commit)

**Total deliverable:** ~30-50 files depending on project size

---

## Step 4: Verify & Ship

### Quick Checklist

```bash
cd /Users/alex/.openclaw/workspace/projects/[project-name]

# 1. Install
./setup.sh

# 2. Test (should pass + show ≥80% coverage)
make test

# 3. Lint (should have zero output = success)
make lint

# 4. Type check (if Python)
mypy src/

# 5. Run (should start without errors)
make run

# 6. Git status (should show clean)
git status
```

If all pass → **Ship immediately. No further iteration needed.**

---

## Example: Full Workflow

### 1. Create TASK-BRIEF

```bash
cat > /Users/alex/.openclaw/workspace/projects/weather-api/TASK-BRIEF.md << 'EOF'
# TASK-BRIEF: Weather API

## Project
Name: weather-api
Language: TypeScript
Framework: Express
Duration: 3h

## Problem Statement
Build a REST API that fetches weather data from wttr.in, caches results, 
and provides endpoints for current weather, 7-day forecast, and alerts.

## Functional Requirements

**Must have:**
- [ ] GET /weather?city=London → returns current weather (temp, conditions, humidity)
- [ ] GET /forecast?city=London&days=7 → returns 7-day forecast
- [ ] Cache results for 30 minutes (in-memory)
- [ ] Handle invalid city names (return 400)
- [ ] Handle wttr.in being down (return 503 with cached data if available)

**Nice to have:**
- [ ] Persistent cache (SQLite)
- [ ] Admin endpoint to clear cache
- [ ] Structured logging

## Constraints
- Node 18+
- No external databases required
- wttr.in only (free tier)

## Success Criteria
- [ ] All endpoints working
- [ ] Tests: ≥80% coverage
- [ ] TypeScript strict mode
- [ ] Setup: `npm install && npm test` ← first try
- [ ] README with working curl examples
EOF
```

### 2. Spawn Claude Code (in your agent code)

```python
from sessions_spawn import spawn_session

response = spawn_session(
    runtime="acp",
    agentId="claude-code",
    mode="session",
    task="""Generate production-grade weather API per TASK-BRIEF.md and CLAUDE-CODE-CONTRACT.md.
    
    Requirements:
    1. TypeScript with strict mode
    2. Express server with proper error handling
    3. wttr.in API integration with retry logic
    4. In-memory caching with 30-min TTL
    5. Full test suite (unit + integration)
    6. Type hints: 100%
    7. Tests: ≥80% coverage, all pass
    8. README with examples
    9. Makefile with test/lint/run targets
    """,
    attachments=[
        read_attachment("CLAUDE-CODE-CONTRACT.md"),
        read_attachment("CODING-CONTEXT.md"),
        read_attachment("TASK-BRIEF.md"),
        read_attachment("PROMPT-CLAUDE-CODE-MASTER.md"),
    ]
)

print(response)  # Returns full project
```

### 3. Verify

```bash
cd workspace/projects/weather-api
npm install
npm test        # Should show ✓ all tests pass, 87% coverage
npm run lint    # Zero output = success
npm start       # Server running on port 3000
```

### 4. Done

```bash
git log --oneline
# commit abc1234: feat: Initial weather API implementation
```

---

## When Claude Code Falls Short

If the code doesn't meet standards:

### Bad (Don't Accept)
```
❌ "I'll generate the core API, you add tests later"
❌ "Here's 80% of the code, complete the TODOs"
❌ "Tests are optional; focus on features"
❌ "TypeScript strict mode breaks things; use `any`"
```

### Good (Request This)
```
✅ "Regenerate src/api.ts with 100% type hints and explicit error handling"
✅ "Add integration tests for all 3 endpoints (GET /weather, /forecast, /health)"
✅ "Ensure npm test passes with ≥80% coverage before returning"
```

### Rejection Protocol

1. **Identify the issue:** "Coverage is 65%, contract requires 80%"
2. **Request specific fix:** "Regenerate tests/integration/api.test.ts with additional test cases for error conditions"
3. **Rerun gate:** "Run `npm test` before returning"

**Do NOT accept partial code.** Quality is non-negotiable.

---

## Contract Violations (Auto-Reject)

Any of these = reject the code and rerequest:

| Violation | Evidence | Action |
|---|---|---|
| Missing type hints | `npm run lint` shows errors | Rerequest with strict enforcement |
| Test failures | `npm test` shows ❌ | Rerequest with passing tests |
| Coverage <80% | `npm test` shows 73% | Rerequest with additional tests |
| Setup broken | `npm install && npm test` fails | Rerequest with working setup |
| README outdated | Examples don't work | Rerequest with verified examples |
| Lint warnings | `npm run lint` shows warnings | Rerequest with clean lint |

---

## Customization

### For Your Own Standards

If you have project-specific rules, add them to `CODING-CONTEXT.md`:

```markdown
## Project-Specific Standards

- Error codes: Use HTTP status codes (200, 400, 404, 500) + custom codes (ERR_RATE_LIMITED, ERR_INVALID_CITY)
- Logging: Always include request_id in logs for tracing
- Async: Use async/await, never callbacks
- Testing: Include smoke tests for external API calls
```

### For Different Languages

The contract works for Python, TypeScript, Go, Rust, C, and C++. Adjust:
1. `CODING-CONTEXT.md` — language-specific rules
2. `PROMPT-CLAUDE-CODE-MASTER.md` — examples in that language

---

## Final Notes

### Quality Is Not Negotiable

This contract exists because "I'll iterate later" projects **never** do. Build right the first time.

### One-Shot Generation

If you're following the contract, you should **never** need to iterate. If you do, something went wrong in TASK-BRIEF or the contract wasn't clear.

### Use This for Everything

Whether it's a 2-hour CLI tool or a 40-hour platform, the contract applies the same. Scale the TASK-BRIEF, not the standards.

### Measure Success

```
Success = Code ships without changes + team doesn't complain about quality
```

Not "it compiles" or "tests pass". Real success.

---

## Questions?

Refer back to:
- **CLAUDE-CODE-CONTRACT.md** — Standards & requirements
- **PROMPT-CLAUDE-CODE-MASTER.md** — Prompt structure
- **TASK-BRIEF-TEMPLATE.md** — How to write requirements
- **CODING-CONTEXT.md** — Language-specific rules

**If it's not in one of those, it's optional.**
