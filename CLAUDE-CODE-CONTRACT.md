# Claude Code Contract — Production Quality Standards

**Purpose:** Ensure Claude Code outputs are production-ready, architecturally sound, and tastefully designed. This contract supersedes generic prompts and applies to all Claude Code sessions.

---

## 🎯 Core Principles

1. **Complete, not iterative.** Code should ship as-is after one run. Zero "run this first, then..." instructions.
2. **Taste matters.** Clean architecture, clear naming, minimal verbosity. Code that other engineers *want* to read.
3. **Tests are not optional.** Every deliverable includes unit tests + integration tests, minimum 80% coverage.
4. **Documentation lives in code.** Docstrings, type hints, and inline comments explain *why*, not *what*.
5. **No placeholder code.** Every function, every class, every module is production-grade or removed.
6. **Right tool for the job.** Use the task mode that matches your intent — generate, refactor, debug, or rewrite.

---

## 🔀 Task Modes

This contract supports four modes. Each mode uses a dedicated prompt from `prompts/` and has its own checklist. **Pick the mode that matches what you're doing.**

| Mode | When to Use | Prompt File | Key Constraint |
|---|---|---|---|
| **Generate** | Build a new project from scratch | `PROMPT-CLAUDE-CODE-MASTER.md` | Ship complete, no TODOs |
| **Refactor** | Restructure without changing behavior | `PROMPT-REFACTOR.md` | Existing tests pass unchanged |
| **Debug** | Find and fix a specific bug | `PROMPT-DEBUG.md` | Failing test first, minimal fix |
| **Partial Rewrite** | Replace a module with new implementation | `PROMPT-PARTIAL-REWRITE.md` | Callers don't break |

### How to Choose

```
"I need to build something new"              → Generate
"This code works but is messy"               → Refactor
"This code is broken"                        → Debug
"This module needs a fundamentally new design" → Partial Rewrite
```

### Shared Standards (All Modes)

These apply regardless of which mode you're in:
- 100% type hints on public APIs
- Zero lint/type warnings
- Zero TODOs or stubs
- Explicit error handling
- Structured logging
- Tests pass (≥80% coverage)

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
└── pyproject.toml / package.json / go.mod / Cargo.toml / CMakeLists.txt
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

## 🦀 Rust-Specific Standards

### Type System & Safety (Required)
```rust
use std::fmt;
use thiserror::Error;

#[derive(Debug, Clone)]
pub struct User {
    pub id: String,
    pub name: String,
    pub email: Option<String>,
    pub metadata: std::collections::HashMap<String, String>,
}

/// Fetch users from database.
///
/// # Arguments
/// * `limit` - Maximum number of users to return.
///
/// # Errors
/// Returns `DatabaseError` if the query fails.
pub fn fetch_users(limit: usize) -> Result<Vec<User>, DatabaseError> {
    // ...
}

pub async fn process_async(user: &User) -> Result<String, ApplicationError> {
    // ...
}
```

### Formatting
- **Formatter:** `rustfmt` (cargo fmt)
- **Linting:** `clippy` with `-D warnings` (zero warnings)
- **Naming:** `snake_case` for functions/vars, `PascalCase` for types/traits, `UPPER_SNAKE` for constants

### Error Handling
```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum ApplicationError {
    #[error("validation error: {0}")]
    Validation(String),

    #[error("not found: {0}")]
    NotFound(String),

    #[error("database error: {source}")]
    Database {
        #[from]
        source: sqlx::Error,
    },
}

pub fn validate_email(email: &str) -> Result<String, ApplicationError> {
    if email.is_empty() || !email.contains('@') {
        return Err(ApplicationError::Validation(
            format!("Invalid email: {email}")
        ));
    }
    Ok(email.to_lowercase())
}
```

### Logging
```rust
use tracing::{info, debug, error};

pub fn process_user(user_id: &str) -> Result<(), ApplicationError> {
    info!(user_id, "Processing user");
    match fetch_user(user_id) {
        Ok(user) => {
            debug!(?user, "Fetched user");
            Ok(())
        }
        Err(e) => {
            error!(user_id, error = %e, "Failed to process user");
            Err(e)
        }
    }
}
```

