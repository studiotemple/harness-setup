# Customization Guide

> How to adapt claude-harness to your specific needs.

## After Running `/harness-setup`

The generated files are **starting points**, not final configurations. Customize them for your project.

### CLAUDE.md

**DO:**
- Update the project description to be accurate
- Add project-specific conventions not covered by rules
- Include links to important documentation
- Keep it under 200 lines

**DON'T:**
- Include information that changes frequently (use .claude/memory/ instead)
- Duplicate content from rules/ files
- Add step-by-step tutorials (link to docs instead)

### Rules

**Adding a new rule:**
```markdown
# Rule Title

glob: **/pattern/**, **/*.ext

> When this rule loads

## Rules
- Concrete, actionable rule 1
- Concrete, actionable rule 2
```

The `glob` line is critical — it ensures the rule loads only when touching relevant files, keeping context lean.

**Common custom rules:**
- `database.md` — Migration patterns, query conventions (`glob: **/migrations/**, **/*.sql`)
- `api.md` — Endpoint conventions, response formats (`glob: **/api/**, **/routes/**`)
- `deployment.md` — Deploy safety rules (`glob: **/deploy/**, **/infra/**`)

### Commands

**Adding a new command:**

Create `.claude/commands/my-command.md`:
```markdown
Description of what this command does.

## Step 1: [Action]
[Instructions]

## Step 2: [Action]
[Instructions]
```

Usage: `/my-command` in Claude Code.

**Common custom commands:**
- `/deploy-check` — Pre-deployment verification
- `/db-migrate` — Safe migration workflow
- `/onboard` — New team member context setup

### Hooks

**Adding a custom hook in `.claude/settings.json`:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Editing file: check conventions'"
          }
        ]
      }
    ]
  }
}
```

### Permission Mode

| Mode | Use When | Risk Level |
|------|----------|------------|
| `plan` | Team projects, production code, learning | Low risk |
| `auto` | Solo projects, prototyping, trusted workflows | Higher risk |

You can switch modes in `.claude/settings.json`:
```json
{
  "permissions": {
    "defaultMode": "plan"
  }
}
```

## Stack-Specific Tips

### TypeScript/Next.js
- Add rule for Server Components vs Client Components
- Add rule for API route patterns
- Consider hook for `next build` before deploy

### Python/FastAPI
- Add rule for Pydantic model patterns
- Add rule for dependency injection conventions
- Consider hook for `mypy` on save

### Go
- Add rule for error wrapping conventions
- Add rule for interface patterns
- Consider hook for `go vet` checks

### Monorepo
- Add subfolder CLAUDE.md files per package
- Use glob patterns scoped to each package
- Consider separate rules per package domain
