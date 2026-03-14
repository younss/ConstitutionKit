# ConstitutionKit Constitution

## Core Principles

### I. Single-File, Browser-Only
ConstitutionKit is a zero-dependency single-file static site (`index.html`). There is no build step, no bundler, no server. All logic runs in the browser. Changes that introduce build tools, package.json dependencies, or server-side components require explicit justification and a new ADR.

### II. Output Generators Are Pure Functions
Functions that build file content (`buildConstitution`, `buildClaudeMd`, `buildFlutterAddendum`, etc.) must be pure: they read only from their arguments, not from the DOM or global mutable state. This makes them independently testable and composable without side effects.

### III. Open/Closed for Platform Support
Adding support for a new platform (e.g. React Native, SwiftUI) follows the addendum pattern established in ADR-001: write a new pure `build[Platform]Addendum()` function and wire it with a single conditional at the output stage. Do not embed new platform logic inside the base generators.

### IV. No Speculative Generality (YAGNI)
Do not add abstractions, configuration options, or extension points without a concrete current use case. A longer but readable function is preferred over a terse abstraction designed for hypothetical future requirements.

### V. HTML Escaping Before innerHTML
Any user-controlled content (textarea values, URL parameters) must be passed through the `esc()` function before being rendered via `innerHTML`. Static/hardcoded content from JS constants does not require escaping but should use `textContent` where possible.

### VI. Privacy by Default
Google Analytics is gated behind explicit user consent (`localStorage` key `ck_consent`). The consent banner must remain functional. Do not load any third-party tracking without consent.

## Development Workflow

- Edit `index.html` directly — no build step
- Open in a browser to test; use browser DevTools console for debugging
- Test the preview pane with Markdown that includes code fences and tables (the Flutter addendum uses both)
- Test share config by clicking "Share config", copying the URL, and opening it in a fresh tab — all Step 4 fields must round-trip correctly

## Quality Gates

- No `console.log` statements in shipped code
- All user-facing textarea content goes through `esc()` before `innerHTML`
- New project types require: type card in HTML, entry in `TYPE_LABELS`, entry in `PRESETS`, and (if platform-specific output is needed) a new addendum function

## Governance

This constitution supersedes all ad-hoc conventions. Amendments require a corresponding ADR in `.specify/adr/`. The constitution itself lives in `.specify/memory/constitution.md` and is read by agents operating on this repository.

**Version**: 1.0.0 | **Ratified**: 2026-03-14 | **Last Amended**: 2026-03-14
