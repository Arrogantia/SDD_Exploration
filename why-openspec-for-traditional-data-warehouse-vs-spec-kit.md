# 为什么在 SDD 试点中优先选择 OpenSpec

## 1. 执行摘要

对于大型传统数仓的试点，建议优先选择 **OpenSpec**，并将其定位为 **需求管理与数仓代码之间的轻量规格层**，而不是新的项目管理系统或数据治理平台。

选择 OpenSpec 的核心原因不是它比 Spec Kit 更完整，而是它的变更模型更贴近当前目标：

- 以单个变更为单位，通过 `proposal`、`specs`、`design`、`tasks` 与 `archive` 管理从需求到落地的过程。
- 官方明确面向 existing codebase / brownfield 开发，适合仅对新增需求和中等复杂变更增量试点。
- 基于 Git 和 Markdown，能够与现有需求管理、SQL/ETL 仓库、代码审查与发布机制并存。
- 当前版本支持自定义 artifact schema 和验证命令，可以将数仓特有的 `data-contract`、`impact-analysis`、`acceptance`、`backfill-plan` 纳入规范流程。

**Spec Kit 并不是弱选项。** 它的官方工程体系、社区成熟度、治理能力、离线和企业部署支持均更强，尤其适合把 SDD 建设成团队或组织级标准。但对于仅选择 2-3 个业务需求、希望快速验证价值的数仓团队，它需要建立 constitution、workflow、模板和更完整的执行习惯，首轮试点成本通常高于 OpenSpec。

推荐路径：

1. 第一阶段采用 `OpenSpec + 数仓自定义 artifact + Git PR` 做 4-6 周 POC。
2. 在 POC 中验证需求澄清、口径争议、返工率、上线问题和流程负担。
3. 如果后续目标上升到跨主题域治理、强制规范、合规审计或多团队统一实施，再将 Spec Kit 作为组织级治理候选进一步评估。

## 2. 传统数仓为什么需要 SDD

传统数仓中的敏捷开发难点，不主要在于 SQL 是否能快速编写，而在于需求到数据交付之间存在大量隐含决策：

| 数仓问题 | 传统方式的常见风险 | SDD 应前置固化的内容 |
|---|---|---|
| 指标口径 | 需求文字可被多种方式实现 | 公式、粒度、周期、过滤和去重规则 |
| 字段含义 | 开发依赖口头解释或历史 SQL 猜测 | 来源字段、转换规则、枚举、空值处理 |
| 模型变更 | 表结构变化影响下游但发现过晚 | schema delta、兼容性、消费者影响 |
| 调度与时效 | 开发完成后才讨论作业安排 | 依赖、批次、SLA、重跑和告警 |
| 历史回填 | 正式上线才发现历史口径不可比 | backfill 范围、校验、灰度和回滚 |
| 测试验收 | 业务验收只看几个报表结果 | 数据质量断言、对账案例和接受条件 |
| 项目管理 | 工单有状态，但缺少可执行规格 | 工作项与版本化 spec/代码/验收的映射 |

因此，适合数仓的 SDD 不应等同于“让 AI 写 SQL”，也不应只是增加一套说明文档。它需要实现：

- 业务决策可审阅。
- 数据变化可版本化。
- 开发任务可追踪。
- 测试与上线条件可验证。
- 变更结束后，规范能成为下一次需求的上下文。

## 3. 工具选择原则

本次选择基于局部 POC，而不是全组织推广，评估重点如下：

| 评价维度 | 对当前 POC 的重要性 | 判断标准 |
|---|---:|---|
| 存量项目增量接入 | 高 | 不要求补齐全仓历史规格，不重构已有 SQL/ETL |
| 既有管理流程与 Git 共存 | 高 | 管理系统负责排期与责任，spec 和代码进入版本控制 |
| 数仓 artifact 可扩展性 | 高 | 能加入字段口径、数据契约、影响分析、回填和验收 |
| 试点流程负担 | 高 | 2-3 个真实需求可以在数周内使用并衡量收益 |
| 团队工具迁移成本 | 高 | 不强制更换 IDE、调度平台或发布体系 |
| 组织级治理能力 | 中 | POC 成功后能否进一步标准化 |
| 社区与企业使用成熟度 | 中 | 文档、部署、安全、扩展机制是否清楚 |

