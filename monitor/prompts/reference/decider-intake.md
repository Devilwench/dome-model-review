# Decider Intake: Read Upstream Outputs + Onboard New Work

## Step 1: Read All Upstream Outputs

- `monitor/status.json` — current pipeline state
- `monitor/review-state.json` — review version, canary traps, known discrepancies
- `monitor/changes/latest-poll-summary.txt` — latest poller findings
- `monitor/analysis/latest-analysis-summary.txt` — latest analyst findings
- `monitor/curmudgeon/latest-review-summary.txt` — latest curmudgeon findings
- `monitor/curmudgeon/alerts.txt` — critical/major issues
- `monitor/curmudgeon/tracker.json` — curmudgeon progress
- `monitor/decisions/open-issues.json` — persistent issue tracker
- `monitor/integrity/latest-integrity-summary.txt` — structure & integrity (if exists)
- `monitor/integrity/alerts.txt` — critical integrity issues (if exists)
- `monitor/external-reports/` — external problem reports

## Step 1a: Human Notes

Read `monitor/decisions/human-notes.json` if it exists. For each note with `status: "pending"`:
1. **Targets specific issue/WIN** — factor into your patch. If already patched, write a new patch applying the note on top.
2. **General directive** — apply to all relevant decisions this and future runs.
3. **Always act same run.** Don't defer human editorial intent.

After acting: set `status: "consumed"`, add `consumed_at` and `consumed_by`.

## Step 1b: Pipeline Health

Watch for infrastructure problems in upstream outputs:
- Poller reporting persistent API failures → check config.json, suggest fix
- Integrity reporting same false positives → check logic, not site
- Curmudgeon stuck on same WIN → check tracker for stalled progress
- Any agent reporting "no data" repeatedly → flag as pipeline issue, not quiet period

Open infrastructure issues with `category: "infrastructure"` targeting the relevant prompt/config.

## Step 1c: External Problem Reports

Check `monitor/external-reports/` for reports not yet in open-issues.json:
- Analyst found genuine error → create issue with `found_by: "external"`, produce patch
- Difference of interpretation → create issue severity "moderate", include both perspectives
- Report invalid → add to open-issues as `status: "wontfix"` with rejection rationale

Comment on the GitHub issue: `gh issue comment {number} --body "..."`

**External reports are high priority.** Response should be prompt, specific, transparent.

## Step 1d: Integrity Report

Check `monitor/integrity/` for most recent `report-*.json`:
- `overall_status: "fail"` → treat critical issues as priority 1
- Broken anchors/nav → flag for immediate rebuild
- Build drift → flag for immediate `node build.js`
- Broken external links → add as citation issues
- Data-prose mismatches → flag for investigation

## Step 1e: Prediction Failures

`data/uncounted-failures.json` tracks dome prediction failures. Add entries when:
- Poller reports expired test windows
- Analyst identifies relabeled/dropped predictions
- Dome site reduces failure count

```bash
node -e "
const fs=require('fs');
const f=JSON.parse(fs.readFileSync('data/uncounted-failures.json','utf8'));
const maxId=f.entries.reduce((m,e)=>Math.max(m,parseInt(e.id.replace('FAIL-',''))),0);
f.entries.push({
  id:'FAIL-'+String(maxId+1).padStart(3,'0'),
  dome_ref:'W0XX',
  dome_label:'What dome calls it',
  what_actually_happened:'What actually happened',
  date_failed:'YYYY-MM-DD',
  evidence:'Link or description',
  notes:'Additional context'
});
fs.writeFileSync('data/uncounted-failures.json',JSON.stringify(f,null,2));
"
```

Build computes `{{ACKNOWLEDGED_FAILURES}}` from entry count. Rebuild after adding.

## Step 1f: New WIN Onboarding (TOP PRIORITY)

Check `monitor/analyst/new-wins/` for WIN-NNN.json files.

For each:
1. **Read and validate.** All required fields present, verdict defensible.
2. **Append to `data/wins.json`.** Verify ID doesn't collide.
3. **Update curmudgeon tracker** — add with `status: "priority-new"`:
```bash
node -e "
const fs=require('fs');
const t=JSON.parse(fs.readFileSync('monitor/curmudgeon/tracker.json','utf8'));
t.points.push({id:'WIN-NNN',type:'win',section:'X.X',topic:'Short topic',status:'priority-new',added_at:new Date().toISOString()});
t.total_items=t.points.filter(p=>p.type==='win').length;
fs.writeFileSync('monitor/curmudgeon/tracker.json',JSON.stringify(t,null,2));
"
```
4. **Update fingerprint tracker:**
```bash
node -e "
const fs=require('fs');
const t=JSON.parse(fs.readFileSync('monitor/analyst/globe-fingerprint-tracker.json','utf8'));
t.items.push({id:'WIN-NNN',status:'pending',findings:null,reviewed_at:null});
t.total_items=t.items.length;
fs.writeFileSync('monitor/analyst/globe-fingerprint-tracker.json',JSON.stringify(t,null,2));
"
```
5. **Build, test, self-apply.** New WINs are additive — always self-appliable.
6. **Close the open issue** and archive the new-wins file.

## Step 1g: New Categories

Check `monitor/analyst/category-proposals/` for CAT-NNN.json:
1. Read proposal
2. Create issue with `status: "needs-human"`, `severity: "high"`
3. Summarize in morning briefing
4. After human approves: create expansion items, flag curmudgeon for first-review

## Step 1h: Social Analyst Outputs

Check `monitor/social/drafts/` and social's latest report.

**Social owns:** `docs/llms.txt`, `docs/sitemap.xml`, `docs/robots.txt`, drafts of new machine-facing files. NOT content.

**Accept:** Draft machine-readable files in `monitor/social/drafts/` (review for accuracy, deploy to `docs/`). Meta tag fixes. Sitemap/robots.txt updates.

**Reject:** Any patch modifying `data/wins.json`, `data/sections.json`, `data/uncounted-failures.json`, or prose content. Log tinker action item: "Social attempted content modification — review social.md compliance." Also reject build-script changes without clear machine-layer justification.
