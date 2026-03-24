# CODING_CONTEXT.md - Persistent Code Context

## Tech Stack

- **Languages:** Python, TypeScript, Bash, Go (primary)
- **Build:** uv, npm, cargo
- **Testing:** pytest, vitest, go test
- **Linting:** ruff, eslint, gofmt

## Coding Standards

- **Python:** Type hints required, Black formatting, 120 char lines
- **TypeScript:** Strict mode, 4-space indent, ESLint strict
- **Bash:** ShellCheck passing, set -euo pipefail
- **Error handling:** Explicit, no silent failures

## Project Structure

```
workspace/
├── skills/           # Agent skills (build123d-cad, etc.)
├── cad-scripts/      # CAD generation scripts
├── cad-output/       # Generated STEP/STL files
└── openclaw-configs/ # Config backup from git
```

## Common Patterns

### API/Web
- Error status codes: 200/201 success, 400 bad input, 404 not found, 500 server error
- Response format: `{ "success": bool, "data": T, "error": string }`
- Rate limiting: Respect 429/Retry-After headers

### File Operations
- Use `trash` for deletions (recoverable)
- Always preserve backups before destructive ops
- Generated files → `/cad-output/`, `/workspace/`

### Shell Scripting
- Prefer `exec` with `yieldMs` for long operations
- Spawn subagents for iterative work
- Log output to files, not just console

## Build Commands (Quick Reference)

| Task | Command |
|------|---------|
| Run Python | `python3 script.py` or `.venv/bin/python` |
| Node project | `npm run dev` or `npm test` |
| Rust/Go | `cargo run` or `go run ./...` |
| Type check | `pyright` or `tsc --noEmit` |
| Lint | `ruff check .` or `eslint .` |

## Visual Validation (ALL GUI/Web Projects — MANDATORY)

> **Lesson learned:** A project shipped with 327 tests, 0 lint errors, and a **completely blank page** because Gradio 6.x moved `theme`/`css` to `launch()`. Logic tests cannot see pixels. Visual validation is a separate, mandatory gate.

**Rule: If users look at a screen to use your project, visual tests are required.**

### What Always Required

| Check | Requirement |
|-------|-------------|
| `tests/ui/` exists | Must exist for any project with visual output |
| App starts | `test_app_starts_and_responds()` — root URL returns 200 |
| Content visible | `test_app_renders_components()` — component tree has expected elements |
| Handlers work | `test_handler_callbacks_execute()` — event callbacks run, return expected types |
| No startup errors | `test_no_startup_errors()` — no ERROR/Traceback in stdout |
| Version compat | `test_framework_version_compatibility()` — version in supported range |

### Gradio Specific

- **V0:** Gradio 6+ moved `theme`/`css` from `Blocks()` constructor → `launch()`. Always check.
- **No `gr.update()`:** Return component instances directly: `gr.Dropdown(choices=...)` not `gr.update(choices=...)`
- **Async handlers:** Use `async def` wrappers — never sync lambdas wrapping coroutines
- **Pin major versions:** `gradio>=5.0,<7` — silent breaking changes happen between majors
- **Test app factory:** `create_app()` must return valid `gr.Blocks` with all tabs and handlers wired
- **Test launch lifecycle:** `app.launch()` + `app.close()` without crash

### Web/JS Specific

- **Mount point:** HTML entry file must contain root element (`#app`, `#root`, `#canvas`)
- **Container dimensions:** Root element must have explicit width/height (not `height: 0`)
- **DevTools console:** Zero errors on startup — any error = FAIL
- **Canvas visibility:** Canvas in DOM with non-zero `getBoundingClientRect()`

### What Counts as Visual FAIL

- Blank page / blank screen (uniform color)
- Spinner that never resolves
- App starts but shows only title, no content
- Clicking a button produces no visible change
- Console errors on startup
- Deprecated framework API warnings

### Test Location

```
tests/
└── ui/
    ├── test_ui_integration.py   # App factory, wiring, lifecycle
    ├── test_visual_smoke.py     # Startup, blank page, content visible
    └── test_handlers.py         # Event handler callbacks
```

## Cache-Friendly Patterns

- Include full file context upfront (don't ask "what's in X?")
- Use consistent naming across projects
- Batch similar requests (3+ CAD designs, then 3+ code reviews)
- Preserve tool outputs in context for multi-step work
