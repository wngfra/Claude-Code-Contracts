# TASK-BRIEF Template — For Claude Code Sessions

**Fill this out before spawning Claude Code. Be specific, be complete.**

---

## Project

**Name:** [Project Name]
**Language:** [Python / TypeScript / Rust / Go / C++ / Swift]
**Framework:** [FastAPI / Express / Axum / Gin / SwiftUI / UIKit / None / Other]
**Type:** [Library / CLI / API Server / Microservice / iOS App / macOS App / Embedded / Other]
**Duration Estimate:** [2h / 4h / 8h / Full day]
**Mode:** [Single-agent / Orchestrate]   ← use Orchestrate for large multi-phase projects (see `MULTI-AGENT-FRAMEWORK.md`)
**max_review_iterations:** 3   ← Orchestrate mode only. Per-phase cap on the Reviewer→Fixer loop. Distinct from the contract's validation-retry cap. Raise only if you understand the cost.

---

## Problem Statement

**What are we building?**

[2-3 sentences explaining the core problem and solution]

Example:
> We're building a GitHub issue triage system that uses Claude AI to automatically categorize issues, extract key details, and suggest labels. Users run it via CLI or API.

---

## Requirements

### Functional

**Must have:**
- [ ] Requirement 1
- [ ] Requirement 2
- [ ] Requirement 3

**Nice to have:**
- [ ] Optional feature A
- [ ] Optional feature B

Example:
```
**Must have:**
- [ ] Read issues from GitHub API
- [ ] Classify by priority (critical/major/minor)
- [ ] Extract stakeholders from issue body
- [ ] Generate summary in <100 words
- [ ] Output as JSON or CLI formatted text

**Nice to have:**
- [ ] Batch processing (100+ issues at once)
- [ ] Custom classification rules
- [ ] Webhook integration
```

### Non-Functional

- **Performance:** [e.g., "Process 100 issues in <30s", "p99 latency <100ms"]
- **Reliability:** [e.g., "Retry on API failure, backoff strategy"]
- **Scale:** [e.g., "Support 10k+ issues without memory issues"]
- **Logging:** [e.g., "Debug logs for every API call"]

---

## Performance Budget

**Define measurable targets. Leave blank if not performance-critical.**

| Metric | Target | How to Measure |
|---|---|---|
| **Latency (p50)** | [e.g., <50ms] | [e.g., HTTP handler timing] |
| **Latency (p99)** | [e.g., <200ms] | [e.g., percentile tracking] |
| **Throughput** | [e.g., 1000 req/s] | [e.g., wrk/bombardier] |
| **Memory (peak)** | [e.g., <256MB] | [e.g., heaptrack / RSS] |
| **Startup time** | [e.g., <500ms] | [e.g., time to first request] |
| **Binary size** | [e.g., <10MB] | [e.g., `ls -lh`] |

**Hot paths to benchmark:**
- [ ] [Function/endpoint that needs benchmarking]
- [ ] [Another hot path]

Example:
```
Latency (p50): <20ms for GET /weather
Latency (p99): <100ms for GET /weather
Throughput: 500 req/s sustained
Memory: <128MB RSS under load
Startup: <200ms

Hot paths:
- Cache lookup (called on every request)
- API response parsing (CPU-bound)
```

---

## Security Requirements

**Leave defaults if not security-critical.**

- **Input validation:** [e.g., "Validate all API params", "Sanitize HTML in user content"]
- **Auth:** [e.g., "JWT bearer tokens", "API key in header", "None (internal tool)"]
- **Secrets:** [e.g., "All in .env", "Use AWS Secrets Manager"]
- **Network:** [e.g., "TLS only", "CORS configured for frontend domain"]
- **Dependencies:** [e.g., "Zero known CVEs", "Audit before ship"]
- **Data:** [e.g., "No PII in logs", "Encrypt at rest"]

