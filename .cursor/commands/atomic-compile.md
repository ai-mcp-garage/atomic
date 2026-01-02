# atomic-compile

## Purpose
Reason collaboratively about how a **set of atomic notes** should be promoted
into reusable agent artifacts:

- AGENTS.md snippets
- Slash commands
- Skills
- Or remain as reference

This command is a **design discussion**, not an automation hammer.

---

## Required Inputs
- **Objective**
  - What are we trying to enable or scale?
- **Audience**
  - Who will use this? (agent, human, mixed)
- **Runtime**
  - Where will this live? (coding agent, CLI, internal tool)
- **Trust Boundary**
  - Can it execute code? Read files? Network?
- **Note Selection**
  - Folder(s), tags, or explicit IDs

---

## Analysis Steps
For each candidate note:
1. Summarize the note’s core idea.
2. Evaluate across these axes:
   - Default vs invoked behavior
   - Cost of forgetting
   - Determinism
   - Scope of impact
   - Stability
3. Identify **possible promotions**, not just one.

---

## Promotion Heuristics

### AGENTS.md Candidate
- Default behavior
- High stability
- Violation is usually a bug
- Constrains *how* work is done

### Slash Command Candidate
- Invoked intentionally
- Workflow or playbook
- Reasoning-heavy
- Context-sensitive

### Skill Candidate
- Procedural
- Deterministic
- Repeatable
- Already scriptable by humans

### Reference-Only
- Personal
- Narrative
- Informational
- Low blast radius

---

## Output Format

### Recommended Promotions
Grouped by artifact type:
- AGENTS.md (with proposed snippets)
- Slash commands (with purpose + inputs)
- Skills (with inputs/outputs)

### Keep as Reference
- Notes that should not be promoted (with reasoning)

### Open Questions
- Ambiguities requiring human judgment

---

## Rules
- Do NOT enforce decisions unilaterally.
- Do NOT collapse nuance.
- Always explain *why* something should or should not be promoted.
