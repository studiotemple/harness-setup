# Harness Specification

> The theoretical framework behind claude-harness.

## What is a Harness?

A harness is the structured environment that enables AI agents to work safely, effectively, and consistently in a codebase. It consists of three layers:

### Layer 1: Guardrails
Safety boundaries that prevent destructive actions.
- Permission modes (plan vs auto)
- PreToolUse hooks blocking dangerous commands
- Security rules for sensitive file patterns

### Layer 2: Context
Project knowledge that helps AI make informed decisions.
- CLAUDE.md as the project map
- Glob-targeted rules loaded only when relevant
- Tech stack awareness

### Layer 3: Workflows
Structured processes for common tasks.
- `/start-task` for plan-before-code
- `/review` for verification-before-completion
- `/session-wrap` for continuous improvement

## The 6-Axis Model

### 1. Structure
**Question**: Is the project organized for AI collaboration?

Components:
- `.claude/` directory with settings, rules, commands
- `.dev/` directory for learnings, experiments, scratch
- Permission mode matching project risk level
- Safety hooks blocking irreversible operations

### 2. Context
**Question**: Does the AI have enough information to make good decisions?

Components:
- `CLAUDE.md` — concise project map (under 200 lines)
- Glob-targeted rules — loaded only when touching relevant files
- Subfolder-specific CLAUDE.md — for monorepo boundaries
- Accurate tech stack documentation

### 3. Planning
**Question**: Does the AI plan before acting?

Components:
- `/start-task` command with Sprint Contract
- `.claude/memory/` for preserving plans and context
- Measurable completion criteria (not "it works" but "tsc --noEmit passes")

### 4. Execution
**Question**: Is the execution approach appropriate for the task?

Decision tree:
```
Simple bug fix → Direct fix, no ceremony
2-3 file change → Context note, then fix
New feature / 5+ files → Full planning process
Independent subtasks → Parallel agent dispatch
```

### 5. Verification
**Question**: How do we know the work is actually done?

Components:
- `/review` with skeptical self-review mindset
- Type checking (tsc, mypy, go vet)
- Cross-file impact analysis
- E2E verification when possible
- Sprint Contract criterion verification

### 6. Compounding
**Question**: Does the harness improve over time?

Components:
- `/session-wrap` capturing patterns and learnings
- `.dev/learnings/` for non-obvious discoveries
- Regular removal of stale rules and hooks
- Assumption freshness checks

## Harness Maturity Model

| Level | Score | Description |
|-------|-------|-------------|
| **Beginner** | 0-1 axes 🟢 | Minimal or no harness. AI works with defaults. |
| **Developing** | 2-3 axes 🟢 | Key elements present. Gaps in workflows or verification. |
| **Mature** | 4-5 axes 🟢 | Solid foundation. Most workflows covered. |
| **Evolving** | 6 axes 🟢 | All axes covered and actively maintained. Stale rules removed. |

The goal is not to reach "Evolving" and stay there. The goal is to **keep evolving** — adding assumptions when needed, removing them when outgrown.

## Anti-Patterns

### Over-Engineering
- 50+ rules for a solo side project
- Multi-agent orchestration for simple bug fixes
- Complex hooks that slow down every command

### Under-Engineering
- No CLAUDE.md in a team project
- No safety hooks with junior team members using AI
- No verification workflow for production code

### Stale Harness
- Rules written for an old tech stack
- Hooks blocking commands the model now handles safely
- Over-prescriptive style rules matching model defaults

## When to Use What

| Situation | Recommended Action |
|-----------|-------------------|
| New project, first time with Claude Code | `/harness-setup` |
| Inherited project, want to add AI workflows | `/harness-setup` |
| Been using harness for a while, want to check health | `/harness-check` |
| After major refactoring or stack change | `/harness-check` then selective regeneration |
| Feeling like the harness is too heavy | `/harness-check` focusing on the "Remove" recommendations |