## 4. OpenSpec 当前能力及其对数仓的意义

### 4.1 工具定位

OpenSpec 是开源的 spec-driven framework。根据其官方 GitHub README 与 OPSX 文档，截至研究日期，其现行标准工作流是把一个变更表达为 artifact 流程，并支持在完成后将 delta specs 归档合并到长期规格中。

官方默认 artifact 结构可概括为：

```text
proposal -> specs -> design -> tasks -> implement -> archive
```

此外，OpenSpec 通过 `openspec/config.yaml` 支持自定义 schema、artifact 类型和依赖关系，官方文档示例包括 review、security-review、verification 等增量流程。这一点对数仓非常重要，因为传统数仓不能只依赖通用软件规格文件。

### 4.2 OpenSpec 与数仓变更天然匹配的部分

传统数仓开发通常由一个“业务变化”触发：新增指标、修改口径、增加字段、调整维度映射、扩大数据覆盖范围或改变时效要求。OpenSpec 的 change-oriented 方式能够自然地对应这种变化。

| OpenSpec 默认 artifact | 在传统数仓中的用途 |
|---|---|
| `proposal.md` | 业务需求摘要、价值、范围、影响域、成功标准 |
| `specs/` | 新增或变更的指标、字段、数据集行为与约束 |
| `design.md` | 分层落位、source-to-target、增量逻辑、调度与回填设计 |
| `tasks.md` | SQL/ETL、调度、质量检查、发布、验收任务 |
| `archive` | 将已上线且验证通过的变化沉淀为可复用基线规格 |

这种映射有两个现实收益：

- 对开发团队，需求不再停留在工单描述或会议纪要中，而是进入与 SQL/ETL 同版本的 Git 资产。
- 对业务和治理团队，口径与影响分析发生在开发前，而不是上线验收或事故排查阶段。

### 4.3 OpenSpec 可以扩展为数仓专属 schema

OpenSpec 的默认 artifact 不足以覆盖大型传统数仓全部风险。因此，建议 POC 不采用裸模板，而是在默认工作流基础上追加数据领域 artifact：

```text
proposal
  -> requirements
  -> specs
  -> data-contract
  -> impact-analysis
  -> design
  -> tasks
  -> acceptance
  -> implement
  -> archive
```

推荐 artifact 与目的如下：

| 自定义 artifact | 核心内容 | 评审责任人 |
|---|---|---|
| `requirements.md` | 术语、指标口径、粒度、边界和样例 | 业务 owner / 数据产品 |
| `data-contract.yaml` | schema、owner、质量、SLA、安全等级、消费者约束 | 数据架构 / 治理 |
| `impact-analysis.md` | 下游表、报表、接口、作业、权限和兼容风险 | 开发 / 架构 |
| `acceptance.md` | 对账样例、质量断言、回填验证和验收签署 | 业务 / 测试 |
| `backfill-plan.md` | 历史回填、重跑、切换、回滚和监控 | 开发 / 运维 |

这是一项**实施建议**，并非 OpenSpec 已经内置数据契约或数仓治理。OpenSpec 提供的是可配置的规格骨架；数据契约字段、质量校验、元数据联动和审批责任仍需由团队设计并接入现有系统。

### 4.4 OpenSpec 对既有敏捷管理流程的适配方式

OpenSpec 不应替换现有项目管理工具。最合理的职责边界是：

| 系统 | 保留职责 | 不建议承担的职责 |
|---|---|---|
| 项目管理系统 | Epic/Story/Sub-task、排期、优先级、负责人、Sprint、状态 | 详细口径全文、完整数据契约、代码级变更规格 |
| OpenSpec / Git | 规格、设计、任务细节、数据契约、影响分析、验收证据 | Sprint 排程、资源分配、组织级看板 |
| SQL/ETL/调度体系 | 实现、执行与运行监控 | 业务定义主来源 |
| CI/CD 或质量平台 | 自动校验、发布门禁 | 需求优先级决策 |

建议以现有工作项编号对齐 change 名称；若当前使用 Jira，可沿用其 Issue Key：

