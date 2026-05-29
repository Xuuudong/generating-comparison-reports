# Chart Type Selector

Decision tree and ECharts config templates for choosing the right chart type.

## Decision Tree

```
Data dimensions?
├── 1D (single metric)
│   ├── Ranking → Horizontal bar (labels left-aligned, no X-axis crowding)
│   ├── A/B comparison → Butterfly (bidirectional bar, left=old right=new)
│   ├── Composition → Donut (pie, radius: ['40%','70%'], center shows total)
│   └── Percentages → Grouped horizontal bar (multi-metric side-by-side)
├── 2D (correlation)
│   └── Scatter + markLine quadrants (bubble size = 3rd dimension)
├── 3-6D (multi-dimensional)
│   └── Radar (top 3: areaStyle fill; bottom 3: dashed line)
├── Time-series (ordered by date)
│   └── Line (per-entity line with legend)
├── Matrix (many entities × many dimensions)
│   └── Heatmap (color intensity per cell)
├── Distribution (multiple samples per entity)
│   └── Boxplot (median, IQR, outliers)
├── Hierarchy (nested categories, proportional)
│   └── Treemap (nested rectangles, size = value)
├── Sequential stages (each is subset of previous)
│   └── Funnel (trapezoid, wider top = larger base)
├── 7+ correlated dimensions
│   └── Parallel (polylines across axes, replaces radar past 6 dims)
└── Flow/Path
    └── Sankey (gradient colors, nodeAlign='justify')
```

## Anti-patterns

| Avoid | Use Instead |
|:------|:-----------|
| All vertical bar charts | ≥3 chart types per report |
| X-axis labels wrapping | Horizontal bar |
| Stacked bar for composition | Donut |
| Stacked bar for multi-dimensional | Radar |
| Bar chart for near-identical values | Table inline progress bar |
| Butterfly: `grid: [{...},{...}]` dual-grid + `gridIndex` | Butterfly: single grid + `xAxis[0] { inverse: true }` |
| Sankey: all nodes one color, `nodeAlign:'left'`, opaque links | Sankey: gradient colors per pipeline, `nodeAlign:'justify'`, `opacity: 0.25` |
| `markLine` at option top level (`{ series:[], markLine:{} }`) | `markLine` inside `series[0]` (`{ series:[{ markLine:{} }] }`) |
| Wrapping chart JS in `(function(){ ... })()` IIFE | Flat `initChart()` calls with helper function |

## Auto-Recommendation Engine

When analyzing sourced data, run this pseudocode logic to auto-select chart types. The model should apply this algorithm BEFORE manually picking charts, and only override if the user explicitly requests a specific chart type.

```
function recommendCharts(analysisData):
    charts = []

    // 1. Multi-entity ranking on a single metric → Horizontal bar
    if hasRankingData():  // entities + one metric per entity (time, score, cost...)
        charts.push(horizontalBar)

    // 2. Exactly 2 entities being compared side-by-side → Butterfly
    if entityCount == 2 && hasStageBreakdown():
        charts.push(butterfly)

    // 3. Composition / proportion data (parts of a whole) → Donut
    if hasCompositionData():  // e.g., success/fail counts, category shares
        charts.push(donut)

    // 4. Comparing 2 metrics per entity (e.g., pass counts from 2 sources) → Grouped horizontal bar
    if hasMultiMetricComparison():
        charts.push(groupedHorizontalBar)

    // 5. 3-6 correlated dimensions per entity → Radar
    if hasMultiDimensionalData():
        charts.push(radar)

    // 6. Two-variable relationship (e.g., time vs quality) → Scatter + quadrant lines
    if hasCorrelationData():
        charts.push(scatter)

    // 7. Pipeline / flow structure (stages with connections) → Sankey
    if hasFlowData():
        charts.push(sankey)

    // 8. Near-identical values where bars look identical → Table inline progress bar
    if hasTightRange():
        charts.push(tableProgressBar)

    // 9. Over-time trend → add line chart
    if hasTimeSeriesData():
        charts.push(line)

    // 10. Many entities × many dimensions (>5×5 matrix) → add heatmap
    if hasMatrixData():
        charts.push(heatmap)

    // 11. Multiple samples per entity (e.g., 5 Agent runs) → add boxplot
    if hasSampleDistribution():
        charts.push(boxplot)

    // 12. Hierarchical proportional data (nested categories) → add treemap
    if hasHierarchicalData():
        charts.push(treemap)

    // 13. Sequential stage data (each stage subset of previous) → add funnel
    if hasSequentialStages():
        charts.push(funnel)

    // 14. 7+ dimensions where radar would be unreadable → add parallel
    if dimensionalityCount > 6:
        charts.push(parallel)

    // Ensure ≥3 distinct chart types
    if uniqueTypes(charts) < 3:
        charts.push(horizontalBar)  // fallback

    return charts
```

