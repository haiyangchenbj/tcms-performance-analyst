---
name: tcms-performance-analyst
description: |
  For tech-product marketing teams — monthly analysis of published output, content-calendar variance, channel performance, product-line coverage, and knowledge-base health, with next-month recommendations.
  Never fabricates metrics (marks gaps DATA_MISSING) or auto-modifies the calendar or triggers writing.
  Not for single-article pre-publish review — use tcms-compliance-reviewer for that.
read_when:
  - 月度报告
  - 效果分析
  - 内容复盘
  - 月度复盘
  - 数据复盘
  - 发文统计
  - monthly report
  - content review
  - performance analysis
version: 1.0.1
disable: false
---

# TCMS Performance Analyst

对项目级内容产出做月度分析，输出结构化报告和优化建议。

## When to use

- 需要统计上一个自然月的内容产出、产品线覆盖和类型分布。
- 需要将排期计划与实际情况做对比。
- 需要基于已有效果数据输出观察。
- 需要为下月提供选题和知识库调整建议。

## Do not use

- 单篇文章预审，由 `content-compliance-reviewer` 处理。
- 自动修改内容日历、发布内容或产品优先级。
- 在效果数据缺失时编造数字。

## Input

```yaml
calendar_path:
published_dir:
channel_data: path or null
product_line_map:
performance_profile:
  - product_priority:
  - content_type_weights:
  - channel_distribution_targets:
analysis_month: YYYY-MM
```

效果数据缺失时只分析可观测维度。

## Workflow

### Step 1: [Deterministic] Confirm scope

默认分析上一个自然月。确认日历、已发布目录和效果数据。

### Step 2: [Deterministic] Collect data

1. 扫描已发布内容目录，提取标题、日期、产品、类型、渠道。
2. 读取排期表，对比计划与实际产出。
3. 如有渠道效果数据，提取阅读、收藏、转发和完读指标。
4. 检查知识库最近更新和积压。

### Step 3: [LLM] Analyze

- 产出完成度：计划 vs 实际。
- 产品线覆盖：各产品的篇数和类型。
- 产品优先级匹配度。
- 内容类型分布。
- 效果数据边界（缺失时标注 `DATA_MISSING`）。
- 知识库健康度。

### Step 4: [LLM] Produce monthly report

```markdown
# Content Monthly Report: YYYY-MM

## Output overview

| Date | Title | Product | Type | Channel | Status |
|---|---|---|---|---|---|

## Plan vs actual

- Planned / Produced / Deferred / Missing.

## Product-line coverage

| Product | Count | Type mix | Priority alignment |
|---|---:|---|---|

## Performance data (if available)

| Article | Reads | Saves | Forwards | Read-completion |
|---|---|---|---|---|

Mark missing data as DATA_MISSING.

## Key findings

- Positive.
- Gaps.
- Action items.

## Recommendations for next month

- Topic suggestions.
- Product and format adjustments.
- Knowledge-base maintenance.

## Knowledge-base health

- Recently updated:
- Thin sections:
- Stale entries:
```

### Step 5: [Deterministic] Save

保存到：`reports/YYYY-MM-monthly-report.md`

## Hard Rules

1. Never fabricate performance data. Missing data is marked `DATA_MISSING`.
2. Never modify the content calendar or trigger downstream writing.
3. Product-line analysis uses the private product map, not inference from titles.
4. Dataset gaps must be reported with the observed scope and data boundary.
5. Human confirmation required before the plan team acts on recommendations.
6. Every finding must reference a date, file or data point.

## Failure Handling

| Scenario | Action |
|---|---|
| Calendar missing | Stop; report the missing file |
| Published directory empty | Output zero-production report |
| Channel data missing | Analyze only output dimensions |
| Knowledge-base not found | Skip the health section |
| Product map missing | Use title-based inference with a boundary note |

## Output Format

```text
reports/YYYY-MM-monthly-report.md
```

## Verification

- [ ] Output count matches actual directory scan.
- [ ] Plan-vs-actual uses the calendar as reference.
- [ ] Every performance figure has an observable source or is marked DATA_MISSING.
- [ ] Recommendations reference concrete gaps.
- [ ] Report does not trigger downstream tasks.
