````md
# AGENTS.md

> `CLAUDE.md` may be a symlink to this file.
> Keep framework/domain specifics in: **{{DOCS_PATHS}}** (e.g. `docs/reference/coding-standards.md`, `apps/*/AGENTS.md`).

## Project

**{{PROJECT_NAME}}** — {{ONE_LINE_DESCRIPTION}}

- Stack: {{STACK_SUMMARY}} (e.g., Tauri+SvelteKit, Next.js+API, etc.)
- Repo shape: {{MONOREPO_OR_SINGLE_APP}} (e.g., “Turborepo monorepo”, “single app”)
- Source of truth: {{SOURCE_OF_TRUTH}} (e.g., local files, DB, API)

## Principles (customize)

- {{PRINCIPLE_1}}
- {{PRINCIPLE_2}}
- {{PRINCIPLE_3}}
- {{PRINCIPLE_4_OPTIONAL}}

> Keep this short and product-defining. If a change violates a principle, it needs explicit justification.

---

## Setup

```bash
pnpm install
pnpm build      # keep if required before tests
pnpm test
````

**Important constraints (customize):**

* {{IF_BUILD_REQUIRED_BEFORE_TESTS_EXPLAIN_HERE}} (e.g., “Tests fail unless `pnpm build` ran first.”)
* {{OTHER_ORDERING_OR_ENV_NOTES}}

---

## Scripts

> Prefer consistent “gate” entrypoints across repos even if internals differ.

```bash
# common
pnpm dev
pnpm build
pnpm test
pnpm lint
pnpm typecheck        # if applicable
pnpm check            # if applicable (TS + framework checks)
pnpm format           # if applicable
pnpm format:check     # if applicable

# confidence gates (required)
pnpm gate:commit      # local gate (used by pre-push; fast-ish & deterministic)
pnpm gate:pr          # PR/CI gate (full confidence; enforced by CI)

