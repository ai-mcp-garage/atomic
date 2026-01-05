---
id: agent-skills-reference
title: "Agent Skills: Modular Capability Packages for AI Agents"
type: reference
scope: tooling
domain: [ai-agents, automation, workflow, extensibility]
stability: medium
intent: guide
tags:
  - skills
  - agent-capabilities
  - modular-design
  - context-management
  - progressive-disclosure
source: doc
confidence: high
---

## What Are Skills?

Modular, self-contained packages that extend an AI agent's capabilities by providing specialized knowledge, workflows, and tools. Think of them as **onboarding guides for specific domains**—they transform a general-purpose agent into a specialized one equipped with procedural knowledge no model can fully possess.

### What Skills Provide

1. **Specialized workflows** — Multi-step procedures for specific domains
2. **Tool integrations** — Instructions for working with specific file formats, APIs, or systems
3. **Domain expertise** — Company-specific knowledge, schemas, business logic
4. **Bundled resources** — Scripts, references, and assets for complex/repetitive tasks

### Skills vs Slash Commands

| Aspect | Skills | Slash Commands |
|--------|--------|----------------|
| Complexity | Multi-step workflows | Simple prompts |
| Structure | Directory with multiple files | Single `.md` file |
| Discovery | Automatic (context-based) | Explicit (`/command`) |
| Resources | Scripts, templates, references | Inline only |
| Triggering | Agent decides based on task | User invokes explicitly |

**Use skills for**: Complex workflows requiring scripts, multiple reference files, or automatic triggering.
**Use slash commands for**: Quick, repeatable prompts you invoke explicitly.

---

## Skill Anatomy

```
skill-name/
├── SKILL.md              # Required: instructions and metadata
├── scripts/              # Optional: executable code (Python/Bash/etc.)
├── references/           # Optional: documentation loaded into context as needed
└── assets/               # Optional: files used in output (templates, images, etc.)
```

### SKILL.md (Required)

Every skill must have a `SKILL.md` containing:

- **Frontmatter (YAML)**: `name` and `description` fields — the ONLY fields the agent reads to decide when to trigger the skill
- **Body (Markdown)**: Instructions loaded AFTER the skill triggers

```markdown
---
name: pdf-editor
description: PDF creation, editing, form filling, and text extraction. Use when working with PDF files for merging, splitting, rotating, filling forms, or extracting content.
---

# PDF Editor

## Overview
[Instructions for using this skill...]
```

### scripts/ (Optional)

Executable code for tasks requiring deterministic reliability or repeatedly rewritten logic.

- **When to include**: Same code rewritten repeatedly; deterministic reliability needed
- **Benefits**: Token efficient, deterministic, can execute without loading into context
- **Note**: Scripts may still be read by agent for patching or environment adjustments

### references/ (Optional)

Documentation loaded into context as needed to inform the agent's process.

- **When to include**: Detailed information the agent should reference while working
- **Examples**: Database schemas, API docs, domain knowledge, company policies
- **Best practice**: For files >10k words, include grep search patterns in SKILL.md
- **Avoid duplication**: Information lives in SKILL.md OR references, not both

### assets/ (Optional)

Files NOT loaded into context—used within output the agent produces.

- **When to include**: Files used in final output
- **Examples**: Templates (.pptx, .docx), images, fonts, boilerplate code directories
- **Benefits**: Agent can use files without loading them into context

---

## Frontmatter Specification

```yaml
---
name: skill-name
description: What the skill does and WHEN to use it
license: Optional license reference
allowed-tools: Optional tool permissions
---
```

| Field | Required | Constraints |
|-------|----------|-------------|
| `name` | Yes | Hyphen-case, lowercase, max 64 chars |
| `description` | Yes | Max 1024 chars, no angle brackets |
| `license` | No | License reference string |
| `allowed-tools` | No | Tool permission specifications |

**Critical**: The `description` is the primary triggering mechanism. Include:
- What the skill does
- Specific scenarios/triggers for when to use it
- File types or task types that should activate it