**Data signal detection rules:**

| Signal | Detection method | Chart |
|:-------|:----------------|:------|
| `hasRankingData()` | Each entity has a single dominant metric (time, score, cost, etc.) | Horizontal bar |
| `entityCount == 2` | Exactly 2 comparison targets | Butterfly |
| `hasCompositionData()` | Categories sum to 100% or represent parts of a whole | Donut |
| `hasMultiMetricComparison()` | Each entity has 2+ parallel metrics (e.g., pass rates from different checks) | Grouped horizontal bar |
| `hasMultiDimensionalData()` | 3-6 correlated scores per entity | Radar |
| `hasCorrelationData()` | Two continuous variables with meaningful relationship | Scatter |
| `hasFlowData()` | Stages with defined links/transitions between them | Sankey |
| `hasTightRange()` | Values differ by <10% across entities | Table progress bar |
| `hasTimeSeriesData()` | Data points ordered by date/time with consistent intervals | Line |
| `hasMatrixData()` | ≥5 entities × ≥5 dimensions, forming a dense score/comparison grid | Heatmap |
| `hasSampleDistribution()` | Multiple samples exist per entity (e.g., 5 Agent runs → min/median/max available) | Boxplot |
| `hasHierarchicalData()` | Data has nested categories with proportional values (parent→child breakdown) | Treemap |
| `hasSequentialStages()` | Each stage value ≤ previous stage value (pipeline attrition pattern) | Funnel |
| `dimensionalityCount > 6` | 7+ parallel dimensions — radar becomes unreadable past 6 axes | Parallel |

**Priority rules:**
- Report must have **≥3 chart types** (never all one type)
- If multiple data patterns match, prefer visual diversity (don't use 3 bars)
- Sankey + radar are "high-value" types — if data supports them, include them
- Small reports (≤3 dimensions): horizontal bar + donut + table

## ECharts Config Templates

### Horizontal Bar (Ranking)

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Title', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
  grid: { left: 160, right: 80, bottom: 30, top: 50 },
  xAxis: { type: 'value', name: 'Unit', axisLabel: { formatter: function(v) { return v + 's'; } } },
  yAxis: { type: 'category', data: ['Entity1', 'Entity2', 'Entity3'], axisLabel: { fontSize: 11 } },
  series: [{
    type: 'bar', barWidth: '60%',
    data: [
      { value: 100, itemStyle: { color: '#00875A' } },
      { value: 80, itemStyle: { color: '#0066B3' } },
      { value: 60, itemStyle: { color: '#E6A23C' } }
    ],
    label: { show: true, position: 'right', fontSize: 11,
      formatter: function(p) { var m = Math.floor(p.value/60); var s = p.value%60; return m+'m'+String(s).padStart(2,'0')+'s'; }
    }
  }]
}
```

**Color assignment rule**: best `#00875A`, normal `#0066B3`, warning `#E6A23C`, worst `#C41230` (or `#f4a0a0` for extreme outliers).

### Grouped Horizontal Bar (Multi-metric Comparison)

