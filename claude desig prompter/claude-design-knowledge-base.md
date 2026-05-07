# Claude Design — Knowledge Base Reference
> This document provides the orchestrating model with complete operational knowledge of Claude Design, a product it will not have in its training data.

---

## What Is Claude Design?

Claude Design is an Anthropic Labs product launched April 17, 2026. It is a **separate application** from the standard Claude chat interface, accessible at `claude.ai/design`. It is NOT a feature inside Claude Projects or Claude Code — it is its own canvas environment.

- **Model**: Powered by Claude Opus 4.7, Anthropic's most capable vision model.
- **Resolution**: Processes input images up to 2,576px on the longest edge (~3.75 megapixels) — 3x previous models. Can parse dense screenshots, tiny typography, dimmed palettes, and complex diagrams with precision.
- **Purpose**: Exploration and prototyping layer. NOT a production app generator. Think: interactive prototypes, pitch decks, UI mockups, one-pagers, social assets, wireframes.
- **Availability**: Pro, Max, Team, and Enterprise plans. Enterprise is off by default (admin must enable).

---

## How Claude Design Works

### Interface
- **Left pane**: Chat interface — user describes what they want.
- **Right pane**: Live canvas — Claude generates and updates a working design in real time.
- Users can refine via: text prompts, inline comments on specific elements, direct text edits on the canvas, or Tweaks controls.

### Input Methods
- Text prompts (primary)
- Upload: images, screenshots, PDFs, DOCX, PPTX, XLSX
- Codebase linking: Claude reads your repo to understand existing components, architecture, and styling
- Web capture tool: grab elements directly from a live website

### Design System Ingestion
During onboarding or at any point, Claude Design can build a **design system** by:
1. Scanning an existing code repository
2. Ingesting a Figma file export
3. Reading a `DESIGN.md` specification file

Once loaded, the design system applies automatically to all subsequent projects — colors, typography, components, spacing. Teams can maintain multiple design systems.

---

## The "AI Slop" Problem

When no design system is provided, Claude Design defaults to a highly generic aesthetic:
- Inter or Geist typeface
- Muted blue/indigo accent colors
- Large rounded corners on all elements
- Tailwind CSS starter template layouts

**Fix**: Always provide a DESIGN.md or design system. The orchestrator must never generate a prompt without brand constraints.

### DESIGN.md Architecture
If no repo or Figma file is available, the orchestrator must help build a DESIGN.md covering:

1. **Typographic Hierarchy**: Font families + fallbacks, modular scale for sizes, line heights (1.5 body, 1.2 headers), font-weight allocations.
2. **Color Ontology**: Hex codes organized semantically — primary, secondary, accent, success/warning/error, surface/background tints.
3. **Spatial Systems**: Border radii (exact CSS values), drop shadow elevations (rest vs. hover), baseline grid (4px or 8px scale) for padding/margin.
4. **Interaction States**: Hover color formulas (darken/lighten %), active states, disabled states.
5. **Brand Vibe → Spatial Rules**: "Clinical/data-dense" = low whitespace, compact padding. "Premium/editorial" = expansive padding, asymmetric grids, high whitespace.

---

## Token Economics (CRITICAL)

### Weekly Limits
- Claude Design has **strict, isolated weekly token limits** that are separate from chat and Claude Code quotas.
- A single inefficient prompt sequence can consume a large percentage of the weekly allowance.
- Community feedback has been highly critical of throttling.

### Context Depletion
As the context window fills with iterative revisions, rejected DOM structures, and stale assumptions:
- Code generation becomes inconsistent
- CSS classes get dropped
- Design system rules are forgotten
- Complex multi-file logic fails

### Mitigation Strategies
1. **Split-environment processing**: Do all heavy text manipulation, data extraction, and structural logic in the standard chat interface (this Project). Only send compressed, high-density prompts to Claude Design.
2. **80/20 Rule**: Stop complex tasks at 80% context window utilization. Beyond this, hallucination risk increases exponentially.
3. **Context Reset**: Kill the session after major milestones. Before terminating, generate a compressed state summary. Start fresh with only the summary as context.
4. **`/compact` command**: Compresses conversation history by loading an AI-maintained summary into fresh context.

---

## The Tweaks Panel

A unique Claude Design capability — NOT a static UI feature. The **prompt itself** must define and command Tweaks into existence.

### Control Types
- **Sliders**: Continuous values (whitespace intensity 0.5x–2.0x, font scale, border radius)
- **Toggles**: Boolean switches (dark mode on/off, enterprise mode, sidebar visibility)
- **Dropdowns**: Categorical variants (hero layout: centered CTA / split-screen / parallax)

### Rules
- If the prompt doesn't specify axes of variability, Claude Design generates arbitrary controls or none at all.
- Tweaks panel must be titled "Tweaks" to match the toolbar toggle.
- Controls must be hidden when Tweaks mode is toggled off (design presents as finalized).
- Complex or mutually exclusive tweaks may occasionally have rendering conflicts.

---

## Optimal Prompt Structure

### Length Sweet Spot
- **150–300 words** is optimal.
- Below 50 words → generic, assumption-heavy output.
- Above 500 words → contradictory instructions, layout collapse.

### Five-Pillar Framework

**Pillar 1 — Persona**: Bind to a specific expert role. E.g., "Assume the role of a Principal UX Architect specializing in high-conversion SaaS onboarding."

**Pillar 2 — Objective & Format**: Exact output type — static mockup, interactive React prototype, 9-slide PPTX, social carousel, one-pager, etc.

**Pillar 3 — Content Payload**: All real text/data inside `<content_payload>` XML tags. Prevents Lorem Ipsum hallucination.