Do NOT put "When to Use" sections in the body—it's only loaded after triggering.

---

## Progressive Disclosure

Skills use a three-level loading system to manage context efficiently:

1. **Metadata** (name + description) — Always in context (~100 words)
2. **SKILL.md body** — When skill triggers (<5k words recommended)
3. **Bundled resources** — As needed (unlimited; scripts execute without context loading)

### Keep SKILL.md Lean

Target <500 lines. Split content into reference files when approaching this limit. When splitting, clearly describe in SKILL.md when to read each file.

### Disclosure Patterns

**Pattern 1: High-level guide with references**

```markdown
# PDF Processing

## Quick start
[Basic example]

## Advanced features
- **Form filling**: See references/forms.md
- **API reference**: See references/api.md
```

**Pattern 2: Domain-specific organization**

```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── references/
    ├── finance.md
    ├── sales.md
    └── product.md
```

Agent only loads `sales.md` when user asks about sales metrics.

**Pattern 3: Variant-based organization**

```
cloud-deploy/
├── SKILL.md (workflow + provider selection)
└── references/
    ├── aws.md
    ├── gcp.md
    └── azure.md
```

Agent only loads the relevant provider reference.

### Guidelines

- Keep references one level deep from SKILL.md
- For files >100 lines, include a table of contents at top
- Link all reference files directly from SKILL.md

---

## Core Principles

### Concise is Key

The context window is a public good. Only add context the agent doesn't already have.

**Default assumption**: The agent is already very smart. Challenge each piece: "Does this justify its token cost?"

Prefer concise examples over verbose explanations.

### Set Appropriate Degrees of Freedom

Match specificity to the task's fragility:

| Freedom Level | When to Use | Format |
|---------------|-------------|--------|
| **High** | Multiple valid approaches, context-dependent | Text instructions |
| **Medium** | Preferred pattern exists, some variation OK | Pseudocode, parameterized scripts |
| **Low** | Fragile operations, consistency critical | Specific scripts, few parameters |

---

## Workflow Patterns

### Sequential Workflows

Break complex tasks into clear steps:

```markdown
Filling a PDF form involves:

1. Analyze the form (run analyze_form.py)
2. Create field mapping (edit fields.json)
3. Validate mapping (run validate_fields.py)
4. Fill the form (run fill_form.py)
5. Verify output (run verify_output.py)
```

### Conditional Workflows

Guide through decision points:

```markdown
1. Determine the modification type:
   **Creating new content?** → Follow "Creation workflow" below
   **Editing existing content?** → Follow "Editing workflow" below

2. Creation workflow: [steps]
3. Editing workflow: [steps]
```

---

## Output Patterns

### Template Pattern

For strict requirements:

```markdown
## Report structure

ALWAYS use this exact template:

# [Analysis Title]

## Executive summary
[One-paragraph overview]

## Key findings
- Finding 1
- Finding 2

## Recommendations
1. Actionable recommendation
```

For flexible guidance:

```markdown
## Report structure

Sensible default format (adapt as needed):

# [Analysis Title]
## Executive summary
## Key findings
## Recommendations

Adjust sections based on specific context.
```

### Examples Pattern

When output quality depends on seeing examples:

```markdown
## Commit message format

**Example 1:**
Input: Added user authentication with JWT tokens
Output:
feat(auth): implement JWT-based authentication

**Example 2:**
Input: Fixed bug where dates displayed incorrectly
Output:
fix(reports): correct date formatting in timezone conversion

Follow this style: type(scope): brief description
```

---

## What NOT to Include

Skills should only contain essential files. Do NOT create:

- README.md
- INSTALLATION_GUIDE.md
- CHANGELOG.md
- User-facing documentation
- Setup/testing procedures

The skill is for the agent, not humans. No auxiliary context about the creation process.

---

## Skill Creation Process

### Step 1: Understand with Concrete Examples

Ask:
- "What functionality should this skill support?"
- "Give examples of how it would be used."
- "What should trigger this skill?"