Example:
```
Input validation: All API params validated with schema (Zod/Pydantic)
Auth: API key in X-API-Key header
Secrets: All in .env, .env.example included
Network: TLS required in production
Dependencies: npm audit clean
Data: No secrets in logs, mask sensitive fields
```

---

## Concurrency Model

**How should this handle concurrent work? Leave blank if single-threaded.**

- **Model:** [e.g., "async/await", "thread pool", "goroutines", "tokio tasks"]
- **Max concurrency:** [e.g., "10 concurrent requests", "unbounded"]
- **Graceful shutdown:** [Yes/No — e.g., "Drain in-flight requests on SIGTERM"]
- **Shared state:** [e.g., "None", "Read-only cache", "Mutex-protected counter"]

Example:
```
Model: async/await with bounded semaphore
Max concurrency: 50 concurrent API calls
Graceful shutdown: Yes, drain on SIGINT/SIGTERM
Shared state: In-memory cache with TTL (thread-safe)
```

---

## UI / Visual Requirements (Skip if no UI)

**Leave blank for CLI tools, libraries, API servers, and embedded systems.**

### Design Reference
- **Design spec / mockup:** [Figma URL, screenshot path, or "agent decides"]
- **Design system:** [Tailwind, Material UI, shadcn/ui, custom, none]
- **Theme:** [Light only / Dark only / Both / System preference]
- **Responsive breakpoints:** [e.g., "320px mobile, 768px tablet, 1024px desktop, 1440px wide"]

### Visual Acceptance Criteria
- [ ] [e.g., "Homepage matches Figma mockup within 5% pixel diff"]
- [ ] [e.g., "Navigation bar sticky on scroll"]
- [ ] [e.g., "Form validation errors shown inline in red"]
- [ ] [e.g., "Loading skeleton shown during API calls"]

### Accessibility Requirements
- **WCAG level:** [AA (default) / AAA]
- **Screen reader tested:** [Yes / No / Not required]
- **Keyboard-only navigation:** [Required (default for web) / Not required]
- **Color contrast:** [≥4.5:1 normal text, ≥3:1 large text (WCAG AA default)]
- **Reduced motion:** [Respect `prefers-reduced-motion` (default) / Not applicable]

Example:
```
Design spec: Figma link or "clean minimal design, agent decides"
Design system: Tailwind + shadcn/ui
Theme: Both (dark/light), system preference default
Breakpoints: 375px, 768px, 1280px

Visual criteria:
- [ ] Dashboard renders data table with sortable columns
- [ ] Chart animates on load, respects reduced-motion
- [ ] Empty state shows illustration + CTA button

Accessibility:
- WCAG AA
- Keyboard navigation required
- Screen reader: tested with VoiceOver
```

---

## Constraints

- **Budget:** [Cost limit for API calls, compute, etc.]
- **Dependencies:** [No external services / Use only stdlib / Must use X library]
- **Compatibility:** [Python 3.11+, Node 18+, Rust 1.75+, Go 1.22+, C++20, Swift 6.0+ / iOS 17+]
- **Deployment:** [Local CLI / Docker / Serverless / API server / Bare metal]
- **Platforms:** [Linux, macOS, Windows, ARM, embedded]

Example:
```
- No external databases (SQLite OK, PostgreSQL no)
- Must use Claude API (not other LLMs)
- No GitHub Premium features (free tier only)
- Runnable on M1 Mac and Ubuntu 22.04
- Python 3.11+
```

---

## User Experience

### Input
**How does a user interact with this?**

Example:
```
# CLI usage
$ issue-triage --repo owner/repo --token gh_xxx

# API usage
POST /api/triage
{
  "github_url": "https://github.com/owner/repo/issues/123",
  "ai_model": "claude-opus"
}
```

### Output
**What does success look like?**

Example:
```
{
  "issue_id": 123,
  "title": "Login fails with special characters",
  "priority": "major",
  "category": "bug",
  "stakeholders": ["@alice", "@bob"],
  "summary": "Users with @ or & in password cannot login. Affects 5% of users.",
  "suggested_labels": ["bug", "authentication", "urgent"],
  "effort_estimate": "2h"
}
```

