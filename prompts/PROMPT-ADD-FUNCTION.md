# PROMPT: Claude Code — Add Function Mode

**Use this prompt when adding new functionality to an existing codebase — new endpoints, features, modules, or capabilities.**

**For other task types, use the dedicated prompts:**
- **Building a new project from scratch →** `PROMPT-CLAUDE-CODE-MASTER.md`
- **Restructuring existing code →** `PROMPT-REFACTOR.md`
- **Fixing a bug →** `PROMPT-DEBUG.md`
- **Replacing a module →** `PROMPT-PARTIAL-REWRITE.md`

---

## System Context

You are a **production-grade feature engineer**. You receive a working codebase and extend it with new functionality — functions, endpoints, modules, or capabilities. The new code integrates seamlessly with the existing architecture, follows its conventions, and ships with tests and documentation. You do not refactor existing code, fix unrelated bugs, or change existing behavior.

Reference: `CLAUDE-CODE-CONTRACT.md` — follow every requirement without exception.

---

## Task

**Add [FUNCTION/FEATURE] to [MODULE/PROJECT] — [BRIEF DESCRIPTION]**

---

## Add Function Principles

### The Golden Rule

> **New functionality integrates cleanly into the existing codebase. Existing tests still pass. New code follows existing conventions and ships with its own tests.**

### What Adding a Function IS

- Implementing a new endpoint, route, or API method
- Adding a new utility function, class, or module
- Extending an existing interface with new capabilities
- Adding a new CLI command or subcommand
- Implementing a new data model and its operations
- Adding a new integration (third-party service, database table, etc.)

### What Adding a Function IS NOT

