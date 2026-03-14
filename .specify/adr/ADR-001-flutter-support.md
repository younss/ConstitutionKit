## ADR-001: Flutter / Dart Platform Support in ConstitutionKit

### Date: 2026-03-14
### Status: Accepted

**Context**

ConstitutionKit v2.0 generates `constitution.md` and `CLAUDE.md` files for software projects. As of this ADR, the tool supports six project types (Web App, Microservices, Data Pipeline, AI System, Platform/SDK, Mobile App) and a stack-tag list that covers common web, backend, and infrastructure technologies.

Flutter has become a primary cross-platform mobile and desktop SDK. Teams using Flutter with agentic AI tooling (Claude Code, spec-kit) have distinct architectural patterns and enforcement requirements that are not addressed by the generic "Mobile App" type or by existing principles. Specifically:

- Flutter's widget/BLoC/repository layering is fundamentally different from iOS MVC or Android MVVM conventions.
- Dart's `const` correctness and isolate-based concurrency are unique enforcement points.
- Platform channels require explicit interface abstraction patterns absent in other mobile stacks.
- Flutter's three-tier test pyramid (unit / widget / integration_test) is platform-specific.

Without first-class Flutter support, agents working on Flutter projects receive a constitution that omits the most important constraints for that platform, increasing the risk of architectural violations (logic in `build()`, missing `const`, raw platform channel calls in business logic, navigation from BLoCs).

**Decision**

Add Flutter as a first-class project type and stack tag in ConstitutionKit, with:

1. A `flutter` project type card in the UI (Step 02 — Project type).
2. `Flutter`, `Dart`, and `Firebase` stack tags in the tech stack selector.
3. A `Flutter & Dart` principle category in the `PRINCIPLES` data object, containing eight Flutter-specific principles:
   - Widget Architecture
   - State Management
   - Flutter TDD
   - Flutter Performance
   - Platform Channels
   - Dependency Injection
   - Navigation & Routing
   - Flutter Accessibility
   - Flutter Error Handling
4. A `flutter` preset in the `PRESETS` object combining all Flutter-specific principles with foundational universal principles (SOLID, DRY, clean code, YAGNI, accessibility, CI/CD, dependency management, local dev).
5. A `buildFlutterAddendum()` pure generator function (no DOM reads, no side effects) that produces a Flutter-specific `constitution.md` addendum section, conditionally appended when the Flutter type or Flutter stack tag is active. This is an Open/Closed extension: the base `buildConstitution()` function is not modified structurally — it delegates to the addendum function via a conditional at the end.
6. A `buildFlutterClaudeAddendum()` pure generator function that produces Flutter-specific agent operating instructions for `CLAUDE.md`, applied under the same condition.
7. A `downloadFile(filename, content)` utility function extracted as a DRY fix (was duplicated inline in download-single and download-all paths).
8. Updated `.specify/templates/plan-template.md` — adds Option 4 Flutter directory structure.
9. Updated `.specify/templates/tasks-template.md` — adds Flutter path conventions.
10. Updated `.specify/templates/constitution-template.md` — adds Flutter Platform Addendum section guidance with inline documentation.

**Rationale**

**Why a separate project type rather than relying solely on stack tags?**
Stack tags are multi-select and advisory; project types are mutually exclusive and drive type-specific labelling in the generated output. Flutter warrants its own type because the architectural constraints (layer hierarchy, const enforcement, isolate rules) apply project-wide and should appear prominently in the constitution header metadata, not buried in the principles section.

**Why pure generator functions rather than inline template strings?**
The `buildFlutterAddendum()` and `buildFlutterClaudeAddendum()` functions are pure (no DOM reads, no global mutation). This makes them independently verifiable: they can be tested by calling them and asserting on the returned string, without needing browser context. It also upholds Single Responsibility — each function has exactly one reason to change (the Flutter platform guidance changes).

