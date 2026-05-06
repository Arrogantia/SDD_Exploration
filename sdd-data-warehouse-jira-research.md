# 大型数仓项目引入 SDD 的研究结论

## 前提说明

本文将 SDD 理解为 **Spec-Driven Development / Specification-Driven Development**，即：

- 规格先行
- 规格作为持续演进的“单一事实来源”
- 代码、测试、文档、验收都围绕规格展开

对于大型存量数仓项目，**最推荐的做法不是“用 SDD 替换 Jira”**，而是：

**保留 Jira 作为项目管理与协同入口，在 Jira 与开发实现之间新增一层 Git 化、可校验、可执行的 Spec/Contract 层。**

这比“继续只靠 Jira + 文档 + 人工口头同步”的传统模式更适合数仓项目的复杂依赖、口径治理、跨域协同和变更控制。

---

## 一句话结论

对于大型存量数仓，最推荐的不是找一个“万能 SDD 框架”整体替换现有体系，而是采用下面这套组合式方案：

**Jira / Jira Product Discovery + Git 中的 Spec 模板 + Data Contract 标准 + dbt（或等价转换层）+ 元数据/数据契约平台（优先 OpenMetadata）+ CI 校验门禁。**

如果只能给一个优先级最高、最稳妥的落地方案，我会推荐：

**`Jira + Git Specs + ODCS 数据契约 + dbt + OpenMetadata + CI/CD Gate`**

---

## 为什么这是大型数仓最合适的形态

### 1. Jira 适合管“工作”，不适合单独承担“规格真相”

Jira 很适合：

- 需求流转
- 优先级管理
- 责任分配
- 进度追踪

但大型数仓真正容易失控的地方通常不是工单数量，而是：

- 指标口径漂移
- 表结构变更缺少明确契约
- 上下游依赖影响不透明
- SLA 与质量要求只写在文档里，无法自动校验
- 历史 Jira ticket 很多，但无法形成长期可执行规范

所以 Jira 应继续保留，但不应继续承担规格主载体角色。

### 2. SDD 对数仓的价值，比对普通应用开发更直接

数仓项目天然适合规格驱动，因为其核心对象本来就可以结构化描述：

- 数据域
- 源表/目标表
- 字段定义
- 指标口径
- 分层规则
- 刷新频率
- 可用时间
- 质量阈值
- 权限和敏感级别

这些内容一旦被结构化，就能直接驱动：

- 开发任务拆解
- SQL / model 实现
- 测试
- 文档
- 验收
- 上线门禁

### 3. 存量项目不适合“全量重构式 SDD”

大型存量数仓的关键不是方法是否先进，而是迁移成本是否可控。

最稳妥的方式是：

- 先对新增需求采用 SDD
- 对高风险高价值主题域补契约
- 对核心指标与共享宽表优先治理
- 对历史遗留链路渐进补齐 spec，而不是一次性推翻

---

## 推荐的目标工作流

## 0. 治理基线层

在仓库建立一套固定的 SDD 基线文件：

- `constitution.md`
- `spec-template.md`
- `plan-template.md`
- `task-template.md`
- `contract-template.yaml`

其中：

- `constitution.md` 定义数仓统一原则
- Spec 模板定义业务需求、指标口径、边界条件、输入输出
- Plan 模板定义技术方案、分层落位、依赖、回填/切换策略
- Task 模板定义原子开发任务
- Contract 模板定义 schema/quality/SLA/security/owner

适合数仓项目写入 `constitution.md` 的固定规则包括：

- 分层规范（ODS/DWD/DWS/ADS 或 Data Vault / Data Product）
- 命名规范
- 主键与唯一性规则
- 分区策略
- 口径定义规则
- 回溯与重跑策略
- 变更兼容策略
- 数据质量最低门槛
- 敏感字段分级规则

## 1. 需求进入 Jira，但先进入 Discovery / Spec 阶段

建议把需求从“直接进 Epic/Story”改成两段式：

1. Discovery / Idea
2. Delivery / Epic-Story-Subtask

推荐做法：

- 用 Jira Product Discovery 承接问题、机会、价值、影响面
- 用自定义字段记录主题域、数据产品、消费者、指标影响、SLA 等
- 通过 Automation 从 Discovery 结果自动生成 Epic / Story / Subtask

这样 Jira 负责“为什么做、优先级、交付跟踪”，而真正的可执行规格进入 Git 仓库。

## 2. 为每个需求生成一套标准 Spec

每个需求至少产出四类文件：

1. `specification.md`
2. `plan.md`
3. `tasks.md`
4. `contract.yaml`

### `specification.md` 应包含

- 业务背景
- 业务问题与目标
- 范围与非范围
- 输入数据集
- 输出数据集/指标
- 字段级口径
- 边界条件
- 验收标准
- 非功能要求
  - 时效
  - 精度
  - 稳定性
  - 权限

