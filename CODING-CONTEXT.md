# CODING-CONTEXT.md — Language & Environment Rules

**This file is your persistent context. Edit it to match your stack and preferences.**

---

## Tech Stack

- **Languages:** Python, TypeScript, Rust, Go, C/C++, Swift
- **Build:** uv, npm, cargo, go, cmake, xcodebuild / swift build
- **Testing:** pytest, vitest, cargo test, go test, Google Test, Swift Testing / XCTest
- **Linting:** ruff, eslint, clippy, go vet, clang-tidy, SwiftLint

## Coding Standards Quick Reference

| Language | Formatter | Linter | Type Check | Test | Benchmark |
|---|---|---|---|---|---|
| **Python** | black | ruff | mypy (strict) | pytest --cov | pytest-benchmark |
| **TypeScript** | prettier | eslint | tsc --noEmit | vitest --coverage | vitest bench |
| **Rust** | rustfmt | clippy (-D warnings) | cargo check | cargo test | cargo bench (criterion) |
| **Go** | gofmt | go vet + staticcheck | go build | go test -race -cover | go test -bench |
| **C/C++** | clang-format | clang-tidy | compiler warnings | gtest | google benchmark |
| **Swift** | swift-format | SwiftLint (--strict) | Swift compiler (strict concurrency) | swift test (Swift Testing) | package-benchmark |

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