Used when comparing multiple scores/metrics per entity side-by-side (e.g., pass counts from different validation sources).

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Metric A vs Metric B — Comparison', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
  legend: { data: ['Metric A', 'Metric B'], top: 30 },
  grid: { left: 140, right: 60, bottom: 30, top: 60 },
  xAxis: { type: 'value', name: 'Items', max: maxValue },
  yAxis: { type: 'category', data: ['Entity1', 'Entity2', 'Entity3'], axisLabel: { fontSize: 11 } },
  series: [
    {
      name: 'Metric A', type: 'bar', barWidth: '40%',
      data: [13, 13, 13],
      itemStyle: { color: '#0066B3' },
      label: { show: true, position: 'right', formatter: '{c}/16' }
    },
    {
      name: 'Metric B', type: 'bar', barWidth: '40%',
      data: [9, 8, 9],
      itemStyle: { color: '#00875A' },
      label: { show: true, position: 'right', formatter: '{c}/10' }
    }
  ]
}
```

### Butterfly (Bidirectional Bar)

> ⚠️ Both sides MUST use the same unit — convert if data sources differ (e.g., seconds → minutes).

**Canonical template (single grid, no `gridIndex`):**

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'A vs B — Butterfly Comparison', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
  color: ['#C41230', '#00875A'],  // REQUIRED: ensures legend matches bar colors
  legend: { data: ['Entity A', 'Entity B'], top: 30 },
  grid: { left: 80, right: 80, bottom: 30, top: 60 },
  xAxis: [
    { type: 'value', name: 'Entity A (分钟)', position: 'bottom', inverse: true, max: 20, axisLabel: { formatter: '{value}分' } },
    { type: 'value', name: 'Entity B (分钟)', position: 'bottom', max: 5, axisLabel: { formatter: '{value}分' }, gridIndex: 0 }
  ],
  yAxis: { type: 'category', data: ['Stage1', 'Stage2', 'Stage3'], axisLabel: { fontSize: 11 } },
  series: [
    { name: 'Entity A', type: 'bar', xAxisIndex: 0, barWidth: '50%',
      data: [2.0, 18.0, 3.0].map(function(v) { return { value: v, itemStyle: { color: '#C41230' } }; }),
      label: { show: true, position: 'left', fontSize: 10, formatter: '{c}分' }
    },
    { name: 'Entity B', type: 'bar', xAxisIndex: 1, barWidth: '50%', 
      data: [0.42, 4.22, 0.75].map(function(v) { return { value: v, itemStyle: { color: '#00875A' } }; }),
      label: { show: true, position: 'right', fontSize: 10, formatter: '{c}分' }
    }
  ]
}
```

### Donut (Composition)

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Title', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'item', formatter: '{b}: {c} ({d}%)' },
  series: [{
    type: 'pie', radius: ['40%', '70%'], center: ['50%', '55%'],
    label: { formatter: '{b}\n{d}%', fontSize: 11 },
    data: [
      { value: 23, name: 'Success', itemStyle: { color: '#00875A' } },
      { value: 2, name: 'Failed (Infra)', itemStyle: { color: '#C41230' } }
    ]
  }]
}
```

### Radar (Multi-dimensional)

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Multi-Dimensional Capability Radar', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: {},
  legend: { data: ['Entity1', 'Entity2', 'Entity3'], top: 30, textStyle: { fontSize: 11 } },
  radar: {
    indicator: [
      { name: 'Dimension1', max: 8 },
      { name: 'Dimension2', max: 5 },
      { name: 'Dimension3', max: 6 }
    ],
    center: ['50%', '58%'],
    radius: '65%',
    axisName: { fontSize: 12, color: '#333' },
    splitArea: { areaStyle: { color: ['rgba(0,51,93,0.02)', 'rgba(0,51,93,0.05)'] } }
  },
  series: [{
    type: 'radar',
    data: [
      {
        value: [8, 5, 5], name: 'Top Performer',
        lineStyle: { color: '#00875A', width: 2.5 },
        areaStyle: { color: 'rgba(0,135,90,0.2)' },
        itemStyle: { color: '#00875A' }
      },
      {
        value: [6, 5, 5], name: 'Mid Performer',
        lineStyle: { color: '#0066B3', width: 2 },
        areaStyle: { color: 'rgba(0,102,179,0.1)' },
        itemStyle: { color: '#0066B3' }
      },
      {
        value: [3, 2, 4], name: 'Low Performer *',
        lineStyle: { color: '#C41230', width: 1.5, type: 'dotted' },
        areaStyle: { color: 'rgba(196,18,48,0.05)' },
        itemStyle: { color: '#C41230' }
      }
    ]
  }]
}
```

**Visual hierarchy**: Top 3 entities get `areaStyle` fill; entities 4+ get dashed/dotted line with lighter or no fill.

