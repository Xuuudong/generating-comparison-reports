# Delivery Standard

Format, data, visualization, and structure rules for comparison reports.

## Color System

| Role | Hex | CSS variable | Usage |
|:-----|:----|:-------------|:------|
| Best/Excellent | `#00875A` | `.kpi-green` / `.tag-best` | Top performers, positive metrics |
| Normal/Info | `#0066B3` | `.kpi-blue` | Standard metrics |
| Warning | `#E6A23C` | `.kpi-gold` / `.tag-warn` | Marginal results |
| Bad/Poor | `#C41230` | `.kpi-red` / `.tag-bad` | Bottom performers, failures |
| Neutral | `#909399` | — | "Other" categories |

## Accessibility (REQUIRED — WCAG 2.1 AA)

Every chart option MUST include:

```javascript
// In every chart option object:
aria: { show: true, decal: { show: true } }
```

**Rule**: Add `aria` config alongside `title`, `tooltip`, `series` in every chart option. No exceptions.

**Decal pattern mapping**: ECharts automatically assigns distinct decal patterns (bubble/circle/rect/roundRect/triangle/diamond/pin/arrow) to each data series so colorblind users can distinguish entities by pattern alone.

**Entity color consistency**: Define a global entity-to-color map in the report `<script>`, used by all charts:

```javascript
var ENTITY_COLORS = {
  'Model A': '#00875A',
  'Model B': '#0066B3',
  'Model C': '#E6A23C',
  'Model D': '#C41230',
  'Model E': '#8E44AD'
};
```

Use `ENTITY_COLORS[name]` in all chart `itemStyle.color` and series `color` overrides. This ensures the same entity is the same color across bar/radar/scatter/line — critical for visual coherence when linked highlighting triggers.

## Format Rules

| Rule | Standard |
|:-----|:---------|
| Table alignment | Descriptive cols `:-----`, numeric cols `:----:` |
| Units | Standardize across report: min / pp / KB / 万 Tokens |
| Key values | **Bold** for core metrics |
| Color-coded ratings | 🟢 excellent 🟡 medium 🟠 warning 🔴 poor |
| Footnotes | `*` `†` `††` below tables for calculation method differences |
| Entity names | Full names only (DeepSeek-V4-Pro, never v4-pro) |
| Infrastructure annotation | Entities affected by environmental failures get `*` suffix and footnoted explanation |

## Data Quality Rules

| Rule | Requirement |
|:-----|:------------|
| Traceability | Key data must reference source file absolute path |
| Calculation method | When multiple methods exist, footnote which is used |
| Per-entity detail | Summary table must be followed by per-entity/per-module detail table |
| Cost estimation | Token usage must be converted to currency (¥) |
| Compliance source | Compliance judgments must cite rule origin (e.g., SKILL.md Step X) |
| Cross-validation | Key numbers cross-checked across sources; contradictions flagged with ⚠️ |
| Infrastructure vs quality | Clearly separate infrastructure failures from entity quality in analysis and scoring |
| Success subset stats | When some runs fail due to infrastructure, compute both "all runs" and "success subset" statistics |
| Incomplete data | Entities with incomplete data get annotated scores (e.g., `15/30*`) with footnote explaining the caveat |

### Source Provenance Tracking

Every data point must be annotated with its reliability tier:

| Tier | Label | Description | Example |
|:-----|:------|:------------|:--------|
| **Primary** | `[P]` | Directly from authoritative source (MCP query, file system, official API) | `237s [P]` — from `run_01.log` MCP timing |
| **Fallback** | `[F]` | From backup/cached source when primary unavailable | `18.2 KB [F]` — from previous run's cached data |
| **Inferred** | `[I]` | Derived/computed from available data when direct measurement impossible | `~14/16 pass [I]` — estimated from 3/5 successful Agents |
| **Missing** | `[M]` | Data gap, explicitly annotated as unavailable | `— [M]` — MCP failed, no data collected |

In HTML reports, inject source tier via `<span class="src-P">` / `<span class="src-F">` / `<span class="src-I">` / `<span class="src-M">` with tooltip. In Markdown, use `^P` `^F` `^I` `^M` superscript annotations.

### Threshold-to-Color Mapping

Formal rules for assigning color classes to data values. Apply consistently — never use ad-hoc color decisions.

