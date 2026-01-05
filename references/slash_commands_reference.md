---
id: slash-commands-reference
title: "Slash Commands: Reusable Prompt Templates for AI Agents"
type: reference
scope: tooling
domain: [ai-agents, prompts, automation, workflow]
stability: medium
intent: guide
tags:
  - slash-commands
  - prompts
  - workflows
  - agent-instructions
  - templates
  - automation
source: doc
confidence: high
---

## What Are Slash Commands?

Markdown files containing reusable prompts that AI agents can execute on demand. They allow users to define frequently-used instructions once and invoke them with a short command. Also called **prompts** (generic) or **workflows** (Google/Antigravity terminology).

## Core Syntax

```
/<command-name> [arguments]
```

- Command name derived from filename (minus `.md` extension)
- Arguments are optional, passed after the command name

## Scope & Discovery

| Scope | Location | Precedence |
|-------|----------|------------|
| **Project** | `./<agent>/commands/` | Higher (wins conflicts) |
| **Personal** | `~/.<agent>/commands/` | Lower (fallback) |

Project commands are shared with teams via version control. Personal commands are private and portable across projects.

## Namespacing

Subdirectories group related commands without affecting the command name:

```
.claude/commands/frontend/component.md  → /component (project:frontend)
.claude/commands/backend/deploy.md      → /deploy (project:backend)
```

Same-name commands in different subdirectories coexist; the namespace appears in descriptions.

## Arguments

### Capture All: `$ARGUMENTS`

```markdown
Fix issue #$ARGUMENTS following our coding standards
```

Usage: `/fix-issue 123 high-priority` → `$ARGUMENTS` = "123 high-priority"

### Positional: `$1`, `$2`, `$3`...

```markdown
Review PR #$1 with priority $2 and assign to $3
```

Usage: `/review-pr 456 high alice` → `$1`="456", `$2`="high", `$3`="alice"

Use positional arguments when parameters have distinct roles.

## Dynamic Content

### Shell Execution: `!` Prefix

Execute commands before the prompt runs; output injected into context:

```markdown
Current git status: !`git status`
Recent commits: !`git log --oneline -10`
```

Requires appropriate tool permissions in frontmatter.

### File References: `@` Prefix

Include file contents inline:

```markdown
Review the implementation in @src/utils/helpers.js
Compare @src/old-version.js with @src/new-version.js
```

## Frontmatter Specification

```yaml
---
allowed-tools: Bash(git add:*), Bash(git status:*)  # Tool permissions
argument-hint: [message]                             # Autocomplete hint
description: Brief description of the command        # Shown in help
model: claude-3-5-haiku-20241022                     # Override model
disable-model-invocation: false                      # Prevent auto-invocation
---
```

| Field | Purpose | Default |
|-------|---------|---------|
| `allowed-tools` | Tools the command can use | Inherit from session |
| `argument-hint` | Expected arguments for autocomplete | None |
| `description` | Brief explanation | First line of prompt |
| `model` | Specific model override | Inherit from session |
| `disable-model-invocation` | Block programmatic invocation | false |

## Complete Example

```markdown
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
argument-hint: [commit message]
description: Create a git commit with context
---

## Context

- Current git status: !`git status`
- Staged diff: !`git diff --cached`
- Current branch: !`git branch --show-current`

## Task

Create a commit with message: $ARGUMENTS
If no message provided, generate one from the diff.
```

## Best Practices

- **One purpose per command**: Keep commands focused and composable
- **Use frontmatter**: Always include `description` for discoverability
- **Hint arguments**: Use `argument-hint` to guide users
- **Scope permissions**: Only grant tools the command actually needs
- **Inject context dynamically**: Use `!` for fresh state, `@` for file contents
- **Keep prompts short**: Long commands dilute focus; link to docs instead

## Slash Commands vs Skills/Agents

| Aspect | Slash Commands | Skills/Agents |
|--------|----------------|---------------|
| **Complexity** | Simple prompts | Complex workflows |
| **Structure** | Single `.md` file | Directory with multiple files |
| **Discovery** | Explicit (`/command`) | Automatic (context-based) |
| **Resources** | Inline only | Scripts, templates, references |

**Use slash commands for**: Quick, repeatable prompts you invoke explicitly.  
**Use skills for**: Multi-step workflows requiring scripts or multiple reference files.

## Programmatic Invocation

Agents can invoke slash commands via tool calls during conversations. To enable:

- Command must have `description` in frontmatter
- Must not be a built-in command
- Must not have `disable-model-invocation: true`

Encourage invocation by referencing commands by name in prompts:

```
Run /write-unit-test when you are about to start writing tests.
```
