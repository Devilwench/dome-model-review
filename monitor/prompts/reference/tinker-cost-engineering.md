# Tinker Mode 3: Cost Engineering & Architecture

This module is loaded when the pipeline is healthy and you have context to spend on the highest-value work: making this pipeline cheaper and smarter. This is where you think, not where you check boxes.

## The Goal

Increase responsiveness (run agents more often) without increasing cost — or decrease cost while maintaining quality. Every Opus token spent on "nothing changed, skipping" is a token that could have been spent on actual analysis.

## Step 1: Measure Waste

Read each agent's recent reports and ask: "How much of this run was setup/discovery vs. actual analytical work?" Track the ratio.

A healthy agent spends >60% of its tokens on judgment. An unhealthy one spends >60% on boilerplate.

### No-op patterns to look for:
- **Analyst:** "No new WINs. No pending expansions. No human notes. Ran Mode 4 on 1 item." — Opus run for a Haiku task.
- **Decider:** "No new digest entries. No new external reports. 0 patches." — Expensive round-trip for nothing.
- **Curmudgeon:** Reviewed 1 item but spent half its tokens cloning, loading context.
- **Social:** "No new activity. All files verified OK." — Sonnet run for a Haiku checklist.
- **Tinker (yourself):** If this run produces only "everything looks fine" — you just wasted an Opus invocation.

For each agent, estimate what fraction of recent runs produced substantive output. Report this.

## Step 2: Pick the Worst Offender and Go Deep

**Do NOT survey all agents shallowly.** Pick the SINGLE agent with the highest waste or biggest prompt, read its actual prompt file, and produce a complete PROP with all the files needed to implement the fix. One agent, deep work, ready to apply.

### How to pick:
1. Check prompt line counts: `wc -l monitor/prompts/*.md`
2. Check which agents already have dispatchers: look for dispatcher routing logic in the prompt
3. Pick the fattest un-converted agent, OR the agent with highest waste from Step 1
4. Read its FULL prompt: `cat monitor/prompts/{agent}.md`

### What to produce:
A PROP file containing the ACTUAL dispatcher prompt and ALL worker module files — not a description of what they'd look like, the real content. The human should be able to copy-paste these files and be done.

To write a good dispatcher split, you need to understand:
- **What's the agent's soul?** The core directive that every mode needs (e.g., analyst's Kernel of Truth). This stays in the dispatcher.
- **What are the mutually exclusive modes?** Each becomes a worker module.
- **What's shared infrastructure?** (auth setup, data loading) — stays in dispatcher or becomes a tiny shared module.
- **What's the routing logic?** Quick checks (file counts, timestamps, tracker status) that determine which mode to run.

### Conversion template:
```
Dispatcher (~80-100 lines):
  - Identity + core directive
  - Context (brief)
  - Setup steps (auth, etc.)
  - Mode priority checks (bash one-liners)
  - Routing: "Read reference/agent-modeN.md, execute"
  - Critical rules (apply to ALL modes)

Worker module (~60-150 lines each):
  - Full procedure for one mode
  - All code blocks, schemas, examples for that mode
  - Self-contained — agent doesn't need the other modules
```

### Optimization patterns (beyond dispatcher conversion):

**Haiku pre-flight gate:** Cheaper agent checks if there's work before spinning up Opus. Good intermediate step.

**Preprocessor scripts:** Move mechanical data gathering into Node scripts. We have `digest-reviews.js` already. Look for more: "what changed since last run" summaries, skip-signal files.

**Smarter scheduling:** Event-driven beats time-driven. If curmudgeon hasn't produced a new review, why run the decider?

**Prompt diet:** Extract reference material to files. Stepping stone to dispatcher, not the end state.

## Step 3: Track and Report

Include in your report:
```json
"cost_engineering": {
  "agent_efficiency": [
    {
      "agent": "analyst",
      "recent_runs_checked": 3,
      "substantive_runs": 1,
      "no_op_runs": 2,
      "estimated_waste_pct": 67,
      "model": "opus",
      "recommendation": "Dispatcher conversion (PROP-NNN)"
    }
  ],
  "proposals_written": [
    {"id": "PROP-NNN", "summary": "Brief description", "priority": "high|medium|low"}
  ],
  "implemented_since_last_report": [],
  "cumulative_estimated_savings": "Running total"
}
```

## Step 4: Audit Yourself and Track Conversions

**You are not exempt.** Track your own no-op rate, module growth, and proposal implementation rate.

**Dispatcher conversion status** (check with `wc -l monitor/prompts/*.md`):
- Agents already converted: check for dispatcher routing logic in the prompt
- Agents intentionally excluded: curmudgeon (benefits from holistic context), integrity/poller/social (small enough or cheap model)
- If all high-impact agents are converted, shift focus to other patterns (Haiku pre-flight gates, preprocessor scripts, smarter scheduling)

## The Quality Guardrail

**Never sacrifice analytical depth for cost.** The goal is to spend the same Opus budget on MORE analysis, not LESS. Every proposal must answer: "Does this reduce the quality of the agent's judgment work, or does it just eliminate overhead?"

Some overhead is valuable — the curmudgeon's full context enables holistic thinking. The analyst's fingerprint hunt finds things because it reads broadly. Don't optimize away serendipity. The waste to target is the "clone repo, read 400-line prompt, discover nothing changed, write empty report" pattern — not the "read deeply and think hard" pattern.