**Why eight Flutter-specific principles rather than one large block?**
Each principle maps to a distinct enforcement mechanism and a distinct area of the codebase. Granular principles allow teams to select only the ones relevant to their situation (e.g., a team not using platform channels can deselect that principle). This upholds Interface Segregation at the documentation level.

**Why a `downloadFile()` utility was extracted as part of this change?**
The DRY violation (V-002 in the audit) was identified during codebase review. The Flutter feature addition exercises the download path; fixing the violation at the same time keeps the changeset coherent and avoids a separate PR for a trivial refactor. This is consistent with the Boy Scout Rule.

**Why not modify `buildConstitution()` structurally?**
Structural modification of `buildConstitution()` to support Flutter would require adding conditional branches inside a function that already has SRP. Appending the Flutter addendum via a conditional call at the output stage keeps the base function's behaviour unchanged (Open/Closed Principle). Any future platform addendum (React Native, SwiftUI, Compose) follows the same pattern.

**Consequences**

Positive:
- Flutter teams receive a complete, platform-specific constitution and CLAUDE.md with actionable, enforceable rules.
- Agents working on Flutter codebases receive explicit Flutter-specific operating constraints, reducing the most common architectural violations.
- The DRY violation in the download path is resolved.
- The template files are updated to include Flutter project structure conventions, improving spec-kit plan generation for Flutter projects.
- New platform addenda can be added by following the same pattern (pure function + conditional append) without touching existing code.

Negative:
- The `PRINCIPLES` data object grows by eight entries. This increases the initial parse time marginally (negligible given the single-page client-side architecture).
- The Flutter addendum adds approximately 60 lines to generated `constitution.md` files for Flutter projects. This is intentional — Flutter-specific constraints are substantive and cannot be compressed without losing enforcement value.

Neutral:
- The `flutter` project type is mutually exclusive with other project types (same behavior as all existing types).
- Selecting "Flutter" as the stack tag while choosing a different project type (e.g., "Platform/SDK" building a Flutter plugin) will still trigger the addendum, which is the correct behavior — Flutter code requires the same architectural constraints regardless of the project classification.

**Constitutional Alignment**

- Single Responsibility: `buildFlutterAddendum()` and `buildFlutterClaudeAddendum()` each have one responsibility.
- Open/Closed: `buildConstitution()` and `buildClaudeMd()` are extended without structural modification.
- DRY: `downloadFile()` utility eliminates the duplicate download blob pattern.
- YAGNI: All eight principles are grounded in current Flutter best practices. No speculative abstractions.
- TDD Mandate alignment: The Flutter TDD principle and `buildFlutterAddendum()` both emphasize test-first; the generator functions themselves are pure and independently testable.

**Supersedes**: N/A (first ADR for this project)

---

## SYNC IMPACT REPORT

### Version: 2.0 → 2.1.0
### Bump Type: MINOR
### Date: 2026-03-14

**Rationale**: New capability added (Flutter project type, stack tags, principles category, presets, generator addenda). Non-breaking — existing generated outputs are unchanged for non-Flutter configurations. MINOR bump is correct.

**Constitutional Changes**:
- Modified Principles: None modified.
- New Principles: `Flutter & Dart` category — 8 principles added.
- Deprecated Principles: None.

**Architectural Impact**:
- `buildConstitution()` → Conditionally appends Flutter addendum at output stage (no structural change).
- `buildClaudeMd()` → Conditionally appends Flutter agent instructions at output stage (no structural change).
- New pure functions: `buildFlutterAddendum()`, `buildFlutterClaudeAddendum()`, `downloadFile()`.
- `TYPE_LABELS` → Extended with `flutter` key.
- `PRESETS` → Extended with `flutter` preset.

**Required Template Updates**:
- [x] `.specify/templates/constitution-template.md` — Flutter Platform Addendum section added.
- [x] `.specify/templates/plan-template.md` — Option 4 Flutter directory structure added.
- [x] `.specify/templates/tasks-template.md` — Flutter path conventions added.

**ADR Reference**: ADR-001 (this document)

**Governance Sign-off**: APPROVED
