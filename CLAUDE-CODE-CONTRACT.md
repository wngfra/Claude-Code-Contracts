# Claude Code Contract — Production Quality Standards

**Purpose:** Ensure Claude Code outputs are production-ready, architecturally sound, performant, secure, and tastefully designed. This contract supersedes generic prompts and applies to all Claude Code sessions.

---

## Core Principles

1. **Complete, not iterative.** Code should ship as-is after one run. Zero "run this first, then..." instructions.
2. **Taste matters.** Clean architecture, clear naming, minimal verbosity. Code that other engineers *want* to read.
3. **Tests are not optional.** Every deliverable includes unit tests + integration tests, minimum 80% coverage.
4. **Performance is a feature.** Measure, don't guess. Include benchmarks for hot paths.
5. **Security by default.** Validate inputs at boundaries. Never trust external data.
6. **Documentation lives in code.** Docstrings, type hints, and inline comments explain *why*, not *what*.
7. **No placeholder code.** Every function, every class, every module is production-grade or removed.

---

## Mandatory Deliverables

Every Claude Code session must output:

| Deliverable | Requirement | Non-Negotiable |
|---|---|---|
| **Source Code** | All files, organized by function not framework | ✅ |
| **Type Hints** | 100% coverage for public APIs | ✅ |
| **Tests** | Unit + integration, ≥80% coverage, zero skips | ✅ |
| **Benchmarks** | Performance tests for hot paths (see Performance Contract) | ✅ |
| **Setup Script** | `setup.sh` or `install.md` — one command to run locally | ✅ |
| **README** | Full project README (see README Generation Spec below) | ✅ |
| **CHANGELOG** | Version history in Keep a Changelog format (see CHANGELOG Contract below) | ✅ |
| **Error Handling** | Explicit error messages, never silent failures | ✅ |
| **Logging** | Structured logging, appropriate levels (DEBUG/INFO/ERROR) | ✅ |
| **Configuration** | `.env.example` + docs, no secrets in code | ✅ |
| **Build/Lint Gate** | `Makefile` or `scripts/` with `test`, `lint`, `build` targets | ✅ |
| **CI Config** | GitHub Actions workflow (or equivalent) for lint + test + build | ✅ |
| **Git Status** | `.gitignore` correct, ready to commit immediately | ✅ |

---

## Architecture Standards (Language-Agnostic)

### Structure
```
project/
├── src/               # Application code
│   ├── core/          # Core logic (independent of framework)
│   ├── api/           # API layer (FastAPI, Express, Axum, Gin, etc.)
│   ├── models/        # Data models / types / structs
│   └── utils/         # Reusable utilities
├── tests/
│   ├── unit/          # Unit tests (core logic)
│   ├── integration/   # Integration tests (API + database)
│   └── fixtures/      # Shared test data
├── benches/           # Performance benchmarks
├── scripts/           # Utility scripts (setup, deploy, etc.)
├── .env.example       # Environment template
├── README.md          # Setup + usage
├── Makefile           # `make test`, `make lint`, `make build`, `make bench`
└── pyproject.toml / package.json / go.mod / Cargo.toml / CMakeLists.txt
```

### Module Organization
- **Core logic:** Independent of framework (testable, reusable)
- **API layer:** Thin, mainly routing + validation
- **Models:** Single source of truth for data structures
- **Utils:** Pure functions, no side effects

### Project Type Adaptations

**Library:**
```
├── src/lib.*           # Public API surface
├── src/internal/       # Private implementation (not exported)
├── examples/           # Working usage examples
├── CHANGELOG.md        # Version history
└── Cargo.toml / pyproject.toml  # Published metadata
```

**CLI Tool:**
```
├── src/main.*          # Entry point
├── src/commands/       # Command implementations
├── src/config.*        # CLI argument parsing + config
└── completions/        # Shell completions (bash, zsh, fish)
```

**API Server:**
```
├── src/routes/         # Route handlers (thin)
├── src/services/       # Business logic
├── src/middleware/      # Auth, logging, rate limiting
├── src/db/             # Database access layer
└── migrations/         # Schema migrations
```

**Embedded / Systems:**
```
├── src/hal/            # Hardware abstraction
├── src/drivers/        # Device drivers
├── src/scheduler/      # Task scheduling
└── linker.ld           # Linker script (if bare metal)
```

**Anti-patterns:**
- Business logic buried in API handlers
- Model definitions scattered across files
- Utility functions with hidden state
- "Misc" folders or generic `helpers.py`

---

## README Generation Spec

**Every generated project must include a comprehensive, accurate README.md. This is not optional.**

### Required Sections (in order)