### Scatter with Quadrants (2D Correlation)

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'X vs Y — Quadrant Analysis', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: {
    trigger: 'item',
    formatter: function(p) { return p.data[3] + '<br/>X: ' + p.data[0] + '<br/>Y: ' + p.data[1]; }
  },
  grid: { left: 80, right: 40, bottom: 40, top: 50 },
  xAxis: { type: 'value', name: 'X-axis Label', splitLine: { show: true, lineStyle: { type: 'dashed' } } },
  yAxis: { type: 'value', name: 'Y-axis Label', splitLine: { show: true, lineStyle: { type: 'dashed' } } },
  series: [{
    type: 'scatter',
    symbolSize: function(data) { return Math.max(14, data[2]); },
    data: [
      [x1, y1, bubbleSize, 'Entity1'],
      [x2, y2, bubbleSize, 'Entity2']
    ],
    itemStyle: {
      color: function(params) {
        var colors = ['#00875A', '#0066B3', '#E6A23C', '#C41230'];
        return colors[params.dataIndex];
      }
    },
    label: { show: true, formatter: function(p) { return p.data[3]; }, position: 'top', fontSize: 10 },
    markLine: {
      silent: true,
      lineStyle: { type: 'dashed', color: '#999', width: 1 },
      data: [
        { xAxis: splitX, label: { formatter: 'X-threshold', position: 'end' } },
        { yAxis: splitY, label: { formatter: 'Y-threshold', position: 'end' } }
      ]
    }
  }]
}
```

### Sankey (Flow/Path)

> ⚠️ Use **gradient colors** (dark→light per pipeline), `nodeAlign:'justify'`, and `opacity:0.25` links. Never use one color for all nodes.

**Canonical template:**

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Pipeline Flow Comparison', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'item', triggerOn: 'mousemove' },
  series: [{
    type: 'sankey', layoutIterations: 32, nodeAlign: 'justify',
    nodeWidth: 20, nodeGap: 14,
    lineStyle: { color: 'gradient', curveness: 0.5, opacity: 0.25 },
    label: { fontSize: 10, color: '#333' },
    emphasis: { focus: 'adjacency', lineStyle: { opacity: 0.5 } },
    data: [
      // Pipeline A (red gradient: dark → light): #B71C1C → #C62828 → #D32F2F → ... → #FF7043
      { name: 'A-Stage1', itemStyle: { color: '#B71C1C' } },
      { name: 'A-Stage2', itemStyle: { color: '#D32F2F' } },
      { name: 'A-Stage3', itemStyle: { color: '#EF5350' } },
      { name: 'A-Final',   itemStyle: { color: '#C41230' } },
      // Pipeline B (green gradient: dark → light): #1B5E20 → #2E7D32 → #388E3C → ... → #4CAF50
      { name: 'B-Stage1', itemStyle: { color: '#1B5E20' } },
      { name: 'B-Stage2', itemStyle: { color: '#388E3C' } },
      { name: 'B-Final',  itemStyle: { color: '#00875A' } }
    ],
    links: [
      { source: 'A-Stage1', target: 'A-Stage2', value: 5 },
      { source: 'A-Stage2', target: 'A-Stage3', value: 4 },
      { source: 'A-Stage3', target: 'A-Final',  value: 3 },
      { source: 'B-Stage1', target: 'B-Stage2', value: 3 },
      { source: 'B-Stage2', target: 'B-Final',  value: 2 }
    ]
  }]
}
```

**Color gradient reference:** Red pipeline: `#B71C1C → #C62828 → #D32F2F → #E53935 → #EF5350 → #E57373 → #EF9A9A → #FFCDD2 → #FF7043 → #C41230 (final)`. Green pipeline: `#1B5E20 → #2E7D32 → #388E3C → #43A047 → #4CAF50 → #66BB6A → #81C784 → #A5D6A7 → #00875A (final)`. Pick evenly spaced color stops based on node count.

### Line (Time-Series Trend)