```text
openspec/changes/DW-1234-customer-retention-rate/
  proposal.md
  requirements.md
  specs/
  data-contract.yaml
  impact-analysis.md
  design.md
  tasks.md
  acceptance.md
  backfill-plan.md
```

管理工单只需保存该 change 的 Git 链接、评审状态和验收结果链接，从而避免工单与 Git 中重复维护完整规格。

### 4.5 OpenSpec 最适合 POC 的原因

#### 原因一：它围绕变更工作，适合不断到来的业务需求

数仓敏捷交付不是一次性建设全仓规格库，而是不断响应业务需求。OpenSpec 的变更目录、delta spec 和 archive 模式，可以让团队先从正在发生的需求开始，在上线后逐步积累可信规格。

#### 原因二：接入成本低，能够隔离试点风险

POC 只需在现有仓库中加入规格目录和模板，不要求：

- 替换现有项目管理系统。
- 替换 SQL 编辑器或调度工具。
- 迁移所有历史文档。
- 为所有历史表补契约。
- 统一全团队的 AI 或 IDE。

这使得失败成本较低，且能用真实业务需求量化试点价值。

#### 原因三：自定义 artifact 能覆盖数仓独有风险

相比纯功能开发，数仓必须处理数据契约、上下游影响、历史回填、SLA 和对账验收。OpenSpec 当前允许定义自有 artifact 流程，能够把这些内容纳入同一变更生命周期。

#### 原因四：规格与实现可以在 PR 中一起评审

OpenSpec 的产物处于 Git 仓库内，最适合形成以下审查方式：

- 业务/架构先审 spec 与 contract。
- 数仓开发再实现 SQL/ETL。
- PR 同时展示规格变化和代码变化。
- 发布前检查 acceptance 与 backfill 条件。
- 完成后归档为持续可引用的事实来源。

这比让规格停留在工单附件或会议纪要中更容易保持一致。

#### 原因五：它允许“够用的严谨度”，更符合第一轮试点

对低风险需求，可以只要求 `proposal + specs + tasks + acceptance`；对涉及共享表、口径变化或回填的需求，再增加 `data-contract + impact-analysis + design + backfill-plan`。这种按风险调整规格深度的方式，更适合敏捷数仓团队。

## 5. OpenSpec 的不足及使用边界

选择 OpenSpec 并不意味着它已经解决了大型数仓治理问题。以下边界必须在 POC 开始前讲清楚：

| 不足或风险 | 对数仓的影响 | 应对方式 |
|---|---|---|
| 不是项目管理系统 | 无 Sprint、排程、审批看板能力 | 保留现有管理工具作为入口 |
| 不是数据契约标准或目录平台 | 不自带可查询 lineage、质量和 SLA 看板 | 自定义 contract，并对接元数据/质量体系 |
| 数仓模板需自建 | 默认 artifact 不覆盖回填、调度、权限和对账 | 创建最小可行的数仓 schema |
| 验证能力需结合工程链路 | Markdown 完整不代表 SQL 与数据正确 | 加入 CI/测试 SQL/调度检查 |
| 企业规模能力仍需实测 | 多主题域、多 repo 和审计协同复杂 | 先做单主题域 POC，再评估扩展 |
| AI 辅助不能代替业务确认 | AI 可能生成错误指标定义 | 业务 owner 对口径和验收签署 |

OpenSpec 是**规格流程的骨架**，而非完整的数据治理解决方案。对于 Snowflake、Hive、Teradata、Oracle 或混合数仓，仍需要现有平台负责执行、元数据、权限和运行治理。

## 6. OpenSpec 与 Spec Kit 的能力对比

### 6.1 定位差异

| 维度 | OpenSpec | Spec Kit |
|---|---|---|
| 核心定位 | 面向变更的轻量 SDD framework | GitHub 提供的完整 Spec-Driven Development toolkit |
| 核心工作方式 | artifact graph、delta spec、archive | constitution、specify、clarify、plan、tasks、analyze、implement |
| 默认起点 | 某个具体变化或需求 | 项目原则及结构化功能交付流程 |
| 当前数仓 POC 契合度 | 较高，适合增量试点 | 较高但偏重，适合治理准备充分的试点 |
| 长期组织治理潜力 | 中高，依赖自建标准与集成 | 高，官方体系和扩展机制更完整 |

