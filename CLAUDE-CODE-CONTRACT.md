# Claude Code Contract — Production Quality Standards

**Purpose:** Ensure Claude Code outputs are production-ready, architecturally sound, and tastefully designed. This contract supersedes generic prompts and applies to all Claude Code sessions.

---

## 🎯 Core Principles

1. **Complete, not iterative.** Code should ship as-is after one run. Zero "run this first, then..." instructions.
2. **Taste matters.** Clean architecture, clear naming, minimal verbosity. Code that other engineers *want* to read.
3. **Tests are not optional.** Every deliverable includes unit tests + integration tests, minimum 80% coverage.
4. **Documentation lives in code.** Docstrings, type hints, and inline comments explain *why*, not *what*.
5. **No placeholder code.** Every function, every class, every module is production-grade or removed.

---

## 📋 Mandatory Deliverables

Every Claude Code session must output:

| Deliverable | Requirement | Non-Negotiable |
|---|---|---|
| **Source Code** | All files, organized by function not framework | ✅ |
| **Type Hints** | 100% coverage for public APIs | ✅ |
| **Tests** | Unit + integration, ≥80% coverage, zero skips | ✅ |
| **Setup Script** | `setup.sh` or `install.md` — one command to run locally | ✅ |
| **README** | Architecture overview, usage examples, known limits | ✅ |
| **Error Handling** | Explicit error messages, never silent failures | ✅ |
| **Logging** | Structured logging, appropriate levels (DEBUG/INFO/ERROR) | ✅ |
| **Configuration** | `.env.example` + docs, no secrets in code | ✅ |
| **Build/Lint Gate** | `Makefile` or `scripts/` with `test`, `lint`, `build` targets | ✅ |
| **Git Status** | `.gitignore` correct, ready to commit immediately | ✅ |

---

## 🏗️ Architecture Standards (Language-Agnostic)

### Structure
```
project/
├── src/               # Application code
│   ├── core/          # Core logic (independent of framework)
│   ├── api/           # API layer (FastAPI, Express, etc.)
│   ├── models/        # Data models (Pydantic, TypeScript interfaces)
│   └── utils/         # Reusable utilities
├── tests/
│   ├── unit/          # Unit tests (core logic)
│   ├── integration/   # Integration tests (API + database)
│   └── fixtures/      # Shared test data
├── scripts/           # Utility scripts (setup, deploy, etc.)
├── .env.example       # Environment template
├── README.md          # Setup + usage
├── Makefile           # `make test`, `make lint`, `make run`
└── pyproject.toml / package.json / go.mod
```

### Module Organization
- **Core logic:** Independent of framework (testable, reusable)
- **API layer:** Thin, mainly routing + validation
- **Models:** Single source of truth for data structures
- **Utils:** Pure functions, no side effects

**Anti-patterns:**
- Business logic buried in API handlers
- Model definitions scattered across files
- Utility functions with hidden state
- "Misc" folders or generic `helpers.py`

---

## 🐍 Python-Specific Standards

### Type Hints (Required)
```python
from typing import Optional, List, Dict, Callable
from dataclasses import dataclass

@dataclass
class User:
    id: str
    name: str
    email: Optional[str] = None
    metadata: Dict[str, str] = field(default_factory=dict)

def fetch_users(limit: int = 10) -> List[User]:
    """Fetch users from database.
    
    Args:
        limit: Maximum number of users to return.
    
    Returns:
        List of User objects.
    
    Raises:
        DatabaseError: If query fails.
    """
    ...

async def process_async(user: User) -> str:
    """Async handler."""
    ...
```

### Formatting
- **Line length:** 100 characters (Black default)
- **Imports:** `isort` sorted, 3 groups (std, third-party, local)
- **Naming:** `snake_case` for functions/vars, `PascalCase` for classes, `UPPER_SNAKE` for constants

### Error Handling
```python
class ApplicationError(Exception):
    """Base application error."""
    pass

class ValidationError(ApplicationError):
    """Invalid input."""
    pass

def validate_email(email: str) -> str:
    if not email or "@" not in email:
        raise ValidationError(f"Invalid email: {email}")
    return email.lower()
```

