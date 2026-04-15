# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Swift (iOS / macOS / Server) language support with full standards — Swift 6.x strict concurrency, SwiftUI patterns, `@Observable` view models, Swift Testing framework, `os.Logger`, typed throws, SPM configuration, snapshot testing, CI template
- iOS App project type adaptation with directory structure template (Feature-based architecture with SwiftUI)
- iOS-specific visual validation — snapshot testing (`swift-snapshot-testing`), Dynamic Type testing, dark/light mode, device rotation
- iOS-specific accessibility validation — `performAccessibilityAudit()`, VoiceOver labels, Dynamic Type, semantic traits, `.accessibilityElement(children:)` patterns
- Visual Validation Contract for UI/frontend projects — screenshot regression testing, responsive breakpoint checks, loading state verification, dark/light mode validation
- Accessibility Contract (WCAG 2.1 AA) — keyboard navigation, color contrast, semantic HTML, ARIA labels, automated audit requirements (axe-core, Lighthouse ≥90)
- Frontend/Web App and Full-Stack App project type adaptations with directory structure templates
- UI/Visual Requirements section in TASK-BRIEF-TEMPLATE with design reference, visual acceptance criteria, and accessibility requirements
- Cross-phase integration testing in multi-agent framework — full project test suite, e2e tests, and project-level visual/a11y checks before integration review
- Visual and accessibility checks in Reviewer prompt (step 10)
- Visual/a11y acceptance criteria examples in PHASE-BRIEF-TEMPLATE
- Visual Validation and Accessibility sections (steps 11-12) in Master Generator prompt
- Accessibility validation section in CODING-CONTEXT with tool commands and quick reference

### Changed
- Mandatory deliverables now scale by mode — Debug/Refactor no longer require benchmarks, setup scripts, CI config, or full README from scratch
- Validation Gate commands are no longer hardcoded to `make` — now adapts to project's build system (Makefile, npm, cargo, go, etc.)
- Coverage threshold (80%) now flexible via task brief or project config; pure-config repos can declare exemptions
- Documentation Sync relaxed for trivial changes — internal refactors, dependency bumps, typo fixes, and test-only changes exempt from CHANGELOG
- Design Thinking Gate criteria clarified — required criteria explicitly take priority when overlapping with optional criteria
- Pre-Delivery Checklist reorganized by mode applicability (universal, generate-only, UI-only, doc-sync)
- README updated to list all 8 task scenarios mapped to 5 prompt modes
- Master Generator prompt now explicitly covers Add/Remove/Modify Function scenarios with scaled deliverables
- Final Handoff format updated to include mode, UI-specific items, and conditional sections
- Integration Reviewer prompt expanded to check cross-module interface mismatches and UI-level concerns

### Fixed
- Mode coverage gap: Add Function, Remove Function, Modify Behavior, and Full Rewrite scenarios now have explicit guidance in Master Generator prompt
- Hardcoded `make test`/`make lint`/`make bench` commands replaced with project-agnostic validation gate
- One-size-fits-all deliverables replaced with mode-scaled requirements
- Missing UI project type in architecture standards
- Missing accessibility standards for user-facing projects
- No cross-phase integration testing in multi-agent framework
- Ambiguous overlap between Design Thinking Gate required/optional criteria
- Overly rigid doc sync requirement for non-behavior-changing code changes

## [2.0.0] - 2026-03-30

### Added
- Multi-agent orchestration mode with Orchestrator, Implementer, and Reviewer roles
- `MULTI-AGENT-FRAMEWORK.md` defining phase pipeline, state handoff, and parallel execution rules
- `PHASE-BRIEF-TEMPLATE.md` for per-phase briefs in orchestrated projects
- `PROMPT-ORCHESTRATOR.md`, `PROMPT-IMPLEMENTER.md`, `PROMPT-REVIEWER.md` prompt files
- Documentation Sync Contract enforcing README and CHANGELOG updates with every code change
- CHANGELOG contract mandating Keep a Changelog format for all project types
- README generation spec with 16 required sections and quality rules
- Design Thinking Gate as a mandatory pre-TDD step for non-trivial logic
- `THINKING-FRAMEWORKS.md` for problem decomposition, algorithm design, and novel domains
- Churn detection in TDD retry loop to prevent guessing
- Concurrency and async pattern standards with examples for Python, Rust, Go, TypeScript
- API design standards (REST conventions, response/error formats, pagination)
- CI/CD templates for Python, Rust, TypeScript, Go, and C/C++
- Code taste section (naming, nesting, single responsibility, comment guidelines)
- Pre-delivery checklist and final handoff format
- Token efficiency guide for attaching only necessary files per session type

### Changed
- Contract scope expanded from Generate-only to all code change scenarios (Generate, Debug, Refactor, Partial Rewrite, Full Rewrite, Add/Remove/Modify Function)
- Validation protocol now includes retry cap (3 attempts) and churn detection
- Quality standards updated to require documentation sync enforcement

## [1.2.0] - 2026-03-25

### Added
- Refactor mode with `PROMPT-REFACTOR.md`
- Debug mode with `PROMPT-DEBUG.md`
- Partial Rewrite mode with `PROMPT-PARTIAL-REWRITE.md`
- `CLAUDE-CODE-CHEATSHEET.md` quick reference with language-specific templates

### Changed
- Contract updated to cover all task modes (not just generation)

## [1.1.0] - 2026-03-25

### Added
- C, C++, Rust, and Go language support with full standards
- Language-specific coding standards, formatters, linters, and benchmark tools

### Fixed
- Replaced non-existent `/add` command with correct `@` file reference syntax
- Clarified CLI usage and `PROMPT-CLAUDE-CODE-MASTER.md` purpose
- Removed duplicate documentation

## [1.0.0] - 2026-03-25

### Added
- Initial contract system (`CLAUDE-CODE-CONTRACT.md`)
- Core principles: TDD, validation, completeness, security, performance
- `PROMPT-CLAUDE-CODE-MASTER.md` for code generation
- `TASK-BRIEF-TEMPLATE.md` for defining project requirements
- `CODING-CONTEXT.md` for language-specific rules
- `CLAUDE-CODE-GUIDE.md` detailed walkthrough
- Python and TypeScript language support
- Architecture standards with standard directory structure
- Performance contract with benchmark requirements
- Security contract (input validation, secrets, dependencies)

[Unreleased]: https://github.com/wngfra/Claude-Code-Contracts/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/wngfra/Claude-Code-Contracts/compare/v1.2.0...v2.0.0
[1.2.0]: https://github.com/wngfra/Claude-Code-Contracts/compare/v1.1.0...v1.2.0
[1.1.0]: https://github.com/wngfra/Claude-Code-Contracts/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/wngfra/Claude-Code-Contracts/releases/tag/v1.0.0