```markdown
# [Project Name]

[One-line description — what it does, not how]

## Overview

[2-3 paragraphs: what the project does, who it's for, why it exists.
Include key features as a bullet list.]

## Quick Start

[Numbered steps from clone to running. Must work on a clean machine.]

1. Clone the repo
2. Run setup: `./setup.sh` or equivalent
3. Configure: `cp .env.example .env` and fill values
4. Run tests: `make test`
5. Start: `make run`

## Installation

[Detailed installation for all supported platforms/package managers.
Include prerequisites (language version, system deps).]

## Usage

[Working code examples with expected output for every public API / CLI command.
Examples MUST be tested — if they don't run, the README fails review.]

### CLI Examples (if CLI)
\`\`\`bash
$ my-tool --help
$ my-tool process --input data.json --output results.json
\`\`\`

### Library Examples (if library)
\`\`\`python
from mylib import process
result = process(data)
print(result)  # Expected output: ...
\`\`\`

### API Examples (if API server)
\`\`\`bash
curl -X POST http://localhost:8000/api/v1/resource \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
# Response: {"data": {...}}
\`\`\`

## Architecture

[Module structure diagram (ASCII tree).
Brief explanation of each top-level directory/module.
Data flow description: input → processing → output.
Key design decisions and why they were made.]

## Configuration

[Table of all environment variables / config options.
Include: name, type, default, description, required/optional.]

| Variable | Type | Default | Required | Description |
|---|---|---|---|---|
| `DATABASE_URL` | string | — | Yes | PostgreSQL connection string |
| `LOG_LEVEL` | string | `INFO` | No | Logging verbosity |

## Testing

[How to run tests, what coverage to expect.
How to run benchmarks.
How to add new tests.]

## Performance

[Benchmark results for hot paths (actual numbers, not vague claims).
Startup time.
Memory usage characteristics.
Throughput baseline.]

## API Reference (if applicable)

[Endpoint list with method, path, description, request/response examples.
Or link to auto-generated docs (Swagger, godoc, rustdoc).]

## Deployment

[How to deploy: Docker, systemd, serverless, etc.
Include Dockerfile if applicable.
Production configuration notes.]

## Troubleshooting

[Common errors and how to fix them.
FAQ for likely issues during setup/usage.]

## Known Limitations

[What this project does NOT do.
Planned improvements (with issue links if applicable).
Compatibility notes.]

## Contributing (if open-source)

[How to contribute, code style, PR process.]

## License

[License type and link to LICENSE file.]

## Changelog

[Link to CHANGELOG.md: "See [CHANGELOG.md](CHANGELOG.md) for version history."]
```

### README Quality Rules

- **Every code example must work** — copy-paste from README, run, get expected output
- **No placeholder text** — `[TODO]`, `[insert here]`, `lorem ipsum` are rejected
- **Architecture section must match actual code** — if you rename a module, update the README
- **Performance numbers must be real** — run the benchmarks, paste the results
- **Quick Start must work on a clean machine** — test the steps mentally, don't assume pre-installed tools
- **Keep it concise** — comprehensive does not mean verbose; use tables and bullet lists

---

## CHANGELOG Contract

**Every project must include a CHANGELOG.md. Not just libraries — all project types.**

### Format: Keep a Changelog (https://keepachangelog.com)

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial project implementation
- CLI interface with `process` and `validate` commands
- REST API with `/api/v1/resource` endpoints
- Unit and integration test suite (87% coverage)
- Performance benchmarks for core processing pipeline
- GitHub Actions CI workflow
- Structured logging with configurable levels

### Security
- Input validation on all API boundaries
- Rate limiting on public endpoints

## [0.1.0] - YYYY-MM-DD

### Added
- Initial release
```

### CHANGELOG Rules

- **Every code change gets a CHANGELOG entry** — no exceptions
- **Use correct categories:** Added, Changed, Deprecated, Removed, Fixed, Security
- **Write for humans** — "Added retry logic for API calls" not "Updated api.py"
- **Group related changes** — don't list every file, describe the feature/fix
- **Unreleased section always exists** — new changes go here until a version is cut
- **Link versions** — `[Unreleased]` and version numbers link to GitHub compare URLs when applicable
- **Date format:** ISO 8601 (YYYY-MM-DD)

### When to Update CHANGELOG

| Event | CHANGELOG Action |
|---|---|
| New feature added | `### Added` entry |
| Existing feature modified | `### Changed` entry |
| Feature deprecated | `### Deprecated` entry |
| Feature removed | `### Removed` entry |
| Bug fixed | `### Fixed` entry |
| Security fix | `### Security` entry |
| Version released | Move `[Unreleased]` to `[X.Y.Z] - YYYY-MM-DD` |

---

## Documentation Sync Contract

**README and CHANGELOG must always reflect the current state of the code. This is enforced, not suggested.**

### The Rule

> Every code change that affects behavior, APIs, configuration, or architecture **must** include corresponding updates to README.md and CHANGELOG.md in the same commit/generation.

### What Triggers a Sync

| Code Change | README Update | CHANGELOG Update |
|---|---|---|
| New function/endpoint/command | Add to Usage/API Reference | `### Added` |
| Changed function signature/behavior | Update Usage examples | `### Changed` |
| Removed function/endpoint | Remove from docs | `### Removed` |
| New config option / env var | Add to Configuration table | `### Added` |
| Removed config option | Remove from Configuration | `### Removed` |
| New dependency | Update Installation if needed | `### Added` (if user-facing) |
| Performance change | Update Performance numbers | `### Changed` |
| Bug fix | Update Troubleshooting if relevant | `### Fixed` |
| Architecture change | Update Architecture diagram | `### Changed` |
| Security fix | Update relevant sections | `### Security` |
| Setup process change | Update Quick Start / Installation | `### Changed` |

### Enforcement

- **Code without matching doc updates is incomplete** — reject and re-request
- **README examples that don't match code are bugs** — treat them with the same severity as failing tests
- **Stale CHANGELOG is a contract violation** — every PR/commit must have an entry
- **Agents must self-check:** Before marking work as complete, verify README and CHANGELOG are current

### For Coding Agents (Claude Code, Copilot, Cursor, etc.)

When modifying existing code in a project that uses this contract:

1. **Before making changes:** Read the current README.md and CHANGELOG.md
2. **After making changes:** Identify which sections of README need updating
3. **Update README.md:** Modify affected sections (examples, config tables, architecture, etc.)
4. **Update CHANGELOG.md:** Add entry under `[Unreleased]` with the correct category
5. **Verify consistency:** Ensure README examples still work with the changed code
6. **Include in same commit:** Doc updates are part of the code change, not a follow-up

**A code change without doc sync is not done. Period.**

---

## Performance Contract

**Performance is measurable. Vague claims are not acceptable.**

### Required for ALL Projects

