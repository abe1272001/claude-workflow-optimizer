# claude-optimizer

A Claude Code plugin that helps you **optimize your workflow** and **build better skills** — two skills, one plugin.

## Skills

### 1. Workflow Optimizer

Scans your `.claude/` directory and detects inefficiencies across 4 dimensions using parallel subagent analysis.

| Dimension | What it checks |
|-----------|---------------|
| **Rule & Instruction Efficiency** (2x weight) | Instruction overload, monolithic CLAUDE.md, rule duplication, dead instructions |
| **Token Efficiency** | Agent profile bloat, inline data, bilingual duplication, unused MCP servers |
| **Consistency** | Agent boundary overlap, memory staleness, rule conflicts, settings drift |
| **Agent & Command Architecture** | Too many agents, unclear triggers, command-agent duplication |

**22 base anti-patterns** + **6 conditional checks**, each with severity rating and specific fix.

```bash
/claude-optimizer:workflow-optimizer analyze   # Report issues (default)
/claude-optimizer:workflow-optimizer optimize   # Analysis + optimized file versions
/claude-optimizer:workflow-optimizer apply      # Report + directly apply safe fixes
/claude-optimizer:workflow-optimizer audit      # Deep dive with git history analysis
/claude-optimizer:workflow-optimizer migrate    # Split monolithic CLAUDE.md into rules/
```

### 2. Skill Advisor

Helps you decide **what** skills to build, **how** to structure them, and **whether** existing skills are well-designed. Based on [Thariq's lessons from building Claude Code](https://x.com/trq212/status/2033949937936085378).

Includes reference materials on the 9 skill categories, 8 design principles, 10 anti-patterns, folder structure templates, and description writing guide.

```bash
/claude-optimizer:skill-advisor audit           # Evaluate skill coverage across 9 categories
/claude-optimizer:skill-advisor plan            # Plan your next skill (asks 3 questions)
/claude-optimizer:skill-advisor discover        # Scan project for skill-worthy patterns
/claude-optimizer:skill-advisor review <path>   # Score a skill against 8 design principles
```

## Installation

```bash
claude plugin marketplace add https://github.com/abe1272001/claude-optimizer
claude plugin install claude-optimizer@claude-optimizer
```

### Updating

```bash
claude plugin marketplace update claude-optimizer
claude plugin update claude-optimizer@claude-optimizer
```

## License

MIT
