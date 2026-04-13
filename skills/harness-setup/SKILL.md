---
name: harness-setup
description: Use when setting up a new project for AI-assisted development, configuring Claude Code harness, or when asked to create CLAUDE.md, rules, hooks, or slash commands for a project. Also use when asked about project scaffolding for AI workflows.
---

# Harness Setup Wizard

> "Every component of a harness encodes an assumption about what the model can't do on its own.
> Those assumptions go stale fast as models improve." — Anthropic Engineering

## Overview

A **harness** is the structured environment that enables an AI agent to work safely and effectively within a project. It encodes project knowledge, safety guardrails, planning workflows, and verification routines so the agent can operate with minimal supervision.

### The Six Axes of a Harness

| Axis | Purpose | Key Files |
|------|---------|-----------|
| **Structure** | Project layout, file organization, navigation aids | `CLAUDE.md`, `.dev/` |
| **Context** | Tech stack, conventions, domain knowledge | `CLAUDE.md`, `.claude/rules/` |
| **Planning** | Task decomposition, sprint contracts, approval gates | `/start-task` command |
| **Execution** | Code generation, tool permissions, safety hooks | `.claude/settings.json` |
| **Verification** | Self-review, testing, quality gates | `/review` command |
| **Compounding** | Learning across sessions, harness self-improvement | `/session-wrap`, `.dev/learnings/` |

### What This Skill Does

This skill runs an **interview-based wizard** that:
1. Analyzes the current project to detect tech stack and existing harness elements
2. Interviews the user to fill in gaps
3. Generates a complete, project-tailored harness
4. Reports what was created and next steps

---

## Process: Analyze -> Interview -> Generate -> Report

---

### Step 1: Project Analysis

Read the following files (only those that exist) to detect the tech stack and current harness state:

| File | Purpose |
|------|---------|
| `~/.claude/CLAUDE.md` | Global AI configuration |
| `CLAUDE.md` | Project-level AI configuration |
| `package.json` | Node.js / JavaScript / TypeScript stack |
| `pyproject.toml` | Python stack |
| `go.mod` | Go stack |
| `Cargo.toml` | Rust stack |
| `Gemfile` | Ruby stack |
| `build.gradle` / `pom.xml` | Java / Kotlin stack |
| `README.md` | Project overview and description |
| `.claude/settings.json` | Existing hooks and permissions |
| `.claude/rules/` | Existing conditional rules |
| `.claude/commands/` | Existing slash commands |
| `.dev/` | Existing dev workspace |

After reading, produce a summary in this format:

```
## Current State
- Stack: [detected tech stack, e.g., "Next.js 14, TypeScript, PostgreSQL, Prisma"]
- Existing harness elements: [list what's already present]
- Missing elements: [list what's absent]
- Estimated effort: [small / medium / large]
```

---

### Step 2: Interview

Present the current state summary first. Then ask only what is missing (use AskUserQuestion, maximum 4 questions):

1. **Primary AI use case**: What kind of tasks do you primarily use AI for? (new features / bug fixes / refactoring / exploration / other)
2. **Team or solo**: Solo project or team-shared? (affects permission mode)
3. **Forbidden actions**: Any tasks AI must NEVER do? (e.g., deploy to production, delete databases, modify .env, push to main)
4. **Multi-session needs**: Any long-running multi-session tasks that need continuity? (e.g., large features spanning multiple days)

Skip questions where the answer is already evident from the project analysis.

---

### Step 3: Generate Harness Files

Based on interview results, generate or update the following files. **Critical rule: if a file already exists, READ it first and MERGE changes. Never overwrite existing configuration.**

#### Target File Structure