### Testing
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_user_creation() {
        let user = User {
            id: "123".into(),
            name: "Alice".into(),
            email: None,
            metadata: Default::default(),
        };
        assert_eq!(user.id, "123");
        assert!(user.email.is_none());
    }

    #[test]
    fn test_validate_email_invalid() {
        let result = validate_email("invalid");
        assert!(result.is_err());
    }
}

// tests/integration/test_api.rs
#[tokio::test]
async fn test_fetch_users_endpoint() {
    let response = client.get("/api/users").send().await.unwrap();
    assert_eq!(response.status(), 200);
}
```

---

## 🔧 C-Specific Standards

### Type Safety & Headers (Required)
```c
#include <stdint.h>
#include <stdbool.h>
#include <stdlib.h>

/* Use fixed-width integers for cross-platform safety */
typedef struct {
    char id[64];
    char name[128];
    char email[256];  /* empty string if not set */
    bool has_email;
} User;

/**
 * Fetch users from database.
 *
 * @param[out] users   Buffer to write users into.
 * @param      capacity Maximum number of users to return.
 * @param[out] count   Actual number of users returned.
 * @return 0 on success, negative error code on failure.
 */
int fetch_users(User *users, size_t capacity, size_t *count);
```

### Formatting
- **Standard:** C11 or later (`-std=c11`)
- **Formatter:** `clang-format` (LLVM or project style)
- **Warnings:** `-Wall -Wextra -Werror -pedantic`
- **Naming:** `snake_case` for functions/vars, `PascalCase` for types/structs, `UPPER_SNAKE` for macros/constants

### Error Handling
```c
#include <errno.h>

typedef enum {
    ERR_OK = 0,
    ERR_INVALID_INPUT = -1,
    ERR_NOT_FOUND = -2,
    ERR_DATABASE = -3,
    ERR_OUT_OF_MEMORY = -4,
} ErrorCode;

const char *error_string(ErrorCode code);

ErrorCode validate_email(const char *email, char *out, size_t out_size) {
    if (!email || !strchr(email, '@')) {
        return ERR_INVALID_INPUT;
    }
    /* normalize to lowercase */
    snprintf(out, out_size, "%s", email);
    for (char *p = out; *p; ++p) *p = tolower((unsigned char)*p);
    return ERR_OK;
}
```

### Memory Management
```c
/* Always pair allocation with cleanup */
User *user = malloc(sizeof(User));
if (!user) {
    return ERR_OUT_OF_MEMORY;
}
/* ... use user ... */
free(user);
user = NULL;

/* Prefer stack allocation for small, fixed-size objects */
User stack_user = {0};
```

### Testing
```c
/* Use CUnit, Unity, or CMocka */
#include <CUnit/CUnit.h>
#include <CUnit/Basic.h>

void test_user_creation(void) {
    User user = {0};
    strncpy(user.id, "123", sizeof(user.id) - 1);
    strncpy(user.name, "Alice", sizeof(user.name) - 1);
    CU_ASSERT_STRING_EQUAL(user.id, "123");
    CU_ASSERT_FALSE(user.has_email);
}

void test_validate_email_invalid(void) {
    char out[256];
    ErrorCode err = validate_email("invalid", out, sizeof(out));
    CU_ASSERT_EQUAL(err, ERR_INVALID_INPUT);
}
```

---

## ⚙️ C++-Specific Standards

### Type Safety & RAII (Required)
```cpp
#include <string>
#include <optional>
#include <unordered_map>
#include <vector>
#include <memory>

struct User {
    std::string id;
    std::string name;
    std::optional<std::string> email;
    std::unordered_map<std::string, std::string> metadata;
};

/**
 * Fetch users from database.
 *
 * @param limit Maximum number of users to return.
 * @return Vector of User objects.
 * @throws DatabaseError if query fails.
 */
std::vector<User> fetch_users(size_t limit = 10);

std::future<std::string> process_async(const User &user);
```

### Formatting
- **Standard:** C++17 or later (`-std=c++17`)
- **Formatter:** `clang-format` (LLVM or project style)
- **Warnings:** `-Wall -Wextra -Werror -pedantic`
- **Naming:** `snake_case` for functions/vars, `PascalCase` for classes/structs, `UPPER_SNAKE` for constants
- **Prefer:** `std::string` over `char*`, `std::vector` over raw arrays, smart pointers over `new`/`delete`

### Error Handling
```cpp
#include <stdexcept>
#include <string>

class ApplicationError : public std::runtime_error {
public:
    explicit ApplicationError(const std::string &msg)
        : std::runtime_error(msg) {}
};

