# atomic-condense

## Purpose
Convert raw, unstructured input into a **single, well-organized markdown note**
that preserves all information in a coherent document structure.

Unlike `atomic-atomize`, this command consolidates related content into **one note**
with logical sections rather than splitting into many granular pieces.

Write the output in a markdown file in the workspace. If the user doesn't specify a name, create an appropriate, snake_case name.

---

## Input
- Freeform text
- Markdown (any quality)
- Bulleted notes
- Mixed domains (technical, personal, creative, operational)

---

## Output (STRICT)
- Output is a **single markdown note**.
- Note MUST include YAML frontmatter.
- Use headings and sections to organize related content within the note.

```markdown
---
id: <stable-slug-or-uuid>
title: <clear, human-readable title>
type: <concept | heuristic | procedure | checklist | reference | narrative | tool | guide>
scope: <work | home | health | creative | tooling | mixed>
domain: <short list of domains, ex: security, mcp, auth, writing>
stability: <low | medium | high>
intent: <inform | guide | enforce | recall>
tags:
  - <freeform, lowercase>
source: <optional: memory | doc | conversation | link>
confidence: <low | medium | high>
---

# Title

## Section 1
<content>

## Section 2
<content>

```

---

## Consolidation Rules
- **One note per input**, regardless of how many ideas are present.
- Use **headings** (##, ###) to separate distinct topics or subtopics.
- Use **bullet points** for lists of related items.
- Keep related examples, caveats, and context together with their parent topic.
- Preserve the logical flow of the original content.

---

## Writing Rules
- Prefer declarative statements.
- Avoid imperative language unless describing a procedure.
- Preserve nuance and uncertainty.
- Do NOT invent facts.
- Do NOT over-summarize — this is loss-minimizing.

---

## Classification Guidance

Use these heuristics when assigning metadata:

* type
    > concept: explanation or mental model
    > heuristic: rule-of-thumb
    > procedure: step-by-step actions
    > checklist: verification items
    > reference: consolidated information for lookup
    > narrative: memory or story
    > guide: multi-part instructional content

* stability
    > high: unlikely to change
    > medium: context-dependent
    > low: exploratory or personal

* intent
    > inform: knowledge
    > guide: influence thinking
    > enforce: invariant or rule
    > recall: memory aid

---

## When to Use This vs atomic-atomize
- **Use atomic-condense** when you want a single searchable document, a reference guide, or when the content is inherently interconnected.
- **Use atomic-atomize** when you want granular, individually retrievable facts that may be recombined later.

---

## Non-Goals
- Do NOT split into multiple notes.
- Do NOT decide how this note will be used later.
- Do NOT optimize for agents yet.

This command creates a coherent reference document, not scattered fragments.
