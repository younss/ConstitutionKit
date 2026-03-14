# Changelog

All notable changes to ConstitutionKit are documented here.

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