| Metric | How to Measure | Requirement |
|---|---|---|
| **Benchmarks** | Language-native benchmark tool | Hot paths must have benchmarks |
| **Memory** | Valgrind / ASan / heaptrack / Node `--max-old-space-size` | No leaks, bounded growth |
| **Startup time** | Time from launch to ready | Document in README |
| **Throughput** | Requests/sec or ops/sec for services | Document baseline |

### Benchmark Requirements

Every project must include a `benches/` directory or equivalent:

**Python:**
```python
# benches/bench_core.py
import pytest

@pytest.mark.benchmark
def bench_process_items(benchmark):
    result = benchmark(process_items, range(10_000))
    assert len(result) == 10_000
```
Run: `pytest benches/ --benchmark-only --benchmark-min-rounds=100`

**Rust:**
```rust
// benches/core_bench.rs
use criterion::{criterion_group, criterion_main, Criterion};
use mylib::process_items;

fn bench_process(c: &mut Criterion) {
    c.bench_function("process_10k_items", |b| {
        b.iter(|| process_items(black_box(10_000)))
    });
}

criterion_group!(benches, bench_process);
criterion_main!(benches);
```
Run: `cargo bench`

**TypeScript:**
```typescript
// benches/core.bench.ts
import { bench, describe } from 'vitest';
import { processItems } from '../src/core';

describe('processItems', () => {
  bench('process 10k items', () => {
    processItems(Array.from({ length: 10_000 }, (_, i) => i));
  });
});
```
Run: `vitest bench`

**Go:**
```go
// core_test.go
func BenchmarkProcessItems(b *testing.B) {
    items := make([]int, 10_000)
    for i := range items { items[i] = i }
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        ProcessItems(items)
    }
}
```
Run: `go test -bench=. -benchmem ./...`

**C/C++:**
```cpp
// benches/bench_core.cpp
#include <benchmark/benchmark.h>
#include "core.h"

static void BM_ProcessItems(benchmark::State& state) {
    std::vector<int> items(10000);
    std::iota(items.begin(), items.end(), 0);
    for (auto _ : state) {
        auto result = ProcessItems(items);
        benchmark::DoNotOptimize(result);
    }
}
BENCHMARK(BM_ProcessItems);
BENCHMARK_MAIN();
```

### Performance Budgets

Define in your task brief:
- **Latency:** p50 < Xms, p99 < Yms
- **Memory:** Peak < Z MB
- **Throughput:** N ops/sec minimum
- **Binary size:** For compiled languages, document final size

If a function is on a hot path (>1000 calls/sec or in a tight loop), it MUST have a benchmark.

---

## Security Contract

### Input Validation
- **All external inputs validated at the boundary** — API params, CLI args, file reads, env vars
- **Fail closed** — reject unknown fields, don't silently ignore
- **Type-safe parsing** — use language-native parsers, not regex for structured data
- **Length limits** — enforce max sizes on strings, arrays, file uploads

### Secrets Management
- **Zero secrets in code** — no API keys, passwords, tokens in source
- **`.env.example`** — document all required env vars with placeholder values
- **Secret loading** — use `os.getenv()`, `dotenv`, or config files, never hardcode
- **Gitignore `.env`** — prevent accidental commits

### Dependency Security
- **Minimize dependencies** — each dep must justify its inclusion
- **Pin versions** — exact or bounded ranges, no `*` or `latest`
- **Audit before ship** — run `pip audit` / `npm audit` / `cargo audit` / `go vuln check`
- **Lock files** — `poetry.lock`, `package-lock.json`, `Cargo.lock`, `go.sum` committed

### Network Security (for API/Network projects)
- **TLS by default** — no plaintext HTTP for production
- **CORS configured** — explicit origin allowlist, not `*`
- **Rate limiting** — protect against abuse
- **Auth before business logic** — validate tokens/keys first

### Memory Safety (for C/C++/unsafe Rust)
- **No `unsafe` without justification** — document why it's needed in a comment
- **AddressSanitizer in CI** — `-fsanitize=address` for C/C++
- **Valgrind clean** — zero leaks, zero invalid reads/writes
- **Bounds checking** — never index without checking length first

---

## Concurrency & Async Patterns

### General Rules
- **Document thread safety** — which types are `Send`/`Sync`, which are not thread-safe
- **No data races** — use appropriate synchronization primitives
- **Bounded queues/channels** — never unbounded queues in production
- **Graceful shutdown** — handle SIGTERM/SIGINT, drain in-flight work
- **Context propagation** — pass cancellation tokens/context through call chains

### Python
```python
import asyncio
from typing import AsyncIterator

async def process_stream(items: AsyncIterator[Item]) -> list[Result]:
    """Process items concurrently with bounded concurrency."""
    semaphore = asyncio.Semaphore(10)
    async def process_one(item: Item) -> Result:
        async with semaphore:
            return await do_work(item)
    return await asyncio.gather(*[process_one(i) async for i in items])
```

### Rust
```rust
use tokio::sync::Semaphore;
use std::sync::Arc;

async fn process_stream(items: Vec<Item>) -> Vec<Result> {
    let semaphore = Arc::new(Semaphore::new(10));
    let mut handles = Vec::new();
    for item in items {
        let permit = semaphore.clone().acquire_owned().await.unwrap();
        handles.push(tokio::spawn(async move {
            let _permit = permit;
            do_work(item).await
        }));
    }
    futures::future::join_all(handles).await
        .into_iter()
        .filter_map(|r| r.ok())
        .collect()
}
```

