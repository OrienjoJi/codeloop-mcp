# Screenshots & marketing assets to add

This file lists the visuals that should be uploaded to `docs/assets/` later.
The README will pick them up automatically once added.

## High priority (used in README hero)

### `codeloop-banner.png` — wide hero banner (1600 × 600)
Wordmark "CodeLoop" with the tagline "Automated verification, self-repair, and confidence gating for AI coding agents." Background: subtle indigo/blue gradient (brand colour `#536bb2`). Optional: faint loop-arrow motif behind the text.

> Once added, swap the `<img>` in README.md from `codeloop-logo.svg` back to `codeloop-banner.png`.

### `loop-diagram.png` (1200 × 800)
The core verify → diagnose → fix → gate loop, visualised. Should show:
- Agent box (left)
- Five MCP tool boxes (`codeloop_verify`, `codeloop_interact`, `codeloop_diagnose`, fix step, `codeloop_gate_check`)
- A curved arrow from `codeloop_gate_check` back to `codeloop_verify` labelled "auto-loop until 94%"
- An exit arrow labelled "ready_for_review → human"

(Reuse from website if available: `packages/website/public/images/loop-diagram.png`.)

### `before-after.png` (1400 × 700)
Two-column comparison:
- **Left ("Before CodeLoop"):** developer manually opening simulator, taking screenshots, writing bug reports
- **Right ("After CodeLoop"):** agent running the loop autonomously while developer drinks coffee

(Reuse from website if available.)

## Medium priority (used in feature sections)

### `interaction-engine.png` (1400 × 800)
Grid showing the 4 interaction targets (web / desktop / Android / iOS) with example actions for each.

### `cross-platform.png` (1400 × 700)
Matrix of OS rows × app-type columns with green/yellow/grey squares, lifted from the README's compatibility table.

### `dev-log-example.png` (1200 × 1000)
Screenshot of an example `docs/DEVELOPMENT_LOG.md` rendered in GitHub or VS Code, with the run history, interaction summary, and gate results visible.

### `cursor-claude-side-by-side.png` (1600 × 700)
Two screenshots side by side — Cursor on the left, Claude Code on the right, both showing the `codeloop_*` tools in the tool list.

## Low priority (for blog posts / docs)

### `gate-pass-screenshot.png`
Cursor agent message showing `codeloop_gate_check` returning `ready_for_review` with "Confidence: 96%".

### `auto-fix-loop-screenshot.png`
Cursor / Claude Code agent message showing the `MANDATORY AUTO-FIX LOOP` directive and the agent looping verify → diagnose → fix → re-verify on its own.

### `video-preview.gif`
A short GIF (≤5 MB) showing a real CodeLoop video recording — the app being interacted with by the agent while the IDE stays in the background.

---

## Where to source these

- The website (`packages/website/public/images/` — private repo) already has `hero-visual.png`, `before-after.png`, `loop-diagram.png`, `platform-compatibility.png` generated for the landing page. Copy those over.
- For new screenshots, take them on a real CodeLoop run (the `artifacts/runs/<run_id>/screenshots/` folder will have great candidates).
- Maintain consistent brand colour `#536bb2` and use the **DIN Alternate Bold** font for headlines (already in the website fonts folder).

## Format guidelines

- PNG for screenshots, SVG for diagrams when possible (sharper on retina + smaller).
- Keep individual files under 500 KB. Optimize with `pngquant` or `svgo` before committing.
- All images should be wide enough to look good on GitHub (≥1200 px wide).
