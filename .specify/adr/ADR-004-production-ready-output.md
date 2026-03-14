## ADR-004: Production-Ready Output — Enforcement Infrastructure, Agent Protocols, and Extended Step 4 Inputs

### Date: 2026-03-14
### Status: Accepted

**Context**

ConstitutionKit v2.3.0 produced constitutions and CLAUDE.md files that were structurally correct but incomplete for production use. Specifically:

- `CLAUDE.md` emitted only 2 of 4 rules per principle (a slice bug), losing half the enforcement intent.
- `constitution.md` lacked a Security Baseline section — a universal requirement that must not depend on which principles a user selects.
- Neither output file contained CI/quality gate definitions, a PR review checklist, or an ADR template and register, leaving teams without enforcement infrastructure.
- `CLAUDE.md` lacked a session startup protocol, an output format contract, and branch-strategy awareness — gaps that cause agentic AI to begin sessions without a baseline and produce inconsistently formatted outputs.
- Step 4 collected only 6 context fields. Missing fields (branch strategy, environment topology, test framework, merge strategy, unusual constraints) caused generators to produce context-poor outputs.
- The `generate()` function passed a bare string as context to both generators, which violated the constitutional requirement that generators read from a context parameter only — not from the DOM.

**Decision**

Implement the following changes as a single MINOR version bump to v2.4.0:

1. **P1-A** — Fix `item.rules.slice(0, 2)` bug in `buildClaudeMd`: emit all rules.
2. **P1-B** — Always append a `## Security Baseline` section to `constitution.md`, regardless of selected principles.
3. **P1-C** — Always append an `## Architecture Decision Records` section (template + register) to `constitution.md`.
4. **P2-A** — Add `## Session startup protocol` to `CLAUDE.md` (dynamically uses `context.buildCmd`).
5. **P2-B** — Add `## Output format contract` to `CLAUDE.md` (commit messages, PR descriptions, reporting, uncertainty, constitutional violations).
6. **P3-A** — Add `## CI / Quality Gates` section to `constitution.md` (generated from `context.buildCmd`; includes test framework line if `context.testFramework` is set).
7. **P3-B** — Add `## Pull Request Review Checklist` to `constitution.md` (universal items always present; principle-specific items generated dynamically from selected principles).
8. **P4-A** — Add 5 new input fields to Step 4: branch strategy, environment topology, test framework & threshold, PR merge strategy, unusual constraints.
9. **P4-B** — Extend `saveToURL()` and `loadFromURL()` to serialise/deserialise all 5 new fields.
10. **P4-C** — Refactor `generate()` to build a structured `context` object (eliminating bare-string context passing); both generators read exclusively from `context`.
11. **P4-D** — Add `## Non-standard decisions and known constraints` section to `CLAUDE.md` when `context.unusualConstraints` is set.
12. **P4-E** — Make the autonomy boundaries section in `CLAUDE.md` branch-strategy-aware.
13. **P4-F** — Replace static `Generated:` header in `constitution.md` with version/date block; append `## Changelog` table at end of `constitution.md`.
14. **P4-G** — Add test framework line to CI/Quality Gates section when `context.testFramework` is set.
15. **P4-H** — Add `## Environment access policy` section to `CLAUDE.md` when `context.envTopology` is set.

**Rationale**

- The rules-slice bug was a correctness defect — fix is non-negotiable.
- Security Baseline and ADR register are governance infrastructure that must be unconditional; they cannot be opt-in via principle selection because a project with no security-related principles still requires the baseline.
- Session startup protocol and output format contract are direct responses to observed agentic AI failure modes: agents beginning sessions without establishing a baseline and producing commits/PRs in inconsistent formats.
- The context object refactor is required for constitutional compliance: generators must not contain DOM reads. Centralising all DOM reads in `generate()` enforces SRP, enables unit testing, and makes the dependency graph explicit.
- Extended Step 4 fields fill gaps that produced context-poor outputs for teams that do not use the defaults.

**Consequences**

- Positive: Generated outputs are materially more production-ready. Security baseline is universally present. Agentic AI has a startup protocol and output format contract. CI gates and PR checklist are codified in `constitution.md`.
- Positive: `generate()` is now the sole DOM-reading entry point for context; both generators are closer to pure functions (they still read global `selectedPrinciples`, `selectedType`, `selectedStack`, `selectedTiers`, but no longer read Step 4 DOM fields directly inside the generator).
- Neutral: MINOR version bump to 2.4.0. No breaking changes to existing output for users who do not set new fields — all new sections either use fallback text or are conditional on new field values.
- Negative: `constitution.md` output is longer by default due to the always-present CI/Quality Gates, PR Checklist, Security Baseline, and ADR sections. This is intentional and desirable.

**Constitutional Alignment**

- SRP: `generate()` now owns all DOM reads; generators own content generation only.
- DRY: context object eliminates scattered `document.getElementById` calls across generator functions.
- Open/Closed: new sections appended to generator output without modifying the per-principle loop.
- YAGNI: all new fields and sections directly address documented gaps from the 2026-03-14 audit.

**Supersedes**: N/A