| Metric type | Green (`#00875A` / `.tag-best`) | Blue (`#0066B3` / normal) | Orange (`#E6A23C` / `.tag-warn`) | Red (`#C41230` / `.tag-bad`) |
|:------------|:------|:------|:--------|:------|
| Success rate | ≥95% | 80-94% | 60-79% | <60% |
| Score (percentage) | ≥90% | 70-89% | 50-69% | <50% |
| Time (lower=better) | Fastest entity | Within 20% of fastest | Within 50% of fastest | >50% slower than fastest |
| Agent consistency (range) | ≤0.5 KB | 0.5-2 KB | 2-5 KB | >5 KB |
| MCP/API reliability | 100% (0 failures) | — | — | <100% (any failure) |
| Format compliance | 0 corrections needed | — | 1-2 corrections | ≥3 corrections |

**Row color coding**:
- `.bad-row`: ≥2 red-category metrics, or any single catastrophic failure
- `.mid-row`: ≥2 orange-category metrics, or data partially incomplete

## HTML Layout Elements

### KPI Cards

```css
.kpi-row { display: grid; grid-template-columns: repeat(N, 1fr); gap: 16px; margin: 24px 0 32px 0; }
/* N = number of cards (4 or 5, match key headline metrics count) */
.kpi-card { background: linear-gradient(135deg, #F0F7FF 0%, #FFFFFF 100%); border: 1px solid #D4E4F7; border-radius: 12px; padding: 20px 16px; text-align: center; transition: transform 0.2s; }
.kpi-card:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(0,51,93,0.1); }
.kpi-card .kpi-value { font-size: 32px; font-weight: 700; line-height: 1.2; }
.kpi-card .kpi-label { font-size: 12px; color: #666; margin-top: 4px; }
.kpi-card .kpi-sub { font-size: 11px; color: #999; margin-top: 2px; }
.kpi-green .kpi-value { color: #00875A; }
.kpi-blue .kpi-value { color: #0066B3; }
.kpi-red .kpi-value { color: #C41230; }
.kpi-gold .kpi-value { color: #E6A23C; }
```

**Card content rules**:
- Decrease metrics: arrow direction old→new (e.g., "100万 → 6万" for ↓94%)
- Increase metrics: same old→new direction
- Neutral comparison: use "vs" format: "新版 6万 vs 旧版 100万"
- Card sub-line: always include baseline comparison

### Section Divider

```css
.section-divider { border: 0; height: 1px; background: linear-gradient(to right, #E8ECF0, #00355D, #E8ECF0); margin: 36px 0; }
```

### Highlight / Callout Boxes

```css
.highlight-box { background: #F0F7FF; border-left: 4px solid #0066B3; padding: 16px 20px; margin: 16px 0; border-radius: 0 8px 8px 0; }
.conclusion-box { background: #FFF8F0; border-left: 4px solid #E6A23C; padding: 16px 20px; margin: 16px 0; border-radius: 0 8px 8px 0; }
.best-box { background: #F0FFF4; border-left: 4px solid #00875A; padding: 16px 20px; margin: 16px 0; border-radius: 0 8px 8px 0; }
.errata-box { background: #FFF0F0; border-left: 4px solid #C41230; padding: 16px 20px; margin: 16px 0; border-radius: 0 8px 8px 0; }
```

- **Blue** (`highlight-box`): key findings, methodology notes, data source description
- **Orange** (`conclusion-box`): summary conclusions, corrigendum summaries
- **Green** (`best-box`): core conclusion, top performer summary
- **Red** (`errata-box`): correction notices for v2.0+ editions

### Chart Containers

```css
.chart-wrap { position: relative; margin: 16px 0; }
.chart-box { width: 100%; height: 420px; margin: 0; }        /* default: scatter, bar, donut */
.chart-box-sm { width: 100%; height: 350px; margin: 0; }      /* compact: simple ranking, pie */
.chart-box-radar { width: 100%; height: 480px; margin: 0; }   /* tall: radar with legend */
.chart-row { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin: 16px 0; }
.chart-row .chart-half { width: 100%; height: 380px; }
```

**⚠️ Every chart container must be wrapped in `.chart-wrap`** (not bare `.chart-box` wrapper). The `.chart-wrap` enables absolute-positioned export buttons.

### Chart Export Buttons

Every chart gets a hover-reveal PNG download button:

