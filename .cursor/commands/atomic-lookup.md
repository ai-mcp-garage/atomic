# atomic-lookup

## Purpose
Retrieve the **most relevant atomic notes** for a given question or situation,
and ground the response explicitly in those notes.

This command prioritizes **precision over completeness**.

---

## Input
- A question, problem, or situation description
- Optional filters:
  - scope
  - domain
  - tags
  - stability threshold

---

## Behavior
1. Search atomic notes using semantic similarity + metadata filters.
2. Select **1–3 highest-signal notes**.
3. Present:
   - Why each note was selected
   - A short synthesized answer grounded in them

---

## Output Format

### Retrieved Notes
For each note:
- title
- id
- why it matched
- key excerpt (quoted)

### Synthesis
- Concise, grounded explanation
- Explicitly reference note IDs
- Call out uncertainty or gaps

---

## Rules
- Do NOT hallucinate missing notes.
- Do NOT promote notes into rules.
- Do NOT merge conflicting notes — surface the conflict.
- If no good notes exist, say so clearly.

---

## Tone
- Calm
- Practical
- Non-authoritative

This command answers questions.
It does NOT create policy.