### `plan.md` 应包含

- 落在哪一层
- 是否新增主题域对象
- 上下游依赖
- 影响评估
- 历史兼容与版本策略
- 回填/灰度/切换策略
- 测试策略
- 监控策略

### `tasks.md` 应包含

- 原子任务拆分
- 对应负责人
- 对应 Jira Story/Subtask
- 开发顺序
- 合并与发布条件

### `contract.yaml` 应包含

- schema
- 关键字段语义
- 质量断言
- SLA
- ownership
- security / classification
- version

## 3. 开发实现由“票驱动”改为“Spec 驱动”

开发不再从 Jira 描述直接写 SQL，而是：

1. 先评审 Spec
2. 再评审 Plan
3. 再拆 Tasks
4. 最后按任务实现 SQL / model / test / docs

对数仓尤其重要的原则：

- 任何 schema 变更先改 contract
- 任何指标口径变更先改 spec
- 任何新增共享输出先定义 owner 和 consumer
- 任何上线前必须经过自动校验

## 4. CI/CD 以“契约和测试”作为上线门禁

推荐在 CI 中至少做这几类检查：

- spec 完整性校验
- contract schema 校验
- SQL / model lint
- 单元测试
- 数据测试
- lineage / 依赖影响检查
- 关键模型 contract-breaking 检查
- 文档生成校验

如果门禁失败，Jira Ticket 不进入 Ready for Release。

## 5. 上线后让元数据平台承接“持续验证”

上线后，Spec 不应沉没在仓库里，而应通过元数据平台持续暴露：

- 数据契约状态
- 质量断言执行结果
- SLA 达成率
- owner / reviewer
- lineage
- 变更审计

---

## 最推荐的组件组合

## 组合 A：最推荐，适合大多数大型数仓

**Jira + Git Specs + ODCS + dbt + OpenMetadata**

### 角色分工

- Jira / Jira Product Discovery：需求管理、优先级、交付流程
- Git Specs：规格、计划、任务、评审记录
- ODCS：数据契约的通用表达标准
- dbt：转换开发、测试、文档、依赖、版本化
- OpenMetadata：契约、元数据、质量、SLA、治理、可视化

### 为什么最推荐

- 对存量项目侵入性相对低
- 与现代数仓开发方式高度匹配
- 规格、契约、实现、治理链条较完整
- 后续可逐步引入 AI 辅助生成 spec / task / test

### 适用场景

- 已有 SQL/ELT 数仓
- 正在或准备使用 dbt
- 需要跨团队共享口径和共享表
- 需要较强治理与透明度

## 组合 B：如果你们已经深度使用 dbt

**Jira + Git Specs + dbt Mesh + dbt Semantic Layer + OpenMetadata**

### 适合场景

- 多团队/多域并行开发
- 数据产品边界清晰
- 共享指标口径冲突明显
- 需要跨项目引用和版本治理

### 优点

- 强化域边界
- 支持跨项目引用
- 支持 access / contract / version 治理
- 语义层能把指标定义进一步标准化

### 风险

- 组织要求更高
- 如果当前仍是集中式团队、分层与 ownership 很混乱，直接上 Mesh 容易过早复杂化

## 组合 C：如果你们当前不是 dbt 体系

**Jira + Git Specs + ODCS + OpenMetadata + 现有调度/SQL 框架**

这适合先做“规格和契约治理”，不急着切换实现引擎。

也就是说：

- 先让需求、契约、SLA、质量变成结构化对象
- 再逐步把开发引擎迁移到 dbt 或保留现有体系

这通常是存量项目的低风险起步方式。

---

## 对“SDD 框架”的判断

## 结论一：没有一个对大型数仓最通吃的单体 SDD 框架

至少在当前实践里，更成熟的不是“一个包打天下的 SDD 产品”，而是：

**方法论 + 模板体系 + 契约标准 + 执行引擎 + 元数据治理平台**

所以更推荐“组合架构”，而不是追求单框架替代。

## 结论二：SDD 方法论可以借鉴，但要做数仓化裁剪

通用 SDD 常见阶段是：

- Constitution
- Specify
- Clarify
- Plan
- Tasks
- Implement

这个骨架是好用的，但数仓项目必须增加自己的专属字段：

- source-to-target mapping
- 指标口径
- 批次/增量/回溯策略
- schema compatibility
- freshness / SLA
- 数据质量断言
- 权限与敏感级别
- 下游消费影响面

所以建议采用：

**通用 SDD 方法论 + 数仓专用 Spec/Contract 模板**

而不是直接照搬软件功能开发模板。

---

## 最值得优先落地的 SDD 组件

如果要按优先级排序，我建议：

### P1. Spec 模板体系

这是最先要做的，因为没有统一模板，SDD 很容易沦为“多写几份文档”。