```css
.chart-export-btn { position: absolute; top: 8px; right: 8px; width: 32px; height: 32px; border: 1px solid #D4E4F7; border-radius: 6px; background: rgba(255,255,255,0.92); cursor: pointer; font-size: 14px; opacity: 0; transition: opacity 0.2s; z-index: 10; display: flex; align-items: center; justify-content: center; }
.chart-wrap:hover .chart-export-btn { opacity: 1; }
.chart-export-btn:hover { background: #F0F7FF; border-color: #0066B3; }
body.dark .chart-export-btn { background: rgba(42,48,64,0.92); border-color: #383C44; color: #D4D6D9; }
```

```html
<div class="chart-wrap">
  <div id="chart-1" class="chart-box"></div>
  <button class="chart-export-btn" onclick="exportChart('chart-1', 'chart-name')" title="下载 PNG">⬇</button>
</div>
```

```javascript
function exportChart(chartId, filename) {
  var c = _charts.find(function(ch) { return ch.getDom().id === chartId; });
  if (!c) return;
  var url = c.getDataURL({ type: 'png', pixelRatio: 2, backgroundColor: '#fff' });
  var a = document.createElement('a');
  a.href = url; a.download = filename + '.png'; a.click();
}
```

### Linked Cross-Chart Highlighting

All charts belong to a connected group. Hovering an entity name in one chart highlights it in all others:

```javascript
// In initChart helper, once at top of script block:
echarts.connect('report-group');

// Each chart option includes:
group: 'report-group',
```

### Lazy Chart Rendering (8+ charts)

Defer non-visible chart initialization with a safety-net fallback:

```javascript
var _rendered = {};

function initChartLazy(id, option) {
  var dom = document.getElementById(id);
  if (!dom) return;
  var observer = new IntersectionObserver(function(entries) {
    entries.forEach(function(e) {
      if (e.isIntersecting && !_rendered[id]) {
        _rendered[id] = true;
        initChart(id, option);
        observer.unobserve(e.target);
      }
    });
  });
  observer.observe(dom);
  setTimeout(function() {
    if (!_rendered[id]) { _rendered[id] = true; initChart(id, option); observer.unobserve(dom); }
  }, 4000);
}
```

**Usage**: First 2 visible charts use `initChart()`; all remaining use `initChartLazy()`.

### ECharts Initialization (REQUIRED)

**CDN — only use `staticfile.net`:**
```html
<script src="https://cdn.staticfile.net/echarts/5.5.0/echarts.min.js"></script>
```
Do NOT use bootcdn, jsdelivr, npmmirror, or fallback CDN scripts.

**Chart init — use a global helper + connected group, never IIFE:**
```javascript
var _charts = [];
function initChart(id, option) {
  var dom = document.getElementById(id);
  if (!dom) return;
  var chart = echarts.init(dom);
  chart.setOption(option);
  _charts.push(chart);
  return chart;
}

// Linked cross-chart highlighting — all charts belong to one group
echarts.connect('report-group');

// Single debounced resize handler for all charts
var _resizeTimer;
window.addEventListener('resize', function() {
  clearTimeout(_resizeTimer);
  _resizeTimer = setTimeout(function() {
    _charts.forEach(function(c) { c.resize(); });
  }, 100);
});

// Each chart: initChart('chart-id', { group: 'report-group', aria: {...}, ... });
```

**Every chart option MUST include**:
```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  // ... rest of option
}
```

**Legend color matching — set top-level `color`:**
```javascript
{
  color: ['#C41230', '#00875A'],  // ensures legend icons match bar colors
  legend: { data: ['Entity A', 'Entity B'] },
  series: [
    { name: 'Entity A', ... },
    { name: 'Entity B', ... }
  ]
}
```

### Table Row Color Coding

```css
.bad-row td { background: #FFF0F0; }    /* severe failures, bottom performers, or infra-affected */
.mid-row td { background: #FFFBE6; }     /* marginal results, needs attention, or corrigendum */
.good-row td { background: #F0FFF4; }    /* exceptional results, top performers */
```

Apply as `<tr class="bad-row">` or `<tr class="mid-row">`.

### Inline Progress Bar

```css
.progress-bar { display: inline-block; width: 80px; height: 8px; background: #E8ECF0; border-radius: 4px; overflow: hidden; vertical-align: middle; margin: 0 6px; }
.progress-fill { height: 100%; border-radius: 4px; }
```