### Go
```go
func ProcessStream(ctx context.Context, items []Item) ([]Result, error) {
    sem := make(chan struct{}, 10) // bounded concurrency
    var wg sync.WaitGroup
    results := make([]Result, len(items))
    errs := make(chan error, len(items))

    for i, item := range items {
        wg.Add(1)
        go func(i int, item Item) {
            defer wg.Done()
            sem <- struct{}{}
            defer func() { <-sem }()

            result, err := doWork(ctx, item)
            if err != nil {
                errs <- err
                return
            }
            results[i] = result
        }(i, item)
    }
    wg.Wait()
    close(errs)
    // collect errors...
    return results, nil
}
```

### TypeScript
```typescript
async function processStream(items: Item[], concurrency = 10): Promise<Result[]> {
  const semaphore = new Semaphore(concurrency);
  return Promise.all(
    items.map(async (item) => {
      await semaphore.acquire();
      try {
        return await doWork(item);
      } finally {
        semaphore.release();
      }
    })
  );
}
```

---

## API Design Standards (REST)

### URL Structure
```
/api/v1/resource          # Collection
/api/v1/resource/:id      # Single item
/api/v1/resource/:id/sub  # Nested resource
```

### Response Format
```json
{
  "data": { ... },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-03-25T10:00:00Z"
  }
}
```

### Error Format
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "email field is required",
    "details": [
      { "field": "email", "issue": "required" }
    ]
  },
  "meta": {
    "request_id": "uuid"
  }
}
```

### Status Codes
| Code | When |
|---|---|
| 200 | Success (GET, PUT, PATCH) |
| 201 | Created (POST) |
| 204 | Deleted (DELETE) |
| 400 | Validation error / bad request |
| 401 | Not authenticated |
| 403 | Not authorized |
| 404 | Resource not found |
| 409 | Conflict (duplicate, version mismatch) |
| 429 | Rate limited |
| 500 | Internal server error |

### Pagination
```
GET /api/v1/resource?limit=20&offset=0
```
```json
{
  "data": [...],
  "pagination": {
    "total": 150,
    "limit": 20,
    "offset": 0,
    "has_more": true
  }
}
```

---

## CI/CD Templates

### GitHub Actions (Python)
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.11", "3.12"]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - run: pip install -e ".[dev]"
      - run: ruff check src/ tests/
      - run: mypy src/
      - run: pytest tests/ --cov=src --cov-report=xml --cov-fail-under=80
      - run: pip audit || true
```

### GitHub Actions (Rust)
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@stable
        with:
          components: clippy, rustfmt
      - run: cargo fmt --check
      - run: cargo clippy -- -D warnings
      - run: cargo test --all-targets
      - run: cargo bench --no-run  # Verify benchmarks compile
```

### GitHub Actions (TypeScript)
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm run lint
      - run: npx tsc --noEmit
      - run: npm test -- --coverage
```

### GitHub Actions (Go)
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: "1.22"
      - run: go vet ./...
      - run: staticcheck ./... || true
      - run: go test -race -coverprofile=coverage.out ./...
      - run: go test -bench=. -benchmem ./...
```

### GitHub Actions (C/C++)
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        build-type: [Debug, Release]
    steps:
      - uses: actions/checkout@v4
      - run: sudo apt-get install -y cmake ninja-build
      - run: cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=${{ matrix.build-type }} -DSANITIZE=ON
      - run: cmake --build build
      - run: cd build && ctest --output-on-failure
      - run: cd build && valgrind --leak-check=full --error-exitcode=1 ./tests/unit_tests || true
```

---

## Python-Specific Standards

### Type Hints (Required)
```python
from typing import Optional, Sequence, Mapping, Callable, TypeVar
from dataclasses import dataclass, field

T = TypeVar("T")

@dataclass(frozen=True)
class User:
    id: str
    name: str
    email: Optional[str] = None
    metadata: Mapping[str, str] = field(default_factory=dict)

def fetch_users(
    limit: int = 10,
    *,
    offset: int = 0,
) -> Sequence[User]:
    """Fetch users from database.

    Args:
        limit: Maximum number of users to return.
        offset: Number of users to skip.

    Returns:
        Sequence of User objects.

    Raises:
        DatabaseError: If query fails.
    """
    ...

async def process_async(user: User) -> str:
    """Async handler."""
    ...
```

### Formatting
- **Line length:** 100 characters
- **Imports:** `isort` sorted, 3 groups (std, third-party, local)
- **Naming:** `snake_case` for functions/vars, `PascalCase` for classes, `UPPER_SNAKE` for constants
- **Tool:** `black` + `isort` + `ruff`

### Error Handling
```python
class ApplicationError(Exception):
    """Base application error."""
    pass

class ValidationError(ApplicationError):
    """Invalid input."""
    def __init__(self, field: str, message: str) -> None:
        self.field = field
        super().__init__(f"{field}: {message}")

class NotFoundError(ApplicationError):
    """Resource not found."""
    def __init__(self, resource: str, id: str) -> None:
        super().__init__(f"{resource} {id} not found")

def validate_email(email: str) -> str:
    if not email or "@" not in email:
        raise ValidationError("email", f"Invalid format: {email}")
    return email.lower()
```

### Logging
```python
import logging
import structlog  # preferred for structured logging

logger = structlog.get_logger()

def process_user(user_id: str) -> None:
    logger.info("processing_user", user_id=user_id)
    try:
        result = fetch_user(user_id)
        logger.debug("user_fetched", user_id=user_id, name=result.name)
    except Exception as e:
        logger.error("user_processing_failed", user_id=user_id, error=str(e), exc_info=True)
        raise
```