class ValidationError : public ApplicationError {
public:
    explicit ValidationError(const std::string &msg)
        : ApplicationError(msg) {}
};

std::string validate_email(const std::string &email) {
    if (email.empty() || email.find('@') == std::string::npos) {
        throw ValidationError("Invalid email: " + email);
    }
    std::string lower = email;
    std::transform(lower.begin(), lower.end(), lower.begin(), ::tolower);
    return lower;
}
```

### Resource Management (RAII)
```cpp
/* Use smart pointers — never raw new/delete in application code */
auto user = std::make_unique<User>();
auto shared_user = std::make_shared<User>();

/* Use RAII wrappers for system resources */
class FileHandle {
public:
    explicit FileHandle(const std::string &path)
        : handle_(fopen(path.c_str(), "r")) {
        if (!handle_) throw std::runtime_error("Cannot open: " + path);
    }
    ~FileHandle() { if (handle_) fclose(handle_); }

    FileHandle(const FileHandle &) = delete;
    FileHandle &operator=(const FileHandle &) = delete;
    FileHandle(FileHandle &&other) noexcept : handle_(other.handle_) {
        other.handle_ = nullptr;
    }
private:
    FILE *handle_;
};
```

### Logging
```cpp
#include <spdlog/spdlog.h>

void process_user(const std::string &user_id) {
    spdlog::info("Processing user: {}", user_id);
    try {
        auto user = fetch_user(user_id);
        spdlog::debug("Fetched user: {}", user.name);
    } catch (const DatabaseError &e) {
        spdlog::error("Failed to process user {}: {}", user_id, e.what());
        throw;
    }
}
```

### Testing
```cpp
/* Use Google Test or Catch2 */
#include <gtest/gtest.h>

TEST(UserTest, Creation) {
    User user{"123", "Alice", std::nullopt, {}};
    EXPECT_EQ(user.id, "123");
    EXPECT_FALSE(user.email.has_value());
}

TEST(ValidationTest, InvalidEmail) {
    EXPECT_THROW(validate_email("invalid"), ValidationError);
}

/* Integration test */
TEST(ApiTest, FetchUsersEndpoint) {
    auto response = client.get("/api/users");
    EXPECT_EQ(response.status(), 200);
    EXPECT_GT(response.json().size(), 0);
}
```

---

## 🐹 Go-Specific Standards

### Type System & Interfaces (Required)
```go
package models

import (
    "context"
    "fmt"
)

// User represents a system user.
type User struct {
    ID       string            `json:"id"`
    Name     string            `json:"name"`
    Email    string            `json:"email,omitempty"`
    Metadata map[string]string `json:"metadata,omitempty"`
}

// UserService defines operations on users.
type UserService interface {
    FetchUsers(ctx context.Context, limit int) ([]User, error)
    ProcessUser(ctx context.Context, user *User) (string, error)
}
```

### Formatting
- **Formatter:** `gofmt` (non-negotiable, standard Go formatting)
- **Linting:** `go vet`, `golangci-lint` with default config
- **Naming:** `camelCase` for unexported, `PascalCase` for exported, acronyms all-caps (`ID`, `HTTP`, `URL`)
- **Imports:** `goimports` sorted (stdlib, third-party, local)

### Error Handling
```go
package errors

import (
    "errors"
    "fmt"
)

var (
    ErrNotFound      = errors.New("not found")
    ErrInvalidInput  = errors.New("invalid input")
)

type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation error on %s: %s", e.Field, e.Message)
}

func ValidateEmail(email string) (string, error) {
    if email == "" || !strings.Contains(email, "@") {
        return "", &ValidationError{
            Field:   "email",
            Message: fmt.Sprintf("invalid email: %s", email),
        }
    }
    return strings.ToLower(email), nil
}

// Always wrap errors with context
func FetchUser(ctx context.Context, id string) (*User, error) {
    user, err := db.QueryUser(ctx, id)
    if err != nil {
        return nil, fmt.Errorf("fetch user %s: %w", id, err)
    }
    return user, nil
}
```

### Logging
```go
package main

import "log/slog"

