# claude-workflow-optimizer

A Claude Code plugin for analyzing and optimizing `.claude/` workflow ecosystems — rules, agents, commands, memory, and settings — following the architecture patterns established by Boris Cherny (Head of Claude Code, Anthropic).

## What it does

Scans your entire `.claude/` directory and detects inefficiencies across 4 dimensions using parallel subagent analysis:

| Dimension | What it checks |
|-----------|---------------|
| **Rule & Instruction Efficiency** (2x weight) | Instruction overload, monolithic CLAUDE.md, rule duplication, dead instructions, over-specified examples |
| **Token Efficiency** | Agent profile bloat, inline data, bilingual duplication, command bloat, unused MCP servers |
| **Consistency** | Agent boundary overlap, memory staleness, orphan memory, rule conflicts, settings drift |
| **Agent & Command Architecture** | Too many agents, unclear triggers, command-agent duplication, disconnected agents |

**22 base anti-patterns** + **6 conditional checks** (activated when pipeline/handoff/SSOT assets are detected), each with BAD/GOOD examples, severity rating, and specific fix.

## Target Metrics

| Asset | Ideal | Warning | Critical |
|-------|-------|---------|----------|
| CLAUDE.md | ~170 lines / ~2.5k tokens | >250 lines | >400 lines |
| Agent profile | ~150 lines / ~2k tokens | >300 lines | >450 lines |
| Rule file | ~50 lines / ~700 tokens | >100 lines | >200 lines |
| Command file | ~30 lines / ~400 tokens | >80 lines | >150 lines |
| Total instructions | <150 across all files | >200 | >300 |

## Installation

```bash
claude plugin marketplace add https://github.com/abe1272001/claude-workflow-optimizer
claude plugin install workflow-optimizer@claude-workflow-optimizer
```

### Updating

```bash
claude plugin marketplace update claude-workflow-optimizer
claude plugin update workflow-optimizer@claude-workflow-optimizer
```

## Usage

```bash
/workflow-optimizer analyze     # Report issues (default, read-only)
/workflow-optimizer optimize    # Full analysis + optimized file versions
/workflow-optimizer apply       # Report + directly apply safe fixes
/workflow-optimizer audit       # Deep dive with git history analysis
/workflow-optimizer migrate     # Split monolithic CLAUDE.md into modular rules/
```

## Output

```
═══════════════════════════════════════════
Workflow Optimizer Report
═══════════════════════════════════════════

## Health Score
| Dimension                      | Score | Status |
|--------------------------------|-------|--------|
| Rule & Instruction Efficiency  | X/5   | G/Y/R  |
| Token Efficiency               | X/5   | G/Y/R  |
| Consistency                    | X/5   | G/Y/R  |
| Agent & Command Architecture   | X/5   | G/Y/R  |
| Overall                        | X/5   |        |

Overall = (Rule Efficiency x 2 + Token + Consistency + Architecture) / 5

## Anti-Patterns Found
### HIGH — [description] — [location] — [fix]
### MEDIUM — ...
### LOW — ...

## Top 5 Recommendations
## Token Savings Estimate
```

## Design Methodology

Built using the [7 Patterns for effective SKILL.md design](https://github.com/kojott/claude-docu-optimizer):

1. **Single-File Architecture** — YAML frontmatter + complete prompt
2. **Three-Phase Execution** — Discovery → Parallel Analysis → Synthesis
3. **Quantified Benchmarks** — Numbers, not adjectives
4. **Anti-Pattern Catalogue** — BAD/GOOD examples for each
5. **Mode Switching** — One skill, multiple use cases
6. **Fixed Output Format** — Comparable, trackable results
7. **Execution Rules** — Hard guardrails at the end

## License

MIT
