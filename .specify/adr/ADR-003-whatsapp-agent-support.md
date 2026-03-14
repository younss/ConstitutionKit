## ADR-003: WhatsApp Agent Project Type Support
### Date: 2026-03-14
### Status: Accepted

**Context**: ConstitutionKit v2.2.0 supports eight project types (Web App, Microservices, Data Pipeline, AI System, Platform/SDK, Mobile App, Flutter App, Full-Stack Solution). A growing number of teams are building AI-powered WhatsApp bots via the WhatsApp Business API (Meta Cloud API or Twilio). These projects have a distinct set of non-negotiable concerns — webhook signature security, message deduplication, conversation state management, LLM prompt safety, WhatsApp-specific rate limits, message type handling, delivery receipt processing, and human handoff — that are not adequately covered by any existing project type or principle category. Without explicit constitutional support, teams shipping WhatsApp agents lack a ratified baseline for the most common failure modes (unsigned webhook processing, duplicate replies, stateless sessions, PII leakage in logs).

**Decision**: Add a `whatsapp-agent` project type to ConstitutionKit v2.3.0 with the following deliverables:

1. A new type card (`data-type="whatsapp-agent"`, icon 💬, name "WhatsApp Agent") in Step 02.
2. Four new stack tags: `WhatsApp API`, `Twilio`, `Meta Cloud API`, `Webhook`.
3. A new `"Messaging & Conversational AI"` principle category containing eight principles: `webhook-security`, `idempotent-msg`, `conversation-state`, `llm-prompt-safety`, `rate-limiting-throttle`, `msg-type-handling`, `delivery-receipts`, `graceful-handoff`.
4. A `whatsapp-agent` preset that includes all eight new principles plus cross-cutting principles from existing categories: `fail-fast`, `retry-backoff`, `circuit-breaker`, `tdd`, `clean-code`, `api-conventions`, `observability`.
5. A new preset button in Step 03.
6. `buildWhatsAppAddendum(context)` — a pure function (no DOM reads) that returns a Markdown addendum for `constitution.md` covering: API setup and signature validation, conversation architecture (session schema, TTL policy), LLM integration (system prompt template, input sanitisation, fallback), operational runbook (ngrok testing pattern, monitoring alerts, escalation SLA), and compliance (GDPR consent, opt-out via STOP keyword, data retention).
7. `buildWhatsAppClaudeAddendum()` — a pure function returning agent operating instructions for `CLAUDE.md`, covering forbidden actions (logging PII, committing secrets, processing before signature verification), required actions (always verify signature, always deduplicate, always sanitise input), and local development commands.
8. Both addenda wired into `buildConstitution()` and `buildClaudeMd()` via `if (selectedType === 'whatsapp-agent')` conditionals at the output stage — the base generator bodies are not modified.
9. `TYPE_LABELS` updated with `'whatsapp-agent': 'WhatsApp Agent'`.
10. Footer version bumped to `v2.3.0`.
11. `CHANGELOG.md` updated with a `[2.3.0]` entry.

**Rationale**: The addendum pattern (Open/Closed Principle) is already established in ADR-001 (Flutter) and ADR-002 (Full-Stack). WhatsApp agents qualify for the same treatment: they have a specialised, stable set of platform constraints that benefit from explicit constitutional encoding without modifying the base generator logic. The eight new principles cover the highest-impact failure modes documented in WhatsApp Business API production incidents. Keeping them in a dedicated category (`"Messaging & Conversational AI"`) makes them discoverable for teams building other messaging integrations in the future (Telegram, SMS, etc.) without requiring another ADR for each.

Pure-function discipline is maintained: `buildWhatsAppAddendum(context)` and `buildWhatsAppClaudeAddendum()` read no global state and access no DOM. They are independently testable. The `context` argument mirrors the existing pattern used by `buildMultiTierAddendum(context, tiers)`.

**Consequences**:
- Positive: WhatsApp agent teams get a ratified, production-grounded constitutional baseline out of the box. The eight principles directly address the most common WhatsApp bot failure modes. The CLAUDE.md addendum provides an explicit forbidden/required list that AI agents can enforce mechanically.
- Positive: The `"Messaging & Conversational AI"` category is extensible to other messaging platforms without another structural change.
- Negative: `index.html` grows by approximately 250 lines, increasing the monolith size noted as a known HIGH violation in the 2026-03-14 audit. This is an accepted trade-off consistent with the decision made in ADR-001 and ADR-002; the single-file architecture constraint is unchanged.
- Neutral: The `loadFromURL` / `saveToURL` functions require no modification — `selectedType === 'whatsapp-agent'` serialises and deserialises identically to existing type values.

**Constitutional Alignment**:
- Open/Closed Principle: base generators are not modified; the addendum is a pure extension appended at the output stage.
- Single Responsibility: `buildWhatsAppAddendum` and `buildWhatsAppClaudeAddendum` each have one reason to change (WhatsApp platform requirements change).
- DRY: the addendum pattern is reused, not reinvented.
- YAGNI: all eight principles correspond to documented, recurring WhatsApp bot failure modes — none are speculative.

**Supersedes**: N/A
