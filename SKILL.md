---
name: pre-estimation
description: "前置评估工作量技能——产品设计前的改造工作量评估。根据需求文字描述和实际项目代码分析，生成含设计流程图和实际工作量的HTML报告。当用户说'调用前置评估工作量技能'、'前置评估'、'设计前评估'、'工作量预评估'，或在产品设计阶段需要评估开发工作量时使用。涉及后端需后端项目路径，涉及前端需前端项目路径。"
argument-hint: "[需求描述] [前端项目路径] [后端项目路径]"
---

# 前置评估工作量技能

产品设计前的改造工作量评估。基于需求文字描述和实际项目代码分析，输出包含设计流程图和实际工作量的HTML报告。

## 使用场景

- 产品经理/业务方提出需求后，设计前评估开发工作量
- 技术方案评审前的快速人力评估
- 多端（前端+后端）项目的整体工作量预估
- 需要向上汇报用的可视化工作量报告

## 前置条件

以下信息会逐步收集，**不要求一次性提供**：

| 条件 | 必需 | 说明 |
|------|------|------|
| 需求文字描述 | **是** | 描述业务意图和功能期望 |
| 前端项目路径 | 按需 | 涉及前端改造时必须提供，本地绝对路径 |
| 后端项目路径 | 按需 | 涉及后端改造时必须提供，本地绝对路径 |
| 目标总人天 | 否 | 可作为参考，不强制凑数 |
| 设计流程图描述 | 否 | 用户可描述期望的流程；未提供则基于代码分析自动生成 |

**缺失项目路径的处理**：
- 若需求描述涉及后端但用户未提供后端路径 → 暂停并询问："这个需求涉及后端改造，请提供后端项目的本地绝对路径"
- 若需求描述涉及前端但用户未提供前端路径 → 暂停并询问："这个需求涉及前端改造，请提供前端项目的本地绝对路径"
- 仅当用户明确表示某一端无需评估时，可跳过对应端的分析

## 工作流程

### 步骤1：需求澄清与项目确认

1. 读取用户提供的需求文字描述
2. 判断需求涉及哪些端（前端/后端/双端）
3. 逐端确认项目路径：
   - 若需求提到页面、交互、UI → 需要前端项目路径
   - 若需求提到接口、数据、存储、逻辑 → 需要后端项目路径
4. 若缺少必要路径，**必须暂停并向用户询问**，不可跳过

### 步骤2：全局代码扫描

对每个项目路径执行：

1. **搜索关键业务词**：从需求描述中提取3-5个核心关键词，用Grep搜索定位相关代码
2. **识别相关模块**：列出涉及的文件目录和模块清单
3. **分析现有实现**：读取核心文件，理解当前代码结构和复用可能
4. **判断改造类型**：
   - 全新模块开发 vs 已有模块扩展
   - 新增路由/页面 vs 已有页面增加条件分支
   - 新增接口 vs 已有接口扩展

**扫描输出**：涉及模块清单、改造类型判断、复用度评估

### 步骤3：改造点分析与工时估算

**核心原则**（贯穿分析全程）：

| 原则 | 解决什么问题 | 实践要点 |
|------|-------------|---------|
| 结合项目实际 | 凭空估算、脱离代码 | 每个改造点必须基于实际代码文件分析；拿不准的实现细节务必读取源码确认，不可猜测 |
| 编码前思考 | 错误假设、隐藏困惑、缺少权衡 | 先理解现有实现的设计意图和约束条件，再判断改造方式（扩展 vs 新建）；识别模块间的隐式依赖 |
| 简洁优先 | 过度复杂、臃肿抽象 | 优先复用已有模块和组件；不过度设计抽象层；一个改造点只做一件事 |
| 精准修改 | 无关编辑、触碰不应碰的代码 | 明确每个改造点的边界和影响范围；不顺手重构、不扩展需求范围外的功能 |
| 目标驱动执行 | 方向偏离、无法验证 | 每个改造点描述可验证的产出标准；工时估算对应明确的可交付成果 |

**已有页面扩展的深度评估**（强制）：

当改造类型为"已有页面/模块扩展"时，必须执行以下步骤，不可仅凭表面复杂度低估人天：

1. **读取已有页面的完整代码和依赖链**：理解现有数据流、组件嵌套关系、条件分支逻辑
2. **识别新旧需求的交集与冲突点**：
   - 新功能是否受现有条件分支限制（如场景值白名单、平台判断）？
   - 现有表单校验/提交逻辑是否需要重构才能容纳新字段？
   - 现有组件的 props/slot 设计是否支持扩展，还是需要改造组件接口？