### Testing
```python
import pytest
from src.models import User
from src.core.validators import validate_email

class TestUserCreation:
    def test_creates_with_defaults(self) -> None:
        user = User(id="123", name="Alice")
        assert user.id == "123"
        assert user.email is None

    def test_frozen_dataclass_is_immutable(self) -> None:
        user = User(id="123", name="Alice")
        with pytest.raises(AttributeError):
            user.name = "Bob"

class TestValidateEmail:
    @pytest.mark.parametrize("email,expected", [
        ("USER@EXAMPLE.COM", "user@example.com"),
        ("test@test.io", "test@test.io"),
    ])
    def test_normalizes_to_lowercase(self, email: str, expected: str) -> None:
        assert validate_email(email) == expected

    @pytest.mark.parametrize("bad_email", ["", "no-at", None, "@no-local"])
    def test_rejects_invalid(self, bad_email: str) -> None:
        with pytest.raises(ValidationError):
            validate_email(bad_email)
```

### Versioning & Dependencies
```toml
# pyproject.toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.11"

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-cov>=4.0",
    "pytest-asyncio>=0.23",
    "pytest-benchmark>=4.0",
    "black>=24.0",
    "ruff>=0.3",
    "mypy>=1.8",
    "structlog>=24.0",
    "pip-audit>=2.7",
]

[tool.ruff]
line-length = 100
select = ["E", "F", "W", "C90", "I", "N", "UP", "B", "A", "SIM"]

[tool.mypy]
strict = true
warn_return_any = true
warn_unused_configs = true

[tool.pytest.ini_options]
asyncio_mode = "auto"
```

---

## TypeScript/JavaScript Standards

### Type Hints (Required)
```typescript
interface User {
  readonly id: string;
  name: string;
  email?: string;
  metadata?: Readonly<Record<string, string>>;
}

type Result<T> =
  | { ok: true; value: T }
  | { ok: false; error: AppError };

async function fetchUsers(
  limit: number = 10,
  options?: { offset?: number; signal?: AbortSignal },
): Promise<readonly User[]> {
  /** Fetch users from database.
   *
   * @param limit - Maximum number of users to return.
   * @param options - Query options.
   * @returns Array of User objects.
   * @throws {DatabaseError} If query fails.
   */
  ...
}
```

### Formatting
- **Line length:** 100 characters
- **Indent:** 2 spaces
- **Semicolons:** Enforce
- **Quotes:** Double quotes
- **Tool:** ESLint + Prettier

### Error Handling
```typescript
class AppError extends Error {
  constructor(
    public readonly code: string,
    message: string,
    public readonly cause?: unknown,
  ) {
    super(message);
    this.name = "AppError";
  }
}

class ValidationError extends AppError {
  constructor(
    public readonly field: string,
    message: string,
  ) {
    super("VALIDATION_ERROR", `${field}: ${message}`);
    this.name = "ValidationError";
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super("NOT_FOUND", `${resource} ${id} not found`);
    this.name = "NotFoundError";
  }
}
```

### Logging
```typescript
import pino from "pino";

const logger = pino({
  level: process.env.LOG_LEVEL ?? "info",
  transport: process.env.NODE_ENV === "development"
    ? { target: "pino-pretty" }
    : undefined,
});

async function processUser(userId: string): Promise<void> {
  const log = logger.child({ userId });
  log.info("processing_user");
  try {
    const user = await fetchUser(userId);
    log.debug({ name: user.name }, "user_fetched");
  } catch (error) {
    log.error({ error }, "user_processing_failed");
    throw error;
  }
}
```

### Testing
```typescript
import { describe, it, expect, vi, beforeEach } from "vitest";
import { validateEmail, fetchUsers } from "../src/core";

describe("validateEmail", () => {
  it("normalizes to lowercase", () => {
    expect(validateEmail("USER@EXAMPLE.COM")).toBe("user@example.com");
  });

  it.each(["", "no-at", "@no-local"])(
    "rejects invalid email: %s",
    (email: string) => {
      expect(() => validateEmail(email)).toThrow(ValidationError);
    },
  );
});

describe("fetchUsers", () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  it("returns users with default limit", async () => {
    const users = await fetchUsers();
    expect(users).toHaveLength(10);
  });

  it("respects offset parameter", async () => {
    const users = await fetchUsers(5, { offset: 10 });
    expect(users).toHaveLength(5);
  });
});
```

### Versioning & Dependencies
```json
{
  "name": "my-project",
  "version": "0.1.0",
  "engines": { "node": ">=18" },
  "scripts": {
    "build": "tsc",
    "test": "vitest run --coverage",
    "test:bench": "vitest bench",
    "lint": "eslint src/ tests/ --fix",
    "typecheck": "tsc --noEmit",
    "dev": "tsx watch src/index.ts",
    "start": "node dist/index.js",
    "audit": "npm audit --production"
  },
  "devDependencies": {
    "typescript": "^5.4",
    "vitest": "^1.6",
    "@vitest/coverage-v8": "^1.6",
    "eslint": "^9.0",
    "pino": "^9.0",
    "tsx": "^4.7"
  }
}
```

---

## Rust-Specific Standards

### Ownership & Types (Required)
```rust
use serde::{Deserialize, Serialize};
use thiserror::Error;

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct User {
    pub id: String,
    pub name: String,
    pub email: Option<String>,
}

/// Fetch users from the database.
///
/// # Errors
///
/// Returns `DbError::Query` if the database query fails.
/// Returns `DbError::Connection` if the pool is exhausted.
pub fn fetch_users(pool: &PgPool, limit: i64) -> Result<Vec<User>, DbError> {
    sqlx::query_as!(User, "SELECT id, name, email FROM users LIMIT $1", limit)
        .fetch_all(pool)
        .map_err(DbError::from)
}

/// Process a user asynchronously.
pub async fn process_user(pool: &PgPool, user_id: &str) -> Result<String, AppError> {
    let user = fetch_user_by_id(pool, user_id).await?;
    Ok(user.name.to_uppercase())
}
```