Conclude when you have a clear sense of supported functionality.

### Step 2: Plan Reusable Contents

For each example, analyze:
1. How would you execute this from scratch?
2. What scripts, references, or assets would help when doing this repeatedly?

**Examples:**
- PDF rotation → `scripts/rotate_pdf.py`
- Frontend webapp → `assets/hello-world/` template
- BigQuery queries → `references/schema.md`

### Step 3: Initialize the Skill

Use the initialization script to create the skill structure:

```bash
python init_skill.py <skill-name> --path <output-directory>
```

### Step 4: Edit the Skill

1. Implement reusable resources (scripts, references, assets)
2. Test scripts by running them
3. Delete example files you don't need
4. Write SKILL.md with proper frontmatter and instructions

### Step 5: Validate and Package

```bash
python quick_validate.py <path/to/skill-folder>
python package_skill.py <path/to/skill-folder> [output-directory]
```

Validation checks:
- YAML frontmatter format and required fields
- Naming conventions (hyphen-case, max 64 chars)
- Description completeness (no angle brackets, max 1024 chars)
- File organization

The `.skill` file is a zip archive ready for distribution.

### Step 6: Iterate

1. Use the skill on real tasks
2. Notice struggles or inefficiencies
3. Update SKILL.md or resources
4. Test again

---

## Scripts for Skill Creation

### init_skill.py

Creates a new skill from template with proper structure:

```python
#!/usr/bin/env python3
"""
Skill Initializer - Creates a new skill from template

Usage:
    init_skill.py <skill-name> --path <path>

Examples:
    init_skill.py my-new-skill --path skills/public
    init_skill.py my-api-helper --path skills/private
"""

import sys
from pathlib import Path


SKILL_TEMPLATE = '''---
name: {skill_name}
description: [TODO: Complete explanation of what the skill does and when to use it. Include WHEN to use this skill.]
---

# {skill_title}

## Overview

[TODO: 1-2 sentences explaining what this skill enables]

## Resources

This skill includes:

### scripts/
Executable code for specific operations.

### references/
Documentation loaded into context as needed.

### assets/
Files used in output (templates, images, fonts, etc.)

**Delete unneeded directories.** Not every skill requires all three.
'''


def title_case_skill_name(skill_name):
    return ' '.join(word.capitalize() for word in skill_name.split('-'))


def init_skill(skill_name, path):
    skill_dir = Path(path).resolve() / skill_name

    if skill_dir.exists():
        print(f"Error: Skill directory already exists: {skill_dir}")
        return None

    skill_dir.mkdir(parents=True, exist_ok=False)
    print(f"Created skill directory: {skill_dir}")

    skill_title = title_case_skill_name(skill_name)
    skill_content = SKILL_TEMPLATE.format(
        skill_name=skill_name,
        skill_title=skill_title
    )

    (skill_dir / 'SKILL.md').write_text(skill_content)
    print("Created SKILL.md")

    (skill_dir / 'scripts').mkdir(exist_ok=True)
    (skill_dir / 'references').mkdir(exist_ok=True)
    (skill_dir / 'assets').mkdir(exist_ok=True)
    print("Created scripts/, references/, assets/ directories")

    print(f"\nSkill '{skill_name}' initialized at {skill_dir}")
    return skill_dir


def main():
    if len(sys.argv) < 4 or sys.argv[2] != '--path':
        print("Usage: init_skill.py <skill-name> --path <path>")
        sys.exit(1)

    skill_name = sys.argv[1]
    path = sys.argv[3]

    result = init_skill(skill_name, path)
    sys.exit(0 if result else 1)


if __name__ == "__main__":
    main()
```

### quick_validate.py

Validates skill structure and frontmatter:

```python
#!/usr/bin/env python3
"""Quick validation script for skills"""

import sys
import re
import yaml
from pathlib import Path


def validate_skill(skill_path):
    skill_path = Path(skill_path)

    skill_md = skill_path / 'SKILL.md'
    if not skill_md.exists():
        return False, "SKILL.md not found"

    content = skill_md.read_text()
    if not content.startswith('---'):
        return False, "No YAML frontmatter found"

    match = re.match(r'^---\n(.*?)\n---', content, re.DOTALL)
    if not match:
        return False, "Invalid frontmatter format"

    try:
        frontmatter = yaml.safe_load(match.group(1))
        if not isinstance(frontmatter, dict):
            return False, "Frontmatter must be a YAML dictionary"
    except yaml.YAMLError as e:
        return False, f"Invalid YAML: {e}"

    ALLOWED = {'name', 'description', 'license', 'allowed-tools', 'metadata'}
    unexpected = set(frontmatter.keys()) - ALLOWED
    if unexpected:
        return False, f"Unexpected keys: {', '.join(sorted(unexpected))}"

    if 'name' not in frontmatter:
        return False, "Missing 'name' in frontmatter"
    if 'description' not in frontmatter:
        return False, "Missing 'description' in frontmatter"

    name = frontmatter.get('name', '').strip()
    if name:
        if not re.match(r'^[a-z0-9-]+$', name):
            return False, f"Name '{name}' must be hyphen-case"
        if name.startswith('-') or name.endswith('-') or '--' in name:
            return False, f"Name '{name}' has invalid hyphen placement"
        if len(name) > 64:
            return False, f"Name too long ({len(name)} chars, max 64)"

    desc = frontmatter.get('description', '').strip()
    if desc:
        if '<' in desc or '>' in desc:
            return False, "Description cannot contain angle brackets"
        if len(desc) > 1024:
            return False, f"Description too long ({len(desc)} chars, max 1024)"

    return True, "Skill is valid!"


if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python quick_validate.py <skill_directory>")
        sys.exit(1)

    valid, message = validate_skill(sys.argv[1])
    print(message)
    sys.exit(0 if valid else 1)
```

### package_skill.py

Packages a skill into a distributable `.skill` file:

```python
#!/usr/bin/env python3
"""
Skill Packager - Creates a distributable .skill file

Usage:
    package_skill.py <path/to/skill-folder> [output-directory]
"""

import sys
import zipfile
from pathlib import Path


def validate_skill(skill_path):
    """Import from quick_validate.py or inline the validation logic"""
    # See quick_validate.py above
    pass


def package_skill(skill_path, output_dir=None):
    skill_path = Path(skill_path).resolve()

    if not skill_path.exists():
        print(f"Error: Skill folder not found: {skill_path}")
        return None

    if not skill_path.is_dir():
        print(f"Error: Path is not a directory: {skill_path}")
        return None

    skill_md = skill_path / "SKILL.md"
    if not skill_md.exists():
        print(f"Error: SKILL.md not found in {skill_path}")
        return None

    print("Validating skill...")
    valid, message = validate_skill(skill_path)
    if not valid:
        print(f"Validation failed: {message}")
        return None
    print(f"{message}\n")

    skill_name = skill_path.name
    if output_dir:
        output_path = Path(output_dir).resolve()
        output_path.mkdir(parents=True, exist_ok=True)
    else:
        output_path = Path.cwd()

    skill_filename = output_path / f"{skill_name}.skill"

    with zipfile.ZipFile(skill_filename, 'w', zipfile.ZIP_DEFLATED) as zipf:
        for file_path in skill_path.rglob('*'):
            if file_path.is_file():
                arcname = file_path.relative_to(skill_path.parent)
                zipf.write(file_path, arcname)
                print(f"  Added: {arcname}")

    print(f"\nPackaged skill to: {skill_filename}")
    return skill_filename


def main():
    if len(sys.argv) < 2:
        print("Usage: package_skill.py <path/to/skill-folder> [output-directory]")
        sys.exit(1)

    skill_path = sys.argv[1]
    output_dir = sys.argv[2] if len(sys.argv) > 2 else None

    result = package_skill(skill_path, output_dir)
    sys.exit(0 if result else 1)


if __name__ == "__main__":
    main()
```