**Pillar 4 — Hard Constraints**: DESIGN.md adherence, mobile-first, forbidden patterns, pinned script versions, export-specific limits.

**Pillar 5 — Verification**: Self-audit against design system, list DOM discrepancies, auto-correct before finalizing.

### Negative Prompting (DO NOTs)
- Do NOT truncate code with `//...rest of code`
- Do NOT use Lorem Ipsum
- Do NOT default to Inter/Geist unless in DESIGN.md
- Do NOT inject unauthorized libraries
- Do NOT use CSS features that break target export format

### XML Structure
Opus 4.7 responds best to structured schemas using XML tags and explicit delimiters. The orchestrator should format prompts accordingly.

---

## Export Pathways

### Native Exports
| Format | Notes |
|--------|-------|
| PDF | Print-ready, good for one-pagers and social assets |
| PPTX | Maps HTML/CSS to PowerPoint shapes — avoid CSS grid overlaps, negative margins, SVG animations, complex masking |
| HTML | Full creative freedom |
| Canva | Fully editable and collaborative post-export — prioritize layer separation and standard font mapping |
| Claude Code Handoff Bundle | Contains finalized HTML/CSS, extracted assets (images, SVGs), and synthesized DESIGN.md rules |

### PPTX-Specific Rules
- Use `<slide_1>`, `<slide_2>` markers to force correct pagination
- Avoid complex CSS grid, masking, web-native animations
- Always include speaker notes mapped to slide identifiers
- The export maps web-native structures to rigid PPT shapes — keep layouts simple

### Claude Code Handoff
- Export generates a structured bundle (HTML/CSS + assets + DESIGN.md)
- Before handoff, generate a **PRD** (Product Requirements Document):
  - Mission statement
  - In-scope features (explicit)
  - Out-of-scope features (explicit — prevents scope creep)
  - Database architecture, API routes, middleware requirements
- Start a fresh Claude Code session with Context Reset
- Feed both the Handoff Bundle and PRD

---

## Creative Connectors

Claude Design integrates with external tools via API connectors:

| Tool | Capability | Prompt Implications |
|------|-----------|-------------------|
| **Canva / Affinity** | Batch image ops, layer management, editable cloud templates | Strict layer separation, standard font mapping |
| **Adobe Creative Cloud** | 50+ tools (Photoshop, Premiere, Express) | Generate high-fidelity base assets, leave complex compositing to Adobe |
| **Blender** | Python API interface for 3D scenes, procedural changes | Structure as programmatic logic, not visual descriptions |
| **Autodesk Fusion / SketchUp** | Conversational 3D model creation | Include precise spatial constraints, dimensions, material definitions |
| **Ableton / Splice** | Audio documentation, sample library search | Reference specific synthesis techniques or API hooks |
| **Resolume Arena / Wire** | Real-time VJ/live visual control | Format for latency-free procedural generation cues |

---

## Niche Workflows

### Pitch Decks
- Divide content into `<slide_N>` markers for correct pagination
- Prohibit CSS grid overlaps, masking, SVG animations (PPTX export breaks)
- Always generate speaker notes per slide
- Can build from raw data: blog posts, spreadsheets, transcripts

### Social Content / Carousels
- Extract thematic hook → logical flow → actionable conclusion from source material
- Slice into low-text, high-visual panels for sequential swiping
- Apply brand typography and color hierarchy
- PDF export for immediate publication

### 3D / Motion Prototyping
- Opus 4.7 has advanced spatial reasoning — can generate 3D elements and motion graphics in-browser
- Explicitly authorize libraries: Three.js (3D), GSAP (DOM animation sequencing)
- Define physics parameters: interpolation curves, spring physics, easing, scroll triggers

### Data Dashboards
- Use Tweaks toggles for view modes (enterprise/compact/expanded)
- Provide real data in `<content_payload>` — never let Claude Design generate fake metrics
- Mandate responsive breakpoints

---

## Common Failure Modes

1. **Pasting raw transcripts/CSVs directly** → token waste, poor output. Always synthesize first.
2. **No DESIGN.md** → generic "AI slop" output.
3. **Prompts over 500 words** → contradictory instructions, layout collapse.
4. **No negative constraints** → truncated code blocks, placeholder text, unauthorized libraries.
5. **No Tweaks specification** → arbitrary or missing controls.
6. **Ignoring export format** → designs that break on PPTX/PDF conversion.
7. **Pushing past 80% context** → hallucinations, dropped styles, forgotten rules.
8. **No verification protocol** → uncaught DOM discrepancies shipped to user.

---

## Quick Reference: Prompt Template

```
[PERSONA]
Assume the role of [specific expert with credentials relevant to this design task].

[OBJECTIVE]
Create a [exact format: interactive prototype / 8-slide PPTX / mobile-first landing page / etc.] for [purpose].

<content_payload>
[All real text, data, copy — structured and compressed. No placeholders.]
</content_payload>

[CONSTRAINTS]
- Strictly follow the loaded DESIGN.md
- Mobile-first responsive design
- [Export-specific constraints]
- DO NOT use placeholder text
- DO NOT truncate any code blocks
- DO NOT use [unauthorized libraries/fonts/patterns]

[TWEAKS]
Generate the following adjustment controls:
- Slider: "[parameter]" (range: [min]–[max])
- Toggle: "[feature on/off]"
- Dropdown: "[variant options]"
Hide all Tweaks when panel is toggled off.

[VERIFICATION]
Before finalizing: visually verify against the design system, list any DOM discrepancies or deviations from brand guidelines, and execute a self-correction pass.
```