```
project-root/
├── CLAUDE.md                    <- Project map (under 200 lines)
├── .claude/
│   ├── settings.json            <- Hooks + Permission Mode
│   ├── rules/
│   │   ├── code-style.md       <- Always loaded
│   │   ├── testing.md          <- glob: *.test.*, __tests__/
│   │   └── security.md        <- glob: auth/, api/, *.sql
│   └── commands/
│       ├── start-task.md       <- /start-task
│       ├── review.md           <- /review
│       └── session-wrap.md     <- /session-wrap
└── .dev/
    ├── README.md
    ├── learnings/
    ├── experiments/
    └── scratch/
```

For multi-session needs, also generate:

```
.claude/
└── memory/
    ├── features.json            <- Feature checklist (only `passes` field mutable)
    └── claude-progress.txt      <- Session handoff notes
```

---

## File Generation Specs

### CLAUDE.md

The project map. Keep under 200 lines. Adapt the template below to the detected stack.

```markdown
# [Project Name] — AI Context

> [One-line project description]

## Core Rules (Always Apply)

1. **Plan First**: Run `/start-task` before coding new features
2. **Read First**: Read relevant docs/code before making changes
3. **Report Per Unit**: Run `/review` after completing each feature
4. **Ask Before Breaking**: Request user decision on API changes, unplanned files, trade-offs
5. **Wrap Up**: Run `/session-wrap` before ending a session

## Tech Stack

- **Framework**: [detected]
- **Database**: [detected]
- **Language**: [detected]
- **Auth**: [detected]
- **Deploy**: [detected]

## Project Structure

[Brief description of key directories and their purposes — auto-detected from file tree]

## Custom Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/start-task` | Plan -> Sprint Contract -> Approval | Starting new work |
| `/review` | Self-check + verification | Before claiming done |
| `/session-wrap` | Pattern discovery -> harness improvement | End of session |

## Key Patterns

[Auto-detected conventions: naming, imports, error handling, API response shapes]

## Known Pitfalls

[Project-specific gotchas discovered during analysis or recorded in .dev/learnings/]
```

---

### .claude/settings.json

Hooks and permission configuration. Adapt danger patterns to the detected stack.

```json
{
  "permissions": {
    "defaultMode": "plan"
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"$CLAUDE_TOOL_INPUT\" | python3 -c \"\nimport sys, json, re\ntry:\n    data = json.load(sys.stdin)\n    cmd = data.get('command', '')\n    danger_patterns = [\n        (r'git push.*--force', 'git push --force is blocked.'),\n        (r'DROP\\\\s+TABLE', 'DROP TABLE is blocked.'),\n        (r'DELETE\\\\s+FROM\\\\s+\\\\w+\\\\s*;', 'DELETE without WHERE is blocked.'),\n        (r'rm\\\\s+-rf\\\\s+/', 'rm -rf / is blocked.'),\n        (r'git\\\\s+reset\\\\s+--hard', 'git reset --hard requires explicit approval.'),\n    ]\n    for pattern, msg in danger_patterns:\n        if re.search(pattern, cmd, re.IGNORECASE):\n            print(f'BLOCKED: {msg}', file=sys.stderr)\n            sys.exit(2)\nexcept Exception:\n    pass\n\""
          }
        ]
      }
    ]
  }
}
```

**Permission Mode guidance:**

| Mode | Behavior | Recommended For |
|------|----------|-----------------|
| `"plan"` | Show plan before execution, ask for approval | Team projects, production codebases |
| `"auto"` | Auto-execute without approval prompts | Solo projects, prototyping |

---

### .claude/rules/code-style.md

Always loaded. Content varies by detected stack.

**TypeScript / JavaScript:**

```markdown
## Code Style

- Use camelCase for variables and functions, PascalCase for components and types
- Never use `any` — use `unknown` and narrow with type guards
- Prefer `async/await` over raw Promises; always handle errors
- Functions should be under 20 lines; extract when logic is reusable (3+ call sites)
- Use early returns to reduce nesting
- Comments explain WHY, not WHAT — no restating code in English
- Import order: external packages -> internal modules -> relative imports
- Prefer named exports over default exports
```

**Python:**

```markdown
## Code Style