# optional (customize)
pnpm test:unit
pnpm test:integration
pnpm test:e2e
pnpm clean
```

---

## Decision Protocol

**Ask the user before:**

* Architectural choices between valid approaches
* Adding dependencies (runtime or dev)
* Changing API contracts, shared types, or cross-package interfaces
* Database schema / migrations / persistent storage format changes
* Deleting or deprecating functionality
* Changing build tooling / CI behavior
* Touching project-wide base utilities that affect many modules/packages
* {{PROJECT_SPECIFIC_ASK_BEFORE_ITEMS}} (e.g., editor extension systems, IPC signatures)

**Don’t ask for:**

* Implementation details within an agreed approach
* Bug fixes, code style, test organization
* Refactors confined to a single module/file that don’t change behavior

**How to ask:** Present 2–4 options with trade-offs, state your recommendation, one paragraph max per option.

---

## Development Flow

1. **Before coding**

   * Read relevant docs: **{{DOCS_PATHS}}**
   * Search for existing patterns and match them (don’t invent new patterns casually).

2. **While coding**

   * TDD loop: **failing test → implement → pass → refactor**
   * Treat requirements as tests: convert “should/must” into test cases.
   * Mock only at boundaries.

3. **After coding**

   * Self-review the diff (correctness, naming, error handling, edge cases).
   * Run the local gate: `pnpm gate:commit`
   * Update docs/CHANGELOG if behavior or usage changed.

**Pause if:** scope grows, assumptions break, a trade-off is needed, or you’re touching unplanned code.

---

## Code Rules

### TypeScript (core)

* Prefer `unknown` over `any`; narrow types explicitly.
* Avoid `@ts-ignore`. If truly needed, add a comment explaining why (and link to an issue/ticket if applicable).
* Named exports over default exports.
* Colocate types until shared by ~3+ files; then extract to a shared module.
* Validate runtime inputs at boundaries (API handlers, IPC, file IO). Tooling may vary (Zod/Valibot/etc); the principle is required.

### Functions (core)

* Keep functions **<30 lines** and **single responsibility**.
* Prefer an **options object** over many positional arguments.
* Derive state where possible; avoid syncing state via effects when derivation/computation is correct and simpler.

### Error handling (core)

* Fail loudly at boundaries; return typed errors where appropriate.
* Don’t swallow errors. If you catch, either:

  * add context and rethrow, or
  * handle fully and test the behavior.

### Performance & correctness (core)

* Prefer correctness over convenience for persistence, coordination, and state.
* Avoid subtle state sync bugs; keep state transitions explicit and testable.

### Framework & domain-specific rules (customize)

Put stack-specific rules in **{{DOCS_PATHS}}** or `apps/*/AGENTS.md`, for example:

* UI framework conventions (React/Next.js, Svelte, etc.)
* Editor/widget/extension constraints
* Backend/service patterns
* Styling/design system rules

This core file should stay framework-agnostic.

---

## Testing

**Philosophy (core):**

* Mock only at boundaries (network, filesystem, DB, external services).
* Test behavior/outcomes, not wiring.
* Prefer high-signal assertions (exact values/ranges/classes). Avoid vacuous assertions (`toBeDefined`, `> 0`) unless they’re truly meaningful.
* Cover edge cases: **null/undefined**, **empty**, **zero**, **negative**, **missing fields**, **malformed inputs**.
* Don’t test library internals; test your integration at your boundary.

**Structure (recommended):**

* Co-locate tests near implementation where practical (e.g., `foo.ts` + `foo.test.ts`).
* Use integration/E2E tests for cross-cutting behavior and visual/interaction correctness.

**Stack-specific testing rules (customize):**

* {{STACK_SPECIFIC_TESTING_RULE_1}}
* {{STACK_SPECIFIC_TESTING_RULE_2}}

---

## Git

**Commits:** `<type>(<scope>): <summary>` — imperative mood, max 50 chars.

Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`
Scopes (customize): `{{SCOPE_1}}`, `{{SCOPE_2}}`, `{{SCOPE_3}}` (or omit scope if your repo doesn’t use it)

**No AI footnotes.** Remove auto-generated signatures before pushing.

---

## Never Modify Without Approval

**Protected categories (core):**

* Dependency manifests / dependency sections (`package.json`, `Cargo.toml`, etc.)
* Build/runtime config (bundler config, framework config, CI config)
* `.env` files and secrets handling
* Database schemas / migrations
* Public/shared API contracts and shared types

**Project-specific protected files (customize):**

* {{FILE_PATH_1}}
* {{FILE_PATH_2}}
* {{FILE_PATH_3}}

---

## Hooks & CI (what runs where)

Hooks are **developer ergonomics**; CI is **authoritative**.
Hooks should call repo scripts (scripts are the source of truth), not re-implement logic.

### Pre-commit hook (fast, staged-only, deterministic)

**Goal:** prevent churn (format/lint) without slowing commits.

Runs (recommended):

* Staged formatting (auto-fix OK locally) via `lint-staged` (or equivalent)
* Staged lint (auto-fix OK locally if safe) via `lint-staged` (or equivalent)

Optional (only if reliably fast and deterministic):

* Minimal targeted checks (e.g., quick unit tests for changed packages)

**Do NOT run in pre-commit:**

* Full typecheck/build
* Full unit/integration/e2e suites
* Anything network-dependent or flaky

**Implementation guidance (tool-agnostic):**

* Use `lint-staged` + a hook manager (Husky / Lefthook / simple git hooks)
* Only touch staged files; do not rewrite the working tree unexpectedly

### Pre-push hook (broader local confidence)

**Goal:** catch breakage before CI cost.

Runs:

* `pnpm gate:commit`

Guidelines:

* Pre-push can take longer than pre-commit, but should still be deterministic and runnable offline when possible.
* If the local gate is too slow, tune `gate:commit`—don’t move heavy checks into pre-commit.

### CI (authoritative PR gate)

**Goal:** enforce merge correctness with full coverage.

Runs:

* `pnpm gate:pr`

Notes:

* CI may split the gate across jobs/workflows, but the **aggregate must be equivalent** to `pnpm gate:pr`.
* CI should be check-only (no auto-fixes). Fixes happen locally.

---

## Quality Gates

These script names must exist in every repo:

### `pnpm gate:commit` (local confidence gate)

**Typical contents (customize to your repo):**

* `pnpm lint`
* `pnpm typecheck` or `pnpm check`
* `pnpm build` (include if tests depend on build artifacts)
* `pnpm test` (unit + integration; avoid heavy/native/docker e2e unless repo policy requires it)

### `pnpm gate:pr` (PR/CI confidence gate)

**Typical contents (customize to your repo):**

* Everything in `gate:commit`, plus:
* `pnpm format:check` (if applicable)
* Full test suite (including E2E where applicable)
* Platform/native/docker E2E (CI-only if it’s too heavy locally)
* Optional CI-only checks (security/license scans, coverage upload, artifact build)

> If the repo requires build→test ordering, encode it inside these gates and document it in **Setup** above.

---

## Continuous Improvement of This Doc

This file is a **living contract**. Keep it accurate, scannable, and enforceable.

* **Update in the same PR** as the change that motivates it (new workflow, new convention, new gate).
* **Prefer scripts over prose:** document *what* runs (e.g., `pnpm gate:commit`) and keep the exact command list in `package.json`.
* **Keep core generic:** move stack/domain detail into `{{DOCS_PATHS}}` and link it (don’t bloat this file).
* **Only add rules that pay rent:** each new rule should prevent a real recurring failure or ambiguity.
* **Make rules enforceable:** if it’s important, encode it in gates/tests/lint (or remove/soften it).
* **Use clear policy language:** *MUST* / *SHOULD* / *MAY*. Avoid long paragraphs.
* **Prune aggressively:** remove outdated sections, duplicated lists, or anything that diverges from reality.
* **Approval boundary:** changing Decision Protocol, protected files, or gate definitions is a policy change—ask first.

---

## Debugging

1. **Capture** evidence (logs, traces, screenshots, failing test output).
2. **Hypothesize** 2–3 likely causes.
3. **Validate** using the fastest tool (focused test, minimal repro, targeted logging).
4. **Fix** or refine the hypothesis.
5. **Verify** with a regression test (unit/integration/E2E as appropriate).

**Common project issues (customize):**

* {{COMMON_ISSUE_1}} → {{HOW_TO_CHECK_1}}
* {{COMMON_ISSUE_2}} → {{HOW_TO_CHECK_2}}

```
```
