# claude-harness

> Set up and diagnose AI harness environments for any project — guardrails, context, and workflows that help Claude work effectively.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Code Plugin](https://img.shields.io/badge/Claude_Code-Plugin-blueviolet)](https://github.com/studiotemple/harness-setup)

---

## What is a Harness?

A **harness** is the structured environment that helps AI agents work safely and effectively in your codebase. It combines three layers:

| Layer | Purpose | Examples |
|-------|---------|----------|
| **Guardrails** | Prevent destructive actions | `git push --force` blocking, `DROP TABLE` detection, permission modes |
| **Context** | Give AI the knowledge it needs | `CLAUDE.md` project map, glob-targeted rules, tech stack awareness |
| **Workflows** | Structure common tasks | `/start-task` for planning, `/review` for verification, `/session-wrap` for learning |

Without a harness, AI agents operate with generic defaults — no awareness of your project's conventions, no safety nets for dangerous operations, no structured process for complex tasks.

With a harness, they become **effective team members** that follow your project's patterns, plan before coding, and improve over time.

## Why claude-harness?

**The problem:** Setting up `CLAUDE.md`, rules, hooks, and commands from scratch is tedious and error-prone. Most teams either skip it entirely or over-engineer it with rules that quickly go stale.

**The solution:** claude-harness provides two commands:

- **`/harness-setup`** — An interactive wizard that analyzes your project, asks the right questions, and generates a tailored harness in minutes
- **`/harness-check`** — A diagnostic tool that audits your existing harness across 6 axes and identifies what to add, improve, or remove

> "Every component of a harness encodes an assumption about what the model can't do on its own.
> Those assumptions go stale fast as models improve." — Anthropic Engineering

claude-harness is designed around this insight: a good harness **evolves**. It adds constraints when needed and removes them when outgrown.

---

## The 6-Axis Framework

claude-harness evaluates and builds your project's AI environment across six axes:

```
                    ┌─────────────┐
                    │  Structure  │ ← Folder layout, permissions, safety hooks
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
       ┌──────┴──────┐    │    ┌───────┴──────┐
       │   Context   │    │    │   Planning   │
       └──────┬──────┘    │    └───────┬──────┘
              │            │            │
              │     ┌──────┴──────┐     │
              └────►│  Execution  │◄────┘
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │                         │
       ┌──────┴───────┐    ┌───────────┴──┐
       │ Verification │    │ Compounding  │
       └──────────────┘    └──────────────┘
```

| # | Axis | Question It Answers | Key Components |
|---|------|---------------------|----------------|
| 1 | **Structure** | Is the project organized for AI collaboration? | `.claude/` directory, permission mode, safety hooks |
| 2 | **Context** | Does the AI have enough info to decide well? | `CLAUDE.md` (< 200 lines), glob-targeted rules |
| 3 | **Planning** | Does the AI plan before acting? | `/start-task`, Sprint Contract, `.claude/memory/` |
| 4 | **Execution** | Is complexity appropriate for the task? | Scale-matched workflows, multi-agent when justified |
| 5 | **Verification** | How do we know the work is actually done? | `/review`, type checks, Sprint Contract verification |
| 6 | **Compounding** | Does the harness improve over time? | `/session-wrap`, learnings, stale rule removal |

---

## Installation

```bash
claude plugin add studiotemple/harness-setup
```

After installation, two new commands become available in Claude Code:
- `/harness-setup` — Build a new harness
- `/harness-check` — Diagnose an existing harness

---

## Quick Start

```bash
# 1. Open your project in Claude Code
cd your-project
claude

# 2. Run the setup wizard
> /harness-setup

# 3. Answer 4 quick questions about your project

# 4. Done! Your project now has a complete harness.

# 5. Start your first task with the new workflow
> /start-task Add user authentication
```

---

## Commands

### `/harness-setup` — Build Your Harness

An interactive wizard that builds a complete harness tailored to your project.

#### How It Works

**Step 1: Analyze** — Reads your project files to detect tech stack and existing configuration:

```
Files checked:
  ✓ package.json          → TypeScript/Node.js detected
  ✓ CLAUDE.md             → Exists (87 lines, outdated stack info)
  ✓ .claude/settings.json → Missing
  ✓ .claude/rules/        → Missing
  ✓ .claude/commands/     → Missing
```

**Step 2: Interview** — Asks up to 4 targeted questions (skips what it can infer):

```
1. What kind of tasks do you primarily use AI for?
   → New features, bug fixes

2. Solo project or team-shared?
   → Team (3 developers)

3. Any tasks AI must NEVER do?
   → Direct deployment, .env modification

4. Any long-running multi-session tasks?
   → Yes, we have a migration project spanning weeks
```

**Step 3: Generate** — Creates all harness files (merges with existing, never overwrites):

```
Created/Updated:
  ✅ CLAUDE.md                     (updated — preserved existing content)
  ✅ .claude/settings.json          (new — plan mode + safety hooks)
  ✅ .claude/rules/code-style.md    (new — TypeScript conventions)
  ✅ .claude/rules/testing.md       (new — glob: **/*.test.*)
  ✅ .claude/rules/security.md      (new — glob: **/auth/**, **/api/**)
  ✅ .claude/commands/start-task.md  (new — Sprint Contract workflow)
  ✅ .claude/commands/review.md      (new — skeptical self-review)
  ✅ .claude/commands/session-wrap.md (new — session improvement loop)
  ✅ .claude/memory/features.json    (new — multi-session tracking)
  ✅ .claude/memory/claude-progress.txt (new — session handoff)
  ✅ .dev/                           (new — learnings, experiments, scratch)
```

**Step 4: Report** — Shows the 6-axis assessment:

```
┌────────────────────────────────────────┐
│        Harness Setup Complete          │
├──────────────┬──────────┬──────────────┤
│     Axis     │  Before  │    After     │
├──────────────┼──────────┼──────────────┤
│ Structure    │    🔴    │     🟢      │
│ Context      │    🟡    │     🟢      │
│ Planning     │    🔴    │     🟢      │
│ Execution    │    🔴    │     🟢      │
│ Verification │    🔴    │     🟢      │
│ Compounding  │    🔴    │     🟢      │
└──────────────┴──────────┴──────────────┘
```

#### What Gets Generated

```
your-project/
├── CLAUDE.md                        ← Project map (under 200 lines)
│                                       Core rules, tech stack, command reference
├── .claude/
│   ├── settings.json                ← Safety hooks + permission mode
│   │                                   Blocks: git push --force, DROP TABLE,
│   │                                   DELETE without WHERE, rm -rf /, git reset --hard
│   ├── rules/
│   │   ├── code-style.md           ← Always loaded
│   │   │                              Stack-specific conventions (TS/Py/Go/...)
│   │   ├── testing.md              ← Auto-loaded for test files
│   │   │                              glob: **/*.test.*, **/__tests__/**
│   │   └── security.md            ← Auto-loaded for auth/api/sql files
│   │                                  glob: **/auth/**, **/*.sql, **/api/**
│   └── commands/
│       ├── start-task.md           ← /start-task
│       │                              Deep codebase read → Sprint Contract → Approval
│       ├── review.md               ← /review
│       │                              Skeptical self-review + verification loop
│       └── session-wrap.md         ← /session-wrap
│                                      Pattern discovery → harness improvement
├── .dev/
│   ├── learnings/                   ← Non-obvious discoveries from sessions
│   ├── experiments/                 ← Scratch work and prototypes
│   └── scratch/                     ← Temporary files
│
└── (Multi-session, if needed)
    ├── .claude/memory/features.json       ← Structured feature checklist
    ├── .claude/memory/claude-progress.txt ← Session handoff notes
    └── init.sh                            ← Environment bootstrap script
```

---

### `/harness-check` — Diagnose Your Harness

Audits your existing harness configuration and reports its health across all 6 axes.

#### Example Output

```
══════════════════════════════════════════
         Harness Diagnostic Report
══════════════════════════════════════════

FILE INVENTORY
──────────────────────────────────────────
 File                          Exists  Quality
 ─────────────────────────────────────────
 CLAUDE.md                      ✅    ⚠️ 243 lines (over 200)
 .claude/settings.json          ✅    ✅ hooks + plan mode
 .claude/rules/code-style.md    ✅    ✅ matches stack
 .claude/rules/testing.md       ✅    ⚠️ missing glob pattern
 .claude/rules/security.md      ❌    —
 .claude/commands/start-task.md  ✅    ✅ has Sprint Contract
 .claude/commands/review.md      ✅    ✅ has verification steps
 .claude/commands/session-wrap.md ❌    —
 .dev/                          ✅    ⚠️ no learnings recorded

6-AXIS ASSESSMENT
──────────────────────────────────────────
 Axis          Status  Key Finding
 ─────────────────────────────────────────
 Structure      🟢    Hooks and permissions properly configured
 Context        🟡    CLAUDE.md over 200 lines, testing.md lacks glob
 Planning       🟢    Sprint Contract workflow in place
 Execution      🟢    Scale-appropriate workflows
 Verification   🟡    No security rules for auth/api files
 Compounding    🔴    No session-wrap, no learnings recorded

IMMEDIATE ACTIONS (🔴)
──────────────────────────────────────────
 1. Create .claude/commands/session-wrap.md
    → Without this, harness improvements don't compound

GRADUAL IMPROVEMENTS (🟡)
──────────────────────────────────────────
 1. Trim CLAUDE.md from 243 to under 200 lines
 2. Add glob pattern to testing.md
 3. Create .claude/rules/security.md

ELEMENTS TO REMOVE OR RELAX
──────────────────────────────────────────
 1. code-style.md: "max 20 lines per function" →
    Model consistently writes concise functions; relax to 30

HARNESS MATURITY: 4/6 🟢 — Mature
══════════════════════════════════════════
```

---

## The Generated Workflow Commands

### `/start-task` — Plan Before You Code

The most important harness command. Ensures AI deeply understands the codebase before making changes.

```
/start-task Add OAuth2 login with Google

Step 0: Interview
  → "Should this support multiple providers or just Google for now?"

Step 1: Deep Codebase Read
  → Reads all auth-related files (logic, not just signatures)
  → Traces imports and references
  → Searches for existing OAuth patterns

Step 2: Sprint Contract
  ┌──────────────────────────────────────────┐
  │ Sprint Contract — OAuth2 Google Login    │
  ├──────────────────────────────────────────┤
  │ ☐ tsc --noEmit PASS                     │
  │ ☐ GET /api/auth/google returns 302      │
  │ ☐ Callback handles token exchange        │
  │ ☐ Session created after successful auth  │
  │ ☐ /review passes all checks              │
  └──────────────────────────────────────────┘

Step 3-4: Context + Checklist written to .claude/memory/

Step 5: ⏸️  Waiting for user approval...
         NO CODING UNTIL APPROVED
```

### `/review` — Skeptical Self-Review

Reviews work as a **skeptical external reviewer**, not a self-congratulator.

| # | Check | What It Verifies |
|---|-------|------------------|
| 1 | Error handling | Every async function has try-catch |
| 2 | Auth verification | All mutation endpoints check authentication |
| 3 | SQL safety | No string interpolation in queries |
| 4 | Secret exposure | No credentials in logs or responses |
| 5 | Type check | `tsc --noEmit` / `mypy` / `go vet` passes |
| 6 | I/O flow trace | Input-to-output path verified |
| 7 | Cross-file impact | Files referencing modified code still work |
| 8 | E2E verification | Actual behavior tested (when possible) |
| 9 | Sprint Contract | Each criterion executed and verified |
| 10 | Edge cases | Empty input, unauthorized access, network failure |

**Key rule:** "Almost done" is **NOT** done. Every criterion must actually PASS.

### `/session-wrap` — Compound Your Improvements

Runs at the end of every session to capture what the harness can learn:

| Signal | Action |
|--------|--------|
| 🔁 Repeated manual work | Suggest new `/command` |
| ⚠️ Repeated mistakes | Suggest new `rule` |
| 📚 Non-obvious discovery | Record in `.dev/learnings/` |
| 🔧 Stale assumption | Suggest removing/relaxing `rule` or `hook` |

---

## Supported Stacks

claude-harness auto-detects your project's tech stack and generates stack-appropriate rules:

| Stack | Detection File | Generated Rules |
|-------|---------------|-----------------|
| **TypeScript / JavaScript** | `package.json` | camelCase naming, no `any` type, async error handling, PascalCase components |
| **Python** | `pyproject.toml`, `requirements.txt` | snake_case naming, type hints (`X \| None` over `Optional[X]`), specific exceptions |
| **Go** | `go.mod` | Effective Go patterns, error wrapping, interface conventions |
| **Rust** | `Cargo.toml` | Ownership patterns, `Result` handling, trait conventions |
| **Ruby** | `Gemfile` | Ruby style guide, block conventions |
| **Java / Kotlin** | `build.gradle`, `pom.xml` | Java conventions, null safety patterns |
| **Other** | Fallback | Language-agnostic: 20-line function limit, why-comments, error handling |

The detection also identifies:
- **Frameworks:** Next.js, FastAPI, Gin, Rails, Spring Boot, etc.
- **Databases:** PostgreSQL, MySQL, MongoDB, SQLite (influences security rules)
- **Auth patterns:** NextAuth, Passport, custom JWT (influences auth rules)

---

## Multi-Session Support

For long-running tasks that span multiple sessions (feature development, migrations, refactoring):

### Feature Tracking — `features.json`

```json
{
  "project": "my-app",
  "lastUpdated": "2026-04-13",
  "features": [
    {
      "id": "feat-001",
      "category": "functional",
      "description": "OAuth2 Google login",
      "steps": ["GET /api/auth/google returns 302", "Token exchange works"],
      "passes": false
    },
    {
      "id": "feat-002",
      "category": "non-functional",
      "description": "Rate limiting on auth endpoints",
      "steps": ["100 req/min limit enforced", "429 response returned"],
      "passes": true
    }
  ]
}
```

**Rule:** Only the `passes` field is mutable during development. JSON format prevents accidental edits.

### Session Handoff — `claude-progress.txt`

```
# Session Progress

## Last Updated: 2026-04-13

## Current State
- Working on: OAuth2 Google login (feat-001)
- Blocked by: Need Google OAuth client credentials
- Next action: Implement callback handler once credentials are set

## Completed This Session
- Created auth route structure
- Added Google OAuth configuration
- Wrote token exchange logic

## Key Decisions
- Chose server-side flow over client-side for security
- Using httpOnly cookies for session tokens

## Files Modified
- src/auth/google.ts: New file — OAuth2 flow handler
- src/middleware/session.ts: Added cookie validation
```

### Environment Bootstrap — `init.sh`

```bash
#!/bin/bash
echo "=== Development Environment Setup ==="
# npm install / pip install / go mod download
echo ""
echo "=== Session Progress ==="
cat .claude/memory/claude-progress.txt 2>/dev/null || echo "No progress file found."
```

---

## Safety Hooks

The generated `settings.json` includes a `PreToolUse` hook that blocks dangerous commands **before they execute**:

| Pattern | Blocked Command | Why |
|---------|----------------|-----|
| `git push.*--force` | Force push | Can destroy remote history |
| `DROP\s+TABLE` | Drop table | Irreversible data loss |
| `DELETE\s+FROM\s+\w+\s*;` | DELETE without WHERE | Deletes all rows |
| `rm\s+-rf\s+/` | Recursive delete from root | Catastrophic file loss |
| `git\s+reset\s+--hard` | Hard reset | Discards uncommitted work |

These hooks are a **starting point**. Customize them based on your project's risk profile:

- **High-risk production:** Add hooks for deploy commands, environment variable changes
- **Solo side project:** Consider relaxing to only the most critical patterns
- **Team project:** Add hooks for branch protection, PR requirements

---

## Permission Modes

| Mode | Behavior | Best For |
|------|----------|----------|
| `plan` | AI shows its plan and asks for approval before executing | Team projects, production code, learning Claude Code |
| `auto` | AI executes immediately, asks only for dangerous operations | Solo projects, prototyping, trusted workflows |

Set in `.claude/settings.json`:
```json
{
  "permissions": {
    "defaultMode": "plan"
  }
}
```

**Recommendation:** Start with `plan` mode. Switch to `auto` once you trust the harness configuration.

---

## Harness Maturity Model

`/harness-check` reports a maturity score based on how many axes are green:

| Level | Score | What It Means |
|-------|-------|---------------|
| **Beginner** | 0–1 axes 🟢 | Minimal or no harness. AI works with generic defaults. Run `/harness-setup`. |
| **Developing** | 2–3 axes 🟢 | Key elements present but gaps remain in workflows or verification. |
| **Mature** | 4–5 axes 🟢 | Solid foundation. Most workflows covered. Focus on refinement. |
| **Evolving** | 6 axes 🟢 | All axes covered and actively maintained. Stale rules being removed. |

The goal is not to reach "Evolving" and stay there forever. The goal is to **keep evolving** — the harness should grow and shrink as your project and the AI model change.

---

## Design Principles

| # | Principle | What It Means |
|---|-----------|---------------|
| 1 | **Detect, don't assume** | Auto-detect tech stack from project files instead of asking users to specify |
| 2 | **Merge, don't overwrite** | Always read existing files before modifying. Preserve user's existing configuration |
| 3 | **Minimal viable harness** | Only add what the project actually needs. A solo side project doesn't need 50 rules |
| 4 | **Evolve, don't accumulate** | Regularly remove stale rules and hooks. A growing rule count is a smell |
| 5 | **Guardrails, not handcuffs** | Protect against real risks. Don't micromanage the AI with over-prescriptive rules |

---

## Anti-Patterns to Avoid

### Over-Engineering 🚫
- Writing 50+ rules for a solo side project
- Multi-agent orchestration for simple bug fixes
- Complex hooks that slow down every single command
- Duplicating framework defaults as explicit rules

### Under-Engineering 🚫
- No `CLAUDE.md` in a team project
- No safety hooks when multiple people use AI on production code
- No verification workflow for code going to production
- No planning step for features touching 5+ files

### Stale Harness 🚫
- Rules written for a tech stack you migrated away from
- Hooks blocking commands the model now handles safely by default
- Style rules that exactly match the model's default behavior (redundant)
- A `CLAUDE.md` that describes the project as it was 6 months ago

---

## FAQ

### How is this different from just writing a CLAUDE.md?

`CLAUDE.md` is one piece of the puzzle — it's the **Context** axis. A full harness also covers **Structure** (permissions, hooks), **Planning** (start-task, Sprint Contract), **Verification** (review workflow), and **Compounding** (session-wrap, learnings). claude-harness generates all of these as an integrated system.

### Will this slow down simple tasks?

No. The generated `/start-task` command automatically classifies task size:
- **Small** (single file bug fix) → Skip planning, fix directly
- **Medium** (2–3 files) → Brief context note, then fix
- **Large** (new feature, 5+ files) → Full planning process

The ceremony scales with the task.

### Can I use this with an existing CLAUDE.md?

Yes. `/harness-setup` reads your existing `CLAUDE.md` and **merges** with it. It will preserve your content and add missing elements. It never overwrites.

### What if my project uses multiple languages?

The wizard detects all present stack files and generates rules covering each language found. For monorepos, it can suggest subfolder-specific `CLAUDE.md` files.

### How often should I run `/harness-check`?

After any major change — new team member, tech stack update, large refactoring. Also good as a monthly health check, especially to identify rules that have gone stale.

### Can I remove generated files I don't need?

Absolutely. The harness should serve your project, not the other way around. If a rule or command isn't adding value, remove it. That's what "Evolve, don't accumulate" means.

---

## Documentation

| Document | Content |
|----------|---------|
| [Harness Specification](docs/harness-spec.md) | Theoretical framework — the 3 layers, 6-axis model, maturity model, anti-patterns |
| [Customization Guide](docs/customization.md) | How to adapt generated files — adding rules, commands, hooks, stack-specific tips |

---

## Contributing

Contributions welcome! Please:

1. Fork this repository
2. Create a feature branch (`git checkout -b feat/my-feature`)
3. Commit your changes
4. Push to the branch (`git push origin feat/my-feature`)
5. Open a Pull Request

### Areas for Contribution

- **Stack support:** Add detection and rules for new languages/frameworks
- **Rule templates:** Better default rules for specific domains (database, deployment, etc.)
- **Translations:** Localize skill descriptions for non-English users
- **Documentation:** Improve examples and guides

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

**Made by [James Seo](https://github.com/studiotemple)** — Built with Claude Code.