func processUser(ctx context.Context, userID string) error {
    slog.Info("processing user", "user_id", userID)

    user, err := fetchUser(ctx, userID)
    if err != nil {
        slog.Error("failed to process user", "user_id", userID, "error", err)
        return fmt.Errorf("process user %s: %w", userID, err)
    }
    slog.Debug("fetched user", "user", user.Name)
    return nil
}
```

### Testing
```go
package models_test

import (
    "testing"

    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
)

func TestUserCreation(t *testing.T) {
    user := User{ID: "123", Name: "Alice"}
    assert.Equal(t, "123", user.ID)
    assert.Empty(t, user.Email)
}

func TestValidateEmail_Invalid(t *testing.T) {
    _, err := ValidateEmail("invalid")
    require.Error(t, err)
    var ve *ValidationError
    assert.ErrorAs(t, err, &ve)
}

// Integration test
func TestFetchUsersEndpoint(t *testing.T) {
    resp, err := http.Get(server.URL + "/api/users")
    require.NoError(t, err)
    defer resp.Body.Close()
    assert.Equal(t, http.StatusOK, resp.StatusCode)
}
```

---

## 🏁 Pre-Delivery Checklists

### All Modes — Universal Checklist

Before returning code in **any** mode, verify:

- [ ] **Type hints 100%** — `mypy`, `tsc`, `cargo check`, or compiler passes with no errors
- [ ] **Tests pass** — `pytest`, `vitest`, `go test`, `cargo test`, or `ctest` runs, all pass, ≥80% coverage
- [ ] **Linting clean** — `ruff`, `eslint`, `clippy`, `clang-tidy`, `go vet` pass, zero warnings
- [ ] **No secrets in code** — all config in `.env.example`, no API keys/tokens
- [ ] **Error messages clear** — users understand what went wrong and how to fix it
- [ ] **Naming consistent** — no `temp_var`, no `x`, no `do_thing_2()`

### Generate Mode — Additional Checklist

- [ ] **All source files complete** — no TODOs, no stubs, no `// TODO: implement X`
- [ ] **Setup script works** — `setup.sh`, `npm install && npm test`, `cargo build`, or `cmake && make` succeeds on clean system
- [ ] **README accurate** — examples run, installation instructions tested
- [ ] **Git-ready** — `.gitignore` correct, `git status` shows only new files
- [ ] **Logging added** — INFO/DEBUG/ERROR levels appropriate, no blind spots

### Refactor Mode — Additional Checklist

- [ ] **Existing tests pass unchanged** — zero modifications to test files (unless imports moved)
- [ ] **Public API preserved** — same function signatures, same error types, same return types
- [ ] **Coverage maintained or improved** — never decreased from before
- [ ] **REFACTOR-PLAN documented** — scope, goal, and what didn't change
- [ ] **One logical change per step** — no combined structural + behavioral changes
- [ ] **No bug fixes mixed in** — discovered bugs documented but not fixed

### Debug Mode — Additional Checklist

- [ ] **Regression test written** — fails before the fix, passes after
- [ ] **Root cause documented** — DEBUG-REPORT with trace from symptom to cause
- [ ] **Minimal fix** — fewest lines changed, no surrounding "improvements"
- [ ] **No refactoring mixed in** — fix only, nothing else
- [ ] **No temporary debug logging left** — all `[DEBUG]` statements removed
- [ ] **Commit message references bug** — includes what was wrong and what test proves it

### Partial Rewrite Mode — Additional Checklist

- [ ] **Callers unaffected** — all integration tests pass, or migration guide provided
- [ ] **REWRITE-PLAN documented** — scope, reason, boundary contracts, risk assessment
- [ ] **New tests written** — unit tests for all new internal functions
- [ ] **Migration guide** — if API changed, step-by-step instructions for callers
- [ ] **Rollback path documented** — how to revert if the rewrite causes issues
- [ ] **No scope creep** — only files in the rewrite scope were modified

---

## 🎨 Code Taste (Subjective, Non-Negotiable)

### What "Good Taste" Means

Good taste is the difference between code that works and code that communicates. It's about making the reader's job easy and the maintainer's job boring.

### Foundation: Naming, Nesting, Responsibility

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

**Minimal nesting (early return):**
```python
# ❌ Bad (3+ levels)
def validate_user(user):
    if user:
        if user['email']:
            if '@' in user['email']:
                return True
    return False

# ✅ Good
def is_valid_email(email: str) -> bool:
    if not email or '@' not in email:
        return False
    return True
```

