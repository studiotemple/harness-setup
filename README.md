# claude-harness

> Set up and diagnose AI harness environments for any project.

A **harness** is the structured environment that helps AI agents work safely and effectively in your codebase — guardrails, context, and workflows combined.

## Philosophy

> "Every component of a harness encodes an assumption about what the model can't do on its own.  
> Those assumptions go stale fast as models improve." — Anthropic Engineering

A good harness is not about adding more rules. It's about encoding the **right** assumptions at the **right** time, and removing them when they're no longer needed.

## The 6-Axis Framework

claude-harness evaluates and builds your project's AI environment across six axes:

| Axis | What It Covers | Example |
|------|---------------|---------|
| **Structure** | Folder layout, permissions, safety hooks | `.claude/settings.json` with danger-blocking hooks |
| **Context** | Project knowledge available to AI | `CLAUDE.md` under 200 lines, targeted rules with glob patterns |
| **Planning** | Plan-before-code workflows | `/start-task` with Sprint Contract |
| **Execution** | Appropriate complexity for project scale | Single agent vs. orchestrator-worker patterns |
| **Verification** | Self-review and quality checks | `/review` with concrete verification steps |
| **Compounding** | Learning from sessions, evolving rules | `/session-wrap` capturing patterns, removing stale rules |

## Installation

```bash
claude plugin add seokangseok/claude-harness
```

Or install from the marketplace:
```bash
claude plugin add claude-harness
```

## Usage

### `/harness-setup` — Build Your Harness

Interactive wizard that:
1. **Analyzes** your project (tech stack, existing config)
2. **Interviews** you (4 targeted questions)
3. **Generates** tailored harness files
4. **Reports** the 6-axis status

```bash
claude
> /harness-setup
```

#### What Gets Generated

```
your-project/
├── CLAUDE.md                    <- Project map (under 200 lines)
├── .claude/
│   ├── settings.json            <- Safety hooks + permission mode
│   ├── rules/
│   │   ├── code-style.md       <- Always loaded (matches your stack)
│   │   ├── testing.md          <- Auto-loaded for test files
│   │   └── security.md        <- Auto-loaded for auth/api/sql files
│   └── commands/
│       ├── start-task.md       <- Plan -> Sprint Contract -> Approval
│       ├── review.md           <- Self-review before claiming done
│       └── session-wrap.md     <- End-of-session improvement loop
└── .dev/
    ├── learnings/               <- Non-obvious discoveries
    ├── experiments/             <- Scratch work
    └── scratch/                 <- Temporary files
```

### `/harness-check` — Diagnose Your Harness

Audit your existing harness across all 6 axes:

```bash
claude
> /harness-check
```

Output includes:
- **File inventory** — what exists and its quality
- **6-axis assessment** — Good / Needs Work / Missing
- **Immediate actions** — critical gaps to fill
- **Stale assumptions** — rules/hooks that can be relaxed or removed
- **Maturity score** — Beginner -> Developing -> Mature -> Evolving

## Supported Stacks

claude-harness auto-detects your project's tech stack and generates appropriate rules:

| Stack | Detection | Tailored Rules |
|-------|-----------|----------------|
| TypeScript/JavaScript | `package.json` | camelCase, no `any`, async error handling |
| Python | `pyproject.toml`, `requirements.txt` | snake_case, type hints, specific exceptions |
| Go | `go.mod` | Effective Go patterns, error wrapping |
| Rust | `Cargo.toml` | Ownership patterns, Result handling |
| Ruby | `Gemfile` | Ruby style guide patterns |
| Java/Kotlin | `build.gradle`, `pom.xml` | Java conventions |
| *Other* | Fallback | Language-agnostic best practices |

## The Generated Commands

### `/start-task` — Plan Before You Code

1. Deep-read the codebase (logic, not just signatures)
2. Write a **Sprint Contract** with measurable completion criteria
3. Wait for user approval
4. Code only after approval

### `/review` — Skeptical Self-Review

Review as a skeptical external reviewer, not a self-congratulator:
- Code quality checks (error handling, auth, SQL safety)
- Type checking + cross-file impact analysis
- Sprint Contract criterion-by-criterion verification
- Edge case audit

### `/session-wrap` — Compound Your Improvements

- Discover patterns -> suggest new commands/rules
- Record non-obvious learnings
- Identify stale assumptions -> suggest removal
- Hand off context for the next session

## Multi-Session Support

For long-running tasks that span sessions, harness-setup can generate:

- `.claude/memory/features.json` — Structured feature checklist
- `.claude/memory/claude-progress.txt` — Session handoff notes

## Design Principles

1. **Detect, don't assume** — Auto-detect tech stack instead of asking
2. **Merge, don't overwrite** — Always read existing files before modifying
3. **Minimal viable harness** — Only add what the project actually needs
4. **Evolve, don't accumulate** — Regularly remove stale rules and hooks
5. **Guardrails, not handcuffs** — Protect against real risks, don't micromanage

## Contributing

Contributions welcome! Please:
1. Fork this repository
2. Create a feature branch
3. Submit a pull request

## License

MIT