### Logging
```python
import logging

logger = logging.getLogger(__name__)

def process_user(user_id: str) -> None:
    logger.info(f"Processing user: {user_id}")
    try:
        result = fetch_user(user_id)
        logger.debug(f"Fetched user: {result}")
    except Exception as e:
        logger.error(f"Failed to process user {user_id}: {e}", exc_info=True)
        raise
```

### Testing
```python
# tests/unit/test_models.py
import pytest
from src.models import User

def test_user_creation():
    user = User(id="123", name="Alice")
    assert user.id == "123"
    assert user.email is None

def test_user_email_validation():
    with pytest.raises(ValidationError):
        User(id="123", name="Alice", email="invalid")

# tests/integration/test_api.py
@pytest.mark.asyncio
async def test_fetch_users_endpoint(client):
    response = await client.get("/api/users")
    assert response.status_code == 200
    assert len(response.json()) > 0
```

---

## 📘 TypeScript/JavaScript Standards

### Type Hints (Required)
```typescript
import { Request, Response } from 'express';

interface User {
  id: string;
  name: string;
  email?: string;
  metadata?: Record<string, string>;
}

async function fetchUsers(limit: number = 10): Promise<User[]> {
  /**
   * Fetch users from database.
   * 
   * @param limit Maximum number of users to return.
   * @returns Array of User objects.
   * @throws {DatabaseError} If query fails.
   */
  ...
}

async function processUser(user: User): Promise<string> {
  ...
}
```

### Formatting
- **Line length:** 100 characters
- **Indent:** 2 spaces (ESLint/Prettier standard)
- **Semicolons:** Enforce (Prettier default)
- **Quotes:** Double quotes (consistent, explicit)

### Error Handling
```typescript
class ApplicationError extends Error {
  constructor(public code: string, message: string) {
    super(message);
    this.name = 'ApplicationError';
  }
}

class ValidationError extends ApplicationError {
  constructor(message: string) {
    super('VALIDATION_ERROR', message);
    this.name = 'ValidationError';
  }
}

function validateEmail(email: string): string {
  if (!email || !email.includes('@')) {
    throw new ValidationError(`Invalid email: ${email}`);
  }
  return email.toLowerCase();
}
```

### Logging
```typescript
import pino from 'pino';

const logger = pino({ level: process.env.LOG_LEVEL || 'info' });

async function processUser(userId: string): Promise<void> {
  logger.info({ userId }, 'Processing user');
  try {
    const user = await fetchUser(userId);
    logger.debug({ user }, 'User fetched');
  } catch (error) {
    logger.error({ userId, error }, 'Failed to process user');
    throw error;
  }
}
```

### Testing
```typescript
// tests/unit/models.test.ts
import { describe, it, expect } from 'vitest';
import { validateEmail } from '../src/models';

describe('validateEmail', () => {
  it('should return lowercase email', () => {
    expect(validateEmail('USER@EXAMPLE.COM')).toBe('user@example.com');
  });

  it('should throw on invalid email', () => {
    expect(() => validateEmail('invalid')).toThrow('Invalid email');
  });
});

// tests/integration/api.test.ts
describe('GET /api/users', () => {
  it('should return 200 with users', async () => {
    const response = await client.get('/api/users');
    expect(response.status).toBe(200);
    expect(response.body.length).toBeGreaterThan(0);
  });
});
```

---

## 🏁 Pre-Delivery Checklist (Claude Code)

Before returning code, verify:

- [ ] **All source files complete** — no TODOs, no stubs, no `// TODO: implement X`
- [ ] **Type hints 100%** — `mypy` or `tsc` passes with no errors
- [ ] **Tests pass** — `pytest` or `vitest` runs, all pass, ≥80% coverage
- [ ] **Linting clean** — `ruff`, `eslint` pass, zero warnings
- [ ] **Setup script works** — `setup.sh` or `npm install && npm test` succeeds on clean system
- [ ] **README accurate** — examples run, installation instructions tested
- [ ] **No secrets in code** — all config in `.env.example`, no API keys/tokens
- [ ] **Git-ready** — `.gitignore` correct, `git status` shows only new files
- [ ] **Error messages clear** — users understand what went wrong and how to fix it
- [ ] **Logging added** — INFO/DEBUG/ERROR levels appropriate, no blind spots
- [ ] **Naming consistent** — no `temp_var`, no `x`, no `do_thing_2()`

