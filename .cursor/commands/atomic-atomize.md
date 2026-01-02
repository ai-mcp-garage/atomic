# atomic-atomize

## Purpose
Convert raw, unstructured input into **clean, atomic, searchable notes**
that can be safely stored, retrieved, and later promoted into agent artifacts
(AGENTS.md snippets, slash commands, or skills).

This command is **loss-minimizing**: preserve meaning, do not over-summarize.
Write the output in a markdown file in the workspace. If the user doesn't specify a name, create an appropriate, snake_case name.
---

## Input
- Freeform text
- Markdown (any quality)
- Bulleted notes
- Mixed domains (technical, personal, creative, operational)

---

## Output (STRICT)
- Output MUST be wrapped in **four backticks** (````) to allow safe nesting.
- Output consists of **one or more atomic markdown notes**.
- Each note MUST include YAML frontmatter.

````markdown
---
id: <stable-slug-or-uuid>
title: <clear, human-readable title>
type: <concept | heuristic | procedure | checklist | reference | narrative | tool>
scope: <work | home | health | creative | tooling | mixed>
domain: <short list of domains, ex: security, mcp, auth, writing>
stability: <low | medium | high>
intent: <inform | guide | enforce | recall>
tags:
  - <freeform, lowercase>
source: <optional: memory | doc | conversation | link>
confidence: <low | medium | high>
---
<note body>

````

⸻

Atomicity Rules
	•	One idea per note.
	•	If multiple ideas are present, split them.
	•	Examples and caveats stay with the idea they support.

⸻

Writing Rules
	•	Prefer declarative statements.
	•	Avoid imperative language unless describing a procedure.
	•	Preserve nuance and uncertainty.
	•	Do NOT invent facts.
	•	Do NOT enforce policy.

⸻

Classification Guidance

Use these heuristics when assigning metadata:

* type
    > concept: explanation or mental model
    > heuristic: rule-of-thumb
    > procedure: step-by-step actions
    > checklist: verification items
    > narrative: memory or story

* stability
    > high: unlikely to change
    > medium: context-dependent
    > low: exploratory or personal

* intent
    > inform: knowledge
    > guide: influence thinking
    > enforce: invariant or rule
    > recall: memory aid

⸻

Non-Goals
	* Do NOT decide how this note will be used later.
	* Do NOT merge notes.
	* Do NOT optimize for agents yet.

This command creates raw material, not finished tools.