3. **以最坏结果评估**：若初始评估人天较少（< 2人天），须假设"已有代码结构不兼容、需重构现有逻辑"的最坏情况重新评估

工时估算方法论：

- 区分新增与复用（已有文件增加条件分支上限1人天，全新模块2-3人天）
- 识别膨胀变更（文案修改、格式化、删除冗余不计入工时）
- 每个改造点上限3人天，超过则拆分
- 工时仅包含开发实现，不含测试、部署、需求理解

**粒度说明**：本技能面向设计前评估，改造点描述以功能模块为单位，属于粗粒度估算。

### 步骤4：用户确认

完成分析后，展示评估摘要供用户确认：

1. 展示涉及的模块清单和改造点数
2. 展示预估总人天（前端+后端分列）
3. 询问：
   - "以上分析是否覆盖了需求范围？是否需要调整？"
   - "是否确认生成HTML报告？请提供HTML文件的输出路径（如 D:\report\estimate.html）"

**必须等待用户确认后才能生成HTML。** 用户可能要求调整改造点、修改人天估算、或补充遗漏项。

### 步骤5：生成HTML报告

用户确认后，生成HTML文件。HTML必须包含以下四个核心区域：

#### 5.1 报告头部
- 需求名称
- 评估日期
- 涉及项目（前端/后端路径）
- 总人天汇总（开发人天合计）

#### 5.2 设计流程图
- 使用 Mermaid.js 渲染
- 展示从用户操作到系统响应的完整流程
- 标注新增模块和改造模块（用不同颜色区分）
- 如果用户提供了流程图描述，优先使用；否则基于代码分析自动推导

#### 5.3 工作量明细
- 按端（前端/后端）分组展示
- 每项包含：功能模块、改造内容、改造类型、开发人天

#### 5.4 汇总与风险提示
- 前端/后端/总计人天
- 关键风险点和建议

## HTML模板

