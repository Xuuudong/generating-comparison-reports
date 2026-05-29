<div align="center">
  <h1>📊 Generating Comparison Reports</h1>
  <p>
    <a href="#chinese">🇨🇳 中文</a> &nbsp;|&nbsp;
    <a href="#english">🇬🇧 English</a>
  </p>
  <p>
    <img src="https://img.shields.io/badge/version-3.0-blue" alt="version">
    <img src="https://img.shields.io/badge/WCAG-2.1%20AA-green" alt="WCAG">
    <img src="https://img.shields.io/badge/license-MIT-green" alt="license">
  </p>
</div>

---

<a id="chinese"></a>

<div id="zh-default">

## 🇨🇳 中文

### ✨ 它能做什么

一个面向 OpenCode/Claude/AI Agent 的 skill，**一键生成自包含 HTML + Markdown 双格式对比分析报告**。只需指定数据源目录，自动完成数据采集、交叉验证、图表推荐、报告生成全流程，产出可直接在浏览器打开的完整报告。

**v3.0 新增**: WCAG 2.1 AA 无障碍（ARIA 标签 + 贴花纹理）、图表 PNG 导出按钮、跨图表联动高亮、懒加载渲染、ECharts 6.0 版本门控图表。

### 🚀 快速开始

用自然语言激活 skill：

```
"横向评测这5个模型的效果，用图表展示差异"
"对比 D:\model-a 和 D:\model-b 的评测报告，生成综合对比分析"
```

六步自动化工作流：

```
数据采集 → 深度探索 → 交叉验证分析
  → HTML 报告生成 → Markdown 报告生成 → 验证与交付
```

**输出结构**:

```
output/
├── 对比分析报告.html       ← 自包含，浏览器直接打开
├── 对比分析报告.md         ← Markdown 对照版
└── tmp/                    ← 中间制品（可追溯）
```

### 🎯 核心特性

| 特性 | 说明 |
|:--------|:------------|
| 🔍 **自动数据采集** | 并行读取多目录下的 SKILL.md、评测报告、日志 |
| 🏷️ **智能图表推荐** | 根据数据特征自动匹配最优图表类型（14+ 种） |
| 📊 **14+ 种图表** | 柱状图、蝴蝶图、环形图、散点图、雷达图、桑基图、折线图、热力图、箱线图、树图、漏斗图、平行坐标、分组柱状图、表格进度条，加 ECharts 6.0 蜂群图/断轴图/弦图 |
| ♿ **WCAG 2.1 AA 无障碍** | 每张图表自动生成 ARIA 标签和贴花纹理 |
| 🔗 **联动高亮** | hover 一个实体，所有图表中该实体同时高亮 |
| ⬇ **图表导出** | 每张图表 hover 显示下载按钮，一键导出 2x PNG |
| ⚡ **懒加载** | 图表滚动到视口才渲染，含 4 秒超时兜底 |
| 🎨 **暗色模式** | 手动切换 + 完整 CSS 暗色主题 |
| 📱 **响应式** | 移动端/平板/桌面三级断点自适应 |
| 🔄 **版本对比** | 自动识别上次运行结果，▲/▼ 变化标注 |
| 🗺️ **Mermaid** | Markdown 报告含原生 Mermaid 流程图 |
| 🏷️ **数据溯源** | [P]主/[F]备/[I]推断/[M]缺失 四级标记 |

### 📊 图表一览

| 数据特征 | 图表类型 |
|:--------------------|:-----------|
| 多实体单维度排名 | 水平柱状图 |
| A/B 二级对比 | 蝴蝶图（双向柱状） |
| 构成占比 | 环形图 |
| 多维度(3-8) | 雷达图 |
| 双变量相关 | 散点图 + 象限线 |
| 流程/路径分流 | 桑基图 |
| 时间序列趋势 | 折线图 |
| 密集矩阵(多×多) | 热力图 |
| 分布(多样本) | 箱线图 |
| 层级占比 | 树图 |
| 逐级流失 | 漏斗图 |
| 7+ 维度 | 平行坐标 |
| 多指标并列 | 分组水平柱状图 |
| 数值差异极小 | 表格内嵌进度条 |
| 多数据点展示 | 蜂群图 (ECharts 6.0) |
| 数量级差距大 | 断轴柱状图 (ECharts 6.0) |
| 跨实体关系 | 弦图 (ECharts 6.0) |

> ECharts 6.0 图表自动含 v5 降级方案 — CDN 版本不限。

### 🆕 v3.0 升级对照

| v2.1 | v3.0 |
|:-----|:-----|
| 14 种图表 | 14+ 种（扩展 ECharts 6.0） |
| 无障碍: 无 | WCAG 2.1 AA: ARIA + 贴花纹理 |
| 图表导出: 无 | 每张图表 PNG 导出按钮 |
| 图表隔离 | 跨图表联动高亮 |
| 全量加载 | 懒加载 + 4 秒安全兜底 |
| 无版本追踪 | 连续运行 ▲/▼ 差异标注 |
| ASCII 流程图 | Markdown Mermaid 块 |
| 仅 ECharts 5.5 | 版本门控兼容 ECharts 6.0 |

### 🧩 文件结构

```
generating-comparison-reports/
├── SKILL.md                      # Skill 定义与完整工作流
├── manifest.yaml                 # 跨平台元数据
├── LICENSE
├── agents/
│   └── openai.yaml               # OpenAI 平台 skill 配置
└── references/
    ├── delivery-standard.md      # 前端规范（CSS、色彩、A11y、响应式）
    └── chart-selector.md         # 14+ 图表 ECharts 模板 + 智能推荐引擎
```

