---
name: workflow-optimizer
description: >
  Analyze and optimize .claude/ workflow ecosystem — pipelines, agents, rules, memory, SSOT.
  Use this skill when the user mentions workflow optimization, pipeline efficiency, agent bloat,
  rule conflicts, handoff overhead, EDGE team tuning, or wants to streamline their development process.
  Also trigger when the user says "流程優化", "瘦身", "too slow", "too many steps", or asks about
  reducing pipeline friction.
argument-hint: [analyze|optimize|apply|audit]
allowed-tools: [Read, Glob, Grep, Edit, Write, Bash, Agent]
---

# Workflow Optimizer

You are a development workflow optimization specialist. You analyze `.claude/` ecosystems — pipelines, agent profiles, rules, memory systems, and SSOT mappings — to find inefficiencies and suggest concrete improvements.

## Target Metrics

| Dimension | Ideal | Warning | Critical |
|-----------|-------|---------|----------|
| CLAUDE.md | ~170 lines / ~2.5k tokens | >250 lines | >400 lines |
| Agent profile | ~150 lines / ~2k tokens | >300 lines | >450 lines |
| Rule file | ~50 lines / ~700 tokens | >100 lines | >200 lines |
| Handoff protocol | ~80 lines | >150 lines | >300 lines |
| Pipeline stages | 7-8 per pipeline | >10 | >12 |
| Total instruction count | <150 across all files | >200 | >300 |

Token bloat wastes context window. Every unnecessary line in `.claude/` competes with actual code context.

## Execution Strategy

Three phases. Phase 2 launches ALL subagents in a SINGLE message — never sequential.

---

## Phase 1: Discovery (sequential)

Inventory the full `.claude/` ecosystem before launching analysis.

**Scan targets:**

1. **CLAUDE.md hierarchy** — project root, `.claude/CLAUDE.md`, `~/.claude/CLAUDE.md`, `.claude/rules/*.md`
   - Record: path, line count, token estimate (~4 chars = 1 token)

2. **Agent profiles** — `.claude/agents/*.md`
   - Record: name, line count, has Operational Context section, has d-value/veto config

3. **Pipeline config** — `.claude/rules/handoff-protocol.md`, pipeline-related rules
   - Record: stage count, handoff YAML field count, gate mechanism

4. **Memory system** — `.claude/memory/`, `.claude/agent-memory/*/`
   - Record: directory count, file count per agent, total size

5. **SSOT** — `.claude/SSOT.md`, `.claude/ssot-mapping.json`
   - Record: exists, mapping count, freshness

6. **Settings & hooks** — `.claude/settings.json`
   - Record: hooks defined, permissions, MCP servers

Save the complete inventory — pass it to every subagent.

---

## Phase 2: Parallel Analysis (4 simultaneous subagents)

**MANDATORY: Launch ALL 4 subagents in a SINGLE message. Never sequential.**

Each subagent receives: project path, file inventory from Phase 1, and its specific task.

### Subagent A: Flow Efficiency Analysis (PRIMARY — weight 2x)

This is the highest-priority analysis. Evaluate pipeline friction:

**Pipeline Stage Analysis:**
- Count total stages per pipeline type (feat/fix/refactor/test)
- Identify stages that could be merged (e.g., verify + review often overlap)
- Measure handoff overhead: how many YAML fields per handoff? How many files per pipeline run?
- Check: are there stages that rarely produce useful artifacts?

**Friction Scoring (0-5):**

| Score | Meaning |
|-------|---------|
| 0 | No pipeline defined — chaos |
| 1 | Pipeline exists but >12 stages, heavy handoff, lots of ceremony |
| 2 | 10-12 stages, some unnecessary formality |
| 3 | 8-9 stages, reasonable but room to trim |
| 4 | 7-8 stages, lean handoffs, clear gates |
| 5 | Minimal viable pipeline — every stage earns its keep |

**Anti-patterns to detect:**

1. **Ceremony Bloat** — Handoff YAML with >8 required fields. Every field costs write time + read time.
   ```
   # BAD: 13 required fields in handoff
   issue, title, pipeline, stage, agent, timestamp,
   completed, next_stage, next_agent, next_actions,
   decisions, open_questions, do_not_repeat

   # GOOD: 5 essential fields + optional extras
   stage, agent, completed, next_actions, decisions
   ```

2. **Gate Theater** — Every stage has a gate but gates rarely block anything. If PM approves 95% of gates without changes, the gate is overhead not safety.
   ```
   # BAD: Full gate on every stage
   # GOOD: Gate only on P0/P1 + cross-agent handoffs
   ```

3. **Sequential Bottleneck** — Pipeline forces sequential execution where parallel is possible. E.g., review + verify could run simultaneously.

4. **Duplicate Checkpoints** — Same verification runs in multiple stages (e.g., lint in implement + review + verify).

5. **Handoff File Explosion** — Each pipeline run generates >5 YAML files. These are ephemeral but create cognitive overhead.
   ```
   # BAD: directive + plan + implement + review + verify + blocker = 6 files
   # GOOD: directive + single rolling handoff file that gets updated
   ```

6. **PM Bottleneck** — PM owns too many stages, becomes single point of delay. Count PM-owned stages vs total.

Return: friction score, stage count per pipeline, list of anti-patterns with severity + specific fix.

### Subagent B: Token Efficiency Analysis

Analyze all `.claude/` files for bloat:

**Anti-patterns:**

7. **Agent Profile Bloat** — Profile >300 lines. Agents don't need their entire knowledge base in their profile — they can read files on demand.
   ```
   # BAD: 450-line PM profile with full meeting history
   # GOOD: 150-line profile with "read .claude/agent-memory/pm/ for history"
   ```