### 6.2 详细比较矩阵

| 比较项 | OpenSpec 优势 | OpenSpec 劣势 | Spec Kit 优势 | Spec Kit 劣势 |
|---|---|---|---|---|
| 小范围 POC | 可从一个 change 开始，流程较轻 | 需自行补数仓 artifact | 可通过 workflow/preset 做定制试点 | 首轮定义 constitution 与模板投入更高 |
| 存量数仓改造 | delta/archive 很适合需求驱动的渐进补充 | 全局治理规则需另行建立 | 支持 brownfield，适合建立长期原则 | 对只处理几个需求可能显得偏重 |
| 规格灵活度 | 当前支持自定义 artifact schema 与依赖 | 新 schema 需要团队自行验证 | workflow、preset、extension 机制成熟 | 定制范围广，也会提高引入决策成本 |
| 治理一致性 | 可在 artifact 中嵌入检查点 | 默认治理约束较少 | `constitution` 天然适合统一分层、质量、安全规则 | 若团队规则尚未成熟，易先写制度后验证价值 |
| 既有管理工具配合 | 以 change 路径与工作项编号绑定即可 | 无原生项目排程能力 | extension 机制可承载外部工具集成 | 具体系统集成需另行核验或自建 |
| 企业/内网落地 | Git/Markdown 基础简单 | 企业部署和规模化材料相对有限 | 官方安装文档覆盖 enterprise、air-gapped、代理和离线分发 | 安装与管理链条更长 |
| Agent/工具兼容 | 支持多种 AI coding assistants | 深度团队场景仍需验证 | 官方列出 30 个 agent integrations | 对 agent 工作流的配置与管理更多 |
| 数仓专属内容 | 可快速扩展 contract/impact/backfill | 无内置数仓领域模型 | 可通过模板、checklist、constitution 固化全面规范 | 仍然不是原生数仓或数据契约产品 |
| 学习与推广 | 开发人员容易围绕需求理解 | 需要有人维护模板纪律 | 更适合作为团队统一方法 | 非开发角色初期学习成本更高 |

### 6.3 OpenSpec 优于 Spec Kit 的场景

优先选 OpenSpec 的条件：

- 当前只准备试点 2-3 个业务需求。
- 核心目标是快速验证是否减少需求澄清和返工。
- SQL/ETL、调度、审批与发布流程都不希望被改变。
- 数据治理规范还在探索，暂时不希望先形成完整组织章程。
- 团队需要按需求风险灵活决定 artifact 深度。

在上述条件下，OpenSpec 的变更中心模型更自然：先把一个需求做扎实，沉淀出有效模板，再决定是否升级治理体系。

### 6.4 Spec Kit 优于 OpenSpec 的场景

优先选 Spec Kit 的条件：

- 多个团队需要统一使用同一 SDD 方法。
- 已经具备明确的数仓分层、命名、质量、安全和发布原则，需要写入统一 constitution。
- 需要企业内网/离线安装、扩展治理、标准 workflow 或 preset。
- POC 本身目的就是验证未来组织级推广，而不是只提升一个小组的敏捷交付。
- 组织愿意承担更系统的培训、模板设计和维护成本。

Spec Kit 的价值不在于更快，而在于更容易把规则和实施路径统一起来。

### 6.5 关于两者成熟度的审慎判断

根据官方资料：

- OpenSpec 已具备版本发布、可配置 schema、变更验证和归档等用于 POC 的关键能力。
- Spec Kit 的官方文档、安装策略、extension/preset/workflow 及企业离线部署说明更为完整，且拥有更广泛的 agent integration 列表。

因此本文的推荐应理解为：

> **OpenSpec 是当前传统数仓局部敏捷 POC 的更优起点；Spec Kit 是治理成熟后更值得评估的组织级候选。**

这不是对两者绝对能力高低的判断。尤其是 OpenSpec 官方 README 对不同工具的对比属于产品自身表述，企业选型仍应通过实际试点验证。

### 6.6 Spec Kit 探索中出现代码缺失信息的可能原因

