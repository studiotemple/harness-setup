---
name: harness-check
description: Use when reviewing or auditing the current project's AI harness configuration, checking CLAUDE.md quality, diagnosing missing rules or hooks, or when asked to evaluate how well the project is set up for AI-assisted development.
---

# Harness Diagnostic Skill

## Overview

Diagnose the current project's AI harness across six axes: **Structure, Context, Planning, Execution, Verification, and Compounding**.

> "Every component of a harness encodes an assumption about what the model can't do on its own. Those assumptions go stale fast as models improve."

This skill reads existing configuration, evaluates each axis, identifies stale assumptions, and produces a diagnostic report. It does NOT modify any files — changes require explicit user approval.

---

## Process: Inventory -> Diagnose -> Evolve -> Report

### Step 1: File Inventory

Check existence and quality of the following files:

| File | Exists | Quality Check |
|------|--------|---------------|
| `~/.claude/CLAUDE.md` (global) | ?/? | — |
| `CLAUDE.md` | ?/? | Under 200 lines? Up to date? |
| `.claude/settings.json` | ?/? | Has hooks? Permission mode set? |
| `.claude/rules/code-style.md` | ?/? | Matches actual stack? |
| `.claude/rules/testing.md` | ?/? | Has glob pattern? |
| `.claude/rules/security.md` | ?/? | Has glob pattern? |
| `.claude/commands/start-task.md` | ?/? | Includes Sprint Contract? |
| `.claude/commands/review.md` | ?/? | Has verification steps? |
| `.claude/commands/session-wrap.md` | ?/? | Has pattern discovery? |
| `.dev/` directory | ?/? | Has learnings/? |

Also check for:
- Subfolder-specific `CLAUDE.md` files (if monorepo — look for `*/CLAUDE.md` and `packages/*/CLAUDE.md`)
- `.claude/memory/` files (if multi-session work)
- Custom commands beyond the standard set (list any extra `.claude/commands/*.md`)

Read each file that exists. Note line counts, last-modified dates, and whether content references the actual tech stack detected in `package.json`, `go.mod`, `Cargo.toml`, `pyproject.toml`, or equivalent.

### Step 2: 6-Axis Diagnosis

Rate each axis as: **Green** (Good) / **Yellow** (Needs Improvement) / **Red** (Missing).

#### Axis 1 — Structure

Evaluate project scaffolding and safety configuration.

- Is `.claude/` directory present with proper subdirectories?
- Is Permission Mode configured in `settings.json`? (`plan` vs `auto`)
- Are safety hooks in place? (dangerous command blocking, pre-commit checks)
- Are there unnecessary over-restrictions blocking productive work?

Criteria:
- **Green**: `settings.json` with hooks + permission mode + organized folder structure
- **Yellow**: Partial setup (e.g., `CLAUDE.md` exists but no hooks or settings)
- **Red**: No `.claude/` directory or settings

#### Axis 2 — Context

Evaluate how well the project communicates its identity and rules to the model.

- Is `CLAUDE.md` current and under 200 lines?
- Do rules in `.claude/rules/` have proper glob patterns for targeted loading?
- Does the tech stack described in `CLAUDE.md` match what's actually in the project?
- Are there subfolder `CLAUDE.md` files where the monorepo structure demands them?

Criteria:
- **Green**: Accurate `CLAUDE.md` + rules with glob patterns + stack matches reality
- **Yellow**: `CLAUDE.md` exists but outdated, over 200 lines, or missing glob-targeted rules
- **Red**: No `CLAUDE.md` or rules directory

#### Axis 3 — Planning

Evaluate whether there is a structured plan-before-code workflow.

- Does `/start-task` command exist with a Sprint Contract template?
- Is there a defined plan-before-code workflow?
- Is `.claude/memory/` used for cross-session context preservation?

Criteria:
- **Green**: `start-task` with Sprint Contract + `memory/` for context continuity
- **Yellow**: `start-task` exists but lacks Sprint Contract or no `memory/` directory
- **Red**: No planning workflow commands

#### Axis 4 — Execution

Evaluate whether the execution workflow fits the project's actual scale.

- Is the workflow complexity appropriate for the project size?
- Is there unnecessary ceremony for simple projects?
- Are multi-agent patterns used only when justified by project complexity?

Criteria:
- **Green**: Workflow complexity matches project complexity
- **Yellow**: Over-engineered or under-structured for the actual scale
- **Red**: No execution guidance in any form

#### Axis 5 — Verification

Evaluate post-implementation quality checks.

- Does `/review` command exist with concrete verification steps?
- Is type checking integrated? (`tsc`, `mypy`, `go vet`, `cargo check`, etc.)
- Are E2E or integration verification methods defined?

Criteria:
- **Green**: Review command + type checks + E2E methods defined
- **Yellow**: Review exists but lacks specific verification commands or steps
- **Red**: No verification workflow

#### Axis 6 — Compounding

Evaluate whether the harness improves over time through feedback loops.

- Does `/session-wrap` command exist?
- Are learnings being recorded in `.dev/learnings/` or equivalent?
- Have stale rules been identified and removed over time?

Criteria:
- **Green**: `session-wrap` + active learnings directory with entries + evidence of rule evolution
- **Yellow**: `session-wrap` exists but no learnings recorded or rules never updated
- **Red**: No improvement feedback loop

### Step 3: Harness Evolution Check

This is the most critical step. For each existing rule, hook, and command, ask:

> "Does this still encode a valid assumption, or has the model outgrown it?"

Examples of assumptions that may have gone stale:
- Overly restrictive file access rules (model may now handle safely)
- Redundant safety hooks for things the model naturally avoids
- Over-prescriptive code style rules that match the model's default behavior
- Complex multi-step verification for tasks the model gets right consistently
- Verbose prompt templates when the model infers intent from brief instructions

For each element, recommend one of:
- **Remove**: The assumption is clearly outdated; the rule adds friction without value
- **Relax**: Reduce strictness while keeping the guardrail's intent
- **Keep**: Still valid and necessary for this project

### Step 4: Diagnostic Report

Output the final report in this format:

```
## Harness Diagnostic Report

### File Inventory
[Table from Step 1 — filled in with actual findings]

### 6-Axis Assessment

| Axis | Status | Key Finding |
|------|--------|-------------|
| Structure | Green/Yellow/Red | ... |
| Context | Green/Yellow/Red | ... |
| Planning | Green/Yellow/Red | ... |
| Execution | Green/Yellow/Red | ... |
| Verification | Green/Yellow/Red | ... |
| Compounding | Green/Yellow/Red | ... |

### Immediate Actions (Red items)
[List of critical missing elements to add now]

### Gradual Improvements (Yellow items)
[List of enhancements to consider in upcoming sessions]

### Elements to Remove or Relax
[List of stale assumptions that can be simplified — from Step 3]

### Harness Maturity Score
[X/6 axes at Green] — [Level]
- 0-1: Beginner — Run /harness-setup to bootstrap
- 2-3: Developing — Key elements present, gaps remain
- 4-5: Mature — Solid foundation, focus on evolution
- 6: Evolving — All axes covered, actively maintained
```

---

## Rules

1. **Read-only** — This skill diagnoses. It does not create, modify, or delete any files. All changes require explicit user approval.
2. **Stack-agnostic** — Works with any language or framework. Detect the actual stack from project files before evaluating.
3. **Proportional** — A simple single-file script does not need 6 Green axes. Judge relative to project complexity.
4. **Honest** — If the harness is over-engineered for the project, say so. More configuration is not always better.
5. **Actionable** — Every finding should include a concrete next step, not just a status color.