### Error Handling
```rust
use thiserror::Error;

#[derive(Debug, Error)]
pub enum AppError {
    #[error("validation error: {field}: {message}")]
    Validation { field: String, message: String },

    #[error("{resource} {id} not found")]
    NotFound { resource: String, id: String },

    #[error(transparent)]
    Database(#[from] DbError),

    #[error("internal error")]
    Internal(#[from] anyhow::Error),
}

// For libraries, use thiserror for typed errors.
// For applications, anyhow is acceptable for top-level error handling.

impl AppError {
    pub fn validation(field: impl Into<String>, message: impl Into<String>) -> Self {
        Self::Validation {
            field: field.into(),
            message: message.into(),
        }
    }

    pub fn not_found(resource: impl Into<String>, id: impl Into<String>) -> Self {
        Self::NotFound {
            resource: resource.into(),
            id: id.into(),
        }
    }
}
```

### Formatting & Linting
- **Tool:** `rustfmt` + `clippy`
- **Rustfmt:** Default settings (4-space indent, 100 char lines)
- **Clippy:** `#![deny(clippy::all, clippy::pedantic)]` — zero warnings
- **Edition:** 2021 minimum
- **Naming:** `snake_case` functions/vars, `PascalCase` types, `SCREAMING_SNAKE` constants

### Logging
```rust
use tracing::{info, debug, error, instrument};

#[instrument(skip(pool), fields(user_id = %user_id))]
pub async fn process_user(pool: &PgPool, user_id: &str) -> Result<String, AppError> {
    info!("processing_user");
    let user = fetch_user_by_id(pool, user_id).await?;
    debug!(name = %user.name, "user_fetched");
    Ok(user.name.to_uppercase())
}
```

### Testing
```rust
#[cfg(test)]
mod tests {
    use super::*;
    use rstest::rstest;

    #[rstest]
    #[case("USER@EXAMPLE.COM", "user@example.com")]
    #[case("test@test.io", "test@test.io")]
    fn test_normalize_email(#[case] input: &str, #[case] expected: &str) {
        assert_eq!(normalize_email(input), expected);
    }

    #[rstest]
    #[case("")]
    #[case("no-at")]
    #[case("@no-local")]
    fn test_reject_invalid_email(#[case] input: &str) {
        let err = validate_email(input).unwrap_err();
        assert!(matches!(err, AppError::Validation { .. }));
    }

    #[tokio::test]
    async fn test_fetch_users_returns_results() {
        let pool = setup_test_db().await;
        let users = fetch_users(&pool, 10).unwrap();
        assert!(!users.is_empty());
    }
}
```

### Unsafe Policy
- **No `unsafe` without explicit justification** — add a `// SAFETY:` comment explaining why it's sound
- **Minimize unsafe scope** — wrap in a safe abstraction, expose safe API
- **Prefer safe alternatives** — use `get()` not `[]`, `checked_sub` not `-`
- **Document invariants** — what must hold for `unsafe` to be correct

### Versioning & Dependencies
```toml
# Cargo.toml
[package]
name = "my-project"
version = "0.1.0"
edition = "2021"
rust-version = "1.75"

[dependencies]
serde = { version = "1.0", features = ["derive"] }
thiserror = "1.0"
anyhow = "1.0"
tracing = "0.1"
tokio = { version = "1.0", features = ["full"] }

[dev-dependencies]
rstest = "0.18"
criterion = { version = "0.5", features = ["html_reports"] }
tokio-test = "0.4"

[profile.release]
lto = true
codegen-units = 1
strip = true

[[bench]]
name = "core_bench"
harness = false
```

---

## C/C++ Standards

### Memory Safety (Non-Negotiable)
```cpp
// Use RAII — never raw new/delete in application code
#include <memory>
#include <vector>
#include <string>

class UserService {
public:
    // Constructor acquires, destructor releases
    explicit UserService(std::shared_ptr<Database> db) : db_(std::move(db)) {}

    std::optional<User> fetch_user(const std::string& id) const {
        if (id.empty()) {
            throw ValidationError("id", "must not be empty");
        }
        return db_->query<User>("SELECT * FROM users WHERE id = ?", id);
    }

private:
    std::shared_ptr<Database> db_;
};

// Prefer stack allocation and value semantics
auto process_items(const std::vector<int>& items) -> std::vector<int> {
    std::vector<int> results;
    results.reserve(items.size());
    for (const auto& item : items) {
        if (item > 0) {
            results.push_back(item * 2);
        }
    }
    return results; // NRVO — no copy
}
```

### Error Handling (C++17+)
```cpp
#include <stdexcept>
#include <string>
#include <expected>  // C++23, or use tl::expected for C++17

class AppError : public std::runtime_error {
public:
    AppError(const std::string& code, const std::string& message)
        : std::runtime_error(message), code_(code) {}
    const std::string& code() const { return code_; }
private:
    std::string code_;
};

class ValidationError : public AppError {
public:
    ValidationError(const std::string& field, const std::string& message)
        : AppError("VALIDATION_ERROR", field + ": " + message), field_(field) {}
    const std::string& field() const { return field_; }
private:
    std::string field_;
};

// For non-throwing code, use std::expected (C++23) or tl::expected
auto parse_config(const std::string& path)
    -> std::expected<Config, std::string> {
    if (path.empty()) {
        return std::unexpected("config path must not be empty");
    }
    // ... parse ...
    return config;
}
```

### Const Correctness
```cpp
// Mark everything const by default — remove const only when mutation is needed
class Processor {
public:
    // Const method — does not modify state
    [[nodiscard]] auto get_count() const -> size_t { return count_; }

    // Non-const — modifies state
    void process(int value) {
        validate(value);  // private const helper
        count_++;
    }

private:
    void validate(int value) const {
        if (value < 0) {
            throw ValidationError("value", "must be non-negative");
        }
    }
    size_t count_ = 0;
};
```