在探索中观察到“使用 Spec Kit 生成的代码效果不好，且缺失了相关信息”，这一现象值得纳入选型判断，但仅凭最终代码还不足以断定是 Spec Kit 工具能力问题。Spec Kit 的核心机制是让上游 Markdown artifact 向下游实现持续传递上下文；如果需求、澄清、计划或任务中没有明确提供数仓关键信息，实施 agent 通常只能生成看似完整但业务上不充分的代码。

Spec Kit 官方 Quick Start 对生产特性或存在实质歧义的工作推荐使用完整质量路径：

```text
/speckit.constitution
  -> /speckit.specify
  -> /speckit.clarify
  -> /speckit.checklist
  -> /speckit.plan
  -> /speckit.tasks
  -> /speckit.analyze
  -> /speckit.implement
```

其中 `/speckit.clarify` 用于在计划前消除需求歧义，`/speckit.checklist` 用于验证规格质量，`/speckit.analyze` 用于在实施前检查 spec、plan 与 tasks 的一致性。官方同时建议复杂项目采用 phased implementation，避免实现任务过大导致 agent 上下文饱和。

结合传统数仓场景，可能原因如下：

| 可能原因 | 为什么会造成生成代码缺信息 | 如何验证 | 建议补救 |
|---|---|---|---|
| 规格停留在业务概述，没有数仓语义 | `specify` 关注“做什么、为什么”，若未明确指标公式、粒度、过滤、去重、SLA，代码无法恢复这些定义 | 检查 `spec.md` 是否包含口径、边界与可验收场景 | 在 `specify/clarify` 阶段补齐数仓业务规则和 Given/When/Then 场景 |
| 跳过或浅用 `clarify` / `checklist` | 模糊需求直接进入 plan，会把隐含假设传递到实现中 | 查看流程记录是否执行质量命令，以及是否仍存在未决问题 | 对指标口径、回填范围、数据质量、权限和消费影响做定向澄清与 checklist |
| `constitution` 缺少数仓工程原则 | 通用软件原则不足以指导分层、主键、增量、慢变维、调度和对账处理 | 检查 constitution 是否只是通用编码/测试规则 | 加入分层规则、命名、schema 兼容、回填、质量和发布门禁原则 |
| `plan` 没有注入存量链路上下文 | 存量数仓开发依赖已有表、历史 SQL、调度依赖和元数据；agent 无法凭规格自行发现可靠来源 | 对比生成代码是否引用了真实输入表、目标层和已有模式 | 在 plan 中明确 source-to-target、现有对象、样例 SQL、调度及运行平台限制 |
| `tasks` 只拆编码，没有拆验证与交付 | agent 完成 SQL 后不会主动补对账、质量检查、回填或发布证据 | 检查 tasks 是否仅包含“创建模型/写 SQL” | 将数据质量 SQL、样例对账、回填验证、发布检查拆为明确任务 |
| 未运行 `analyze` 即直接实施 | spec、plan、tasks 之间的信息断层没有在编码前被发现 | 检查 implement 前是否产出一致性分析结果 | 在实施前强制执行 analyze，并处理所有 coverage gap |
| 一次实现范围过大 | 多表、多指标、多回填任务同时生成，会压缩 agent 可用上下文，使细节被忽略 | 观察缺失信息是否集中在后半段任务或复杂依赖处 | 按指标/模型/回填/验收拆成阶段，逐段生成、检查和实现 |
| Agent 无法访问真实数据环境 | Spec Kit 组织了流程，但本身不自动提供 Snowflake 表结构、权限或样本数据 | 检查 agent 是否得到 schema、对象定义、现有 SQL 或可执行验证入口 | 在开发工具中提供受控 Snowflake 上下文；网页端可结合 Cortex Code 进行 SQL 实现与验证 |

对于本项目，最可能发生的不是 Spec Kit “漏写代码”，而是 **通用 SDD 流程未被数仓化配置，或者关键质量步骤尚未完整执行**。尤其需要检查以下信息是否在 `/speckit.implement` 前已经明确并可被 agent 读取：

- 指标公式、粒度、统计窗口、过滤与去重规则。
- 源表、目标表、字段映射、分层落位与现有 SQL 模式。
- 增量逻辑、历史回填范围、重跑和回滚要求。
- 数据质量断言、样例对账、SLA 与权限限制。
- 影响范围、消费者兼容性和发布验收条件。

