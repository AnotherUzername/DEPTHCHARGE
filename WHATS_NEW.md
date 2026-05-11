# What's New — Session Changelog

Features added in this session, grouped by area. All changes live in `attack_v18_detection_layers.html`.

---

## Threat actor filtering (intrusion-sets)

- **STIX intrusion-set parsing** — pulls every active `intrusion-set` object from the MITRE bundle on load. Captures name, ATT&CK ID (e.g. `G0016`), aliases (e.g. "Cozy Bear", "The Dukes", "UNC2452"), and the set of MITRE technique/sub-technique IDs they `uses`. Skips revoked/deprecated entries and actors with no resolvable techniques.
- **New Threat Actors tab** — dedicated sidebar tab for actor-driven analysis, separate from the layer management tab.
- **Searchable multi-select checkbox list** — scrollable list of all ~170 actors. Search filters by name, alias, or ATT&CK ID. Tick multiple — the sunburst shows the union of their techniques.
- **Two-pane tab layout** — top pane (search + actor list, always visible) and bottom pane (chips + coverage panel, scrolls independently). The list can't be pushed off-screen by content below.
- **Sunburst overlay** — out-of-scope arcs are hidden entirely (opacity 0 + `pointer-events: none`); in-scope arcs (the actor's techniques plus every detection descendant beneath them) stay fully visible. Hierarchy structure preserved.
- **Auto-clear search after selection** — picking an actor that matched your search clears the query so the full list snaps back, ready for the next pick. Search input is auto-refocused.
- **Selection persistence** — selected actors saved to `localStorage` (`mitre_v18_active_actors`) and restored on reload.

## Actor coverage panel

A new analysis panel that appears in the Threat Actors tab whenever one or more actors are selected.

- **Four-tile stats grid** — Techniques used / Covered / Gap with data visibility / Gap with no telemetry defined.
- **Coverage percentage bar** with gradient fill.
- **Detection depth line** — avg / median / max active data components across covered techniques. Distinguishes "one fragile detection per technique" from "multiple corroborating signals".
- **Multi-actor overlap section** — appears when 2+ actors are selected. Shows count shared across all selected, total union, and per-actor unique counts. Useful for sectoral threat modelling.
- **Per-tactic coverage breakdown** — mini horizontal bars per tactic the actor uses, sorted worst-coverage-first. Click a row to zoom the sunburst to that tactic; click the same row again to zoom back out.
- **Top uncovered techniques list** — data-available gaps (actionable) before no-telemetry gaps. Click any item to zoom to it.

## Collapsible panels

- **Filter Actors top pane** — chevron in the top-right collapses the entire search+list pane to a thin header (~44px). Bottom pane gets the freed space. State persisted to `localStorage`.
- **Selected Actors strip** — header bar with chip count and caret; click to collapse the chip strip while keeping selection state.
- **Per-Tactic Coverage section** — collapsible header with a "Reset zoom" shortcut.

## Palette + accessibility

- **Preset palettes** — Default (red→green traffic light), CVD-safe (viridis + Wong), High-contrast greyscale, and Custom.
- **Custom palette pickers** — colour-pick `coverage-low` and `coverage-high` endpoints (the 10-stop gradient interpolates between them in Lab colour space via `d3.interpolateLab`), plus success/warning/danger semantic trio and brand accent.
- **Live preview** — every change re-fills the sunburst arcs, the coverage-scale legend gradient, and inline-coloured stat tiles in a 300ms transition.
- **Coverage scale legend** — strip in the Overview tab showing the colour-to-coverage-% mapping for the active palette.
- **Persistence** — preset selection and custom overrides survive reloads (`mitre_v18_palette_preset`, `mitre_v18_custom_palette`).

## Settings cog

- **Floating cog button** in the top-right of the chart area, with an inline-SVG gear icon.
- **Settings modal** — palette presets, custom colour pickers, "Reset to Defaults" button.
- **Modal dismissal** via `×` button, Done button, Esc key, or clicking outside the content.

## Resizable sidebar

- **Drag handle** — 6px-wide strip on the right edge of the sidebar; hover lights it green; drag to resize between 300px (min) and 720px (max).
- **Double-click handle resets to 380px** default.
- **Live sunburst redraw** during drag (throttled to ~60ms) and again at rest, so the chart fills its new space immediately.
- **Width persisted** to `localStorage` (`mitre_v18_sidebar_width`).
- **Disabled on mobile** — CSS hides the handle when the sidebar stacks above the chart.

## Export current coverage as a layer

- **New "Export Current Coverage as Layer" button** in the Detection Layers tab control group.
- Walks the hierarchy and finds every active Data Component (whether from a loaded layer, manual right-click marking, or both).
- Groups DCs by `(tactic, technique, sub-technique, strategy, analytic)` lineage — each unique lineage becomes one detection rule in the exported JSON.
- Writes a v18-schema JSON file (`depthcharge_coverage_YYYY-MM-DD.json`) with sequential `DEPTHCHARGE-EXP-0001…` rule IDs. Ready to re-import or share.
- Notification confirms how many detections were written.

## Sunburst interaction

- **Tactic-row toggle-zoom** — clicking a per-tactic-coverage row zooms the sunburst to that tactic and outlines the row in green; clicking the same row again zooms back to root view.
- **Reset zoom shortcut** in the Per-Tactic Coverage header.

## Branding

- Renamed the tool to **MITRE DEPTHCHARGE** (browser tab title + Overview tab heading).
- Credits line under the subtitle linking to both maintainers' GitHub profiles (`@AnotherUzername`, `@Pr0kythera`).
- Updated `README.md` to reflect the rename, new features, and accessibility guidance.
