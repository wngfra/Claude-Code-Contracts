# Claude Code Guide — How to Use the Production Contract

**Standalone guide for generating production-grade code with Claude Code.**

Works independently. Use with any Claude Code interface.

---

## Quick Start (TL;DR)

1. **Fill `TASK-BRIEF-TEMPLATE.md`** — Be specific about what you want
2. **Attach these files to your Claude Code session:**
   - `CLAUDE-CODE-CONTRACT.md` — Quality standards
   - `CODING-CONTEXT.md` — Language rules
   - `TASK-BRIEF.md` — Your filled template
   - `prompts/PROMPT-CLAUDE-CODE-MASTER.md` — Master prompt
3. **Prompt:** "Generate production code following CLAUDE-CODE-CONTRACT.md"
4. **Claude Code generates** — Full project, no iteration needed
5. **Verify:** `./setup.sh && make test && make lint && make bench`
6. **Ship it.**

---

## The Contract System

### Four Files

| File | Purpose | When |
|---|---|---|
| **CLAUDE-CODE-CONTRACT.md** | Quality standards (100% type hints, 80%+ tests, benchmarks, security) | Every session |
| **PROMPT-CLAUDE-CODE-MASTER.md** | The master prompt (detailed generation instructions) | Claude Code session |
| **TASK-BRIEF-TEMPLATE.md** | What you're building (fill before spawning) | Fill before spawning |
| **CODING-CONTEXT.md** | Language-specific rules and patterns | Every session |

### The Flow

```
You fill TASK-BRIEF.md
        ↓
Attach all 4 files to Claude Code
        ↓
Claude Code reads CONTRACT and generates code
        ↓
Code meets 100% of standards in CONTRACT
        ↓
You verify: ./setup.sh && make test && make lint && make bench
        ↓
Ship immediately (no iteration)
```

---

## Step 1: Fill Out TASK-BRIEF.md

Copy `TASK-BRIEF-TEMPLATE.md` to your project directory and fill it in.

**Example: GitHub Issue Triage System**

```markdown
# TASK-BRIEF: GitHub Issue Triage AI

## Project
Name: github-issue-triage
Language: Python
Framework: FastAPI
Type: API + CLI
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

## Performance Budget
Latency (p50): <200ms per issue classification
Latency (p99): <1s per issue classification
Throughput: 100 issues in <60s (parallel processing)
Memory: <512MB RSS under load

Hot paths:
- Claude API call (network-bound, needs retry)
- Issue parsing (CPU-bound)

## Security
Input validation: All API params validated with Pydantic
Auth: API key in X-API-Key header
Secrets: All in .env
Dependencies: pip audit clean

## Constraints
- No external databases (SQLite OK)
- Use Claude API (not other LLMs)
- Runnable on M1 Mac and Ubuntu 22.04
- Python 3.11+

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
- [ ] Tests: >=80% coverage, all pass
- [ ] Benchmarks: Claude API call latency benchmarked
- [ ] Setup works: `setup.sh && make test` ← first try
- [ ] README has all required sections with working examples
- [ ] CHANGELOG.md present with all changes documented
- [ ] README and CHANGELOG synced with actual code
- [ ] No console warnings/errors
- [ ] Type hints: 100% on public APIs
- [ ] Security: pip audit clean

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

Attach the 4 files and prompt:

```
Generate production-grade GitHub issue triage system per TASK-BRIEF.md, 
following CLAUDE-CODE-CONTRACT.md completely.

Must include:
- Source code (src/) with 100% type hints
- Tests (tests/) with >=80% coverage
- Benchmarks (benches/) for Claude API calls
- CI config (.github/workflows/ci.yml)
- Setup script & Makefile
- README with all required sections and working examples
- CHANGELOG.md with all changes documented
- .env.example
- Ready to git commit
```

---

## Step 3: Claude Code Generates

Claude Code reads the contract and generates:

- Complete source code (no TODOs)
- Full test suite (≥80% coverage, all pass)
- Benchmarks for hot paths
- Type hints (100% on public APIs)
- CI/CD configuration
- Documentation (full README per spec + CHANGELOG)
- README and CHANGELOG synced with code
- Configuration (`.env.example`)
- Build tools (Makefile, setup script)
- Git ready (`.gitignore`, first commit)

---

## Step 4: Verify & Ship

### Quick Checklist

```bash
cd project/

# 1. Install
./setup.sh

# 2. Test (should pass + show ≥80% coverage)
make test

# 3. Lint (should have zero output = success)
make lint

# 4. Benchmarks (should compile and run)
make bench

# 5. Security audit
pip audit   # or npm audit / cargo audit

# 6. Type check (language-specific)
mypy src/          # Python
tsc --noEmit       # TypeScript
cargo clippy       # Rust
go vet ./...       # Go

# 7. Run (should start without errors)
make run

# 8. Git status (should show clean)
git status
```

If all pass → **Ship immediately. No further iteration needed.**

---

## Example: Full Workflow

### 1. Create TASK-BRIEF

```bash
cat > weather-api/TASK-BRIEF.md << 'EOF'
# TASK-BRIEF: Weather API

## Project
Name: weather-api
Language: Rust
Framework: Axum
Type: API Server
Duration: 4h

## Problem Statement
Build a REST API that fetches weather data from wttr.in, caches results, 
and provides endpoints for current weather, 7-day forecast, and alerts.