- Use snake_case for variables and functions, PascalCase for classes
- Add type hints to all function signatures
- Catch specific exceptions — never bare `except:`
- Functions should be under 20 lines; extract when logic is reusable (3+ call sites)
- Use early returns to reduce nesting
- Comments explain WHY, not WHAT
- Import order: stdlib -> third-party -> local
- Use pathlib over os.path
```

**Go:**

```markdown
## Code Style

- Follow Effective Go conventions
- Handle errors immediately after the call — no deferred error checks
- Functions should be under 20 lines
- Use early returns to reduce nesting
- Comments on exported symbols follow godoc format
- Keep interfaces small (1-3 methods)
- Prefer composition over inheritance
```

**General (fallback):**

```markdown
## Code Style

- Functions should be under 20 lines
- Use early returns to reduce nesting
- Comments explain WHY, not WHAT — no restating code
- Three similar lines are better than a premature abstraction
- No helpers/utilities for code used only once
- Import order: external -> internal -> relative
```

---

### .claude/rules/testing.md

Conditionally loaded via glob patterns.

```markdown
---
glob: **/*.test.*, **/__tests__/**, **/test_*.py, **/*_test.go, **/*.spec.*
---

## Testing Rules

- Write integration tests first, unit tests as supplementary
- Follow the **Arrange / Act / Assert** pattern
- Never commit `.only()` or `.skip()` — they disable other tests silently
- Test the behavior, not the implementation — avoid mocking internals
- Each test should be independent and idempotent
- Name tests descriptively: `should [expected behavior] when [condition]`
- Run the full related test suite before claiming work is done
- For E2E tests: test the critical user path end-to-end before marking complete
```

---

### .claude/rules/security.md

Conditionally loaded via glob patterns.

```markdown
---
glob: **/auth/**, **/*.sql, **/api/**, **/routes/**, **/middleware/**
---

## Security Rules

- Never modify `.env` or secret files without explicit user confirmation
- No `git push --force` without explicit user request
- No `DROP TABLE`, no `DELETE FROM` without `WHERE` clause
- SQL: Use parameterized queries only (`$1`, `?`, `:name` bindings) — never string interpolation
- Auth: Verify authentication and authorization on ALL mutation endpoints
- Secrets: Never log, print, or commit secrets, tokens, or credentials
- Input: Validate and sanitize all user-facing inputs at the boundary
- CORS: Do not set `Access-Control-Allow-Origin: *` in production
- Dependencies: Do not add new dependencies without stating the reason
```

---

### .claude/commands/start-task.md

The task entry point. Deep-read, plan, then code.

```markdown
# Start Task

New task entry point. Deep-read the codebase, write a Sprint Contract, get user approval before coding.

## Process

### Step 0: Interview
- Summarize what you understand about the request
- Ask a maximum of 3 clarifying questions
- Confirm scope boundaries (what is IN and OUT of scope)

### Step 1: Deep Codebase Read
- Read full file logic, not just function signatures
- Trace imports and dependencies for all files you plan to modify
- Search for existing similar features or patterns to reuse
- Identify files that will be affected (directly and indirectly)

### Step 2: Sprint Contract
Write to `.claude/memory/plan.md`:
- **Goal**: One sentence describing what will be built
- **Scope**: Files to create/modify (with line-level detail)
- **Completion Criteria**: Measurable, verifiable conditions (not vague "works correctly")
- **Out of Scope**: Explicitly list what will NOT be done
- **Risks**: Potential issues and mitigation strategies

### Step 3: Context Document
Write to `.claude/memory/context.md`:
- Codebase findings relevant to this task
- Design decisions made and why
- Rejected alternatives and reasoning
- File paths and their roles in the change

### Step 4: Checklist
Write to `.claude/memory/todo.md`:
- Step-by-step implementation checklist
- Each item is small enough to complete in one focused pass
- Include verification steps between implementation steps

### Step 5: Wait for Approval
Present the Sprint Contract to the user.
**NO CODING BEFORE USER APPROVAL.**

### Step 6: Execute
After approval:
- Follow the checklist in order
- Git commit per logical unit of work
- Run verification after each unit
- Update todo.md as items are completed
```

---

### .claude/commands/review.md

Self-review before claiming completion.

```markdown
# Review

Self-review before claiming work is done.

> Key mindset: Review as a skeptical external reviewer.
> Suppress the urge to praise your own work. Find problems.

## Process

### Step 1: Inventory Changes
- Run `git diff --name-only` to list all modified files
- Summarize what changed and why for each file

### Step 2: Code Quality Check
For each modified file, verify:
- [ ] Error handling: Are all error paths handled? No swallowed exceptions?
- [ ] Auth: Are new endpoints protected? Are permissions checked?
- [ ] SQL safety: Parameterized queries only? No injection vectors?
- [ ] Secret exposure: No credentials in code, logs, or comments?
- [ ] Type safety: No `any`, no unchecked casts?

### Step 3: Verification Loop
- [ ] Type check passes (tsc, mypy, etc.)
- [ ] Trace I/O flow: follow data from input to output
- [ ] Cross-file impact: check all callers of modified functions
- [ ] Run related test suite — all green?
- [ ] E2E test for the affected user path

### Step 4: Sprint Contract Verification
- Open `.claude/memory/plan.md`
- Run each completion criterion explicitly
- **"Almost done" is NOT done.** Every criterion must PASS or be marked FAIL with explanation.

### Step 5: Edge Cases
Test or reason through:
- Empty / null / undefined inputs
- Unauthorized access attempts
- Network failure / timeout scenarios
- Concurrent modification
- Maximum / minimum boundary values

### Step 6: Verdict
- All checks PASS -> "Self-review passed. Ready for user review."
- Any check FAIL -> Fix the issue, then re-run this review from Step 1.
```

---

### .claude/commands/session-wrap.md

End-of-session wrap-up and harness improvement.

```markdown
# Session Wrap

Wrap up the current session and improve the harness for future sessions.

## Process

### Step 1: Review Session Work
- Read `.claude/memory/plan.md` and `.claude/memory/todo.md`
- Summarize what was completed vs. what remains
- Note any incomplete items for the next session

### Step 2: Pattern Discovery
Look for opportunities to improve the harness:

| Signal | Action |
|--------|--------|
| Repeated manual work | Suggest a new `/command` to automate it |
| Repeated mistakes | Suggest a new `.claude/rules/` file to prevent them |
| Non-obvious discovery | Record in `.dev/learnings/` for future reference |
| Stale assumption | Suggest removing or updating an existing rule/hook |
| Missing context | Suggest additions to `CLAUDE.md` |

### Step 3: Harness Health Check
- [ ] Is `CLAUDE.md` under 200 lines? (If over, split into rules)
- [ ] Are all `.claude/rules/` files still relevant?
- [ ] Are there unused commands in `.claude/commands/`?
- [ ] Is `.claude/memory/` stale? (Clean up completed plans)
- [ ] Any new patterns worth codifying?

### Step 4: Session Summary
Output a summary for the user:

```
## Session Summary

### Completed
- [list of completed items]

### Remaining
- [list of incomplete items, if any]

### Harness Improvements
- [suggested changes to rules, commands, or CLAUDE.md]

### Next Session Handoff
- [context the next session needs to continue effectively]
```

If multi-session continuity is enabled, also update `.claude/memory/claude-progress.txt`.
```

---

### Multi-Session Memory Pattern

When the user needs continuity across sessions, generate these additional files:

#### .claude/memory/features.json

```json
{
  "project": "[Project Name]",
  "lastUpdated": "YYYY-MM-DD",
  "features": [
    {
      "id": "feat-001",
      "category": "functional",
      "description": "Feature description",
      "steps": ["How to verify this feature"],
      "passes": false
    },
    {
      "id": "feat-002",
      "category": "non-functional",
      "description": "Another feature",
      "steps": ["Verification method 1", "Verification method 2"],
      "passes": false
    }
  ]
}
```

Rules: Only the `passes` field should be mutated during development. JSON format is safer than markdown for preventing unintended modifications.

#### .claude/memory/claude-progress.txt

```
# Session Progress

## Last Updated: [date]

## Current State
- Working on: [feature/task]
- Blocked by: [nothing / specific issue]
- Next action: [what to do next]

## Completed This Session
- [item 1]
- [item 2]

## Key Decisions
- [decision and reasoning]

## Files Modified
- [file path]: [what changed and why]
```

#### init.sh (Optional)

A bootstrap script that restores the development environment and displays session progress:

```bash
#!/bin/bash
echo "=== Development Environment Setup ==="

# Install dependencies (adapt to your stack)
# npm install / pip install / go mod download

# Show session progress
echo ""
echo "=== Session Progress ==="
cat .claude/memory/claude-progress.txt 2>/dev/null || echo "No progress file found."
```

---

## Multi-Agent Decision Tree

When a task is too large for a single agent, use this decision tree to select an architecture:

```
Is a single LLM call sufficient?
  |
  YES --> Single agent (no orchestration needed)
  |
  NO
  |
  v
Can the task decompose into independent subtasks?
  |
  YES --> Orchestrator-Worker pattern
  |        - Orchestrator plans and distributes
  |        - Workers execute independently in parallel
  |        - Orchestrator merges results
  |
  NO
  |
  v
Can you trust the agent's self-evaluation of generation quality?
  |
  NO --> Generator-Evaluator split
  |       - Generator produces output
  |       - Evaluator critiques and scores
  |       - Loop until quality threshold met
  |
  YES --> Re-evaluate whether single agent is truly insufficient
```

**Guidance:**
- Default to single agent. Multi-agent adds complexity.
- Use Orchestrator-Worker when tasks are embarrassingly parallel (e.g., modify 5 independent files).
- Use Generator-Evaluator when output quality is critical and hard to self-assess (e.g., security review, complex refactoring).
- Never use multi-agent for sequential tasks where each step depends on the previous.

---

### Step 4: Report

After generating all files, output a completion report:

```
## Harness Setup Complete

### Files Created/Modified
- [check] CLAUDE.md — Project context map
- [check] .claude/settings.json — Hooks and permission mode
- [check] .claude/rules/code-style.md — Language-specific style rules
- [check] .claude/rules/testing.md — Test conventions
- [check] .claude/rules/security.md — Security guardrails
- [check] .claude/commands/start-task.md — Task planning workflow
- [check] .claude/commands/review.md — Self-review checklist
- [check] .claude/commands/session-wrap.md — Session wrap-up
- [check] .dev/ — Dev workspace (learnings, experiments, scratch)

### 6-Axis Status
| Axis | Before | After |
|------|--------|-------|
| Structure | [status] | [status] |
| Context | [status] | [status] |
| Planning | [status] | [status] |
| Execution | [status] | [status] |
| Verification | [status] | [status] |
| Compounding | [status] | [status] |

### Next Steps
1. Edit the `[Project Name]` and `[One-line description]` in CLAUDE.md
2. Review and adjust `.claude/rules/` to match your actual conventions
3. Start your first task with `/start-task`
4. Run `/session-wrap` at the end of each session to compound improvements
5. Periodically review `.dev/learnings/` and promote patterns to rules
```

---

## Design Principles

1. **Detect, don't assume.** Always read the project before generating. A Python project should not get TypeScript rules.
2. **Merge, don't overwrite.** Existing configuration is sacred. Read first, then add what's missing.
3. **Minimal viable harness.** Generate only what the project needs. A simple script doesn't need multi-session memory.
4. **Assumptions expire.** Every rule and hook encodes an assumption about model capability. Review and prune regularly.
5. **Compound, don't repeat.** Each session should leave the harness slightly better than it found it.