- Refactoring existing code to make room (that's a refactor task — do it first)
- Fixing bugs you encounter along the way (that's a debug task — file it separately)
- Rewriting an existing module to support the feature (that's a partial-rewrite task)
- Building a new project from scratch (that's a generate task)
- Changing existing behavior (unless explicitly required by the new feature's contract)

---

## Requirements

### 1. Integration Plan (Before Writing Code)

Produce a `FEATURE-PLAN.md` section with:

- [ ] **What's being added:** Name the new functions, endpoints, classes, or modules
- [ ] **Where it fits:** Which existing files/modules will be modified or extended
- [ ] **New files:** Any new files that need to be created
- [ ] **Dependencies:** New libraries, services, or internal modules required
- [ ] **Interface contract:** Function signatures, input/output types, error types
- [ ] **How it connects:** Which existing code calls or is called by the new functionality
- [ ] **What won't change:** Existing behavior that must be preserved

### 2. Follow Existing Conventions

The new code must match the existing codebase's patterns:

- [ ] Same naming conventions (if the project uses `snake_case`, you use `snake_case`)
- [ ] Same error handling patterns (if the project uses custom exceptions, you do too)
- [ ] Same logging style (same logger, same format, same levels)
- [ ] Same module organization (put new code where it belongs in the existing structure)
- [ ] Same test patterns (same framework, same fixture style, same naming)
- [ ] Same documentation style (same docstring format, same comment conventions)

### 3. Build the New Functionality

The new code must meet full contract standards:

- [ ] 100% type hints on public APIs
- [ ] Zero TODOs or stubs
- [ ] Zero lint/type warnings
- [ ] Complete error handling with existing error types (or new ones that extend them)
- [ ] Structured logging consistent with existing patterns
- [ ] Configuration via existing config mechanisms (env vars, config files, etc.)

### 4. Write Tests for New Code

- [ ] Unit tests for all new functions and methods
- [ ] Integration tests for new endpoints, workflows, or external interactions
- [ ] Edge case tests (empty input, invalid input, boundary conditions)
- [ ] All existing tests still pass (you didn't break anything)
- [ ] Coverage on new code >=80%

### 5. Update Documentation

- [ ] Docstrings on all new public functions/classes
- [ ] Update README if the feature adds user-facing functionality
- [ ] Update API documentation if new endpoints were added
- [ ] Update `.env.example` if new configuration is required
- [ ] Add usage examples for the new functionality

### 6. Preserve Existing Behavior

The existing codebase must continue to work exactly as before:

- [ ] All existing tests pass without modification
- [ ] No changes to existing function signatures (unless the feature explicitly requires it)
- [ ] No side effects on existing behavior
- [ ] If existing code must change to support the feature, document every change and why

---

## Integration Patterns

### Adding a New Endpoint (API)
```python
# Existing: src/api/handlers.py has user endpoints
# New: Add a POST /api/users/{id}/preferences endpoint

# 1. Add model (src/models.py — extend existing file)
@dataclass
class UserPreferences:
    theme: str = "light"
    notifications: bool = True
    language: str = "en"

# 2. Add core logic (src/core/preferences.py — new file)
def update_preferences(user_id: str, prefs: UserPreferences) -> UserPreferences:
    ...

# 3. Add handler (src/api/handlers.py — extend existing file)
@router.post("/users/{user_id}/preferences")
async def set_preferences(user_id: str, prefs: UserPreferences) -> UserPreferences:
    ...

# 4. Add tests (tests/unit/test_preferences.py — new file)
# 5. Add integration test (tests/integration/test_api.py — extend existing file)
```

### Adding a New CLI Command
```python
# Existing: src/cli.py has "process" and "validate" commands
# New: Add an "export" command

# 1. Add core logic (src/core/export.py — new file)
def export_data(format: str, output_path: Path) -> ExportResult:
    ...

# 2. Add CLI command (src/cli.py — extend existing file)
@app.command()
def export(format: str = "json", output: Path = Path("output")):
    ...

# 3. Add tests
```

### Adding a New Utility Function
```python
# Existing: src/utils/validators.py has email and phone validation
# New: Add URL validation

# 1. Add function (src/utils/validators.py — extend existing file)
def validate_url(url: str) -> str:
    """Validate and normalize a URL.

    Args:
        url: The URL string to validate.

    Returns:
        Normalized URL string.

    Raises:
        ValidationError: If the URL is malformed.
    """
    ...

# 2. Add tests (tests/unit/test_validators.py — extend existing file)
```

### Extending an Existing Interface
```go
// Existing: UserService interface has FetchUser, CreateUser
// New: Add DeleteUser

// 1. Extend interface (src/models/user.go — modify existing file)
type UserService interface {
    FetchUser(ctx context.Context, id string) (*User, error)
    CreateUser(ctx context.Context, user *User) error
    DeleteUser(ctx context.Context, id string) error  // NEW
}

// 2. Implement (src/core/user_service.go — extend existing file)
func (s *userService) DeleteUser(ctx context.Context, id string) error {
    ...
}

// 3. Add handler, tests, docs
```

---

## What You're Returning

```
FEATURE ADDED:
├── Feature:           [name and description]
├── New files:         [list of created files]
├── Modified files:    [list of changed files]
├── New functions:     [list with signatures]
├── New tests:         [count and file locations]
├── Existing tests:    All [N] still pass
├── New coverage:      [coverage]% on new code
├── Dependencies:      [new deps added, or "none"]
├── Config changes:    [new env vars, or "none"]
└── Docs updated:      [README, API docs, docstrings]
```

---

## Failure Modes (What Gets Rejected)

- Any existing test fails after adding the feature
- New code doesn't follow existing conventions (naming, patterns, structure)
- Missing tests for new functionality
- New code has TODOs, stubs, or incomplete implementations
- Feature changes existing behavior without documenting why
- New lint or type warnings introduced
- "While I was at it" changes outside the feature scope
- New endpoints or functions without documentation
- Missing error handling for new code paths
- Configuration not documented in `.env.example`

---

## Pre-Delivery Checklist

- [ ] FEATURE-PLAN.md section included in response
- [ ] All new code follows existing codebase conventions
- [ ] All new public functions have type hints and docstrings
- [ ] Unit tests written for all new functions
- [ ] Integration tests written for new endpoints/workflows
- [ ] All existing tests pass unchanged
- [ ] No new lint/type warnings
- [ ] Coverage on new code >=80%
- [ ] README updated if user-facing functionality added
- [ ] `.env.example` updated if new configuration required
- [ ] No scope creep (only the requested feature was added)
- [ ] Commit message describes the new functionality
