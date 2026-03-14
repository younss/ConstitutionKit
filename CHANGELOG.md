# Changelog

All notable changes to ConstitutionKit are documented here.

## [2.4.0] — 2026-03-14

### Fixed
- **P1-A** — `buildClaudeMd()` emitted only 2 of 4 rules per principle due to `item.rules.slice(0, 2)`. All rules are now emitted.

### Added
- **P1-B** — `constitution.md` always includes a `## Security Baseline` section (no secrets, CVE scanning, input validation, least-privilege, logging hygiene) regardless of which principles are selected.
- **P1-C** — `constitution.md` always includes an `## Architecture Decision Records` section with ADR template and ADR register table.
- **P2-A** — `CLAUDE.md` now includes a `## Session startup protocol` section (git branch check, green baseline, forbidden paths review, plan declaration, scope check). The test step uses `context.buildCmd` when available.
- **P2-B** — `CLAUDE.md` now includes an `## Output format contract` section covering commit message format (Conventional Commits), PR description structure, reporting completed work, expressing uncertainty, and reporting constitutional violations.
- **P3-A** — `constitution.md` now includes a `## CI / Quality Gates` section with pipeline steps (from `context.buildCmd`), gate classification table (BLOCKING vs ADVISORY), and definition of done checklist. A warning is shown when no build commands were provided.
- **P3-B** — `constitution.md` now includes a `## Pull Request Review Checklist` section. Universal items are always present; principle-specific items (solid, dry, tdd, clean-code, api-conventions, api-versioning, circuit-breaker, fail-fast, observability, zero-trust, owasp, webhook-security, idempotent-msg) are appended when the corresponding principle is selected.
- **P4-A** — Step 4 has 5 new input fields: branch strategy (select), environment topology (textarea), test framework & coverage threshold (input), PR merge strategy (select), unusual constraints (textarea).
- **P4-B** — `saveToURL()` and `loadFromURL()` serialise/restore all 5 new fields via URL hash params: `branch`, `envtopo`, `testfw`, `merge`, `unusual`.
- **P4-C** — `generate()` now builds a structured `context` object containing all Step 4 fields and passes it to both generators. Neither generator reads the DOM directly (SRP fix; DOM reads are consolidated in `generate()`).
- **P4-D** — `CLAUDE.md` includes a `## Non-standard decisions and known constraints` section when `context.unusualConstraints` is set.
- **P4-E** — The autonomy boundaries section in `CLAUDE.md` is now branch-strategy-aware: trunk-based and GitFlow strategies produce explicit branch model statements.
- **P4-F** — `constitution.md` header now includes `Version: v1.0.0`, `Generated:`, and `Last amended:` fields. A `## Changelog` table is appended at the end of the constitution.
- **P4-G** — The CI/Quality Gates section includes a test framework line when `context.testFramework` is set.
- **P4-H** — `CLAUDE.md` includes a `## Environment access policy` section when `context.envTopology` is set.
- **ADR-004** — Architecture Decision Record for production-ready output improvements at `.specify/adr/ADR-004-production-ready-output.md`.

### Changed
- Footer version bumped to `v2.4.0`.

---

## [2.3.0] — 2026-03-14

### Added
- **WhatsApp Agent project type** — new type card (`data-type="whatsapp-agent"`, icon 💬, name "WhatsApp Agent") in the project type selector
- **New stack tags**: `WhatsApp API`, `Twilio`, `Meta Cloud API`, `Webhook`
- **New principle category: "Messaging & Conversational AI"** — 8 principles covering the primary failure modes of production WhatsApp bots:
  - `webhook-security` — X-Hub-Signature-256 verification on every inbound request
  - `idempotent-msg` — deduplication by `message.id` via Redis/DB
  - `conversation-state` — durable session state per `wa_id` with TTL policy
  - `llm-prompt-safety` — input sanitisation and system prompt injection constraints
  - `rate-limiting-throttle` — WhatsApp tier limits, per-user throttling, 429 backoff
  - `msg-type-handling` — explicit handling of all inbound WhatsApp message types
  - `delivery-receipts` — status webhook processing, retry on `failed`, persistent failure alerting
  - `graceful-handoff` — escalation triggers, human agent routing with full context
- **WhatsApp Agent preset** — selects all 8 new principles plus `fail-fast`, `retry-backoff`, `circuit-breaker`, `tdd`, `clean-code`, `api-conventions`, `observability`
- **`buildWhatsAppAddendum(context)`** — pure function (no DOM reads) appending a WhatsApp-specific section to `constitution.md` covering: API setup and signature validation, conversation architecture, LLM integration, operational runbook, and GDPR/opt-out/retention compliance
- **`buildWhatsAppClaudeAddendum()`** — pure function returning WhatsApp-specific agent operating instructions for `CLAUDE.md` (forbidden actions, required invariants, local dev commands)
- **ADR-003** — Architecture Decision Record for WhatsApp Agent support at `.specify/adr/ADR-003-whatsapp-agent-support.md`

### Changed
- `TYPE_LABELS` updated with `'whatsapp-agent': 'WhatsApp Agent'`
- Footer version bumped to `v2.3.0`

---

## [2.2.0] — 2026-03-14

### Added
- **Full-Stack Solution project type** — new type card, tier picker (Mobile App, Web/Admin App, Backend API, Microservices, Database/Cache, Auth Service), and `TIER_LABELS` map
- **New principle category: "Cross-Tier Architecture"** — 8 principles: API Gateway, Event-Driven Architecture, Distributed Tracing, Shared Auth & Identity, Cross-Tier Data Consistency, Cross-Platform UX Consistency, Monorepo Structure, Deployment Orchestration
- **Full-Stack preset**
- **`buildMultiTierAddendum(context, tiers)`** and **`buildMultiTierClaudeAddendum(tiers)`** pure functions for OCP-compliant output composition
- Tier state serialised in share URL (`tiers=` param); `loadFromURL` restores tier picker selection and visibility
- **ADR-002** — Architecture Decision Record for Multi-Tier / Full-Stack Solution support

### Changed
- Footer version bumped to `v2.2.0`

---

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