**Comment the "why", not the "what":**
```python
# ❌ Bad
x = user['age']  # Get the user's age

# ✅ Good
age = user['age']
# Only notify users 18+ due to GDPR requirements
if age >= 18:
    send_email(user.email, subject="Important Update")
```

### Advanced: Data Flow & API Design

**Make data flow visible.** A reader should trace data through your code without jumping between files.

```python
# ❌ Bad — hidden data flow through mutation
def process_order(order):
    enrich_order(order)        # mutates order.metadata
    validate_order(order)      # reads order.metadata, mutates order.errors
    if not order.errors:
        save_order(order)      # reads everything

# ✅ Good — explicit data flow through return values
def process_order(raw_order: RawOrder) -> ProcessedOrder:
    enriched = enrich_order(raw_order)
    validation = validate_order(enriched)
    if not validation.errors:
        return save_order(enriched)
    raise ValidationError(validation.errors)
```

**Design APIs from the caller's perspective.** Write the calling code first, then implement.

```python
# ❌ Bad — designed from implementation out
result = query_engine.execute(
    QueryConfig(table="users", filters=[Filter("age", "gt", 18)],
    projections=["name", "email"], limit=LimitConfig(count=10, offset=0))
)

# ✅ Good — designed from caller's needs in
users = db.users.where(age__gt=18).select("name", "email").limit(10)
```

**Separate policy from mechanism.** Policy decides *what* to do. Mechanism does it.

```python
# ❌ Bad — policy and mechanism tangled
def handle_payment(payment):
    if payment.amount > 10000:
        require_manager_approval(payment)
        log_high_value_transaction(payment)
    if payment.currency != "USD":
        rate = fetch_exchange_rate(payment.currency)
        payment.amount *= rate
    charge_card(payment)

# ✅ Good — policy is readable, mechanism is reusable
def handle_payment(payment: Payment) -> ChargeResult:
    payment = normalize_currency(payment, target="USD")
    enforce_approval_policy(payment)
    return charge_card(payment)
```

### Advanced: Abstraction Discipline

**Earn your abstractions.** An abstraction is justified when it eliminates duplication across 3+ call sites, or when it captures a domain concept that makes the code more readable. Never create an abstraction for a single use site.

```python
# ❌ Bad — abstraction for single use
class UserCreationStrategyFactory:
    def create(self, source: str) -> UserCreationStrategy: ...

# ✅ Good — just do the thing
def create_user(name: str, email: str) -> User:
    user = User(name=name, email=email)
    db.save(user)
    return user
```

**Keep abstractions at consistent levels.** A function should operate at one level of abstraction throughout.

```python
# ❌ Bad — mixed levels (high-level + low-level in same function)
def deploy_application(config):
    validate_config(config)                    # high-level
    socket.connect((config.host, config.port)) # low-level
    upload_artifacts(config.artifacts)          # high-level
    os.chmod(f"/opt/{config.name}/run", 0o755) # low-level
    start_service(config.name)                 # high-level

# ✅ Good — one level throughout
def deploy_application(config: DeployConfig) -> DeployResult:
    validate_config(config)
    establish_connection(config.host, config.port)
    upload_artifacts(config.artifacts)
    configure_permissions(config.name)
    start_service(config.name)
```

### Advanced: Composition Over Configuration

**Prefer small composable pieces over large configurable ones.**

```python
# ❌ Bad — god function with boolean flags
def send_notification(user, message, urgent=False, retry=True,
                      channel="email", template=None, cc=None):
    ...

# ✅ Good — compose behavior
notification = (
    Notification(user, message)
    .via(Email(cc=manager))
    .with_retry(max_attempts=3)
    .urgent()
)
notification.send()
```

**Make illegal states unrepresentable.** Use the type system to prevent bugs at compile time.

```python
# ❌ Bad — states are implicit, can be inconsistent
class Connection:
    host: str
    port: int
    socket: Optional[Socket]  # None if disconnected
    error: Optional[str]      # None if no error... or if not checked yet?

# ✅ Good — each state is explicit
@dataclass
class Disconnected:
    host: str
    port: int

@dataclass
class Connected:
    host: str
    port: int
    socket: Socket

@dataclass
class Failed:
    host: str
    port: int
    error: str

Connection = Disconnected | Connected | Failed
```

### The Taste Test

