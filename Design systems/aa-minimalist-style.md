---
name: aa-minimalist-style
description: Apply the AA Minimalist design system (the Artificial Analysis aesthetic, artificialanalysis.ai) to data visualizations, dashboards, charts, infographics, comparison tables, leaderboards, and metrics reports: white canvas, heavy whitespace, Space Grotesk display + Inter body, one purple accent (#7F4BF3), a fixed per-entity categorical palette, value labels on bars, dotted gridlines, eyebrow-headed cards, and sortable pill-filtered tables. This is Satej's DEFAULT for data-viz/dashboard/infographic work — propose it automatically. Trigger when building any chart, graph, bar/line/scatter plot, dashboard, infographic, leaderboard, comparison grid, benchmark table, or metrics report, or when the user references "Artificial Analysis", "AA style", or "AA minimalist". Do NOT use for app/UI/interactive builds (default Liquid Glass) or portfolio-brand work (editorial-brutalist/Fraunces). Goal: comprehensive + minimal + professional.
---

# AA Minimalist

A faithful implementation of the **Artificial Analysis** look (artificialanalysis.ai) — the cleanest, most data-dense way to ship a chart, dashboard, or infographic. Its personality is **quiet authority**: a white page, near-black ink, one purple accent, and so much whitespace that the *data* is the only thing with color. Every pixel of chrome is in service of the numbers.

**Comprehensive is the point.** Don't strip information to look clean — pack it in (every series labeled, every caveat footnoted, sortable/filterable tables) and let whitespace + restraint do the calming. Dense and minimal at the same time.

This is Satej's **default for data viz / dashboards / infographics.** (For UI/app/interactive builds the default is Liquid Glass; for portfolio-brand work it's the editorial-brutalist system. Neither applies here.)

---

## When to use

- Charts of any kind — bar, horizontal-bar, line, scatter, bubble, frontier plots
- Dashboards, leaderboards, benchmark/comparison grids, metrics scorecards
- Infographics and explainer data pages
- Anything referencing "Artificial Analysis", "AA style", "minimalist data viz"

**Do NOT use for:** app/product UI, interactive toys, hero/marketing surfaces (→ Liquid Glass), or portfolio-site brand work (→ editorial-brutalist / Fraunces). **Fraunces never appears here.** If the user supplies their own tokens, those win.

---

## Required first step

Read **`tokens.css`** — the canonical variables (colors, type stacks, radii, the per-entity categorical palette). **Use these; do not invent colors or sizes.** Then open **`template.html`** — a working, real-data starter (bar chart, frontier scatter, sortable+filterable table, eyebrow cards, method strip, pills, footer). Build by copying its patterns and swapping the data array. Ship self-contained static HTML for artifacts.

---

## Layout

- **One centered column**, `max-width: 980px`, generous side padding (`22px` mobile-safe), big bottom padding (`~90px`). Never full-bleed.
- **Whitespace is structural.** ~34px above the page, ~38px between major blocks, ~18–24px inside cards. When unsure, add space.
- **Topbar:** small wordmark (left) + a `tabular-nums` "Updated <month year>" stamp (right) in `--faint`.
- **Card radius 16px** (`--r`); bar/track radius 5px (`--r-sm`).

## Typography

| Role | Font | Notes |
|---|---|---|
| Display / headlines / eyebrow `<h2>` | **Space Grotesk** (`--display`) | weight 600, tight tracking (`-0.015em` on h1). This replaces AA's serif. |
| Body / UI / labels / **numbers** | **Inter** (`--sans`) | data labels use `font-variant-numeric: tabular-nums` |
| Code / IDs (rare) | system mono (`--mono`) | |