Used when data points are ordered by time (dates, weeks, quarters) and you want to show trends or growth/decline patterns across entities.

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Token消耗趋势对比 (近6个月)', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'axis' },
  legend: { data: ['Entity A', 'Entity B'], top: 30 },
  grid: { left: 80, right: 40, bottom: 30, top: 60 },
  xAxis: { type: 'category', data: ['Jan','Feb','Mar','Apr','May','Jun'], axisLabel: { fontSize: 11 } },
  yAxis: { type: 'value', name: '万 Tokens' },
  series: [
    { name: 'Entity A', type: 'line', data: [100, 95, 88, 82, 75, 70],
      lineStyle: { color: '#C41230', width: 2 }, itemStyle: { color: '#C41230' },
      smooth: false },
    { name: 'Entity B', type: 'line', data: [50, 48, 52, 45, 42, 38],
      lineStyle: { color: '#00875A', width: 2 }, itemStyle: { color: '#00875A' },
      smooth: false }
  ]
}
```

> Line chart is low-risk — no dual-grid, no special nesting. Only pitfall: ensure xAxis `data` array length matches each series `data` array length.
> For datasets with **>500 data points**, add `progressive: 200` to the series to avoid rendering lag: `{ name: '...', type: 'line', progressive: 200, data: [...] }`.

### Heatmap (Dense Matrix Comparison)

Used when comparing many entities across many dimensions simultaneously — where a table would be too dense to read. Each cell's color intensity represents the value.

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '多模型 × 多维度评分热力图', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { position: 'top' },
  grid: { left: 120, right: 60, bottom: 30, top: 40 },
  xAxis: { type: 'category', data: ['准确性','完整性','速度','成本','稳定性'], axisLabel: { fontSize: 11 }, position: 'top' },
  yAxis: { type: 'category', data: ['Model1','Model2','Model3','Model4','Model5'], axisLabel: { fontSize: 11 } },
  visualMap: { min: 0, max: 100, calculable: true, orient: 'vertical', right: 10, top: 'center',
    inRange: { color: ['#FFF0F0', '#FFCDD2', '#EF9A9A', '#E57373', '#C41230'] },
    text: ['高', '低'], textStyle: { fontSize: 11 } },
  series: [{
    type: 'heatmap',
    data: [
      [0,0,95],[0,1,88],[0,2,72],[0,3,60],[0,4,91],
      [1,0,82],[1,1,90],[1,2,68],[1,3,75],[1,4,85],
      [2,0,78],[2,1,72],[2,2,95],[2,3,55],[2,4,80],
      [3,0,91],[3,1,85],[3,2,70],[3,3,92],[3,4,78],
      [4,0,65],[4,1,70],[4,2,85],[4,3,60],[4,4,88]
    ],
    label: { show: true, fontSize: 10 }
  }]
}
```

**Heatmap data format**: Each point is `[xAxisIndex, yAxisIndex, value]`. Indices are zero-based into their respective axis `data` arrays. Count must equal `xAxis.data.length × yAxis.data.length`.

> Heatmap is low-risk — uses standard 2D coordinate system. Ensure `visualMap.min/max` covers your data range to avoid washed-out colors.

### Boxplot (Distribution Comparison)

Used when you have multiple data points per entity (e.g., 5 Agent runs per model) and need to compare median, spread, and outliers. Box height = IQR (Q1-Q3), middle line = median, whiskers = min/max.

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '各模型执行时间分布对比', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'item', axisPointer: { type: 'shadow' } },
  grid: { left: 100, right: 40, bottom: 30, top: 40 },
  xAxis: { type: 'category', data: ['Model A','Model B','Model C','Model D'], axisLabel: { fontSize: 11 } },
  yAxis: { type: 'value', name: '耗时 (分钟)' },
  series: [{
    type: 'boxplot',
    data: [
      [2.5, 4.0, 5.5, 7.0, 9.0],   // [min, Q1, median, Q3, max]
      [1.0, 2.0, 3.0, 4.5, 6.0],
      [0.3, 0.4, 0.5, 0.6, 0.8],
      [3.0, 5.0, 7.0, 10.0, 15.0]
    ],
    itemStyle: { color: '#0066B3', borderColor: '#00355D' }
  }]
}
```

**Boxplot data format**: Each box is `[min, Q1, median, Q3, max]`. If you have raw data points instead of statistics, use `echarts.dataTool.prepareBoxplotData()` helper.

> Low-risk: standard cartesian chart. Pitfall: ensure all 5 values per box are properly ordered (min ≤ Q1 ≤ median ≤ Q3 ≤ max).

### Treemap (Hierarchical Size Comparison)

Used to show proportional breakdown across nested categories — e.g., Token cost by pipeline stage and sub-stage, or asset allocation by class and region.

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: 'Token成本层级分布', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { formatter: function(p) { return p.name + ': ' + p.value + ' 万 Tokens'; } },
  series: [{
    type: 'treemap', roam: false, nodeClick: false, breadcrumb: { show: false },
    label: { show: true, formatter: '{b}\n{c}万', fontSize: 11 },
    upperLabel: { show: true, height: 24, fontSize: 12, color: '#fff' },
    itemStyle: { gapWidth: 2, borderColor: '#fff' },
    levels: [
      { colorSaturation: [0.35, 0.5], itemStyle: { borderWidth: 2, gapWidth: 2 } },
      { colorSaturation: [0.3, 0.6], colorMappingBy: 'value' }
    ],
    data: [
      { name: 'AI观点生成', value: 3500, itemStyle: { color: '#0066B3' },
        children: [
          { name: '权益点评', value: 1200 },
          { name: '债券点评', value: 800 },
          { name: '商品汇率', value: 700 },
          { name: '信号解读', value: 800 }
        ]
      },
      { name: 'Skill指令', value: 2800, itemStyle: { color: '#8E44AD' } },
      { name: 'HTML渲染', value: 2700, itemStyle: { color: '#00875A' },
        children: [
          { name: '骨架填充', value: 1500 },
          { name: '收尾校验', value: 1200 }
        ]
      },
      { name: '其他开销', value: 2800, itemStyle: { color: '#909399' } }
    ]
  }]
}
```

