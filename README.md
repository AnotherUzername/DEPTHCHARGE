# MITRE DEPTHCHARGE

**An interactive MITRE ATT&CK visualization tool for detection engineers — coverage, threat-actor focus, and gap analysis in one sunburst.**

Built by [@AnotherUzername](https://github.com/AnotherUzername) and [@Pr0kythera](https://github.com/Pr0kythera).

This tool moves beyond the traditional "Bingo Card" by visualizing detection depth across the full ATT&CK hierarchy. It supports multi-layer import, temporal comparison, granular gap analysis, threat-actor (intrusion-set) filtering, and exporting your manual or layer-driven coverage state back out as a re-importable detection layer. The tool fetches the latest MITRE ATT&CK STIX bundle on load, so it stays current as MITRE publishes new releases.

## Key Features

* **Full ATT&CK Hierarchy** — visualizes the complete path: `Tactic → Technique → Sub-technique → Detection Strategy → Analytic → Data Component`.
* **Multi-Layer Management** — import, toggle, and merge multiple coverage layers (e.g. Visibility + Threat Intel + Active Detections).
* **Layer Comparison (Diffing)** — compare two timestamped layers to visualize coverage drift, improvements, or regressions over time.
* **Threat Actor Filtering** — select one or many MITRE intrusion-sets (APT29, Scattered Spider, FIN7…) and the sunburst hides every technique they don't use, leaving only the relevant TTPs lit. Supports multi-select for sectoral threat modelling.
* **Actor Coverage Panel** — for the selected actor(s), see techniques used, covered, gaps with data visibility, gaps without telemetry, coverage percentage, detection depth (avg/median/max active data components per covered technique), per-tactic breakdown, and a top-uncovered list.
* **Multi-Actor Overlap** — when 2+ actors are selected, shared/union/unique counts so you can prioritise techniques common across your modelled adversaries.
* **Contextual Activation** — prevents false-positive coverage by only activating Data Components within their specific detection path.
* **Gap Analysis** — identify where you have data visibility but lack detection rules (actionable), vs. where MITRE defines no telemetry at all (needs investment).
* **Customisable Palette + CVD-Safe Mode** — pick between Default (red→green traffic light), CVD-safe (viridis + Wong), High-contrast greyscale, or build your own with the per-colour pickers. The 10-stop coverage gradient is auto-interpolated in Lab colour space between your two chosen endpoints.
* **Configurable Ring Layout** — pick between *tree-depth* (rings follow MITRE's natural tree) or *type-grouped* (each ring is exactly one primitive type) from the Settings cog.
* **Resizable Sidebar** — drag the right edge to set your preferred width; double-click the handle to reset to default.
* **Export Current Coverage** — capture every active Data Component (manual right-click marking, an imported layer, or both) into a detection-layer JSON file that re-imports cleanly.
* **Local Persistence** — layers, actor selection, palette, sidebar width, and panel-collapse state all save to browser `localStorage`.

## Visualization Hierarchy

Unlike standard navigators, this tool renders the full depth of the ATT&CK data model:

1. **Tactic** (e.g. Credential Access)
2. **Technique** (e.g. T1110 — Brute Force)
3. **Sub-technique** (e.g. T1110.003 — Password Spraying)
4. **Detection Strategy** (e.g. DET0487 — Detect volume-based failures)
5. **Analytic** (e.g. AN1336 — Windows Event Logic)
6. **Data Component** (e.g. DS0028 — Logon Session)

## Usage Guide

1. **Load** — open `DEPTHCHARGE.HTML` in a modern browser (it fetches the MITRE STIX bundle automatically). No build step or server required.
2. **Import** — drag & drop valid detection-layer JSON onto the Detection Layers tab (see schema below).
3. **Analyze**:
   * **Toggle** — use the Detection Layers tab to enable/disable specific layers.
   * **Filter by actor** — open the Threat Actors tab; type a name, alias, or ATT&CK ID (e.g. "Cozy Bear", "APT29", "G0016") and tick the actor(s) you want to focus on. Out-of-scope techniques fade out; the Actor Coverage panel populates below.
   * **Compare** — load exactly two layers with timestamps and click **Compare Layers** for a diff report.
   * **Inspect** — hover over segments to see layer sources and metadata; click to zoom; right-click (when no layers are active) to mark coverage manually.
   * **Export** — click **Export Current Coverage as Layer** on the Detection Layers tab to download whatever is currently active as a re-importable JSON file.
4. **Settings** — click the cog icon in the top-right of the chart area to change the ring layout, switch palette (including a CVD-safe option), build a custom colour scheme, or reset to defaults.

## Detection Layer Schema

Files must be valid JSON. The tool uses a specialized schema to support the full ATT&CK detection hierarchy.

```json
{
  "name": "SIEM Coverage Q1",
  "version": "1.0",
  "domain": "enterprise-attack",
  "description": "Production detections",
  "timestamp": "2026-01-06T12:00:00Z",
  "detections": [
    {
      "ruleName": "Brute Force Detection",
      "ruleId": "RULE-001",
      "tactics": ["credential-access"],
      "techniques": ["T1110"],
      "subtechniques": ["T1110.003"],
      "detectionStrategies": ["DET0487"],
      "analytics": ["AN1336"],
      "dataComponents": ["DS0028"],
      "score": 1.0,
      "enabled": true
    }
  ]
}
```

`timestamp` is required for layer comparison. All ID fields default to `[]` when omitted, but a detection must specify at least one of `techniques` or `dataComponents`. The `tactics` field is honoured during activation: when populated, detections only light up Data Components inside the named tactic subtree(s), so a technique that exists under multiple tactics doesn't activate across all of them by accident.

## Accessibility

* **CVD-safe palette** — built-in viridis + Wong palette for users with deuteranopia, protanopia, or tritanopia. Pick it from the cog → Settings.
* **Custom palette** — colour-pick the coverage endpoints and the semantic trio yourself if neither preset works.
* **Keyboard navigation** — actor list, tactic rows, and the sunburst centre are all keyboard-reachable; Tab to focus, Space/Enter to activate, Esc closes the search and the settings modal.

## Credits

Built by [@AnotherUzername](https://github.com/AnotherUzername) and [@Pr0kythera](https://github.com/Pr0kythera). See `Blog.md` for the design rationale.