## Functional Requirements

**Must have:**
- [ ] GET /weather?city=London → returns current weather
- [ ] GET /forecast?city=London&days=7 → returns 7-day forecast
- [ ] Cache results for 30 minutes (in-memory)
- [ ] Handle invalid city names (return 400)
- [ ] Handle wttr.in being down (return 503 with cached data if available)

**Nice to have:**
- [ ] Rate limiting per IP
- [ ] Health check endpoint

## Performance Budget
Latency (p50): <10ms for cache hits
Latency (p99): <500ms for cache misses (upstream API)
Memory: <64MB RSS
Startup: <100ms

Hot paths:
- Cache lookup (every request)
- JSON deserialization of upstream response

## Constraints
- Rust 1.75+
- No external databases required
- wttr.in only (free tier)

## Success Criteria
- [ ] All endpoints working
- [ ] Tests: >=80% coverage
- [ ] Benchmarks: cache lookup and JSON parsing
- [ ] Setup: `cargo test` ← first try
- [ ] README with working curl examples
EOF
```

### 2. Generate Code

Attach files and prompt Claude Code to generate the project.

### 3. Verify

```bash
cd weather-api
cargo test --all-targets
cargo clippy -- -D warnings
cargo bench
cargo run &
curl http://localhost:3000/weather?city=London
```

### 4. Done

```bash
git add . && git commit -m "feat: Initial weather API implementation"
git log --oneline
# commit abc1234: feat: Initial weather API implementation
```

---

## When Claude Code Falls Short

If the code doesn't meet standards:

### Bad (Don't Accept)
```
"I'll generate the core API, you add tests later"
"Here's 80% of the code, complete the TODOs"
"Tests are optional; focus on features"
"TypeScript strict mode breaks things; use `any`"
"Benchmarks aren't needed for this project" (when hot paths exist)
```

### Good (Request This)
```
"Regenerate src/api.rs with 100% type hints and explicit error handling"
"Add integration tests for all 3 endpoints with >=80% coverage"
"Add criterion benchmarks for cache_lookup() and parse_response()"
"Ensure cargo clippy -- -D warnings passes before returning"
"Add .github/workflows/ci.yml with test + lint + bench steps"
```

### Rejection Protocol

1. **Identify the issue:** "Coverage is 65%, contract requires 80%"
2. **Request specific fix:** "Add integration tests for error conditions in tests/integration/"
3. **Rerun gate:** "Run `cargo test --all-targets` before returning"

**Do NOT accept partial code.** Quality is non-negotiable.

---

## Contract Violations (Auto-Reject)

Any of these = reject the code and re-request:

| Violation | Evidence | Action |
|---|---|---|
| Missing type hints | Linter shows errors | Re-request with strict enforcement |
| Test failures | Test command shows failures | Re-request with passing tests |
| Coverage <80% | Coverage report shows low % | Re-request with additional tests |
| No benchmarks | `benches/` empty or missing | Re-request with benchmarks |
| Setup broken | `setup.sh` fails | Re-request with working setup |
| README outdated | Examples don't work | Re-request with verified examples |
| README incomplete | Missing required sections | Re-request with all sections per spec |
| Missing CHANGELOG | No CHANGELOG.md | Re-request with CHANGELOG in Keep a Changelog format |
| Docs out of sync | README doesn't match code | Re-request with doc sync |
| Lint warnings | Linter shows warnings | Re-request with clean lint |
| Secrets in code | API keys in source files | Re-request with .env pattern |
| No CI config | `.github/workflows/` missing | Re-request with CI configuration |
| Unsafe without justification | `unsafe` blocks without SAFETY comment | Re-request safe version or documented unsafe |

---

## Customization

### For Your Own Standards

Add project-specific rules to `CODING-CONTEXT.md`:

```markdown
## Project-Specific Standards

- Error codes: Use HTTP status codes + custom codes (ERR_RATE_LIMITED, ERR_INVALID_CITY)
- Logging: Always include request_id in logs for tracing
- Async: Use async/await, never callbacks
- Testing: Include smoke tests for external API calls
```

### For Different Languages

The contract works for Python, TypeScript, Rust, Go, C/C++. Adjust:
1. `CODING-CONTEXT.md` — language-specific rules
2. `TASK-BRIEF.md` — language field + framework
3. `PROMPT-CLAUDE-CODE-MASTER.md` — examples in that language

---

## Final Notes

### Quality Is Not Negotiable

This contract exists because "I'll iterate later" projects **never** do. Build right the first time.

### One-Shot Generation

If you're following the contract, you should **never** need to iterate for quality issues. If you do, something went wrong in TASK-BRIEF or the contract wasn't clear.

### Use This for Everything

Whether it's a 2-hour CLI tool or a 40-hour platform, the contract applies the same. Scale the TASK-BRIEF, not the standards.

### Measure Success

```
Success = Code ships without changes + team doesn't complain about quality + benchmarks show expected performance
```

Not "it compiles" or "tests pass". Real success.

---

## Questions?

Refer back to:
- **CLAUDE-CODE-CONTRACT.md** — Standards & requirements (authoritative)
- **PROMPT-CLAUDE-CODE-MASTER.md** — Prompt structure
- **TASK-BRIEF-TEMPLATE.md** — How to write requirements
- **CODING-CONTEXT.md** — Language-specific rules

**If it's not in one of those, it's optional.**