建议将一次 Spec Kit 对照试验设计为：

1. 为传统数仓建立专用 `constitution`，写入分层、契约、回填和质量原则。
2. 选择与 OpenSpec POC 相同的指标需求，保证比较对象一致。
3. 在 `specify` 后必须执行面向口径、字段、回填、SLA 的 `clarify` 与 `checklist`。
4. 在 `plan` 中显式提供 Snowflake 或现有数仓对象上下文、source-to-target mapping 与验收查询要求。
5. 在 `tasks` 后执行 `analyze`，未闭合的信息不进入 implement。
6. 分阶段实施 SQL、验证与交付，而非一次生成完整链路。

若完成上述补强后，Spec Kit 输出质量显著改善，则问题主要来自数仓上下文和流程配置不足；若在同等规格完整度、同等 agent 与同等数据上下文条件下，仍明显低于 OpenSpec 方案，再将其计入工具适配性劣势会更客观。

## 7. 依据 OpenSpec 官方文档的数仓 Spec 示例工作流

### 7.1 官方 OPSX 流程如何映射到数仓

OpenSpec 官方当前将 OPSX 定义为标准工作流，强调它是可反复修订的 actions，而不是锁死的阶段。默认 `core` profile 提供快速路径：

```text
/opsx:propose -> /opsx:apply -> /opsx:sync -> /opsx:archive
```

启用 expanded commands 后，则可采用更适合数仓评审的逐步路径：

```text
/opsx:new -> /opsx:continue or /opsx:ff -> /opsx:apply -> /opsx:verify -> /opsx:archive
```

官方 Getting Started 以以下路径介绍 artifact 的形成过程：

```text
proposal -> specs -> design -> tasks -> implement
```

OPSX 文档进一步说明，其依赖图中 `specs` 与 `design` 都可以在 `proposal` 后进入就绪状态，而 `tasks` 依赖二者完成。对数仓而言，这意味着业务口径细化与技术方案探索可以迭代推进，但在拆实现任务前应共同收敛。

其中 `openspec/specs/` 表示当前系统行为的事实来源，`openspec/changes/<change>/specs/` 是本次变更的 delta specs。归档时，`ADDED`、`MODIFIED`、`REMOVED` requirements 会分别加入、替换或移除主规格，已完成 change 则保留为审计历史。

对于数仓，最值得保留的官方机制是：

| OpenSpec 官方概念 | 数仓解释 |
|---|---|
| `proposal` | 需求目的、范围、消费方和成功标准 |
| delta `specs` | 新增或变化的指标/数据集要求，明确业务可验收行为 |
| `design` | 分层、源目标映射、SQL/ELT、增量与回填方案 |
| `tasks` | 实现、校验、发布和验收清单 |
| `apply` | 实现 SQL、模型、测试和部署脚本 |
| `verify` | 将实现结果与规格、测试和样例对账进行核对 |
| `sync/archive` | 将已验证的口径变化合入稳定规格并留存变更记录 |

### 7.2 示例需求：新增 30 日客户留存率指标

示例业务需求：

> 营销分析团队需要在月度经营看板中增加“注册客户 30 日留存率”。指标需要按注册月、渠道和区域统计；源数据位于 Snowflake；首期仅覆盖 2025 年以来注册客户，并要求能够回填历史月份。

该需求适合用 OpenSpec POC，因为它同时包含指标口径、分层设计、SQL 实现、历史回填、数据质量和消费方验收。

### 7.3 初始化和发起变更

按照官方 Quick Start，OpenSpec 要求 Node.js `20.19.0` 或更高版本；项目首次接入时执行：

```bash
npm install -g @fission-ai/openspec@latest
openspec init
```

若采用默认快速路径，发起变更的指令可以是：

```text
/opsx:propose add-30d-customer-retention-metric
```

如果团队希望先评审口径再生成完整设计，可以启用 expanded workflow 后使用：

```text
/opsx:new add-30d-customer-retention-metric
/opsx:continue
```

详细解释：

