---
id: agents-md-reference
title: "AGENTS.md: Briefing Files for AI Agents"
type: reference
scope: tooling
domain: [ai-agents, documentation, workflow]
stability: medium
intent: guide
tags:
  - agents.md
  - ai-agents
  - context-injection
  - project-documentation
  - agent-instructions
source: doc
confidence: high
---

## What is AGENTS.md?

A Markdown file placed in project directories (or a user home directory for personal overrides) that provides AI agents with domain-specific context. It complements human-facing documentation by containing the detailed instructions, conventions, and constraints agents need to operate effectively within a given scope.

## Discovery Hierarchy

Agents typically search in order (first match wins):
1. `./AGENTS.md` in current working directory
2. Nearest parent directory up to project root
3. Any `AGENTS.md` in sub-folders being worked on
4. Personal override in user home directory (e.g., `~/AGENTS.md`)

Multiple files can coexist; closer files take precedence.

## Recommended Sections

| Section | Purpose |
|---------|---------|
| **Context & Scope** | What domain this covers, boundaries of agent authority |
| **Commands & Actions** | Exact commands, tools, or procedures the agent should use |
| **Architecture / Structure** | Overview of how things are organized |
| **Constraints** | What the agent must NOT do, hard limits |
| **Conventions & Patterns** | Naming, formatting, style rules |
| **Gotchas** | Non-obvious pitfalls, edge cases, domain quirks |

## Syntax Expectations

- Top-level headings (`#`) denote sections
- Bullet lists for commands or rules
- Inline code (backticks) for exact commands, paths, identifiers
- Links to external docs or resources

## Best Practices

- **Keep it short**: ≤150 lines; long files bury signal
- **Be concrete**: Specific instructions beat vague guidance
- **Update alongside changes**: Treat as living documentation
- **One source of truth**: Link to docs instead of duplicating
- **Make requests precise**: Specificity improves task completion
- **Define proof of success**: How does the agent know it's done?

## Agent Workflow

1. **Ingestion**: Agent loads nearest AGENTS.md into context window
2. **Planning**: Instructions inform task decomposition and approach
3. **Execution**: Conventions and constraints guide actions
4. **Validation**: Success criteria and gotchas reduce errors

## Warning Signs of Agent Drift

- Plans rewriting mid-execution
- Actions outside declared scope
- Claims of completion without evidence
- Output bloated with unrelated changes

**Recovery**: Tighten scope, salvage valid work, restart with clearer instructions, or intervene manually.
