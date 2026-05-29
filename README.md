# 📊 Generating Comparison Reports

> 生成专业多维度对比分析报告 | Professional Multi-Dimensional Comparative Analysis Report Generator

[![Python](https://img.shields.io/badge/version-3.0-blue.svg)]()
[![WCAG](https://img.shields.io/badge/WCAG-2.1%20AA-green.svg)]()
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

---

[English](#english) | [中文](#chinese)

---

## English

### ✨ What It Does

A skill for OpenCode/Claude/AI agents that generates **self-contained HTML + Markdown dual-format comparison reports** with ECharts visualizations. Point it at multiple data sources and get a professional report with 14+ chart types, KPI cards, TOC navigation, dark mode, and responsive layout — all opening directly in any browser.

**v3.0 adds**: WCAG 2.1 AA accessibility (ARIA + decal patterns), chart PNG export buttons, linked cross-chart highlighting, lazy rendering, and ECharts 6.0 version-gated chart types.

### 🚀 Quick Start

Activate the skill with natural language:

```
"Compare the two benchmark runs in D:\run-v1 and D:\run-v2, generate a comprehensive report"
```

```
"横向评测这5个模型的效果，用图表展示差异"
```

The skill runs through 6 automated steps:

```
Data Collection → Deep Exploration → Analysis & Cross-validation
  → HTML Report → Markdown Report → Validation & Delivery
```

**Output**:

```
output/
├── 对比分析报告.html       ← self-contained, browser-ready
├── 对比分析报告.md         ← markdown companion
└── tmp/                    ← intermediate artifacts (traceable)
    ├── collected_data_{timestamp}.json
    ├── analysis_{timestamp}.json
    └── validation_{timestamp}.json
```

### 🎯 Key Features

| Feature | Description |
|:--------|:------------|
| 🔍 **Auto Data Collection** | Reads SKILL.md, reports, logs from multiple directories |
| 🏷️ **Smart Chart Selection** | Auto-recommends chart types based on data patterns (14 types) |
| 📊 **14+ Chart Types** | Bar, Butterfly, Donut, Scatter, Radar, Sankey, Line, Heatmap, Boxplot, Treemap, Funnel, Parallel, Grouped Bar, Table Progress, plus ECharts 6.0 Beeswarm/Broken Axis/Chord |
| ♿ **WCAG 2.1 AA** | ARIA labels + decal patterns on every chart — screen-reader and colorblind friendly |
| 🔗 **Linked Highlighting** | Hover an entity in one chart → same entity highlights in ALL charts simultaneously |
| ⬇ **PNG Export** | Hover-reveal download button on every chart (2x resolution PNG) |
| ⚡ **Lazy Rendering** | Charts render only when scrolled into view, with 4-second safety net fallback |
| 🎨 **Dark Mode** | Manual toggle + CSS dark theme for all components |
| 📱 **Responsive** | Mobile/tablet/desktop breakpoints with adaptive chart heights |
| 🔄 **Report Diff** | Auto-detects previous runs in `tmp/` and annotates ▲/▼ changes |
| 📈 **Progressive Rendering** | Batched data-point rendering for large datasets (>500 points) |
| 🗺️ **Mermaid in MD** | Architecture diagrams as Mermaid blocks (renders natively on GitHub/GitLab) |
| ✅ **Cross-Validation** | Checks numbers across sources, flags contradictions, separates infra failures from quality issues |
| 🏷️ **Data Provenance** | [P]rimary/[F]allback/[I]nferred/[M]issing tier labels on every data point |

### 📊 Chart Types

| Data Characteristic | Chart Type |
|:--------------------|:-----------|
| Multi-entity ranking | Horizontal Bar |
| A/B binary comparison | Butterfly (bidirectional bar) |
| Composition breakdown | Donut (pie with radius) |
| Multi-dimensional (3-8) | Radar |
| Two-variable correlation | Scatter + markLine quadrants |
| Flow / path divergence | Sankey |
| Time-series trend | Line (with progressive rendering) |
| Dense matrix (many × many) | Heatmap |
| Distribution (samples per entity) | Boxplot |
| Hierarchical proportion | Treemap |
| Stage attrition / conversion | Funnel |
| 7+ correlated dimensions | Parallel |
| Multi-metric comparison | Grouped Horizontal Bar |
| Near-identical values | Table Inline Progress Bar |
| Multiple data points per entity | Beeswarm (ECharts 6.0) |
| Large value gaps | Broken Axis Bar (ECharts 6.0) |
| Cross-entity relationships | Chord (ECharts 6.0) |

> ECharts 6.0 charts include automatic v5 fallback — zero risk on older ECharts CDNs.

### 🆕 What's New in v3.0

| v2.1 | v3.0 |
|:-----|:-----|
| 14 chart types | 14+ chart types (ECharts 6.0 extended) |
| No accessibility | WCAG 2.1 AA: ARIA + decal patterns |
| No export | PNG export button per chart |
| Isolated charts | Cross-chart linked highlighting |
| All charts load at once | Lazy rendering with safety net |
| No version tracking | Consecutive-run diff with ▲/▼ |
| ASCII diagrams in MD | Mermaid blocks in MD reports |
| Basic CDN | version-gated ECharts 6.0 support |

### 🧩 File Structure

```
generating-comparison-reports/
├── SKILL.md                      # Skill definition & full workflow
├── manifest.yaml                 # Cross-platform metadata
├── LICENSE
├── agents/
│   └── openai.yaml               # OpenAI-platform skill config
└── references/
    ├── delivery-standard.md      # CSS, color system, accessibility, responsive, dark mode
    └── chart-selector.md         # 14+ chart ECharts templates + auto-recommendation engine
```

### 📖 Usage Examples

**English**:
```
"Compare the evaluation reports of 3 models in D:\models\*, 
generate a comprehensive comparison report with charts to D:\output"
```

**中文**:
```
"对比 D:\model-a 和 D:\model-b 的评测报告，
生成综合对比分析，保存到 D:\output"
```

### 🤝 Contributing

PRs welcome. Chart templates are in `references/chart-selector.md` — add new ones or refine existing ECharts configs.

### 📄 License

MIT © [Xuuudong](https://github.com/Xuuudong)

---

## 中文

### ✨ 它能做什么

一个面向 OpenCode/Claude/AI Agent 的 skill，**一键生成自包含 HTML + Markdown 双格式对比分析报告**。只需指定数据源目录，自动完成数据采集、交叉验证、图表推荐、报告生成全流程，产出可直接在浏览器打开的完整报告。

**v3.0 新增**: WCAG 2.1 AA 无障碍（ARIA 标签 + 贴花纹理）、图表 PNG 导出按钮、跨图表联动高亮、懒加载渲染、ECharts 6.0 版本门控图表。

### 🚀 快速开始

用自然语言激活 skill：

```
横向评测这5个模型的效果，用图表展示差异
```

```
Compare the two benchmark runs in D:\run-v1 and D:\run-v2
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
    ├── collected_data_{timestamp}.json
    ├── analysis_{timestamp}.json
    └── validation_{timestamp}.json
```

### 🎯 核心特性

| 特性 | 说明 |
|:--------|:------------|
| 🔍 **自动数据采集** | 并行读取多目录下的 SKILL.md、评测报告、日志 |
| 🏷️ **智能图表推荐** | 根据数据特征自动匹配最优图表类型（14+ 种） |
| 📊 **14+ 种图表** | 柱状图、蝴蝶图、环形图、散点图、雷达图、桑基图、折线图、热力图、箱线图、树图、漏斗图、平行坐标、分组柱状图、表格进度条，加上 ECharts 6.0 蜂群图/断轴图/弦图 |
| ♿ **WCAG 2.1 AA 无障碍** | 每张图表自动生成 ARIA 标签和贴花纹理，支持屏幕阅读器和色盲用户 |
| 🔗 **联动高亮** | 在任意图表中 hover 一个实体，报告中所有图表中的该实体同时高亮 |
| ⬇ **图表导出** | 每张图表 hover 显示下载按钮，一键导出 2x PNG |
| ⚡ **懒加载** | 图表滚动到视口才渲染，含 4 秒超时兜底，永不空白 |
| 🎨 **暗色模式** | 手动切换 + 完整 CSS 暗色主题 |
| 📱 **响应式** | 移动端/平板/桌面三级断点自适应 |
| 🔄 **版本对比** | 自动识别 `tmp/` 中上次运行结果，输出 ▲/▼ 变化标注 |
| 📈 **渐进渲染** | 大数据集（>500 点）分批渲染，避免卡顿 |

### 📊 图表一览

| 数据特征 | 图表类型 |
|:--------------------|:-----------|
| 多实体单维度排名 | 水平柱状图 |
| A/B 二级对比 | 蝴蝶图（双向柱状） |
| 构成占比 | 环形图 |
| 多维度(3-8) | 雷达图 |
| 双变量相关 | 散点图 + 象限线 |
| 流程/路径分流 | 桑基图 |
| 时间序列趋势 | 折线图（渐进渲染） |
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

> ECharts 6.0 图表自动含 v5 降级方案 — CDN 未升级也不会报错。

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