- 快速路径适合定义已经稳定的小型需求，一次生成规划 artifact。
- 逐步路径更适合数仓指标需求，因为 `proposal` 和 `specs` 的业务口径通常需要先获得确认，才能安全进入 `design` 与 `tasks`。
- 数仓 POC 推荐使用 expanded workflow，并在实现前至少完成一次规格评审。

### 7.4 `proposal.md` 示例：先确认意图和边界

以下示例遵循官方将 proposal 定义为 intent、scope、approach 的方式：

```markdown
# Proposal: Add 30-Day Customer Retention Metric

## Intent
为经营看板新增注册客户 30 日留存率，使营销团队能够比较不同
注册月份、渠道和区域的客户持续活跃表现。

## Scope
- 新增按 `registration_month`、`channel_id`、`region_id` 聚合的 30 日留存指标。
- 首次发布覆盖注册日期从 2025-01-01 起的客户。
- 产出每日刷新，并支持首次上线的历史回填。

## Out of Scope
- 不调整现有 7 日活跃率指标。
- 不追溯 2025-01-01 之前的注册客户。
- 不变更渠道和区域维表的主数据规则。

## Approach
基于 Snowflake 中的注册事实与客户活动事实表，建立可复用的留存
聚合模型，并为指标口径、质量验证和历史回填定义验收条件。
```

为什么这一产物重要：

- `Intent` 防止开发人员只看到“加一个字段”，而忽略其业务比较目的。
- `Scope` 与 `Out of Scope` 限制范围扩张，尤其避免把历史全量治理顺带塞入 POC。
- `Approach` 只约定方向，不在业务口径尚未确认前锁定 SQL 细节。

### 7.5 Delta spec 示例：把指标口径写成可验收要求

官方文档要求 delta spec 使用 `ADDED`、`MODIFIED`、`REMOVED` requirements，并推荐以 Given/When/Then 场景描述行为。对于新增指标，可生成：

文件路径示例：`openspec/changes/add-30d-customer-retention-metric/specs/retention-metrics/spec.md`

```markdown
# Delta for Retention Metrics

## ADDED Requirements

### Requirement: 30-Day Customer Retention Rate
The warehouse SHALL publish a 30-day customer retention rate grouped by
registration month, acquisition channel, and customer region.

#### Scenario: Customer qualifies as retained
- GIVEN a customer registered on `registration_date`
- WHEN the customer has at least one qualifying activity between day 1 and day 30 after registration
- THEN the customer SHALL be counted as a retained customer for the 30-day metric

#### Scenario: Metric calculation
- GIVEN a cohort with registered customer count greater than zero
- WHEN the daily aggregation completes
- THEN `retention_rate_30d` SHALL equal `retained_customer_count_30d / registered_customer_count`

#### Scenario: Initial history coverage
- GIVEN the first production release
- WHEN historical data is backfilled
- THEN all registration cohorts from `2025-01-01` through the latest complete cohort SHALL be published

#### Scenario: Freshness
- GIVEN the scheduled daily load succeeds
- WHEN business consumers query the metric after 08:00 local business time
- THEN the latest eligible cohort results SHALL be available
```

为什么这是 SDD 核心：

- 它把“留存”的判断窗口、分母、分子、聚合粒度和首次历史覆盖从聊天描述转为可测试规则。
- 它不限定必须使用哪一种 SQL 实现，因此允许实现优化，同时保持业务行为稳定。
- 若后续将活动窗口从 30 日改为 45 日，应通过新的 `MODIFIED Requirements` change 实施，而不是悄悄修改 SQL。

### 7.6 `design.md` 示例：从规格到 Snowflake 实现方案

```markdown
# Design: Add 30-Day Customer Retention Metric

## Data Inputs
- `DWD_CUSTOMER_REGISTRATION`: customer_id, registration_date, channel_id, region_id
- `DWD_CUSTOMER_ACTIVITY`: customer_id, activity_date, activity_type

## Target Output
- `ADS_CUSTOMER_RETENTION_MONTHLY`
- Grain: registration_month + channel_id + region_id
- Measures: registered_customer_count, retained_customer_count_30d, retention_rate_30d

## Transformation Strategy
- 以注册事实建立 cohort。
- 在注册日后的 day 1 至 day 30 窗口关联 qualifying activity。
- 使用 customer_id 级别去重后聚合到目标粒度。
- 对尚未完成 30 日观察窗口的 cohort 不发布最终留存率。

## Load and Backfill
- 日常任务每日刷新已成熟 cohort。
- 初次上线回填 2025-01-01 起的成熟 cohort。
- 回填结果需与抽样客户明细和按渠道汇总结果双重对账。

## Data Quality
- 聚合粒度必须唯一。
- `retained_customer_count_30d <= registered_customer_count`。
- `retention_rate_30d` 必须在 0 与 1 之间。
- 目标表产出时间满足已批准的数据可用时间。
```