Usage: `<span class="progress-bar"><span class="progress-fill" style="width:85%;background:#00875A;"></span></span>`

### Tags

```css
.tag-best { background: #00875A; color: #fff; padding: 2px 8px; border-radius: 10px; font-size: 11px; }
.tag-warn { background: #E6A23C; color: #fff; padding: 2px 8px; border-radius: 10px; font-size: 11px; }
.tag-bad { background: #C41230; color: #fff; padding: 2px 8px; border-radius: 10px; font-size: 11px; }
```

### Score / Medal Cards (Conclusion Section)

```css
display: grid; grid-template-columns: repeat(N, 1fr); gap: 12px; margin: 16px 0;
/* N = number of entities */
/* Each card: */
#1st { background: #F0FFF4; border: 1px solid #C8E6C9; }
#2nd-3rd { background: #F0F7FF; border: 1px solid #D4E4F7; }
#warning { background: #FFFBE6; border: 1px solid #FFE0B2; }
#last { background: #FFF0F0; border: 1px solid #FFCDD2; }
```

Each card contains: 🏅 emoji + score/N + entity name + one-line role tag.

### TOC Navigation

Grouped layout with color-coded section categories and CSS Grid dual-column sub-items.

**Font size hierarchy**: title 16px → group title 15px → link 13px → sub-link 12px

**CSS template**:
```css
.toc { background: #F8FAFB; border: 1px solid #E8ECF0; border-radius: 8px; padding: 18px 24px; }
.toc-title { font-size: 16px; font-weight: 700; color: #00355D; border-bottom: 2px solid #00355D; }
.toc-group-title { font-size: 15px; font-weight: 700; letter-spacing: 2px; border-bottom: 2px solid; }
.toc-group-title.g-overview { color: #0066B3; border-bottom-color: #0066B3; }   /* Blue */
.toc-group-title.g-data { color: #00875A; border-bottom-color: #00875A; }         /* Green */
.toc-group-title.g-review { color: #B8860B; border-bottom-color: #B8860B; }       /* Gold */
.toc-group-title.g-conclusion { color: #C41230; border-bottom-color: #C41230; }   /* Red */
.toc-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 4px 24px; list-style: none; }
.toc-grid a { font-size: 13px; color: #0066B3; }
.toc-grid .toc-sub a { font-size: 12px; color: #666; }
```

**Group categories**: Overview (blue) / Data Comparison (green) / Scoring (gold) / Conclusion & Recommendations (red)

### HTML Table Formatting

| Rule | Standard |
|:-----|:---------|
| Column widths | Use `<colgroup>` with percentage widths (e.g., `<col style="width:18%">`) to prevent content-driven misalignment |
| Dimension column | First column left-aligned + bold (`class="left" style="font-weight:600"`) |
| Data columns | Default center alignment (CSS `td { text-align: center }`); use `class="left"` only for long text or descriptive columns |
| Technical names | Wrap in `<code>` tags (e.g., `<code>active-equity-html-report</code>`) |
| Row backgrounds | Old-version rows: `style="background:#FFF3E0"`; even rows via `tr:nth-child(even)`; exceptional rows via `.bad-row` / `.mid-row` |
| Footnote rows | Collapsed font for explanatory text: `<p style="font-size:11px; color:#999;">` |
| Row spanning | Use `rowspan` for summary/aggregate rows (e.g., "验证汇总" row spanning check columns) |

### Responsive & Dark Mode (REQUIRED in every report `<style>`)

**Mobile / tablet breakpoints:**
```css
@media (max-width: 768px) {
  .page, .container { padding: 16px; }
  h1 { font-size: 20px; }
  h2 { font-size: 16px; }
  .kpi-row { grid-template-columns: repeat(2, 1fr); gap: 10px; }
  .kpi-card { padding: 14px 12px; }
  .kpi-value { font-size: 24px; }
  .chart-row { grid-template-columns: 1fr; }
  .chart-row .chart-half { height: 300px; }
  .chart-box, .chart-box-sm, .chart-box-radar { height: 320px; }
  .toc-grid { grid-template-columns: 1fr; }
  table { font-size: 11px; }
  th, td { padding: 6px 8px; }
  .score-cards { grid-template-columns: 1fr; }
}

@media (max-width: 480px) {
  .kpi-row { grid-template-columns: 1fr; }
  .kpi-value { font-size: 20px; }
  .chart-box, .chart-box-sm, .chart-box-radar { height: 260px; }
  .page, .container { padding: 10px; }
}
```

