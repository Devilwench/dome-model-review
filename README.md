# Critical Review: Ovoid Cavity Cosmological Model V51.0

(formerly Dome Cosmological Model V50.6)

Point-by-point analysis of 67 claimed wins, new site pages, falsification tests, version change tracking, and AI prompt injection analysis.

## Live Site

**[View the full review](https://funwithscience-org.github.io/dome-model-review/)**

## Downloads

- [Word Document V4 (.docx)](downloads/critical-review-dome-model-v4.docx)
- [PDF Version V4](downloads/critical-review-dome-model-v4.pdf)
- [Word Document V3 (.docx)](downloads/critical-review-dome-model-v3.docx) — original V50.6 review
- [PDF Version V3](downloads/critical-review-dome-model-v3.pdf) — original V50.6 review

## Version History

| Version | Date | Site Version | WINs Analyzed | Notes |
|---------|------|-------------|---------------|-------|
| V1-V3 | 2026-03-12 | V50.6 | 39 | Initial review, falsification tests, AI directive analysis |
| V4.0–4.8 | 2026-04-05 | V51.0 | 67 | Version change tracking, self-consistency analysis, Self-Contradicted category, independent adversarial review, unified build pipeline |

## Build

```bash
npm install
node build.js          # Rebuild HTML + DOCX + PDF
node build.js html     # HTML only (fast)
node build.js publish  # Build all + git commit + push
```

All WIN data lives in `data/wins.json` (single source of truth). See `CLAUDE.md` for full architecture docs.

## Structure

- `data/wins.json` — All 67 WINs: claims, verdicts, findings, detailed analyses
- `build.js` — Unified build pipeline
- `build-scripts/generate-html.js` — Generates HTML from wins.json
- `build-scripts/build-doc-v4.js` — Generates DOCX from wins.json
- `build-scripts/add-references.js` — Injects clickable source links
- `docs/` — Generated HTML site (GitHub Pages) — do not edit directly
- `downloads/` — Generated Word and PDF versions
- `raw-text/` — Extracted ECM site content
- `raw-text-v50.6-2026-03-12/` — Archived V50.6 baseline for version comparison

## Key Finding

Of 67 claimed wins: 11 refuted by data, 11 self-contradicted by the dome's own geometry, 15 explained by standard physics, 23 misleading, 3 not demonstrated, 4 unfalsifiable. Zero survive scrutiny.

## Source

Analysis of: https://john09289.github.io/predictions