解释：

- `design` 负责技术实现，而非重新发明指标口径；口径冲突应回到 delta spec 修改。
- 对传统数仓，`Data Inputs`、`Target Output`、`Load and Backfill`、`Data Quality` 是设计评审最低限度内容。
- 若生产仓库还涉及调度依赖、权限或敏感分类，应在同一设计中增加明确检查项。

### 7.7 `tasks.md` 示例：让敏捷开发可以跟踪完成

```markdown
# Tasks: Add 30-Day Customer Retention Metric

## 1. Specification Review
- [ ] 1.1 Confirm cohort grain and qualifying activity types with the business owner
- [ ] 1.2 Approve initial backfill range and data availability expectation

## 2. Snowflake Development
- [ ] 2.1 Implement cohort and retained-customer transformation SQL
- [ ] 2.2 Create or update `ADS_CUSTOMER_RETENTION_MONTHLY`
- [ ] 2.3 Configure daily load and initial backfill execution

## 3. Verification
- [ ] 3.1 Validate sample customer classification for retained and non-retained cases
- [ ] 3.2 Execute uniqueness, range and count-consistency checks
- [ ] 3.3 Reconcile channel-level aggregation with expected sample output

## 4. Delivery
- [ ] 4.1 Review specification changes and implementation in the same pull request
- [ ] 4.2 Publish verified output and archive the OpenSpec change
```

解释：

- tasks 中同时存在确认、开发和验证任务，防止“SQL 提交完成”被误认为“需求交付完成”。
- 实施中如果发现活动类型或观察窗口有误，按照 OPSX 的 fluid workflow，应更新 `specs` 或 `design`，再继续执行，而不是让实现偏离规格。

### 7.8 Apply、Verify、Sync 与 Archive 的使用方式

开发阶段可使用：

```text
/opsx:apply add-30d-customer-retention-metric
```

对于 expanded workflow，开发完成后执行：

```text
/opsx:verify add-30d-customer-retention-metric
/opsx:archive add-30d-customer-retention-metric
```

在默认 core profile 下，可以在完成实现后先同步稳定规格，再归档：

```text
/opsx:sync add-30d-customer-retention-metric
/opsx:archive add-30d-customer-retention-metric
```

数仓语境中的解释：

- `apply` 应生成或修改 Snowflake SQL、模型与校验脚本，但不能自动批准指标定义。
- `verify` 应核对实现是否满足 delta spec 中的场景，并运行数据质量与抽样对账。
- `sync` 将已确认的变化同步进入稳定规格，使后续需求可以引用同一指标事实。
- `archive` 形成变更历史，保存为什么改、如何改、如何验收的上下文。

## 8. 在 Snowflake 平台实现 SDD 开发



## 9. 参考资料

以下资料于 2026-05-26 核验：

- OpenSpec 官方 GitHub 仓库与 README：https://github.com/Fission-AI/OpenSpec
- OpenSpec Getting Started：https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md
- OpenSpec OPSX Workflow：https://github.com/Fission-AI/OpenSpec/blob/main/docs/opsx.md
- Spec Kit 官方文档：https://github.github.com/spec-kit/
- Spec Kit 官方 GitHub 仓库：https://github.com/github/spec-kit
- Spec Kit Installation Guide：https://github.github.com/spec-kit/installation.html
- Spec Kit Quick Start：https://github.github.com/spec-kit/quickstart.html
- Spec Kit Extensions：https://github.github.com/spec-kit/reference/extensions.html
- Spec Kit Presets：https://github.github.com/spec-kit/reference/presets.html
- Snowflake Cortex Code in Snowsight：https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code-snowsight
