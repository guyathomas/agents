<!-- Template source: https://raw.githubusercontent.com/guyathomas/agents/refs/heads/main/AGENTS.md -->

# AGENTS.md Template

> A generic template for AI coding agents. Copy to your repository root as `AGENTS.md` and customize the `[TEMPLATE: ...]` sections.

---

# AGENTS.md

> `CLAUDE.md` should symlink to this file for cross-tool compatibility: `ln -s AGENTS.md CLAUDE.md`

## Project

[TEMPLATE: Brief 1-2 line project description]

```
Examples:
- "Turborepo monorepo: apps/api (Express) + apps/web (React)"
- "Django REST API with PostgreSQL and Celery"
- "CLI tool built with Go and Cobra"
```

## Setup

[TEMPLATE: Commands required before running tests or development]

```bash
# Example for Node.js project
npm install     # Install dependencies
npm run build   # Build (if required before tests)
npm test        # Run tests
```

[TEMPLATE: Add any important notes about setup order or prerequisites]

## Scripts

[TEMPLATE: List your key development commands]

```bash
# Common examples - customize for your project
npm run dev        # Start development server
npm run build      # Build for production
npm run lint       # Run linter
npm run typecheck  # Type checking
npm run test       # Run tests
npm run test:e2e   # End-to-end tests
```

## Project Structure

[TEMPLATE: Document key directories - remove or add as needed]

```
project-root/
├── src/              # Source code
│   ├── components/   # UI components
│   ├── lib/          # Utilities and helpers
│   ├── api/          # API routes/handlers
│   └── types/        # TypeScript types
├── tests/            # Test files
├── docs/             # Documentation
└── scripts/          # Build/deploy scripts
```

---

## Decision Protocol

**Ask the user before:**
- Architectural choices between valid approaches
- Adding new dependencies
- Changing API contracts or public interfaces
- Database schema modifications
- Removing existing functionality

**Don't ask for:**
- Implementation details within an agreed approach
- Bug fixes with clear solutions
- Code style or formatting
- Test structure and organization

**How to ask:** Present 2-4 options with trade-offs, state your recommendation, keep each option to one paragraph max.

---

## Development Flow

### Before Implementation
1. Research using available MCP tools (GitHub, web search, Context7) and codebase exploration
2. Draft 2-3 implementation approaches with pros/cons
3. Present options to user with a recommendation before coding

### During Implementation
1. Write failing test → implement → verify → refactor
2. Match existing patterns in the codebase

### After Implementation
Review your changes and report issues by priority:
- **Critical** - Security vulnerabilities, data loss risks, breaking changes
- **High** - Bugs, performance issues, missing error handling
- **Medium** - Code quality, missing tests, unclear naming
- **Low** - Style inconsistencies, minor refactoring opportunities

Propose which issues to fix now vs. defer, with brief rationale.

**Pause and ask if:**
- Scope is growing beyond the original request
- An assumption turns out to be wrong
- A trade-off decision is needed
- Changes would affect unplanned areas

---

## Code Rules