**Dark mode** — use a `.dark` class on `<body>` toggled by a button. This ensures dark mode works regardless of OS settings.

**Toggle button HTML (place right after `<body>`):**
```html
<button id="dark-toggle" style="position:fixed;top:16px;right:16px;z-index:9999;width:40px;height:40px;border-radius:50%;border:1px solid #D4E4F7;background:#FFF;cursor:pointer;font-size:18px;box-shadow:0 2px 8px rgba(0,0,0,0.1);" title="切换暗色模式">🌙</button>
```

**Toggle JS (place before `</body>`):**
```javascript
(function() {
  var btn = document.getElementById('dark-toggle');
  if (!btn) return;
  var isDark = localStorage.getItem('dark') === '1';
  var isLight = localStorage.getItem('dark') === '0';
  var osDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
  if (isLight) { btn.textContent = '🌙'; }
  else if (isDark || osDark) { document.body.classList.add('dark'); btn.textContent = '☀️'; }
  else { btn.textContent = '🌙'; }
  btn.addEventListener('click', function() {
    document.body.classList.toggle('dark');
    var d = document.body.classList.contains('dark');
    btn.textContent = d ? '☀️' : '🌙';
    localStorage.setItem('dark', d ? '1' : '0');
  });
})();
```

**Dark mode CSS:**
```css
body.dark { background: #1A1D21; color: #D4D6D9; }
body.dark .page, body.dark .container { background: #242830; }
body.dark h1, body.dark h2, body.dark h3, body.dark strong { color: #E0E4E8; }
body.dark .meta { color: #8B8F94; }
body.dark table { color: #D4D6D9; }
body.dark th { background: #2A3040; }
body.dark tr:nth-child(even) td { background: #2A2E35; }
body.dark td { border-color: #383C44; }
body.dark .kpi-card { background: linear-gradient(135deg, #2A3040 0%, #242830 100%); border-color: #383C44; }
body.dark .kpi-label, body.dark .kpi-sub { color: #8B8F94; }
body.dark .toc { background: #242830; border-color: #383C44; }
body.dark .highlight-box { background: #1A2A40; border-left-color: #4A90D9; }
body.dark .best-box { background: #1A3028; border-left-color: #3DA35D; }
body.dark .conclusion-box { background: #302820; border-left-color: #D4A03C; }
body.dark .bad-row td { background: #3A2020; }
body.dark .mid-row td { background: #3A3820; }
body.dark .good-row td { background: #203020; }
body.dark .section-divider { background: linear-gradient(to right, #383C44, #4A90D9, #383C44); }
body.dark code { background: #2A3040; color: #D4D6D9; }
body.dark .score-card.first { background: #1A3028; border-color: #3DA35D; }
body.dark .score-card.second { background: #302820; border-color: #D4A03C; }
body.dark .sc-name { color: #E0E4E8; }
body.dark .footnote, body.dark .sc-tag { color: #8B8F94; }
body.dark #dark-toggle { background: #2A3040; border-color: #383C44; color: #D4D6D9; }
```
1. Title + metadata line (date, version, data source)
2. [Optional] Errata/Correction notice (for v2.0+ editions)
3. KPI cards (4-5 headline metrics)
4. TOC navigation (grouped by category)
5. Executive Summary (one-sentence conclusion + top metrics table)
6-N. Dimension Analysis (chart + table alternating, each chapter with charts)
  - Efficiency: horizontal bar + scatter plot
  - Cost: grouped bar + treemap (Token/pricing breakdown)
  - Stability: donut + table with color-coded rows
  - Quality: grouped horizontal bar + detailed validation table
  - Compliance: grouped bar + table with violation-type breakdown
  - Radar: multi-dimensional radar chart + scoring breakdown table
N+1. Optimization Suggestions (P0/P1/P2 with problem→detail→suggestion table)
  - Follow with expected effect table
N+2. Conclusion & Recommendations
  - Medal ranking cards grid
  - Deployment scenario recommendation table
  - Final corrigendum summary (if applicable)
Appendix A: Data Sources & Validation Methodology
Appendix B: Scoring System Reference
```