最低必备模板：

- 需求规格模板
- 技术方案模板
- 任务拆解模板
- 数据契约模板
- 验收清单模板

### P1. 数据契约

这是数仓场景里最核心的 SDD 组件。

原因是它把最容易失控的内容结构化了：

- schema
- 语义
- 质量
- SLA
- owner
- 使用限制

### P1. 自动校验门禁

没有自动门禁，规格很快会退化成形式主义。

至少需要：

- PR 校验
- 发布前校验
- 上线后巡检

### P2. 元数据 / 契约平台

如果没有统一的可见平台，规格只存在 Git，对业务方和消费者不够友好。

OpenMetadata 在这里是很强的候选。

### P2. 语义层

如果你们的核心痛点是指标口径不一致、BI 重复定义、分析团队多套指标并存，那么语义层优先级会显著上升。

### P3. AI 生成辅助

只有当前面的模板、契约、门禁都稳定后，再让 AI 参与自动生成：

- spec 初稿
- task 拆解
- dbt model 草稿
- test 草稿
- impact analysis 摘要

否则容易把混乱放大。

---

## 对大型存量数仓的实施建议

## 第一阶段：1 个主题域试点

先选择一个：

- 需求频繁
- 下游消费者多
- 指标口径争议大
- 质量事故较多

的主题域试点。

试点范围建议只覆盖：

- 1 套 spec 模板
- 1 套 contract 模板
- 1 条 Jira -> Spec -> Delivery 流程
- 1 套 CI 门禁

不要一开始做全仓推广。

## 第二阶段：治理共享对象

优先治理这些对象：

- 核心公共维表
- 核心事实表
- 高复用宽表
- 核心指标层
- 被多个团队依赖的主题域接口表

因为这些对象最适合契约化。

## 第三阶段：推广到增量需求

要求所有新增需求：

- 先有 spec，再开工
- 先定义 contract，再改 schema
- 先明确验收，再写任务

旧链路则按风险和收益逐步补齐，不做一次性补档工程。

---

## 不推荐的几种做法

### 1. 只把 Jira 描述写详细，就当成 SDD

这通常不够。因为 Jira ticket 不天然具备：

- 结构化 schema 表达
- 可执行校验
- 契约版本管理
- 与代码同版本演进

### 2. 一上来就要求全量历史数仓补 spec

这很容易失败，成本过高，且团队会把 SDD 视为额外负担。

### 3. 只有文档，没有门禁

没有自动校验时，spec 很快过期。

### 4. 直接上 Mesh / 语义层，但 ownership 还没理清

如果数据域 owner、消费者、边界、共享对象都不清楚，先上复杂框架会放大治理成本。

---

## 最终建议

如果你的目标是：**在原有大型数仓项目基础上，逐步引入 SDD，而不是推倒重来**，我最推荐的路线是：

### 推荐路线

1. 保留 Jira 作为需求与交付主入口
2. 新增 Git 化的 Spec 层
3. 用数据契约把 schema / 质量 / SLA / owner 固化
4. 用 dbt 或等价开发层承接实现、测试、文档与依赖
5. 用 OpenMetadata 做契约和元数据可视化、审批、持续验证
6. 用 CI/CD 把 spec 和 contract 变成真正的上线门禁

### 推荐技术组合

**优先推荐：`Jira + Git Specs + ODCS + dbt + OpenMetadata + CI`**

### 如果只能先做 3 件事

1. 统一 Spec 模板
2. 引入 Data Contract
3. 建立自动门禁

这三件事做成了，SDD 就不是“理念”，而会变成你们数仓项目真正可执行的开发方式。

---

## 参考资料

- Spec-Driven Development: https://specdriven.ai/
- Open Data Contract Standard (ODCS): https://bitol-io.github.io/open-data-contract-standard/latest/
- OpenMetadata Data Contracts: https://docs.open-metadata.org/latest/how-to-guides/data-contracts/spec
- dbt Mesh: https://www.getdbt.com/product/dbt-mesh
- dbt Semantic Layer: https://www.getdbt.com/product/semantic-layer
- dbt Test & Observe: https://www.getdbt.com/product/test-and-observe
- Jira Product Discovery: https://support.atlassian.com/jira-product-discovery/docs/what-is-jira-product-discovery/
- Jira Product Discovery workflow: https://support.atlassian.com/jira-product-discovery/docs/manage-your-discovery-space-workflows/
- Jira 自动生成层级任务示例: https://confluence.atlassian.com/automationkb/automation-rule-create-issues-for-all-the-subsequent-issue-type-hierarchy-when-an-issue-is-created-in-the-top-issuetype-hierarchy-1375863070.html
- Jira Product Discovery 关联交付项: https://support.atlassian.com/jira-product-discovery/docs/link-an-idea-to-a-jira-issue/