### Swift (iOS / macOS / Server)
- Version: Swift 6.x (6.3+ recommended). Strict concurrency checking enabled by default
- UI: SwiftUI-first for new projects; UIKit via `UIViewRepresentable` when needed
- Formatting: `swift-format` (Apple's, `apple/swift-format`) — integrated as SPM plugin
- Linting: SwiftLint (`realm/SwiftLint`) with `.swiftlint.yml`, `--strict` in CI
- Error handling: `throws` / `do-catch` for synchronous; typed throws (`throws(MyError)`) for library APIs with well-defined error domains; `Result<Success, Failure>` only when async/await is unavailable
- Concurrency: Swift structured concurrency — `async/await`, `Actor`, `@Sendable`, `TaskGroup`, `AsyncSequence`. Avoid `@unchecked Sendable` except for genuine bridge cases. Enable `-strict-concurrency=complete`
- Logging: `os.Logger` (from `import os`) for app targets — structured, performant, integrates with Console.app. Use `swift-log` (`apple/swift-log`) for server-side / cross-platform packages
- Testing: Swift Testing (`import Testing`, `@Test`, `#expect`) for new unit tests. XCTest still required for UI tests and performance measurements. Migrate incrementally
- Benchmarks: `package-benchmark` (`ordo-one/package-benchmark`) for SPM packages; `XCTMetric` / `measure {}` for Xcode targets
- Dependencies: Swift Package Manager (`Package.swift` with `swift-tools-version:6.0`+). `Package.resolved` committed. CocoaPods/Carthage are legacy — avoid for new projects
- Security audit: No built-in `spm audit`. Use GitHub Dependabot / Snyk for SPM dependency scanning, or OWASP Dependency-Check with Swift analyzer
- Minimum deployment: iOS 17+ for new apps (iOS 16 if broader reach needed)
- Naming: `camelCase` for functions/properties, `PascalCase` for types/protocols, `UPPER_SNAKE_CASE` for static constants
- Access control: Default to `private`/`internal`; use `public` only for intentional API surface. Prefer `final` classes unless inheritance is designed for
- Protocols: Prefer protocol-oriented design over class inheritance. Keep protocols small (1-3 requirements)

### Bash
- ShellCheck passing
- `set -euo pipefail` at top of every script
- Quote all variables: `"$var"` not `$var`
- Use `[[ ]]` over `[ ]`
- Functions over inline scripts for anything >20 lines

---

## Build Commands (Quick Reference)

| Task | Python | TypeScript | Rust | Go | C/C++ | Swift |
|---|---|---|---|---|---|---|
| **Install** | `uv sync` | `npm ci` | `cargo build` | `go mod download` | `cmake -B build && cmake --build build` | `swift package resolve` |
| **Run** | `python -m src.main` | `npm start` | `cargo run` | `go run ./cmd/...` | `./build/myapp` | `swift run` / `xcodebuild` |
| **Test** | `pytest tests/ --cov=src` | `npm test` | `cargo test` | `go test -race ./...` | `cd build && ctest` | `swift test --enable-code-coverage` |
| **Lint** | `ruff check . && mypy src/` | `npm run lint && tsc --noEmit` | `cargo clippy -- -D warnings` | `go vet ./...` | `clang-tidy src/**` | `swiftlint --strict` |
| **Format** | `black . && isort .` | `npx prettier --write .` | `cargo fmt` | `gofmt -w .` | `clang-format -i src/**` | `swift-format format -ir Sources/` |
| **Benchmark** | `pytest --benchmark-only` | `npm run bench` | `cargo bench` | `go test -bench=. -benchmem` | `./build/benchmarks` | `swift package benchmark` |
| **Audit** | `pip audit` | `npm audit` | `cargo audit` | `govulncheck ./...` | Manual review | Dependabot / Snyk |

---

## Visual Validation (ALL GUI/Web Projects — MANDATORY)

**Rule: If users look at a screen to use your project, visual tests are required.** See `CLAUDE-CODE-CONTRACT.md § Visual Validation Contract` for the full spec.

### Smoke Checks (All UI Projects)

| Check | Requirement |
|---|---|
| App starts | Root URL returns 200 |
| Content visible | Component tree has expected elements |
| Handlers work | Event callbacks execute and return expected types |
| No startup errors | Zero ERROR/Traceback in stdout |
| DevTools console | Zero errors on load |
| Container dimensions | Root element has non-zero width/height |

### Screenshot Regression (All UI Projects)

| Tool | Command | Config |
|---|---|---|
| **Playwright** | `npx playwright test --project=visual` | `playwright.config.ts` with `toHaveScreenshot()` |
| **Cypress** | `npx cypress run --spec "cypress/e2e/visual.cy.ts"` | `cypress-image-snapshot` plugin |
| **Storybook** | `npx chromatic --project-token=...` | Chromatic or Percy integration |

Baseline screenshots live in `tests/visual/` or alongside e2e tests. Review diffs manually on first run; CI rejects unexpected diffs after baseline is set.

### Responsive Breakpoints

Test at minimum: 320px (mobile), 768px (tablet), 1024px (desktop), 1440px (wide). Use Playwright viewport sizes or CSS `@media` tests.

### Web/JS Specific
- HTML entry must contain root element (`#app`, `#root`, `#canvas`)
- Root element must have explicit `width`/`height` (not `height: 0`)
- Canvas in DOM with non-zero `getBoundingClientRect()`

### iOS / SwiftUI Specific
- App launches to the expected initial screen in Simulator
- All views render with non-zero frames (no collapsed layouts)
- Navigation flows work (push, pop, tab switching, modal presentation)
- Dynamic Type renders correctly at all text sizes (use `@Environment(\.dynamicTypeSize)`)
- Dark mode and light mode both render correctly
- Device rotation handles layout transitions (if applicable)
- UI tests via XCTest UI Testing framework (`XCUIApplication`, `XCUIElement`)

**Snapshot Testing (iOS):**

| Tool | Package | Usage |
|---|---|---|
| **swift-snapshot-testing** | `pointfreeco/swift-snapshot-testing` | `assertSnapshot(of: view, as: .image)` |
| **XCTest screenshots** | Built-in | `XCUIScreen.main.screenshot()` in UI tests |

```swift
// Snapshot test example (pointfreeco/swift-snapshot-testing)
import SnapshotTesting
import SwiftUI
import XCTest

final class HomeViewSnapshotTests: XCTestCase {
    func testHomeView() {
        let view = HomeView(viewModel: .mock)
        assertSnapshot(of: view, as: .image(layout: .device(config: .iPhone13)))
    }

    func testHomeViewDarkMode() {
        let view = HomeView(viewModel: .mock)
            .environment(\.colorScheme, .dark)
        assertSnapshot(of: view, as: .image(layout: .device(config: .iPhone13)))
    }

    func testHomeViewAccessibilitySize() {
        let view = HomeView(viewModel: .mock)
            .environment(\.dynamicTypeSize, .accessibility3)
        assertSnapshot(of: view, as: .image(layout: .device(config: .iPhone13)))
    }
}
```

---

## Accessibility Validation (ALL GUI/Web/iOS Projects — MANDATORY)

**Rule: If users look at a screen, accessibility is required.** See `CLAUDE-CODE-CONTRACT.md § Accessibility Contract` for the full WCAG 2.1 AA spec.

### Automated Checks (Web)

| Tool | Command | Threshold |
|---|---|---|
| **axe-core** (Playwright) | `npx playwright test --grep @a11y` | Zero violations |
| **axe-core** (Cypress) | `cy.injectAxe(); cy.checkA11y()` | Zero violations |
| **Lighthouse** | `npx lighthouse <url> --only-categories=accessibility` | Score ≥ 90 |
| **eslint-plugin-jsx-a11y** | `npm run lint` (included in eslint config) | Zero warnings |

### Automated Checks (iOS / SwiftUI)

| Tool | Command | Threshold |
|---|---|---|
| **Xcode Accessibility Inspector** | Xcode → Open Developer Tool → Accessibility Inspector | Zero warnings on audit |
| **performAccessibilityAudit()** | XCTest UI tests (Xcode 15+) | Zero audit issues |
| **Accessibility Inspector audit** | Run per-screen in Simulator | All elements labeled, traits correct |

```swift
// XCTest accessibility audit example (Xcode 15+)
import XCTest

final class AccessibilityTests: XCTestCase {
    func testHomeScreenAccessibility() throws {
        let app = XCUIApplication()
        app.launch()
        try app.performAccessibilityAudit()
    }

    func testSettingsScreenAccessibility() throws {
        let app = XCUIApplication()
        app.launch()
        app.tabBars.buttons["Settings"].tap()
        try app.performAccessibilityAudit()
    }
}
```

### iOS / SwiftUI Accessibility Requirements

- **VoiceOver labels:** Every interactive element has `.accessibilityLabel()`. Decorative images use `.accessibilityHidden(true)`
- **Dynamic Type:** Use system fonts (`.font(.body)`, `.font(.title)`). Test at all Dynamic Type sizes including accessibility sizes
- **Color contrast:** ≥4.5:1 for body text, ≥3:1 for large text. Use semantic colors (`.primary`, `.secondary`) or verified custom colors
- **Traits:** Correct `.accessibilityAddTraits()` — `.isButton` for tappable elements, `.isHeader` for section headers, `.isLink` for links
- **Hints:** `.accessibilityHint()` for non-obvious actions ("Double-tap to open settings")
- **Reduced motion:** Respect `UIAccessibility.isReduceMotionEnabled` or `@Environment(\.accessibilityReduceMotion)`. No essential information conveyed only through animation
- **Rotor support:** Custom rotor items for complex navigation (e.g., map pins, data table rows)
- **Semantic grouping:** Use `.accessibilityElement(children: .combine)` to group related elements into a single VoiceOver target

```swift
// SwiftUI accessibility example
struct ProductCard: View {
    let product: Product

    var body: some View {
        VStack(alignment: .leading) {
            AsyncImage(url: product.imageURL)
                .accessibilityHidden(true) // decorative

            Text(product.name)
                .font(.headline)
                .accessibilityAddTraits(.isHeader)

            Text(product.price, format: .currency(code: "USD"))

            Button("Add to Cart") {
                addToCart(product)
            }
            .accessibilityHint("Adds \(product.name) to your shopping cart")
        }
        .accessibilityElement(children: .combine)
    }
}
```

### Quick Reference (Web)

```typescript
// Playwright a11y test example
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

test('homepage has no accessibility violations @a11y', async ({ page }) => {
  await page.goto('/');
  const results = await new AxeBuilder({ page }).analyze();
  expect(results.violations).toEqual([]);
});
```

### Checklist (Before Release — All Platforms)
- [ ] Screen reader test — VoiceOver (iOS/macOS) or NVDA (Windows) makes sense
- [ ] All interactive elements labeled and reachable
- [ ] Dynamic Type / zoom to 200% — no clipping or content loss
- [ ] `prefers-reduced-motion` / `accessibilityReduceMotion` respected
- [ ] Color contrast ≥ 4.5:1 (normal text), ≥ 3:1 (large text)
- [ ] (Web) Tab through every page — logical focus order
- [ ] (iOS) VoiceOver swipe-through every screen — logical reading order

---

## Cache-Friendly Patterns (for LLM Sessions)

- Include full file context upfront (don't ask "what's in X?")
- Use consistent naming across projects
- Batch similar requests
- Preserve tool outputs in context for multi-step work
