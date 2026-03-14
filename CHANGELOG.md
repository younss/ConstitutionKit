# Changelog

All notable changes to ConstitutionKit are documented here.

## [2.1.0] — 2026-03-14

### Added
- **Flutter support** — new Flutter App project type, Flutter/Dart/Firebase stack tags, Flutter/Dart preset, and Flutter & Dart principle category (8 principles: Widget Architecture, State Management, TDD, Performance, Platform Channels, DI, Navigation, Accessibility)
- `buildFlutterAddendum()` and `buildFlutterClaudeAddendum()` pure functions for OCP-compliant output composition
- `.specify/` directory: ADR-001, SpecKit templates (constitution, plan, tasks, spec, checklist, agent), scripts, and memory files
- `.github/agents/` and `.github/prompts/` — SpecKit agent definitions and prompts
- **GDPR consent banner** — Google Analytics now loads only after explicit user consent; consent stored in `localStorage`
- **Markdown renderer improvements** — preview pane now correctly renders triple-backtick code fences and pipe tables
- **Share config now includes Step 4** — project description, build commands, forbidden paths, key directories, autonomy level, and team size are all serialised into the share URL

### Fixed
- DRY violation: extracted `downloadFile(filename, content)` utility, removing two duplicate inline blob download sequences
- `saveToURL` / `loadFromURL` previously silently dropped all Step 4 (project context) fields from share links

### Changed
- Version bumped to `2.1.0` in footer (MINOR — fully backwards compatible; no existing output changes for non-Flutter configurations)

## [2.0.0] — 2025 (initial public release)

- Initial release with spec-kit and CLAUDE.md generators
- Project type selector: Web App, Microservices, Data Pipeline, AI System, Platform/SDK, Mobile App
- Principle library across 10 categories
- Presets: AI-First, Startup Speed, Enterprise Grade, Security-Critical
- Share config via URL hash
- 100% browser-side, no account, no server
