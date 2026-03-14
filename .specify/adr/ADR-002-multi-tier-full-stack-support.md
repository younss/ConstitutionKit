## ADR-002: Multi-Tier / Full-Stack Solution Support
### Date: 2026-03-14
### Status: Accepted

**Context**: ConstitutionKit supported only single project types (web-app, mobile, flutter, etc.). Teams building solutions that span multiple tiers — mobile client, web admin, backend API, microservices, database/cache, and auth service — had no way to generate a constitution that addresses cross-tier architectural concerns. The tool produced constitutions relevant to only one tier, forcing teams to either pick the closest match or generate multiple files and manually merge them.

**Decision**: Add a new "Full-Stack Solution" project type (`full-stack`) that:

1. Renders a type card in Step 02 alongside existing project type cards.
2. When selected, reveals a tier picker (checkboxes) allowing users to declare which tiers their solution includes: Mobile App, Web / Admin App, Backend API, Microservices, Database / Cache, Auth Service.
3. Introduces a new `selectedTiers` Set in application state, parallel to `selectedStack`.
4. Adds a new `Cross-Tier Architecture` principles category in `PRINCIPLES` with eight principles: API Gateway, Event-Driven Architecture, Distributed Tracing, Shared Auth & Identity, Cross-Tier Data Consistency, Cross-Platform UX Consistency, Monorepo Structure, Deployment Orchestration.
5. Implements two pure addendum functions — `buildMultiTierAddendum(context, tiers)` and `buildMultiTierClaudeAddendum(tiers)` — following the exact same OCP-compliant extension pattern established by `buildFlutterAddendum()` and `buildFlutterClaudeAddendum()`.
6. The generated constitution includes a tier-specific section per selected tier (conditional on `tiers.has()`), plus the cross-cutting principle sections from Step 03.
7. Adds a "Full-Stack" quick preset selecting all eight cross-tier principles plus foundational resilience, security, API design, and DevOps principles.
8. `saveToURL` / `loadFromURL` serialize `selectedTiers` as a comma-joined `tiers` query parameter, parallel to the existing `stack` parameter.
9. Version bumped from 2.1.0 to 2.2.0 (MINOR — new capability, non-breaking).

**Rationale**: The existing addendum pattern (Flutter addendum) proved the right abstraction. Pure functions that receive state as arguments rather than reading from DOM or global variables are independently testable and composable. Extending the same pattern for multi-tier support keeps the architecture consistent and avoids any modification to the existing generator functions (OCP). The tier picker is a secondary selector that appears only when the `full-stack` type is active (ISP — the UI does not burden non-full-stack users with irrelevant controls). The `selectedTiers` Set mirrors `selectedStack` in both naming and usage pattern (DRY at the architectural level).

**Consequences**:
- Positive: Teams building multi-tier solutions get a constitution that addresses the hardest problems in distributed system development (consistency, tracing, identity, deployment coordination).
- Positive: The eight new `Cross-Tier Architecture` principles are available to all project types via manual selection or the Full-Stack preset — they are not locked behind the full-stack type.
- Positive: The addendum generates tier-specific sections conditionally, so a team with only Mobile + Backend API gets exactly those sections, not boilerplate for tiers they don't have.
- Negative: The `buildMultiTierAddendum` function uses template-literal conditional string building (`tiers.has()`) which, while readable, grows linearly with the number of tiers. Acceptable for the current tier count (6).
- Neutral: `index.html` grows from ~1520 to ~1800 lines. The single-file constraint remains, but the file is approaching the point where a build step would improve maintainability (tracked as existing HIGH violation in project memory).

**Constitutional Alignment**:
- **Open/Closed**: Base `buildConstitution()` and `buildClaudeMd()` functions are extended via conditional composition, not modified.
- **Single Responsibility**: `buildMultiTierAddendum` is solely responsible for the multi-tier constitution section. `buildMultiTierClaudeAddendum` is solely responsible for the agent operating instructions variant.
- **DRY**: `TIER_LABELS` is the single source of truth for tier display names, consumed by both the addendum functions.
- **Interface Segregation**: The tier picker UI is hidden until the `full-stack` type is selected; other project types are not affected by this addition.
- **YAGNI**: Only the six explicitly requested tiers are implemented. No speculative "future tiers" placeholder slots were added.

**Supersedes**: N/A