使用以下模板结构生成HTML。模板中的占位符 `{{PLACEHOLDER}}` 在实际生成时替换为真实内容。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>前置评估工作量报告 - {{REPORT_TITLE}}</title>
<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<style>
  :root {
    --primary: #1a73e8;
    --success: #0d904f;
    --warning: #e37400;
    --danger: #d93025;
    --bg: #f8f9fa;
    --card-bg: #ffffff;
    --text: #202124;
    --text-secondary: #5f6368;
    --border: #e0e0e0;
    --radius: 8px;
    --shadow: 0 1px 3px rgba(0,0,0,0.08), 0 1px 2px rgba(0,0,0,0.06);
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif;
    background: var(--bg);
    color: var(--text);
    line-height: 1.6;
  }
  .container { max-width: 1100px; margin: 0 auto; padding: 24px; }

  /* Header */
  .report-header {
    background: linear-gradient(135deg, #1a73e8 0%, #1557b0 100%);
    color: #fff;
    padding: 32px 40px;
    border-radius: var(--radius);
    margin-bottom: 24px;
  }
  .report-header h1 { font-size: 24px; margin-bottom: 8px; }
  .report-header .meta { font-size: 14px; opacity: 0.85; }
  .report-header .meta span { margin-right: 24px; }

  /* Summary cards */
  .summary-cards {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 16px;
    margin-bottom: 24px;
  }
  .summary-card {
    background: var(--card-bg);
    border-radius: var(--radius);
    padding: 20px 24px;
    box-shadow: var(--shadow);
  }
  .summary-card .card-label { font-size: 13px; color: var(--text-secondary); margin-bottom: 4px; }
  .summary-card .card-value { font-size: 28px; font-weight: 700; }
  .summary-card .card-sub { font-size: 12px; color: var(--text-secondary); margin-top: 4px; }
  .card-value.frontend { color: var(--primary); }
  .card-value.backend { color: var(--success); }
  .card-value.total { color: var(--warning); }

  /* Section */
  .section {
    background: var(--card-bg);
    border-radius: var(--radius);
    padding: 24px 32px;
    margin-bottom: 20px;
    box-shadow: var(--shadow);
  }
  .section h2 {
    font-size: 18px;
    margin-bottom: 16px;
    padding-bottom: 12px;
    border-bottom: 2px solid var(--primary);
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .section h2 .icon { font-size: 20px; }

  /* Flowchart */
  .flowchart-container {
    background: #fafbfc;
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
    overflow-x: auto;
  }
  .flowchart-container .mermaid { text-align: center; }

  /* Table */
  .table-wrapper { overflow-x: auto; }
  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 14px;
  }
  thead th {
    background: #f1f3f4;
    color: var(--text);
    font-weight: 600;
    text-align: left;
    padding: 10px 14px;
    border-bottom: 2px solid var(--border);
    white-space: nowrap;
  }
  tbody td {
    padding: 10px 14px;
    border-bottom: 1px solid var(--border);
    vertical-align: top;
  }
  tbody tr:hover { background: #f8f9fa; }
  .badge {
    display: inline-block;
    padding: 2px 8px;
    border-radius: 12px;
    font-size: 12px;
    font-weight: 600;
  }
  .badge-new { background: #e8f5e9; color: #2e7d32; }
  .badge-mod { background: #fff3e0; color: #e65100; }
  .badge-reuse { background: #e3f2fd; color: #1565c0; }

  /* Risk */
  .risk-list { list-style: none; }
  .risk-list li {
    padding: 8px 12px;
    margin-bottom: 6px;
    border-left: 3px solid var(--warning);
    background: #fff8e1;
    border-radius: 0 4px 4px 0;
    font-size: 14px;
  }
  .risk-list li.high { border-left-color: var(--danger); background: #fce8e6; }

  /* Footer */
  .report-footer {
    text-align: center;
    color: var(--text-secondary);
    font-size: 12px;
    padding: 16px;
    margin-top: 8px;
  }

  .tag-row { display: flex; gap: 8px; flex-wrap: wrap; margin-top: 4px; }

  @media print {
    body { background: #fff; }
    .section { box-shadow: none; border: 1px solid #ddd; }
  }
</style>
</head>
<body>
<div class="container">

  <!-- Header -->
  <div class="report-header">
    <h1>{{REPORT_TITLE}}</h1>
    <div class="meta">
      <span>评估日期：{{REPORT_DATE}}</span>
      <span>前端项目：{{FRONTEND_PROJECT}}</span>
      <span>后端项目：{{BACKEND_PROJECT}}</span>
    </div>
  </div>

  <!-- Summary Cards -->
  <div class="summary-cards">
    <div class="summary-card">
      <div class="card-label">前端开发人天</div>
      <div class="card-value frontend">{{FE_DEV_DAYS}}</div>
      <div class="card-sub">改造点：{{FE_POINTS}}个</div>
    </div>
    <div class="summary-card">
      <div class="card-label">后端开发人天</div>
      <div class="card-value backend">{{BE_DEV_DAYS}}</div>
      <div class="card-sub">改造点：{{BE_POINTS}}个</div>
    </div>
    <div class="summary-card">
      <div class="card-label">开发总人天</div>
      <div class="card-value total">{{TOTAL_DAYS}}</div>
      <div class="card-sub">改造点：{{TOTAL_POINTS}}个</div>
    </div>
  </div>

  <!-- Flowchart -->
  <div class="section">
    <h2><span class="icon">📊</span> 设计流程图</h2>
    <div class="flowchart-container">
      <div class="mermaid">
{{MERMAID_CHART}}
      </div>
    </div>
  </div>

  <!-- Frontend Work Breakdown -->
  <div class="section">
    <h2><span class="icon">🎨</span> 前端工作量明细</h2>
    <div class="table-wrapper">
      <table>
        <thead>
          <tr>
            <th>功能模块</th>
            <th>改造内容</th>
            <th>改造类型</th>
            <th>开发人天</th>
          </tr>
        </thead>
        <tbody>
{{FE_TABLE_ROWS}}
        </tbody>
      </table>
    </div>
  </div>

  <!-- Backend Work Breakdown -->
  <div class="section">
    <h2><span class="icon">⚙️</span> 后端工作量明细</h2>
    <div class="table-wrapper">
      <table>
        <thead>
          <tr>
            <th>功能模块</th>
            <th>改造内容</th>
            <th>改造类型</th>
            <th>开发人天</th>
          </tr>
        </thead>
        <tbody>
{{BE_TABLE_ROWS}}
        </tbody>
      </table>
    </div>
  </div>

  <!-- Summary & Risk -->
  <div class="section">
    <h2><span class="icon">⚠️</span> 汇总与风险提示</h2>
    <table style="margin-bottom: 16px;">
      <thead>
        <tr><th>端</th><th>改造点数</th><th>开发人天</th></tr>
      </thead>
      <tbody>
        <tr>
          <td>前端</td><td>{{FE_POINTS}}</td><td>{{FE_DEV_DAYS}}</td>
        </tr>
        <tr>
          <td>后端</td><td>{{BE_POINTS}}</td><td>{{BE_DEV_DAYS}}</td>
        </tr>
        <tr style="font-weight:700;background:#f1f3f4;">
          <td>合计</td><td>{{TOTAL_POINTS}}</td><td>{{TOTAL_DAYS}}</td>
        </tr>
      </tbody>
    </table>
    <h3 style="font-size:15px;margin-bottom:10px;">风险点</h3>
    <ul class="risk-list">
{{RISK_ITEMS}}
    </ul>
  </div>

  <div class="report-footer">
    本报告由前置评估工作量技能自动生成 | 评估日期：{{REPORT_DATE}} | 工时仅含开发实现，不含测试和部署
  </div>

</div>

<script>
  mermaid.initialize({
    startOnLoad: true,
    theme: 'default',
    flowchart: { useMaxWidth: true, htmlLabels: true, curve: 'basis' },
    securityLevel: 'loose'
  });
</script>
</body>
</html>
```

### 模板变量说明

生成HTML时替换以下占位符：

| 占位符 | 说明 | 示例 |
|--------|------|------|
| `{{REPORT_TITLE}}` | 报告标题 | 内部版发起确认页签署设置信息展示-前置评估 |
| `{{REPORT_DATE}}` | 评估日期 | 2026-05-11 |
| `{{FRONTEND_PROJECT}}` | 前端项目名或"无" | anysign-cmcc-portal-frontend |
| `{{BACKEND_PROJECT}}` | 后端项目名或"无" | anysign-cmcc-portal-backend |
| `{{FE_DEV_DAYS}}` | 前端开发人天 | 5 |
| `{{BE_DEV_DAYS}}` | 后端开发人天 | 8 |
| `{{TOTAL_DAYS}}` | 开发总人天 | 13 |
| `{{TOTAL_POINTS}}` | 改造点总数 | 8 |
| `{{FE_POINTS}}` | 前端改造点数 | 3 |
| `{{BE_POINTS}}` | 后端改造点数 | 5 |
| `{{MERMAID_CHART}}` | Mermaid流程图定义 | graph TD; A[开始]-->B[结束] |
| `{{FE_TABLE_ROWS}}` | 前端表格行HTML | `<tr>...</tr>` |
| `{{BE_TABLE_ROWS}}` | 后端表格行HTML | `<tr>...</tr>` |
| `{{RISK_ITEMS}}` | 风险项列表HTML | `<li>...</li>` |

### 流程图生成规则

Mermaid流程图使用 `graph TD`（自上而下）布局：

1. 识别用户操作入口（如：点击按钮、进入页面）
2. 识别系统处理节点（如：接口调用、数据处理）
3. 识别分支判断（如：是否开启某功能、场景类型判断）
4. 用 `style` 标注新增节点（绿色）和改造节点（橙色）
5. 示例格式：

```
graph TD
    A[用户进入发起确认页] --> B{是否内部版}
    B -->|是| C[展示签署设置信息]
    B -->|否| D[跳过签署设置]
    C --> E[SignInfoDisplay组件渲染]
    E --> F[显示签署方式/保密级别/截止时间等]
    style C fill:#e8f5e9,stroke:#2e7d32
    style E fill:#e8f5e9,stroke:#2e7d32
```

## 输出规则

1. **先确认后生成**：必须展示评估摘要并等待用户确认，再生成HTML
2. **必须真实项目**：所有分析基于实际代码，不允许凭空估算
3. **双端覆盖**：涉及前后端的需求，两端都要分析
4. **流程图必含**：每个需求报告都必须有设计流程图
5. **HTML独立可打开**：不依赖本地服务器，直接在浏览器打开即可渲染
6. **风险点不少于2条**：每个报告至少提供2个风险提示

## 安全规则

- 报告中不暴露服务器地址、内网IP、数据库连接信息
- 不展示具体代码实现细节
- 不包含Git提交历史中的敏感信息
- 文件名和路径做脱敏处理（仅保留项目名+文件相对路径的模块名）