Before delivering code, ask:
1. **Can I read any function and understand it without reading other functions?**
2. **If I change one requirement, how many files do I touch?** (Ideally: one)
3. **Does the code structure mirror the problem structure?**
4. **Would I be confused by this code 6 months from now?**

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

### Rust
```toml
# Cargo.toml
[package]
name = "my-project"
version = "0.1.0"
edition = "2021"
rust-version = "1.75"

[dependencies]
thiserror = "2"
tracing = "0.1"
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }

[dev-dependencies]
tokio-test = "0.4"

[profile.release]
lto = true
```

### Go
```go
// go.mod
module github.com/org/my-project

go 1.22

require (
    github.com/stretchr/testify v1.9.0
)
```

### C/C++ (CMake)
```cmake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.20)
project(my-project VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_compile_options(-Wall -Wextra -Werror -pedantic)

# Source
add_library(mylib src/core/logic.c src/models.c)
add_executable(myapp src/main.c)
target_link_libraries(myapp PRIVATE mylib)

# Testing
enable_testing()
add_executable(test_all tests/test_models.c tests/test_logic.c)
target_link_libraries(test_all PRIVATE mylib)
add_test(NAME unit_tests COMMAND test_all)
```

---

## 🚀 Final Handoff

When code is ready, format the summary based on task mode:

### Generate Mode
```
✅ DELIVERABLES
├── src/                # All source code
├── tests/              # Full test suite
├── README.md           # Setup + usage
├── .env.example        # Config template
├── Makefile / scripts/ # Build tools
└── IMPLEMENTATION.md   # This contract fulfilled

Ready to: git add . && git commit -m "feat: Initial [project] implementation"
```

### Refactor Mode
```
✅ REFACTOR COMPLETE
├── Files modified:     [list]
├── Files added:        [extracted modules, if any]
├── Files removed:      [consolidated, if any]
├── Tests:              All [N] existing tests pass (unchanged)
├── Coverage:           [before]% → [after]%
├── Key changes:        [bullet list]
└── Public API:         Unchanged

Ready to: git add . && git commit -m "refactor: [description of restructuring]"
```

### Debug Mode
```
✅ BUG FIXED
├── Bug:                [one-line description]
├── Root cause:         [what was wrong]
├── Files modified:     [list]
├── Lines changed:      [count]
├── Regression test:    [test name]
├── All tests:          [N] passed, [coverage]%
└── Verified:           Reproduction steps no longer trigger bug

Ready to: git add . && git commit -m "fix: [description of bug fix]"
```

### Partial Rewrite Mode
```
✅ REWRITE COMPLETE
├── Module rewritten:   [name]
├── Reason:             [why]
├── Files replaced:     [old → new]
├── API changes:        [none / list]
├── Migration required: [yes/no]
├── Tests:              [N] existing passed, [N] new added
├── Coverage:           [before]% → [after]%
└── Rollback:           [revert instructions]

Ready to: git add . && git commit -m "rewrite: [description of module replacement]"
```

---

## 🔄 When Claude Code Falls Short

If output doesn't meet this standard:

1. **Call it out immediately** — "This violates section [X] of the contract"
2. **Be specific** — "Missing error handling in `fetch_user()`" not "bad code"
3. **Rerun with clarification** — "Regenerate `src/api.ts` with explicit error types and 100% test coverage"
4. **Check the mode** — Did you use the right task mode? A "debug" request handled as "generate" will produce wrong results
5. **Escalate if needed** — Use Sonnet for architectural review

### Mode-Specific Rejection Examples

**Refactor rejected:**
> "Existing test `test_validate_email` fails after your refactoring. The refactor changed behavior — `validate_email()` now returns lowercase but the test expects original case. Revert the behavior change and only restructure."

**Debug rejected:**
> "Your fix also refactored the surrounding code. Revert lines 45-78 which are cosmetic changes. Keep only the one-line fix on line 52 and the regression test."

**Partial Rewrite rejected:**
> "The `fetch_user()` function now raises `NotFoundError` instead of returning `None`, but callers in `src/api/handlers.py` still check for `None`. Either preserve the old behavior or update all callers and document the migration."

---

## 📝 Attached to Every Session

**Always include this contract in your Claude Code session:**
```
system_prompt: "You are a production code generator. Follow CLAUDE-CODE-CONTRACT.md completely."
context: [CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, project-specific files]
```

This ensures consistency and prevents regressions to lower-quality patterns.