- `h1`: `clamp(2.3rem, 6.4vw, 3.7rem)`, line-height ~1.02, `max-width: ~14ch` so it wraps to a tight block.
- **Font robustness:** Space Grotesk loads from the Google Fonts CDN. Its stack falls back to **Inter**, then to a system sans — so if the webfont fails (sandbox, offline, blocked CDN) headlines degrade to a clean sans, **never a serif and never Times**. This is deliberate; keep the fallback order intact.
- Subtext / ledes in `--lede` (#3a3a3a); captions, units, N/A in `--faint`.

## Color

- **Neutrals carry the page** (see `tokens.css`): bg `#fff`, ink `#1f1f1f`, muted `#737373`, faint `#a3a3a3`, borders `#e8e8e8`, gridlines `#f1f1f1`.
- **Exactly one accent — purple `#7F4BF3`** — and only for: eyebrow squares, the "Updated/New" pills, active pill/sort state, sort arrows, list bullets, focus rings. Soft tint `#f2ecff` behind accent elements. **Do not add a second UI accent.**
- **Data is the only place vivid color lives.** Use the **fixed per-entity categorical palette** (`--c-openai` black, `--c-google` green, `--c-anthropic` clay, `--c-mistral` orange, `--c-deepseek` blue, `--c-meta`/`--c-kimi`/`--c-zai` blue, `--c-minimax` pink, `--c-xai` violet, etc.). One color per entity, reused across every chart in the page so the reader learns it once. New entity with no brand color → `--c-neutral`.

---

## Components & conventions

**Eyebrow header** — every card/section opens with a small colored rounded square + a Space Grotesk `<h2>`, then a `--muted` subline. The square's color tags the section's metric/entity.

**"Higher/Lower is better"** — state the direction in the subline (e.g. `Higher is better · per chip`). Non-negotiable for any ranked chart.

**Horizontal bars** (the AA workhorse) — grid `[label | track | value]`. Track is `--grid`; fill is the entity's categorical color. **The value is always printed** at the end of the row, `tabular-nums`, with a faint unit. Sort descending by value unless context says otherwise. Animate width in on load (`transition`, respect `prefers-reduced-motion`).

**N/A / missing data** — never drop the row. Render a short **hatched** fill (45° repeating-linear-gradient grey) at a stub width and put the reason in `--faint` where the value goes (`no FP8`, `undisclosed`, `Not currently available`). Honesty about gaps is part of the look.

**Scatter / frontier plots** — inline SVG. **Dotted or hairline gridlines** (`--grid`), solid axis baselines (`--axis`), axis titles in `--muted` with a trailing `→`. Points at `fill-opacity: .62` in entity color, hover to `.85`. Optionally shade a soft-green **"most attractive region"** (e.g. top-left = high value / low cost) and label it. Bubble size can encode a third metric — note it in the legend.

**"Updated" / "New" pills** — small rounded pill, `--accent` text on `--accent-soft`, to flag freshness on a card.

**Method / source strip** — a top-and-bottom hairline-ruled band with a `METHOD` (or `UPDATE`) eyebrow in `--accent` + small-caps tracking, explaining normalization/definitions. Comprehensive work shows its work.

**Sortable tables with pill filters** — filter **pills** above (active = `--ink` bg, white text); columns click-to-sort with an `--accent` arrow; first column sticky-left on horizontal scroll; rows divide on `--grid`, hover `--hover`; all numbers `tabular-nums`; inline `est`/unit notes in `--faint`. This is how AA stays *comprehensive* without clutter.

**Footer** — `--faint`, hairline top border: data sources, "est" key, "as of" date.

---

## Rules (do / don't)

- **Do** keep it to one accent; let categorical data colors do the talking.
- **Do** print values on bars and label every series.
- **Do** show gaps (hatched N/A) rather than omitting.
- **Do** keep ~980px width and heavy whitespace.
- **Don't** use Fraunces, serifs, dark backgrounds, gradients on chrome, drop shadows for elevation (use the hairline border), or a second UI accent color.
- **Don't** confuse this with Liquid Glass — no refraction/blur/glass here. This system is flat, white, and printed-report crisp.
- **Don't** sacrifice information density to look minimal. Comprehensive + minimal together.
