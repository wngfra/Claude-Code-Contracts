# CODING-CONTEXT.md — Language & Environment Rules

**This file is your persistent context. Edit it to match your stack and preferences.**

---

## Tech Stack

- **Languages:** Python, TypeScript, Rust, Go, C/C++
- **Build:** uv, npm, cargo, go, cmake
- **Testing:** pytest, vitest, cargo test, go test, Google Test
- **Linting:** ruff, eslint, clippy, go vet, clang-tidy

## Coding Standards Quick Reference

| Language | Formatter | Linter | Type Check | Test | Benchmark |
|---|---|---|---|---|---|
| **Python** | black | ruff | mypy (strict) | pytest --cov | pytest-benchmark |
| **TypeScript** | prettier | eslint | tsc --noEmit | vitest --coverage | vitest bench |
| **Rust** | rustfmt | clippy (-D warnings) | cargo check | cargo test | cargo bench (criterion) |
| **Go** | gofmt | go vet + staticcheck | go build | go test -race -cover | go test -bench |
| **C/C++** | clang-format | clang-tidy | compiler warnings | gtest | google benchmark |

## Language-Specific Rules

### Python
- Type hints: 100% on public APIs, `strict = true` in mypy config
- Formatting: black (100 char lines), isort (3 groups)
- Error handling: Custom exception hierarchy inheriting from `ApplicationError`
- Logging: `structlog` preferred, `logging` fallback
- Dependencies: `pyproject.toml` with pinned ranges, `uv` for management
- Minimum version: Python 3.11+

### TypeScript
- Strict mode enabled — zero `any` types
- Formatting: Prettier (2-space indent, semicolons, double quotes)
- Error handling: Custom error classes extending `AppError`
- Logging: `pino` with structured JSON output
- Dependencies: `package.json` with exact or caret ranges, lockfile committed
- Minimum version: Node 18+

### Rust
- Edition: 2021 minimum, MSRV documented in Cargo.toml
- Clippy: `#![deny(clippy::all, clippy::pedantic)]` — zero warnings
- Error handling: `thiserror` for library errors, `anyhow` for application errors
- Logging: `tracing` with structured spans
- Unsafe: Forbidden without `// SAFETY:` justification and minimized scope
- Async: `tokio` runtime, document `Send + Sync` bounds
- Dependencies: Cargo.lock committed

### Go
- Version: 1.22+ (take advantage of range-over-int, improved stdlib)
- Formatting: Standard gofmt (tabs, enforced by toolchain)
- Error handling: `error` interface, `%w` wrapping, sentinel errors (`ErrXxx`)
- Logging: `log/slog` (stdlib, structured)
- Race detection: `go test -race` always in CI
- Interfaces: Define at consumer, keep small (1-3 methods)
- No underscores in names (except test files and cgo)

### C/C++
- Standard: C++20 minimum (use `std::expected`, `std::span`, concepts)
- Build: CMake 3.20+, Ninja generator preferred
- Memory: RAII only, no raw `new`/`delete` in application code
- Smart pointers: `std::unique_ptr` by default, `std::shared_ptr` only when ownership is truly shared
- Const correctness: Everything `const` by default
- Sanitizers: AddressSanitizer + UndefinedBehaviorSanitizer in Debug builds
- Testing: Google Test + Google Benchmark
- Formatting: clang-format (Google style or project-specific .clang-format)
- Linting: clang-tidy enabled
- Header guards: `#pragma once`

### Bash
- ShellCheck passing
- `set -euo pipefail` at top of every script
- Quote all variables: `"$var"` not `$var`
- Use `[[ ]]` over `[ ]`
- Functions over inline scripts for anything >20 lines

---

## Build Commands (Quick Reference)

| Task | Python | TypeScript | Rust | Go | C/C++ |
|---|---|---|---|---|---|
| **Install** | `uv sync` | `npm ci` | `cargo build` | `go mod download` | `cmake -B build && cmake --build build` |
| **Run** | `python -m src.main` | `npm start` | `cargo run` | `go run ./cmd/...` | `./build/myapp` |
| **Test** | `pytest tests/ --cov=src` | `npm test` | `cargo test` | `go test -race ./...` | `cd build && ctest` |
| **Lint** | `ruff check . && mypy src/` | `npm run lint && tsc --noEmit` | `cargo clippy -- -D warnings` | `go vet ./...` | `clang-tidy src/**` |
| **Format** | `black . && isort .` | `npx prettier --write .` | `cargo fmt` | `gofmt -w .` | `clang-format -i src/**` |
| **Benchmark** | `pytest --benchmark-only` | `npm run bench` | `cargo bench` | `go test -bench=. -benchmem` | `./build/benchmarks` |
| **Audit** | `pip audit` | `npm audit` | `cargo audit` | `govulncheck ./...` | Manual review |

---

## Visual Validation (ALL GUI/Web Projects — MANDATORY)

**Rule: If users look at a screen to use your project, visual tests are required.**

| Check | Requirement |
|---|---|
| App starts | Root URL returns 200 |
| Content visible | Component tree has expected elements |
| Handlers work | Event callbacks execute and return expected types |
| No startup errors | Zero ERROR/Traceback in stdout |
| DevTools console | Zero errors on load |
| Container dimensions | Root element has non-zero width/height |

### Web/JS Specific
- HTML entry must contain root element (`#app`, `#root`, `#canvas`)
- Root element must have explicit `width`/`height` (not `height: 0`)
- Canvas in DOM with non-zero `getBoundingClientRect()`

---

## Cache-Friendly Patterns (for LLM Sessions)

- Include full file context upfront (don't ask "what's in X?")
- Use consistent naming across projects
- Batch similar requests
- Preserve tool outputs in context for multi-step work