> Treemap hierarchy: use nested `children` for sub-categories. Top-level items can be leaf nodes (no children) or branches (with children). Use `nodeClick: false` to avoid zoom-in behavior in static reports.

### Funnel (Stage Conversion / Attrition)

Used to show sequential stage metrics where each stage is a subset of the previous — e.g., validation pass rates across pipeline layers, or user conversion funnel.

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '五层验证逐层通过率', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'item', formatter: '{b}: {c}项 ({d}%)' },
  series: [{
    type: 'funnel', sort: 'descending', gap: 4,
    label: { show: true, position: 'inside', formatter: '{b}\n{c}项', fontSize: 11 },
    data: [
      { value: 54, name: '数据验证', itemStyle: { color: '#00875A' } },
      { value: 52, name: 'Codegen验证', itemStyle: { color: '#0066B3' } },
      { value: 48, name: '观点验证', itemStyle: { color: '#E6A23C' } },
      { value: 45, name: 'MD验证', itemStyle: { color: '#E6A23C' } },
      { value: 42, name: 'DOCX验证', itemStyle: { color: '#C41230' } }
    ]
  }]
}
```

> Funnel sorts data by value descending by default. Use `sort: 'none'` to preserve order. The trapezoid shape naturally conveys attrition — narrower top = fewer items passed.

### Parallel Coordinates (>6 Dimensions)

Used when you have 7+ correlated dimensions per entity that would make a radar chart unreadable. Each entity is a polyline crossing all axes.

```javascript
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '多模型多维能力平行坐标', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: {},
  parallelAxis: [
    { dim: 0, name: '速度', type: 'value', min: 0, max: 10, axisLabel: { fontSize: 10 } },
    { dim: 1, name: '准确性', type: 'value', min: 0, max: 10 },
    { dim: 2, name: '成本', type: 'value', min: 0, max: 10 },
    { dim: 3, name: '稳定性', type: 'value', min: 0, max: 10 },
    { dim: 4, name: '合规性', type: 'value', min: 0, max: 10 },
    { dim: 5, name: '覆盖率', type: 'value', min: 0, max: 10 },
    { dim: 6, name: '响应速度', type: 'value', min: 0, max: 10 }
  ],
  parallel: { axisExpandable: false },
  series: [{
    type: 'parallel', lineStyle: { width: 2, opacity: 0.7 },
    data: [
      [8, 9, 7, 10, 8, 9, 6],   // Model A
      [5, 8, 4, 7, 9, 8, 5],    // Model B
      [9, 6, 9, 6, 6, 7, 8],    // Model C
      [4, 7, 3, 8, 7, 6, 4]     // Model D
    ]
  }]
}
```

**Parallel data format**: Each row is an array of values matching `parallelAxis` order (by `dim` index). Value count MUST equal `parallelAxis.length`.

> Use `axisExpandable: false` in static reports. Override radar when dimensions > 6 — radar becomes unreadable past 6 axes due to label overlap.

---

## ECharts 6.0 Chart Types (Version-Gated)

> ⚠️ These chart types require ECharts 6.0+. Always provide a v5 fallback. Use the version gate:
> ```javascript
> var E6 = (typeof echarts !== 'undefined' && echarts.version && String(echarts.version).startsWith('6'));
> ```

### Beeswarm (Single-Axis Scatter, No Overlap)

Used when you have multiple data points per entity (e.g., per-Agent-run times) and want to show individual data points without boxplot's information loss. Points are distributed to avoid overlap.

```javascript
// ECharts 6.0 beeswarm — fallback to boxplot on v5
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '各模型运行时间散点分布', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'item' },
  grid: { left: 120, right: 40, bottom: 30, top: 40 },
  xAxis: { type: 'value', name: '耗时 (分钟)' },
  yAxis: { type: 'category', data: ['Model A', 'Model B', 'Model C', 'Model D'], axisLabel: { fontSize: 11 } },
  series: [{
    type: 'beeswarm',
    data: [
      [2.3, 'Model A'], [2.8, 'Model A'], [3.1, 'Model A'], [2.5, 'Model A'], [4.2, 'Model A'],
      [1.0, 'Model B'], [1.2, 'Model B'], [0.9, 'Model B'], [1.5, 'Model B'], [1.1, 'Model B'],
      [5.0, 'Model C'], [4.8, 'Model C'], [6.2, 'Model C'], [5.5, 'Model C'], [5.9, 'Model C'],
      [0.3, 'Model D'], [0.4, 'Model D'], [0.35, 'Model D'], [0.5, 'Model D'], [0.3, 'Model D']
    ],
    itemStyle: { color: function(params) { return ENTITY_COLORS[params.data[1]] || '#0066B3'; } }
  }]
}
```

**Version gate pattern**:
```javascript
if (E6) {
  initChart('chart-dist', beeswarmOption);
} else {
  initChart('chart-dist', boxplotOption);
}
```

### Broken Axis (Value Gap Visualization)

Used when entity values differ by orders of magnitude (e.g., 2s vs 180s). The axis shows a torn-paper visual break around the gap, then resumes at the actual value.

```javascript
// ECharts 6.0 broken axis — fallback to annotated bar on v5
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '执行时间对比（含断轴）', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
  grid: { left: 140, right: 40, bottom: 30, top: 40 },
  xAxis: { type: 'value', name: '耗时',
    breakAxis: {
      breakAt: 20,    // break between 20 and 140
      gap: 4          // visual gap size
    },
    axisLabel: {
      formatter: function(v) { return v < 30 ? v + '分钟' : v + '分钟'; }
    }
  },
  yAxis: { type: 'category', data: ['Model A', 'Model B', 'Model C'], axisLabel: { fontSize: 11 } },
  series: [{
    type: 'bar', barWidth: '60%',
    data: [
      { value: 180, itemStyle: { color: '#C41230' }, label: { show: true, position: 'right', formatter: '180分钟' } },
      { value: 2, itemStyle: { color: '#00875A' }, label: { show: true, position: 'right', formatter: '2分钟' } },
      { value: 15, itemStyle: { color: '#0066B3' }, label: { show: true, position: 'right', formatter: '15分钟' } }
    ]
  }]
}
```

**Version gate pattern**:
```javascript
if (E6) {
  initChart('chart-speed', brokenAxisOption);
} else {
  // v5 fallback: standard bar with annotation
  initChart('chart-speed', standardBarWithFootnoteOption);
}
```

### Chord (Cross-Entity Relationship)

Used to show relationship strength between entities — e.g., which models share similar output patterns, or which dimensions have the most shared influence.

```javascript
// ECharts 6.0 chord — fallback to heatmap on v5
{
  group: 'report-group',
  aria: { show: true, decal: { show: true } },
  title: { text: '跨模型输出相似度关系', left: 'center', textStyle: { fontSize: 14 } },
  tooltip: {},
  series: [{
    type: 'chord',
    data: [
      { name: 'Model A', itemStyle: { color: '#00875A' } },
      { name: 'Model B', itemStyle: { color: '#0066B3' } },
      { name: 'Model C', itemStyle: { color: '#E6A23C' } },
      { name: 'Model D', itemStyle: { color: '#C41230' } }
    ],
    links: [
      { source: 'Model A', target: 'Model B', value: 0.85 },
      { source: 'Model A', target: 'Model C', value: 0.42 },
      { source: 'Model A', target: 'Model D', value: 0.30 },
      { source: 'Model B', target: 'Model C', value: 0.55 },
      { source: 'Model B', target: 'Model D', value: 0.38 },
      { source: 'Model C', target: 'Model D', value: 0.62 }
    ],
    ribbonType: true,
    label: { show: true, fontSize: 10 },
    lineStyle: { color: 'source', curveness: 0.3, opacity: 0.4 }
  }]
}
```

**Version gate pattern**:
```javascript
if (E6) {
  initChart('chart-relation', chordOption);
} else {
  initChart('chart-relation', heatmapOption);
}
```