[TEMPLATE: Customize these rules for your language/framework. Below are common patterns - keep what applies, remove what doesn't]

**General:**
- Prefer `unknown` over `any`, narrow types explicitly
- Validate inputs at system boundaries
- Named exports over default exports
- Keep functions under 30 lines, single responsibility
- Use options objects over many positional arguments

**[TEMPLATE: Framework-specific rules]**

```
Examples for different stacks:

React/Next.js:
- Server Components by default, 'use client' only when needed
- Fetch data in Server Components, not useEffect
- Use <Link> for navigation, not router.push
- Use next/image for images, next/font for fonts

Python/Django:
- Use type hints on all function signatures
- Prefer class-based views for complex logic
- Use Django ORM over raw SQL
- Keep business logic in services, views stay thin

Go:
- Handle errors explicitly, don't ignore them
- Use context for cancellation and timeouts
- Prefer composition over inheritance
- Keep interfaces small and focused
```

**Testing:**
- Mock only at system boundaries (APIs, databases, file system)
- Test behavior, not implementation details
- Include edge cases: null, empty, zero, negative, boundary values

---

## Git

**Commits:** `<type>(<scope>): <summary>`

- Max 50 characters for summary
- Use imperative mood ("add" not "added")
- Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`

**Examples:**
- `feat(auth): add OAuth2 login flow`
- `fix(cart): handle empty cart edge case`
- `refactor(api): extract validation middleware`

**CI:** Own your builds. Watch CI runs until green. Fix failures without being asked.

---

## Never Modify Without Approval

[TEMPLATE: List files that should not be changed without explicit permission]

- `.env*` files (contain secrets)
- Main config files (e.g., `webpack.config.js`, `next.config.js`, `tsconfig.json`)
- `package.json` / `requirements.txt` / `go.mod` dependencies
- Database migration files (after they've been applied)
- CI/CD configuration (`.github/workflows/`, `Dockerfile`)

---

## Quality Gates

All must pass before submitting changes:

[TEMPLATE: Customize for your project's tooling]

```bash
npm run lint && npm run typecheck && npm run build && npm run test
```

---

## Debugging

1. **Reproduce** - Using Chrome Devtools MCP, cURL, E2E or Unit tests
2. **Isolate** - Narrow scope with a minimal reproduction
3. **Capture** - Gather logs, traces, error messages, screenshots
4. **Search** - Look for similar issues using Context7 MCP, GitHub MCP, and Serper search
5. **Hypothesize** - Form 2-3 potential causes
6. **Validate** - Use the fastest tool to test each hypothesis
7. **Loop** - Repeat steps 5-6 until root cause is identified
8. **Fix** - Implement the solution
9. **Clean** - Remove debugging artifacts and ensure fix is minimal
10. **Verify** - Add a regression test to prevent recurrence
11. **Report** - Document root cause, solution, and prevention measures

---

## Documentation

Maintain these files in `/docs/` - keep all docs concise and current:

| File | Purpose |
|------|---------|
| `DECISIONS.md` | Architectural decisions with rationale |
| `ARCHITECTURE.md` | System overview and component relationships |
| `CODING_PATTERNS.md` | Repo-specific patterns (not general best practices) |
| `FEATURES.md` | Catalog of implemented features with key details |
| `TROUBLESHOOTING.md` | Common issues and solutions (optional) |

Update docs when changes affect architecture or establish new patterns.

---

## Self-Improvement

Propose updates to AGENTS.md or `/docs/` when:
- You give the same instruction twice → add to relevant doc
- You hit an undocumented gotcha → add to Setup or Troubleshooting
- A pattern is used 2+ times → add to CODING_PATTERNS.md
- An architectural decision is made → add to DECISIONS.md
- Existing docs contradict the codebase → propose correction

Keep docs lean: update existing sections rather than adding new ones.

---

## Monorepo / App-Specific Docs

[TEMPLATE: For monorepos, create AGENTS.md files in subdirectories. Agents read the nearest file in the directory tree.]

```
project-root/
├── AGENTS.md              # Root - shared conventions
├── apps/
│   ├── api/AGENTS.md      # API-specific patterns
│   └── web/AGENTS.md      # Frontend-specific patterns
```

**App-specific AGENTS.md template:**

```markdown
# AGENTS.md - [App Name]

> For project-wide conventions, see root `AGENTS.md`.

## Overview
- **Framework**: [Framework and version]
- **Key Libraries**: [Main dependencies]

## Directory Structure
[Layout for this app]

## Key Commands
[App-specific commands]

## Patterns
[Code examples for this app's patterns]
```

---

# Documentation Templates

## DECISIONS.md

```markdown
# Decisions

## [YYYY-MM-DD] Decision Title
**Context:** Why this came up
**Decision:** What we chose
**Consequences:** Trade-offs accepted
```

## ARCHITECTURE.md

```markdown
# Architecture

## Overview
[1-2 sentence system description]

## Components
| Component | Responsibility | Key Files |
|-----------|---------------|-----------|
| Auth | User authentication | `src/auth/` |

## Data Flow
[User → API → DB, etc.]

## External Dependencies
- Service X: [purpose]
```

## CODING_PATTERNS.md

```markdown
# Coding Patterns

## Pattern Name
**When:** [situation to use this]
**Example:**
\`\`\`typescript
// code from repo
\`\`\`
**Avoid:**
\`\`\`typescript
// anti-pattern
\`\`\`
```

## FEATURES.md

```markdown
# Features

## Feature Name
**Added:** YYYY-MM-DD | **Status:** Active | Deprecated | Experimental

Brief description of what this feature does and why it exists.

**Limitations:** Edge cases, constraints, or gotchas not obvious from the code.
```

## TROUBLESHOOTING.md (optional)

```markdown
# Troubleshooting

## Problem description
**Symptoms:** What you see
**Cause:** Why it happens
**Fix:** How to resolve
```

---

# Tips for Effective AGENTS.md

1. **Keep it concise** - Aim for under 500 lines; split into app-specific files if larger
2. **Use code examples** - Reference real files: "See `src/auth/login.ts`" beats abstract descriptions
3. **Update regularly** - Outdated docs are worse than no docs
4. **Link, don't duplicate** - Reference external docs rather than copying
5. **Focus on "why"** - Explain reasoning, not just rules
6. **Test your docs** - Use them yourself to find gaps