### Build System (CMake)
```cmake
cmake_minimum_required(VERSION 3.20)
project(my-project VERSION 0.1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Compiler warnings
add_compile_options(-Wall -Wextra -Wpedantic -Werror)

# Source
add_library(mylib
    src/core/processor.cpp
    src/models/user.cpp
    src/utils/validator.cpp
)
target_include_directories(mylib PUBLIC include/)
target_link_libraries(mylib PUBLIC fmt::fmt spdlog::spdlog)

# Tests
enable_testing()
add_executable(unit_tests
    tests/unit/test_models.cpp
    tests/unit/test_core.cpp
)
target_link_libraries(unit_tests PRIVATE mylib GTest::gtest_main)
add_test(NAME unit_tests COMMAND unit_tests)

# Benchmarks
add_executable(benchmarks benches/bench_core.cpp)
target_link_libraries(benchmarks PRIVATE mylib benchmark::benchmark)

# Sanitizers (Debug builds)
if(CMAKE_BUILD_TYPE STREQUAL "Debug" OR SANITIZE)
    add_compile_options(-fsanitize=address,undefined -fno-omit-frame-pointer)
    add_link_options(-fsanitize=address,undefined)
endif()
```

### Logging
```cpp
#include <spdlog/spdlog.h>
#include <spdlog/sinks/stdout_sinks.h>

auto process_user(const std::string& user_id) -> std::string {
    spdlog::info("processing_user: {}", user_id);
    try {
        auto user = fetch_user(user_id);
        spdlog::debug("user_fetched: name={}", user.name);
        return user.name;
    } catch (const std::exception& e) {
        spdlog::error("user_processing_failed: id={}, error={}", user_id, e.what());
        throw;
    }
}
```

### Testing (Google Test)
```cpp
#include <gtest/gtest.h>
#include "core/validator.h"

class ValidateEmailTest : public ::testing::TestWithParam<std::pair<std::string, std::string>> {};

TEST_P(ValidateEmailTest, NormalizesToLowercase) {
    auto [input, expected] = GetParam();
    EXPECT_EQ(normalize_email(input), expected);
}

INSTANTIATE_TEST_SUITE_P(
    Emails,
    ValidateEmailTest,
    ::testing::Values(
        std::make_pair("USER@EXAMPLE.COM", "user@example.com"),
        std::make_pair("test@test.io", "test@test.io")
    )
);

TEST(ValidateEmailTest, RejectsEmpty) {
    EXPECT_THROW(validate_email(""), ValidationError);
}

TEST(ValidateEmailTest, RejectsNoAt) {
    EXPECT_THROW(validate_email("no-at"), ValidationError);
}
```

### .gitignore (C/C++)
```
build/
cmake-build-*/
compile_commands.json
.cache/
*.o
*.a
*.so
*.dylib
```

---

## Go-Specific Standards

### Types & Interfaces
```go
package core

import (
    "context"
    "fmt"
)

// User represents a user in the system.
type User struct {
    ID       string `json:"id"`
    Name     string `json:"name"`
    Email    string `json:"email,omitempty"`
}

// Repository defines data access methods.
// Implement this interface for testability.
type Repository interface {
    GetUser(ctx context.Context, id string) (*User, error)
    ListUsers(ctx context.Context, opts ListOptions) ([]User, error)
}

// ListOptions controls pagination.
type ListOptions struct {
    Limit  int
    Offset int
}

// FetchUsers retrieves users from the repository.
//
// Returns ErrNotFound if no users match the criteria.
// Returns ErrInvalidInput if opts.Limit is negative.
func FetchUsers(ctx context.Context, repo Repository, opts ListOptions) ([]User, error) {
    if opts.Limit < 0 {
        return nil, fmt.Errorf("%w: limit must be non-negative", ErrInvalidInput)
    }
    if opts.Limit == 0 {
        opts.Limit = 10
    }
    return repo.ListUsers(ctx, opts)
}
```

### Error Handling
```go
package core

import (
    "errors"
    "fmt"
)

var (
    ErrNotFound     = errors.New("not found")
    ErrInvalidInput = errors.New("invalid input")
    ErrUnauthorized = errors.New("unauthorized")
)

// ValidationError provides field-level error detail.
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Message)
}

func (e *ValidationError) Unwrap() error {
    return ErrInvalidInput
}

// WrapError wraps an error with context.
func WrapError(op string, err error) error {
    return fmt.Errorf("%s: %w", op, err)
}

// Usage:
// return WrapError("fetch user", ErrNotFound)
// errors.Is(err, ErrNotFound)
// errors.As(err, &validationErr)
```

### Formatting & Linting
- **Tool:** `gofmt` + `go vet` + `staticcheck` + `golangci-lint`
- **Format:** Standard Go formatting (tabs, 1-char indent)
- **Naming:** `camelCase` unexported, `PascalCase` exported, no underscores
- **Error variables:** `ErrXxx` pattern (e.g., `ErrNotFound`)

### Logging
```go
package core

import (
    "context"
    "log/slog"
)

func ProcessUser(ctx context.Context, userID string) error {
    log := slog.With("user_id", userID)
    log.InfoContext(ctx, "processing_user")

    user, err := fetchUser(ctx, userID)
    if err != nil {
        log.ErrorContext(ctx, "user_fetch_failed", "error", err)
        return fmt.Errorf("process user: %w", err)
    }

    log.DebugContext(ctx, "user_fetched", "name", user.Name)
    return nil
}
```

