# Analyst Mode 1: Section Expansion Queue + Human Notes

## Human Notes (check first, every Mode 1/2 run)

Read `monitor/analyst/human-notes.json` if it exists. For each note with `status: "pending"`:

1. **If the note targets a pending expansion** — incorporate it when you work on that item.
2. **If the note targets a COMPLETED expansion** — create a revision. Read the completed output, apply the insight, overwrite the output file. Update tracker: `status: "revised"`, add `revised_at` and `revision_notes`.
3. **Cross-cutting notes** (no specific target) — factor into current work.

After incorporating: set note `status` to `"consumed"`, add `consumed_at` timestamp and `consumed_by` note (e.g., `"consumed_by": "EXP-003 revision — added π×R critique to paragraph 3"`).

## Check for Orphaned Issues

The decider may assign issues to you without creating a matching expansion entry:

```bash
node -e "const o=JSON.parse(require('fs').readFileSync('monitor/decisions/open-issues.json','utf8'));const t=JSON.parse(require('fs').readFileSync('monitor/analyst/expansion-tracker.json','utf8'));const tracked=new Set(t.items.flatMap(i=>i.issue_ids||[]));const orphans=o.issues.filter(i=>i.status==='assigned-analyst'&&!tracked.has(i.id));if(orphans.length)console.log('ORPHANED:',orphans.length);else console.log('ALL TRACKED')"
```

If orphaned: group related issues, create new EXP entries, work in priority order.

## Expansion Procedure

Work **one item per run** (first pending item). After completing, continue to check for dome changes.

### For each expansion:

1. **Read the curmudgeon review** — the tracker gives the file path. Study every hole, severity, and recommended fixes.

2. **Read the current section text** — from `data/wins.json` (WIN detail fields) or `data/sections.json` (prose sections).

3. **Read the dome source material** — fetch the relevant dome page(s). The curmudgeon often finds we're attacking a strawman. Get the dome's real position.

4. **Apply the Kernel of Truth standard.** Find what the dome got right, acknowledge it, then show why it fails.

5. **Write the replacement text** to `monitor/analyst/expansions/{item-id}.json`:
```json
{
  "item_id": "EXP-001",
  "target": "description of what section this replaces",
  "curmudgeon_review": "monitor/curmudgeon/reviews/WIN-NNN.json",
  "current_word_count": 100,
  "replacement_word_count": 500,
  "replacement_html": "<p>The full replacement HTML text...</p>",
  "holes_addressed": ["hole 1", "hole 2"],
  "new_evidence_added": ["source 1", "source 2"],
  "anticipated_objections": ["objection 1 → rebuttal", "objection 2 → rebuttal"],
  "kernel_of_truth": "What the dome genuinely gets right about this topic"
}
```

6. **Mark item complete** in tracker (`status: "complete"`, `completed_at`, `output_file` path).

## What Makes a Good Expansion

- **Engage with the dome's actual claim**, not a strawman. Read their page.
- **Lead with the strongest argument**, not the weakest.
- **Include specific numbers** — ratios, measurements, citations.
- **Anticipate escape hatches** (aetheric refraction, n(r), "future version will fix it") and close them pre-emptively.
- **Match tone and depth** of our best sections (Kill-Shot #1 is the gold standard).
- **Cross-reference other sections** where relevant.
