# claude-workflow-optimizer

A Claude Code plugin for analyzing and optimizing `.claude/` workflow ecosystems — pipelines, agents, rules, memory, and SSOT mappings.

## What it does

Scans your entire `.claude/` directory and detects inefficiencies across 4 dimensions using parallel subagent analysis:

| Dimension | What it checks |
|-----------|---------------|
| **Flow Efficiency** (2x weight) | Pipeline stage count, handoff overhead, sequential bottlenecks, duplicate checkpoints |
| **Token Efficiency** | Agent profile bloat, rule duplication, bilingual redundancy, inline data |
| **Consistency** | SSOT drift, agent boundary overlap, memory staleness, rule conflicts |
| **Agent Architecture** | Too many agents, unclear triggers, protocol overhead, communication gaps |

**22 anti-patterns** detected, each with BAD/GOOD examples, severity rating, and specific fix.

## Target Metrics

| Dimension | Ideal | Warning | Critical |
|-----------|-------|---------|----------|
| CLAUDE.md | ~170 lines / ~2.5k tokens | >250 lines | >400 lines |
| Agent profile | ~150 lines / ~2k tokens | >300 lines | >450 lines |
| Rule file | ~50 lines / ~700 tokens | >100 lines | >200 lines |
| Handoff protocol | ~80 lines | >150 lines | >300 lines |
| Pipeline stages | 7-8 per pipeline | >10 | >12 |
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
```

## Output

```
═══════════════════════════════════════════
🔧 Workflow Optimizer Report
═══════════════════════════════════════════

## Health Score
| Dimension        | Score | Status |
|------------------|-------|--------|
| Flow Efficiency  | X/5   | 🟢/🟡/🔴 |
| Token Efficiency | X/5   | 🟢/🟡/🔴 |
| Consistency      | X/5   | 🟢/🟡/🔴 |
| Agent Arch       | X/5   | 🟢/🟡/🔴 |

## Anti-Patterns Found
### 🔴 HIGH — [description] — [location] — [fix]
### 🟡 MEDIUM — ...
### 🟢 LOW — ...

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