### 🤝 贡献

欢迎 PR。图表模板在 `references/chart-selector.md`，可新增或优化现有 ECharts 配置。

### 📄 License

MIT © [Xuuudong](https://github.com/Xuuudong)

<p align="right"><a href="#english">English ↓</a></p>

</div>

---

<a id="english"></a>

<details>
<summary><b>🇬🇧 English</b> （点击展开 / Click to expand）</summary>

<br>

### ✨ What It Does

A skill for OpenCode/Claude/AI agents that generates **self-contained HTML + Markdown dual-format comparison reports** with ECharts visualizations. Point it at multiple data sources and get a professional report with 14+ chart types, KPI cards, TOC navigation, dark mode, and responsive layout — opening directly in any browser.

**v3.0 adds**: WCAG 2.1 AA accessibility (ARIA + decal patterns), chart PNG export buttons, linked cross-chart highlighting, lazy rendering, and ECharts 6.0 version-gated charts.

### 🚀 Quick Start

Activate with natural language:

```
"Compare the two benchmark runs in D:\run-v1 and D:\run-v2"
"横向评测这5个模型的效果，用图表展示差异"
```

6-step automated workflow:

```
Data Collection → Deep Exploration → Cross-validation
  → HTML Report → Markdown Report → Validation & Delivery
```

**Output**:

```
output/
├── comparison-report.html       ← self-contained, browser-ready
├── comparison-report.md         ← markdown companion
└── tmp/                         ← traceable intermediate artifacts
```

### 🎯 Key Features

| Feature | Description |
|:--------|:------------|
| 🔍 **Auto Data Collection** | Parallel read of SKILL.md, reports, logs from multiple dirs |
| 🏷️ **Smart Chart Selection** | Auto-recommends chart types based on data patterns |
| 📊 **14+ Chart Types** | Bar, Butterfly, Donut, Scatter, Radar, Sankey, Line, Heatmap, Boxplot, Treemap, Funnel, Parallel, Grouped Bar, Table Progress, plus ECharts 6.0 Beeswarm/Broken Axis/Chord |
| ♿ **WCAG 2.1 AA** | ARIA labels + decal patterns on every chart |
| 🔗 **Linked Highlighting** | Hover an entity → same entity highlights in ALL charts |
| ⬇ **PNG Export** | Hover-reveal download button per chart (2x PNG) |
| ⚡ **Lazy Rendering** | Charts render on scroll, with 4-second safety net |
| 🎨 **Dark Mode** | Manual toggle + full CSS dark theme |
| 📱 **Responsive** | Mobile/tablet/desktop breakpoints |
| 🔄 **Report Diff** | Auto-detects previous runs, annotates ▲/▼ |
| 🗺️ **Mermaid** | Architecture diagrams as native Mermaid blocks in MD |
| 🏷️ **Data Provenance** | [P]rimary/[F]allback/[I]nferred/[M]issing tier labels |

### 📊 Chart Types

| Data Characteristic | Chart Type |
|:--------------------|:-----------|
| Multi-entity ranking | Horizontal Bar |
| A/B binary comparison | Butterfly (bidirectional bar) |
| Composition breakdown | Donut |
| Multi-dimensional (3-8) | Radar |
| Two-variable correlation | Scatter + quadrants |
| Flow / path divergence | Sankey |
| Time-series trend | Line (progressive rendering) |
| Dense matrix (many × many) | Heatmap |
| Distribution (samples) | Boxplot |
| Hierarchical proportion | Treemap |
| Stage attrition | Funnel |
| 7+ dimensions | Parallel |
| Multi-metric comparison | Grouped Horizontal Bar |
| Near-identical values | Table Inline Progress Bar |
| Multiple data points | Beeswarm (ECharts 6.0) |
| Large value gaps | Broken Axis (ECharts 6.0) |
| Cross-entity relationships | Chord (ECharts 6.0) |

> ECharts 6.0 charts auto-degrade to v5 — zero risk on older CDNs.

### 🆕 What's New in v3.0

| v2.1 | v3.0 |
|:-----|:-----|
| 14 chart types | 14+ (ECharts 6.0 extended) |
| No accessibility | WCAG 2.1 AA: ARIA + decal |
| No export | PNG export button per chart |
| Isolated charts | Cross-chart linked highlighting |
| All-at-once loading | Lazy rendering + safety net |
| No version tracking | Consecutive-run ▲/▼ diff |
| ASCII diagrams | Mermaid in MD reports |
| Basic CDN | Version-gated ECharts 6.0 |

### 🧩 File Structure

```
generating-comparison-reports/
├── SKILL.md                      # Skill definition & workflow
├── manifest.yaml                 # Cross-platform metadata
├── LICENSE
├── agents/
│   └── openai.yaml               # OpenAI platform config
└── references/
    ├── delivery-standard.md      # CSS, colors, a11y, responsive, dark mode
    └── chart-selector.md         # 14+ chart templates + auto-recommendation engine
```

### 🤝 Contributing

PRs welcome. Chart templates in `references/chart-selector.md`.

### 📄 License

MIT © [Xuuudong](https://github.com/Xuuudong)

<p align="right"><a href="#chinese">中文 ↑</a></p>

</details>