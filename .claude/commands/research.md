# Research Command

Topic: $ARGUMENTS

## Setup

Create `research/{slug}/` with:

- `findings.json` (empty array)
- `state.json`: `{iteration: 0, totalSearches: 0, questions: [], completed: []}`

## Phase 1: Decomposition

Break topic into 7-10 sub-questions covering:

1. Core definitions and background
2. Current state / recent developments
3. Key players and entities
4. Competing perspectives
5. Data and evidence
6. Criticisms and limitations
7. Future implications

## Phase 2: Broad Research

For each question, spawn a subagent:

```
claude -p "You have web_search and firecrawl_scrape tools.

Research thoroughly: {QUESTION}

1. Run 4-6 varied searches (rephrase, try different angles)
2. Pick 5 most authoritative URLs (prefer primary sources, papers, official docs)
3. firecrawl_scrape each
4. Extract all substantive facts—be comprehensive

Return JSON only:
{
  question: string,
  findings: string[],        // 15-25 detailed facts
  sources: {url, title, type}[],  // type: primary|secondary|news|academic
  gaps: string[],
  contradictions: string[],
  confidence: 'high'|'medium'|'low',
  searchesRun: number
}"
```

Append to `findings.json`. Update `state.json`.

## Phase 3: Gap Filling

1. Collect all gaps and contradictions from `findings.json`
2. Generate 5-8 follow-up questions
3. Spawn subagents for each
4. Repeat until: no major gaps OR iteration >= 4 OR totalSearches >= 50

## Phase 4: Synthesis

Write `report.md`:

1. **Executive Summary** (300 words)
2. **Background & Context**
3. **Key Findings** (by theme)
4. **Competing Perspectives**
5. **Evidence Quality Assessment**
6. **Open Questions**
7. **Conclusion**
8. **Sources** (grouped by type)

## Rules

- Orchestrator coordinates—subagents search
- Target: 40-60 total searches
- Prioritize primary sources
- Continue until comprehensive