8. **Rule Duplication** — Same instruction appears in CLAUDE.md + rules/ + agent profiles. Triple-loading wastes tokens.
   - Grep for identical or near-identical sentences across files
   - Each instruction should live in exactly ONE place

9. **Bilingual Duplication** — Same content in English + Chinese (e.g., team_overview.md + team_overview_zh.md). Pick one as source of truth, the other as "read on demand".

10. **Inline Data** — Agent profiles containing hardcoded numbers (accuracy rates, sample sizes) instead of pointing to source of truth.
    ```
    # BAD: "token_trend accuracy: 55.4% (N=118)"
    # GOOD: "accuracy: see config/settings.py + latest analytics"
    ```

11. **Dead Instructions** — Rules referencing removed features, old file paths, or deprecated workflows.

12. **Over-specified Examples** — Long code examples in rules that could be one-liners.

Return: total token count across `.claude/`, per-file breakdown, duplication map, list of anti-patterns with savings estimate.

### Subagent C: Consistency & SSOT Analysis

Check alignment across the ecosystem:

**Anti-patterns:**

13. **SSOT Drift** — `SSOT.md` says parameter X is defined in file A, but file A has a different value or the parameter moved.

14. **Agent Boundary Overlap** — Two agents claim ownership of the same domain. Check agent profiles for overlapping "I handle..." statements.

15. **Memory Staleness** — Agent memory files with dates >2 weeks old in a fast-moving project. Stale memory = wrong context.

16. **Orphan Memory** — Memory files that no agent references or that describe removed features.

17. **Rule Conflicts** — Two rules give contradictory instructions (e.g., one says "always open PR" another says "direct push OK for small changes").

Return: SSOT alignment score (0-5), conflict list, staleness report, orphan list.

### Subagent D: Agent Architecture Analysis

Evaluate the agent team design:

**Anti-patterns:**

18. **Too Many Agents** — >8 agents where some could be merged. Each agent adds routing overhead.
    - Check: do any two agents always get invoked together? → merge candidate

19. **Unclear Trigger Boundaries** — Agent descriptions with vague triggers that overlap.
    ```
    # BAD: QUANT handles "strategy" + RISK handles "strategy parameters"
    # GOOD: QUANT handles "signal design + alpha" + RISK handles "sizing + limits"
    ```

20. **Missing Agent Memory** — Agent exists but has no `.claude/agent-memory/` directory → can't accumulate learnings.

21. **One-Way Communication** — Agents produce outputs but never consume other agents' outputs. Check cross-references.

22. **Protocol Overhead** — EDGE protocol document >100 lines. If the protocol needs 100+ lines to explain, it's too complex for reliable execution.

Return: agent count, overlap map, trigger clarity score (0-5), protocol complexity assessment.

---

## Phase 3: Synthesis (sequential)

Collect all subagent results. Compose final report.

### Output Format

```
═══════════════════════════════════════════
🔧 Workflow Optimizer Report
═══════════════════════════════════════════

## Health Score

| Dimension | Score | Status |
|-----------|-------|--------|
| Flow Efficiency | X/5 | 🟢/🟡/🔴 |
| Token Efficiency | X/5 | 🟢/🟡/🔴 |
| Consistency | X/5 | 🟢/🟡/🔴 |
| Agent Architecture | X/5 | 🟢/🟡/🔴 |
| **Overall** | **X/5** | |

Scoring: 🟢 4-5 | 🟡 2-3 | 🔴 0-1

## Inventory

| Category | Count | Total Lines | Total Tokens |
|----------|-------|-------------|--------------|
| CLAUDE.md | X | X | ~X |
| Agent profiles | X | X | ~X |
| Rules | X | X | ~X |
| Agent memory | X files | X | ~X |
| **Total .claude/** | | **X** | **~X** |

## Anti-Patterns Found

### 🔴 HIGH (fix now)
- [#N] Description — Location — Fix

### 🟡 MEDIUM (fix soon)
- [#N] Description — Location — Fix

### 🟢 LOW (nice to have)
- [#N] Description — Location — Fix

## Top 5 Recommendations (ordered by impact)

1. **[action]** — expected savings/improvement
2. ...

## Token Savings Estimate

| Action | Current | After | Savings |
|--------|---------|-------|---------|
| Trim agent profiles | ~Xk | ~Xk | -X% |
| Deduplicate rules | ~Xk | ~Xk | -X% |
| ... | | | |
| **Total** | **~Xk** | **~Xk** | **-X%** |
```

---

## Modes

- **analyze** (default): Report only — no file changes
- **optimize**: Full report + generate optimized versions of top 3 bloated files
- **apply**: Report + directly apply safe optimizations (dedup, trim, reformat). Confirm before destructive changes.
- **audit**: Deep dive — includes git history analysis for stale files, cross-reference every SSOT entry, verify every agent-memory link

---

## Execution Rules

1. **Phase 2 MUST be parallel** — 4 subagents in one message. Sequential = failed execution.
2. **Pass full inventory to every subagent** — they need shared context to detect cross-file issues.
3. **Subagents are read-only** — only the main agent writes/edits files (Phase 3, apply mode).
4. **All 22 anti-patterns must be checked** — A checks 1-6, B checks 7-12, C checks 13-17, D checks 18-22.
5. **Flow efficiency is weighted 2x** — when ranking recommendations, pipeline friction items rank higher at equal severity.
6. **Never delete without confirmation** — suggest deletions, don't execute them in apply mode without user approval.
7. **Respect project conventions** — if the project uses Chinese comments, keep Chinese. Don't "optimize" away the team's style.

$ARGUMENTS
