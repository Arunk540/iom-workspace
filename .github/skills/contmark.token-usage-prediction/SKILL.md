---
name: contmark-token-usage-prediction
description: Stage 3 token usage scan — % vs model cap, payload for evolution loop. Load when monitoring or trimming skill bloat.
---

# Token Usage — Scan, Show %, Build Payload

Runs in Stage 3. `$activeSkill` and agent variables already in working memory.
Reads active model directly from context — no pre-set required.

## Model cap & budgets

```
modelCap (REAL context window):
  claude-*  → 200K  |  gpt-4*  → 128K  |  gemini-*  → 1M  |  default → 128K

liveWindow     = modelCap        ← what ONE agent thread can hold right now
pipelineBudget = modelCap        ← cumulative work budget. (Was ×2.5 — that assumed FULLY
                                   isolated sub-agent windows. False on shared-thread runtimes
                                   like GitHub Copilot, where the orchestrator thread accumulates
                                   every payload it sends + every gate it gets back. The ×2.5
                                   phantom budget reported "37%" while a real 200K window sat at
                                   92%. Raise above modelCap ONLY if the runtime is verified to
                                   give each sub-agent an isolated window.)
stage ceilings (% of pipelineBudget):
  plan 10% | implement 40% | unit-test 20% | component-test 24%
```

**Live-window guard — the REAL exhaustion signal. Run FIRST, at every stage boundary (not just Stage 4c):**
```
live% = currentThreadTokens ÷ liveWindow × 100
  ≥ 70% → WARN + trim: persist blobs (ticket, plan) to disk, pass PATHS not contents
  ≥ 85% → CONTEXT_PRESSURE — STOP before the next sub-agent call; compact or split the task
```
`pipeline%` alone is a lagging, cumulative metric — it reports healthy while the live window fills. `live%` is what actually protects a run.

## Calculate

Content rates:
```
prose         → chars ÷ 4
code          → chars ÷ 3
mixed/output  → chars ÷ 3.5
system prompt → chars ÷ 4   (re-sent every call — never skip)
```

Tool schema rates — re-sent with every API call, never skip:
```
IDE simple   (list_dir, run_in_terminal, show_content)          →  80 tokens each
IDE complex  (read_file, grep_search, edit, apply_patch)        → 120 tokens each
GitHub MCP   (get_issue, push_files, create_pr, search_code)    → 250 tokens each
Atlassian MCP (getJiraIssue, getConfluencePage, searchCQL)      → 300 tokens each
custom / unknown                                                 → 120 tokens each
```

Tool results — every MCP response, terminal output, file read, search result:
```
all tool results → chars ÷ 3.5
```

```
input  = user msgs + all tool results + tool schemas + system prompt
output = assistant msgs + tool call args
```

## Show %

```
live%     = currentThreadTokens ÷ liveWindow    × 100   ← check FIRST, every stage boundary
call%     = thisCallTokens      ÷ modelCap       × 100
pipeline% = totalTokens         ÷ pipelineBudget × 100
stage%    = stageTokens         ÷ stageCeiling   × 100
```

Print — append block to PR body in Stage 4 (budgets shown for claude modelCap 200K):
```
live       178K / 200K  89%  ⚠ CONTEXT_PRESSURE — compact before next stage
plan        18K /  20K  90%
implement   96K /  80K 120%  [$kafkaSkill]  ⚠ over ceiling · one-shot 62% (target 80%)
unit-test   34K /  40K  85%
pipeline   192K / 200K  96%  ⚠ approaching RUNAWAY
```
`live% ≥ 85%` → `CONTEXT_PRESSURE`. STOP before the next sub-agent call. `pipeline% > 100` → `RUNAWAY_PIPELINE`. STOP. Do not proceed to Stage 4. (Both now measure against the REAL window — no ×2.5 inflation.)

## Waste signals

Scan conversation. At each occurrence record `$agent` running + `$activeSkill` active.

| Signal              | Pattern                                              | Agent        |
|---------------------|------------------------------------------------------|--------------|
| `RETRY_STORM`       | edit→bash→edit 3+ times in one stage                 | $implementer |
| `COMPILE_LOOP`      | same error string 2+ times                           | $implementer |
| `TEST_CHURN`        | same test file edited 3+ times                       | $unitTester  |
| `EXPLORATION_DRIFT` | file reads >30% of stage tokens, zero edits          | $planner     |
| `REDUNDANT_MCP`     | same MCP tool + same key param called 2+ times       | $agent       |
| `LARGE_OUTPUT`      | terminal/MCP result >2K tokens AND called 3+ times   | $implementer |
| `TOOL_BLAST`        | >15 tool calls in a single stage                     | $agent       |
| `REPEATED_READ`     | same file read 3+ times, zero edits between reads    | $agent       |

One-shot targets: implement ≥ 80% | unit-test ≥ 85% | component-test ≥ 75%

Routing threshold: 3+ occurrences this conversation OR one-shot misses 3 cycles → build payload.
Exception — `REDUNDANT_MCP`: route at first occurrence. One duplicate MCP call is already waste.
Hint = `lessons.md` entry written by `$agent` at time of correction. Read it — never invent it.

```
{ signal, agent: $agent, skill: $activeSkill, occurrences, hint: <lessons.md entry> }
```
Pass payload to `skill-evolution-loop` (already loaded in Stage 3).

## Execute

```
1. Model   — read active model from context → set modelCap, liveWindow, pipelineBudget
2. Count   — scan all messages + tool results + schemas, attribute to stage
3. Show %  — live% FIRST (flag CONTEXT_PRESSURE if ≥ 85%), then build token block, flag RUNAWAY_PIPELINE if pipeline% > 100
4. Scan    — detect all 8 signals, record $agent + $activeSkill at each occurrence
5. Hint    — read matching lessons.md entry for each signal at threshold
6. Payload — pass to skill-evolution-loop for each signal at threshold
```