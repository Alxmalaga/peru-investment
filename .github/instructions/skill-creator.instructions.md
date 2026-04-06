---
applyTo: "**/skills/**, **/*.skill.md, **/SKILL.md, **/.github/instructions/**"
---

# Skill Creator — Build & Optimize AI Instruction Files

Activate when: user wants to create a new skill/instruction file, improve an existing one, run evaluations on a skill, or optimize a skill's trigger description.

## What Is a Skill?

A skill is a structured instruction file (Markdown) that an AI coding assistant reads before executing a specific category of task. It encodes expert knowledge, patterns, and guardrails so the AI behaves like a senior specialist — not a generalist.

**For GitHub Copilot**: Skills become `.instructions.md` files in `.github/instructions/`
**For Claude Desktop**: Skills are `SKILL.md` files referenced in system prompts

## Skill File Structure

```markdown
---
applyTo: "**/*.ext, **/folder/**"   # Glob patterns — when to auto-activate
---

# Skill Name — Short Description

Activate when: [trigger conditions]
Do NOT use when: [exclusions]

## Quick Reference
[Table of tasks → approaches]

## Core Patterns
[The actual expert knowledge — code, rules, workflows]

## Critical Rules
[Numbered or bulleted — the "never do X" and "always do Y" rules]

## Dependencies
[Tools, packages, setup required]
```

## Creating a New Skill — Process

### Step 1: Define Scope
- What is the ONE category of task this skill covers?
- What are the trigger conditions? (file types, keywords, user intent)
- What are the explicit exclusions? (what NOT to do with this skill)

### Step 2: Research Expert Patterns
- Study the best tools, libraries, and workflows for this domain
- Identify the most common mistakes and how to prevent them
- Document the 20% of knowledge that covers 80% of use cases

### Step 3: Write the Skill

#### The `applyTo` Field (Copilot)
```yaml
# Trigger on specific file types
applyTo: "**/*.docx, **/*.xlsx"

# Trigger on folder patterns
applyTo: "**/components/**, **/pages/**"

# Trigger globally (use sparingly)
applyTo: "**"
```

#### Description Quality (for Claude skills)
The description is a trigger classifier — it must:
- Start with "Use this skill when..." or "Activate when..."
- List specific, concrete trigger phrases
- Include explicit NOT conditions
- Be scannable in 2 seconds

```markdown
# ✅ Good description
"Use when user asks to create/edit .xlsx files, mentions 'spreadsheet' or 'Excel',
or needs tabular data with a spreadsheet deliverable.
Do NOT use for Word docs, HTML reports, or Google Sheets API."

# ❌ Bad description
"Use for data tasks involving files and analysis."
```

### Step 4: Test & Evaluate

#### Manual Trigger Test
For each intended trigger, ask: "Would this skill activate correctly?"
For each exclusion, ask: "Would this skill correctly NOT activate?"

#### Evaluation Pairs (10 minimum)
```markdown
## Trigger Cases (should activate)
- "Create an Excel model with NPV and IRR calculations"
- "Fix the formulas in my budget.xlsx"
- "Read the data from report.csv and clean it"

## Non-Trigger Cases (should NOT activate)
- "Help me debug my Python script"
- "Write a summary of this PDF"
- "Create a Google Sheets integration"
```

### Step 5: Optimize

#### Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| Skill activates too broadly | Add more specific `applyTo` patterns or explicit exclusions |
| Skill misses valid triggers | Add more keyword variants to trigger conditions |
| Instructions ignored | Move critical rules to top; use bold/caps for must-follow rules |
| Too long, AI skips sections | Compress to Quick Reference table + expandable details |
| Conflicting with another skill | Add explicit "Do NOT use when [other skill] applies" |

#### Length Guidelines
- Quick Reference table: always include (scannable at a glance)
- Core patterns: include only what's non-obvious or error-prone
- Examples: 1–3 per pattern (enough to demonstrate, not overwhelm)
- Total length: aim for 200–500 lines; trim anything the AI already knows

## Skill File Organization

```
.github/
└── instructions/
    ├── docx.instructions.md        # Word documents
    ├── xlsx.instructions.md        # Excel/spreadsheets
    ├── pptx.instructions.md        # PowerPoint
    ├── pdf.instructions.md         # PDF operations
    ├── frontend-design.instructions.md
    ├── mcp-builder.instructions.md
    └── skill-creator.instructions.md  # This file
```

## Updating Existing Skills

When a skill is failing:
1. Identify whether the issue is **triggering** (wrong activation) or **content** (wrong output)
2. For triggering issues → fix `applyTo` and trigger description
3. For content issues → add/clarify the specific failing pattern
4. Re-test with the same evaluation cases

When a skill is outdated:
- Update library versions and deprecated APIs
- Add new patterns discovered from real usage
- Remove patterns that are now handled by the AI's base knowledge

## Quality Checklist

- [ ] `applyTo` covers all file/folder patterns where skill applies
- [ ] Trigger conditions are specific and non-ambiguous
- [ ] Exclusions are explicit ("Do NOT use for...")
- [ ] Quick Reference table is present and accurate
- [ ] Critical rules use **bold** or `CRITICAL:` prefix
- [ ] Code examples are tested and correct
- [ ] Dependencies section lists all required tools/packages
- [ ] Total length is proportional to domain complexity