---

## Architecture Hints (Optional)

**If you have strong opinions, specify them. If not, Claude Code decides.**

Example:
```
Preferred architecture:
- CLI: Python click framework
- Core logic: Pure functions, no state
- API: FastAPI with pydantic models
- Testing: pytest with fixtures
- Error handling: Custom exception classes
```

---

## Example Code or Reference

**If you have existing code or libraries to work with:**

```python
# Use Claude AI like this:
from anthropic import Anthropic

client = Anthropic()
response = client.messages.create(
    model="claude-opus-4-1-20250805",
    max_tokens=1024,
    messages=[...],
)
```

---

## Success Criteria

**The code is done when (check items applicable to your mode — see contract § Mandatory Deliverables):**

- [ ] All functional requirements working
- [ ] Tests pass with ≥80% coverage on changed files
- [ ] Benchmarks exist for hot paths (Generate/Full Rewrite mode)
- [ ] Setup script works (Generate/Full Rewrite mode)
- [ ] README has all required sections with working examples (Generate mode; other modes: affected sections updated)
- [ ] CHANGELOG.md present with changes documented (behavior-changing modes)
- [ ] README and CHANGELOG synced with actual code
- [ ] No console errors or warnings
- [ ] Code is readable and well-documented
- [ ] Error handling is explicit (no silent failures)
- [ ] Security audit clean (Generate/Full Rewrite mode)
- [ ] CI workflow included (Generate/Full Rewrite mode)
- [ ] Visual regression tests pass (UI projects)
- [ ] Accessibility audit passes — Lighthouse ≥90, zero critical violations (UI projects)
- [ ] Responsive at all specified breakpoints (UI projects)
- [ ] Keyboard navigation works for all interactive elements (UI projects)

---

## Known Unknowns

**What might come up?**

Example:
```
- Q: How do we handle rate limits from GitHub API?
- A: Exponential backoff, max 3 retries, then error
  
- Q: What if Claude API is down?
- A: Return cached result if available, otherwise error
  
- Q: Should we support private repos?
- A: Yes, if user provides valid token
```

---

## Documentation Requirements

**README and CHANGELOG are mandatory deliverables. Customize expectations here.**

### README Sections (all required by default)
- [ ] Overview, Quick Start, Installation
- [ ] Usage with working examples
- [ ] Architecture diagram
- [ ] Configuration table (env vars)
- [ ] Testing, Performance, Deployment
- [ ] Troubleshooting, Known Limitations

**Additional README notes:**
```
[e.g., "Include Swagger link for API docs", "Add Docker Compose example"]
```

### CHANGELOG
- [ ] Keep a Changelog format (default)
- [ ] Initial entries under `[Unreleased]`

---

## File Delivery

**Where should output go?**

```
./[project-name]/
├── src/
├── tests/
├── benches/
├── .github/workflows/
├── README.md
├── CHANGELOG.md
└── setup.sh
```

**Format:**
- [ ] Individual files (default)
- [ ] Git repo (init + commit)

---

## Timeline

**When do you need this?**

- [ ] ASAP (urgent)
- [ ] This week
- [ ] Next week
- [ ] Whenever

---

## Notes

[Any additional context, preferences, or gotchas]

Example:
```
- This will eventually be open-sourced, so clean code matters
- The team uses VSCode + Copilot, so type hints are critical
- We'll run this on GitHub Actions, so it needs to be deterministic
```

---

## Ready to Start?

Copy this template, fill it out completely, and pass it to Claude Code with:

```
system_prompt: "Generate code following CLAUDE-CODE-CONTRACT.md"
files: [CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, TASK-BRIEF.md, prompts/PROMPT-CLAUDE-CODE-MASTER.md]
```

**Don't start until TASK-BRIEF is complete.**
