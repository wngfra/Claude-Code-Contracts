# TASK-BRIEF Template — For Claude Code Sessions

**Fill this out before spawning Claude Code. Be specific, be complete.**

---

## Project

**Name:** [Project Name]
**Language:** [Python / TypeScript / Go / Other]
**Framework:** [FastAPI / Express / Flask / None / Other]
**Duration Estimate:** [2h / 4h / 8h / Full day]

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

- **Performance:** [e.g., "Process 100 issues in <30s"]
- **Reliability:** [e.g., "Retry on API failure, backoff strategy"]
- **Scale:** [e.g., "Support 10k+ issues without memory issues"]
- **Logging:** [e.g., "Debug logs for every API call"]

---

## Constraints

- **Budget:** [Cost limit for API calls, compute, etc.]
- **Dependencies:** [No external services / Use only stdlib / Must use X library]
- **Compatibility:** [Python 3.10+, Node 18+, etc.]
- **Deployment:** [Local CLI / Docker / Serverless / API server]

Example:
```
- No external databases (SQLite OK, PostgreSQL no)
- Must use Claude API (not other LLMs)
- No GitHub Premium features (free tier only)
- Runnable on M1 Mac and Ubuntu 22.04
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

**The code is done when:**

- [ ] All functional requirements working
- [ ] Tests pass with ≥80% coverage
- [ ] Setup script works (`setup.sh && make test`)
- [ ] README has working examples
- [ ] No console errors or warnings
- [ ] Code is readable and well-documented
- [ ] Error handling is explicit (no silent failures)

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

## File Delivery

**Where should output go?**

```
/Users/alex/.openclaw/workspace/projects/[project-name]/
├── src/
├── tests/
├── README.md
└── setup.sh
```

**Format:**
- [ ] Zip file
- [ ] Individual files
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
files: [CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, TASK-BRIEF.md]
```

**Don't start until TASK-BRIEF is complete.**