### Testing
```go
package core

import (
    "context"
    "testing"

    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
)

func TestNormalizeEmail(t *testing.T) {
    tests := []struct {
        name     string
        input    string
        expected string
    }{
        {"uppercase", "USER@EXAMPLE.COM", "user@example.com"},
        {"already lowercase", "test@test.io", "test@test.io"},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            assert.Equal(t, tt.expected, NormalizeEmail(tt.input))
        })
    }
}

func TestValidateEmail_RejectsInvalid(t *testing.T) {
    tests := []struct {
        name  string
        input string
    }{
        {"empty", ""},
        {"no at", "no-at"},
        {"no local", "@no-local"},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := ValidateEmail(tt.input)
            require.Error(t, err)
            assert.ErrorIs(t, err, ErrInvalidInput)
        })
    }
}

func BenchmarkProcessItems(b *testing.B) {
    items := make([]int, 10_000)
    for i := range items {
        items[i] = i
    }
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        _, _ = ProcessItems(context.Background(), items)
    }
}
```

### Race Detection
- **Always run `go test -race`** in CI
- **Use channels or `sync.Mutex`** for shared state
- **Never close over loop variables** without passing as argument (pre-Go 1.22)

### Versioning & Dependencies
```go
// go.mod
module github.com/user/myproject

go 1.22

require (
    github.com/stretchr/testify v1.9
    golang.org/x/sync v0.7
)
```

---

## Code Taste (Subjective, Non-Negotiable)

### What "Good Taste" Means

**Clear naming:**
```python
# Bad
def process(data):
    x = data['x']
    y = x * 2
    return y

# Good
def calculate_double_value(config: dict[str, int]) -> int:
    """Double the configured value."""
    initial_value = config['initial_value']
    return initial_value * 2
```

**Minimal nesting (early return):**
```rust
// Bad (3+ levels deep)
fn process(user: Option<User>) -> bool {
    if let Some(u) = user {
        if let Some(email) = u.email {
            if email.contains('@') {
                return true;
            }
        }
    }
    false
}

// Good (guard clauses)
fn has_valid_email(user: &Option<User>) -> bool {
    let Some(u) = user else { return false };
    let Some(email) = &u.email else { return false };
    email.contains('@')
}
```

**One responsibility per function:**
```go
// Bad (does 3 things)
func processAndSaveAndNotify(userID string) error {
    user := fetchUser(userID)
    processUser(user)
    saveToDB(user)
    sendEmail(user)
    return nil
}

// Good (one per function)
func fetchAndValidate(ctx context.Context, userID string) (*User, error) {
    user, err := repo.GetUser(ctx, userID)
    if err != nil { return nil, fmt.Errorf("fetch: %w", err) }
    if err := validate(user); err != nil { return nil, fmt.Errorf("validate: %w", err) }
    return user, nil
}
```

**Comment the "why", not the "what":**
```typescript
// Bad (obvious)
const age = user.age; // Get the user's age
if (age > 18) { // Check if age is greater than 18
  sendEmail(); // Send an email
}

// Good (explains intent)
const age = user.age;
// Only notify users 18+ due to GDPR requirements
if (age >= 18) {
  sendEmail(user.email, "Important Update");
}
```

---

## Pre-Delivery Checklist (Claude Code)

Before returning code, verify:

- [ ] **All source files complete** — no TODOs, no stubs, no `// TODO: implement X`
- [ ] **Type hints 100%** — `mypy` / `tsc` / `cargo clippy` passes with no errors
- [ ] **Tests pass** — all tests green, ≥80% coverage, zero skips
- [ ] **Benchmarks exist** — hot paths have `benches/` entries
- [ ] **Linting clean** — `ruff` / `eslint` / `clippy` / `go vet` pass, zero warnings
- [ ] **Security audit** — `pip audit` / `npm audit` / `cargo audit` clean
- [ ] **Setup script works** — `./setup.sh && make test` succeeds on clean system
- [ ] **README complete** — all sections present, examples run, installation instructions tested (see README Generation Spec)
- [ ] **CHANGELOG current** — all changes documented under `[Unreleased]` with correct categories
- [ ] **Docs synced with code** — README examples match actual APIs, config table matches actual env vars
- [ ] **No secrets in code** — all config in `.env.example`, no API keys/tokens
- [ ] **Git-ready** — `.gitignore` correct, `git status` shows only new files
- [ ] **Error messages clear** — users understand what went wrong and how to fix it
- [ ] **Logging added** — INFO/DEBUG/ERROR levels appropriate, no blind spots
- [ ] **Naming consistent** — no `temp_var`, no `x`, no `do_thing_2()`
- [ ] **CI config present** — `.github/workflows/ci.yml` (or equivalent) included
- [ ] **Concurrency safe** — no data races, bounded queues, graceful shutdown

---

## Final Handoff

When code is ready:

```
DELIVERABLES
├── src/                # All source code
├── tests/              # Full test suite
├── benches/            # Performance benchmarks
├── .github/workflows/  # CI configuration
├── README.md           # Full project README (see README Generation Spec)
├── CHANGELOG.md        # Version history (Keep a Changelog format)
├── .env.example        # Config template
├── Makefile / scripts/ # Build tools
└── .gitignore          # Language-appropriate ignores

Ready to: git add . && git commit -m "feat: Initial [project]"
```

---

## When Claude Code Falls Short

If generated code doesn't meet this standard:

1. **Call it out immediately** — "This violates section [X] of the contract"
2. **Be specific** — "Missing error handling in `fetch_user()`" not "bad code"
3. **Rerun with clarification** — "Regenerate `src/api.ts` with explicit error types and 100% test coverage"
4. **Expect full file** — Don't ask for "just add line X"; request the entire fixed module

---

## Attached to Every Session

**Always include this contract in your Claude Code session:**
```
system_prompt: "You are a production code generator. Follow CLAUDE-CODE-CONTRACT.md completely."
context: [CLAUDE-CODE-CONTRACT.md, CODING-CONTEXT.md, project-specific files]
```

This ensures consistency and prevents regressions to lower-quality patterns.