---

## 🎨 Code Taste (Subjective, Non-Negotiable)

### What "Good Taste" Means

**Clear naming:**
```python
# ❌ Bad
def process(data):
    x = data['x']
    y = x * 2
    return y

# ✅ Good
def calculate_double_value(config: Dict[str, int]) -> int:
    """Double the configured value."""
    initial_value = config['initial_value']
    return initial_value * 2
```

**Minimal nesting:**
```python
# ❌ Bad (3+ levels)
def validate_user(user):
    if user:
        if user['email']:
            if '@' in user['email']:
                return True
    return False

# ✅ Good (early return)
def is_valid_email(email: str) -> bool:
    """Check if email is valid format."""
    if not email or '@' not in email:
        return False
    return True
```

**One responsibility per function:**
```python
# ❌ Bad (does 3 things)
def process_and_save_and_notify(user_id):
    user = fetch_user(user_id)
    process_user(user)
    save_to_db(user)
    send_email(user)

# ✅ Good (one per function)
def fetch_and_validate_user(user_id: str) -> User:
    """Fetch user from DB, validate."""
    user = fetch_user(user_id)
    validate_user(user)
    return user

def save_user(user: User) -> None:
    """Persist user to database."""
    db.save(user)

def notify_user(user: User) -> None:
    """Send notification email."""
    send_email(user.email, ...)
```

**Comment the "why", not the "what":**
```python
# ❌ Bad (obvious)
x = user['age']  # Get the user's age
if x > 18:  # Check if age is greater than 18
    send_email()  # Send an email

# ✅ Good (explains intent)
age = user['age']
# Only notify users 18+ due to GDPR requirements
if age >= 18:
    send_email(user.email, subject="Important Update")
```

---

## 📦 Versioning & Dependencies

### Python
```python
# pyproject.toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.10"

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "black>=23.0",
    "ruff>=0.1",
    "mypy>=1.0"
]

[tool.black]
line-length = 100

[tool.ruff]
line-length = 100
select = ["E", "F", "W", "C90", "I"]
```

### TypeScript/Node
```json
{
  "name": "my-project",
  "version": "0.1.0",
  "engines": { "node": ">=18" },
  "scripts": {
    "build": "tsc",
    "test": "vitest",
    "lint": "eslint src/ --fix",
    "dev": "tsx watch src/index.ts"
  },
  "dependencies": {},
  "devDependencies": {
    "typescript": "^5.0",
    "vitest": "^1.0",
    "eslint": "^8.0"
  }
}
```

---

## 🚀 Final Handoff

When code is ready:

```
✅ DELIVERABLES
├── src/                # All source code
├── tests/              # Full test suite
├── README.md           # Setup + usage
├── .env.example        # Config template
├── Makefile / scripts/ # Build tools
└── IMPLEMENTATION.md   # This contract fulfilled

Ready to: git add . && git commit -m "Initial commit: [project]"
```

---

## 🔄 When Claude Code Falls Short

If generated code doesn't meet this standard:

1. **Call it out immediately** — "This violates section [X] of the contract"
2. **Be specific** — "Missing error handling in `fetch_user()`" not "bad code"
3. **Rerun with clarification** — "Regenerate `src/api.ts` with explicit error types and 100% test coverage"
4. **Escalate if needed** — Use Sonnet for architectural review

---

## 📝 Attached to Every Session

**Always include this contract in your Claude Code session:**
```
system_prompt: "You are a production code generator. Follow CLAUDE-CODE-CONTRACT.md completely."
context: [CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, project-specific files]
```

This ensures consistency and prevents regressions to lower-quality patterns.
