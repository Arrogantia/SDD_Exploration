# 大型传统数仓引入 SDD 工具的可行性评估方案

## 1. 结论先行

推荐排序：

- 第一推荐：OpenSpec
- 第二推荐：LeanSpec
- 第三推荐：Spec Kit
- 第四推荐：Kiro

总判断：

如果目标是“大型传统数仓中针对少量业务需求做敏捷 POC”，最适合的是 **OpenSpec**。它轻量、Git 友好、可渐进接入，不要求全团队换 IDE，也比较适合把 Jira 需求转成 `proposal / design / tasks / spec delta`。LeanSpec 更轻、更灵活，但 Jira provider 官方仍标注为未来能力；Spec Kit 成熟度和社区热度最高，但流程较重；Kiro 的 spec 体验很好，但 IDE 绑定明显，不适合作为传统数仓团队的首选低风险试点方案。

## 2. 背景与评估假设

传统数仓引入 SDD 不能照搬普通应用研发模式。普通软件 SDD 多关注功能行为、接口、UI 和代码实现，而数仓更容易失控的地方在于“业务口径和数据契约”。

数仓 SDD 必须额外关注：

- 需求口径：同一业务词在不同报表、部门、模型中可能含义不同。
- 字段定义：字段来源、清洗规则、派生逻辑、枚举、空值策略必须明确。
- 指标一致性：指标公式、统计周期、过滤条件、去重口径要前置确认。
- 上下游依赖：一张 DWD/DWS/ADS 表可能影响多个调度、报表和接口。
- 数据质量：唯一性、完整性、波动阈值、账实一致性需要进入验收。
- SLA：产出时间、刷新频率、最大延迟、失败告警要可检查。
- 回填与重跑：历史数据修复、口径切换、分区重算是数仓特有风险。
- 上线治理：上线不只是合代码，还包括调度、权限、监控、回滚和通知。
- Jira 协同：Jira 继续管理 Epic/Story/Sub-task，SDD 负责把需求变成可审查、可追踪、可验收的规格资产。

所以这里的推荐不是“哪个 SDD 工具最强”，而是“哪个最适合在 Jira + 传统数仓开发方式下做低风险敏捷试点”。

## 3. 四个工具逐项分析

### OpenSpec

#### 工具定位

OpenSpec 是一个轻量级、开源的 spec-driven framework，面向 AI coding assistants。官方强调 Universal、Open Source、No API Keys、No MCP，并支持多种 AI 编程工具；它的核心产物是 repo 内的 `proposal.md`、`design.md`、`tasks.md` 和 `specs/` 变更规格。

#### 适配大型传统数仓的方式

它适合把每个 Jira Story 或数据需求转成一个 OpenSpec change folder。对数仓来说，可以把 `proposal.md` 用作业务目标和影响范围，`design.md` 用作分层设计、源到目标映射、调度和回填方案，`tasks.md` 用作 ETL/SQL/测试/上线任务拆解，`specs/` 用作指标口径、字段定义和表级行为变更。

#### 可用于 POC 的典型流程

Jira Story 创建后，在 Git 仓库中创建 OpenSpec change；业务、数据架构、开发一起评审 proposal 和 spec delta；确认后补充 design 和 tasks；开发按 tasks 实现 SQL/ETL；上线后 archive，把变更沉淀为长期规格。

#### 推荐落地方式

采用 `OpenSpec + 数仓自定义模板 + Jira Key 关联`。不要让 OpenSpec 替代 Jira，而是让它成为 Jira 与代码之间的规格层。

#### 优点

- 轻量、上手快、适合 brownfield。
- 官方明确支持既有代码库，并建议随着开发逐步补 specs，而不是一次性生成全量规格。
- “review intent, not just code”的理念很适合数仓，因为数仓很多事故来自口径和影响范围，而不是 SQL 本身。

#### 风险

- 团队协作、大型代码库、多 repo、深度定制和集成能力仍在建设中。
- 对传统数仓专属字段、指标、SLA、回填等内容，需要自行扩展模板。

#### 是否推荐

推荐，作为当前 POC 第一选择。

#### 推荐原因

它最符合“低侵入、少量需求试点、不改变技术栈、不强制迁移 IDE”的约束。数仓团队可以先用它规范需求澄清和任务拆解，再逐步补数据契约和 CI 校验。

### LeanSpec

#### 工具定位

LeanSpec 是轻量、tool-agnostic 的 spec framework。官方强调 specs 应保持短小、适合人和 AI 阅读，并提供 CLI、MCP server、VS Code extension、GitHub Actions、Web UI、Kanban、搜索、依赖关系和统计能力。

#### 适配大型传统数仓的方式

LeanSpec 适合把数仓需求拆成小规格，每个 spec 控制在较短上下文中，记录业务口径、模型变更、字段规则、依赖关系和验收项。它的 board、search、deps 能帮助数据团队管理多个并行需求的依赖。

#### 可用于 POC 的典型流程

Jira Story 进入开发后，在 LeanSpec 中创建对应 spec；spec 中记录需求背景、指标定义、模型影响、依赖和验收清单；开发过程中持续更新 spec 状态和决策；完成后通过 validate 和实际代码/提交校验确认闭环。

#### 推荐落地方式

采用 `LeanSpec markdown provider + Jira Key 手工关联`。LeanSpec 官方 README 说明 markdown provider 可用，但 GitHub、ADO 是 Planned，Jira 是 Future，所以当前不建议把它作为 Jira 原生集成工具使用。

#### 优点

- 非常敏捷，适合边做边沉淀。
- 强调 context economy 和短规格。
- 有 Kanban、搜索、依赖、Web UI，对数据团队做 POC 很友好。

#### 风险

- Jira provider 目前不是现成能力。
- 社区和生态规模明显小于 Spec Kit。
- 如果团队希望强治理、审批流、企业级标准化，LeanSpec 本身偏轻，需要额外制度和模板补足。

#### 是否推荐

推荐，但排在 OpenSpec 之后。

#### 推荐原因

它适合小团队或数据开发小组先做轻量试点，尤其适合“敏捷需求很多、每个需求不想写很重文档”的场景。但对 Jira 和企业级治理的现成支持不足。

### Kiro

#### 工具定位

Kiro 是 agentic IDE，内置 Specs、Steering、Hooks、MCP 等能力。Kiro Specs 生成 `requirements.md`、`design.md`、`tasks.md`，支持 Feature Specs、Bugfix Specs、Requirements-First、Design-First、任务执行和进度跟踪。

#### 适配大型传统数仓的方式

Kiro 的 Design-First 很适合已有技术约束的数仓需求，例如已有 ODS/DWD/DWS/ADS 架构、固定调度平台、固定数据库和严格 SLA。它也支持从 Jira、Confluence 或 Word 导入已有需求，方式包括 MCP 或手工导入文件。

#### 可用于 POC 的典型流程

把 Jira 需求或设计文档导入 Kiro spec session；用 requirements/design/tasks 三段式生成规格；由 Kiro 辅助拆任务和执行；最后把 spec 文件提交到 Git。

#### 推荐落地方式

只适合选一个小组做 IDE 内闭环试点，例如一个愿意使用 Kiro 的数据开发小组。不要把它作为全数仓团队默认方案。

#### 优点

- 产品体验完整，spec 工作流清晰。
- 需求、设计、任务三件套天然贴近敏捷开发。
- MCP 能接外部知识和工具，模型选择也比较丰富。

#### 风险

- 最大问题是 IDE 绑定。
- 传统数仓团队常常使用 SQL IDE、调度平台、数据库客户端、内部发布系统，强制迁移到 Kiro 成本高。
- MCP server 可能访问源代码、环境变量、凭据等，需要安全审查和隔离。

#### 是否推荐

不作为当前首选，只推荐做“IDE 内 AI 辅助开发体验”的局部试验。

#### 推荐原因

Kiro 的 spec 能力强，但不符合“尽量不改变现有工具链、不强制团队迁移 IDE”的核心约束。

### Spec Kit

#### 工具定位

Spec Kit 是 GitHub 开源的 SDD toolkit，目标是让组织围绕产品场景和可预测结果开发。它提供 `constitution -> specify -> clarify -> plan -> tasks -> analyze -> implement` 这类结构化流程，支持 CLI 初始化、模板、agent 集成和项目原则治理。

#### 适配大型传统数仓的方式

Spec Kit 很适合把数仓治理原则前置到 `constitution`，例如分层规范、命名规范、指标口径规则、数据质量门禁、SLA、权限要求和回填策略。它也明确覆盖 brownfield iterative enhancement，适合存量系统逐步增强。

#### 可用于 POC 的典型流程

先建立数仓项目 constitution；对一个 Jira 需求执行 specify；用 clarify 澄清口径；用 plan 固化技术方案；用 tasks 拆开发、测试、上线任务；用 analyze 做计划检查；最后由开发执行。

#### 推荐落地方式

适合作为“企业级 SDD 标准化候选方案”做第二阶段试点，而不是第一周就直接落到所有需求。建议先在一个主题域建模板，再评估是否值得推广。

#### 优点

- 成熟度和社区热度强。
- 安装文档覆盖 Windows、PowerShell、Python 3.11+、uv/pipx，以及 enterprise / air-gapped 安装。
- 更像组织级方法框架，适合后续形成统一 SDD 规范。

#### 风险

- 流程较重，初始引入成本高。
- 对传统数仓敏捷 POC 来说，constitution、clarify、plan、tasks、analyze、implement 全套流程可能让团队感觉像新增一套项目管理制度。
- 更适合“组织级 SDD 工程体系”，不是最轻的试点工具。

#### 是否推荐

推荐作为中长期候选，不推荐作为第一轮轻量 POC 首选。

#### 推荐原因

如果企业后续想把 SDD 标准化，Spec Kit 很值得看；但如果当前只是验证少量数仓需求能否改善敏捷开发，OpenSpec 和 LeanSpec 更快。

## 4. 对比矩阵

| 维度 | OpenSpec | LeanSpec | Kiro | Spec Kit |
|---|---|---|---|---|
| 存量项目适配 | 高：官方强调 brownfield 和 existing codebase | 高：markdown 默认，适合渐进补 specs | 中：可用于既有项目，但依赖 Kiro 工作区 | 高：支持 brownfield，但流程较重 |
| 数仓语义表达能力 | 中高：可通过自定义模板表达字段、指标、回填 | 中：短 spec 友好，但需自定义数仓模板 | 中：requirements/design/tasks 清晰，但偏软件功能 | 高：constitution 和模板适合沉淀数仓治理规则 |
| Jira 并存能力 | 强：用 Jira Key 关联 change folder 即可 | 中：可手工关联，Jira provider 官方标注 Future | 中高：可从 Jira/Confluence 导入，但在 Kiro 内工作 | 中：可并存，但需要额外约定映射 Jira |
| POC 启动成本 | 低：Node 安装、init、创建 change | 低中：轻量，但 CLI/MCP/UI 选型要定 | 中：要安装和使用 Kiro IDE | 中高：Python/uv/pipx、constitution 和流程学习 |
| 工具侵入性 | 低：主要新增 repo 内 openspec 目录 | 低：markdown provider 可低侵入 | 高：IDE 绑定明显 | 中：新增 `.specify`、模板和分支/命令流程 |
| 团队学习成本 | 低：proposal/design/tasks/spec delta 容易理解 | 低中：理念简单，但 provider/CLI 要学习 | 中：IDE 和 spec session 要学习 | 中高：流程完整，学习项多 |
| AI 辅助能力 | 强：支持多 AI assistant | 强：CLI/MCP 面向 AI-native | 强：IDE 内闭环体验强 | 强：支持多 agent 和结构化命令 |
| 企业推广潜力 | 中：团队/多 repo 能力仍在建设 | 中：轻量灵活，但企业治理能力需补 | 中：企业能力有，但工具绑定强 | 高：更像组织级方法框架 |
| 锁定风险 | 低：Git + Markdown + 多 agent | 低中：LeanSpec 工具链小生态 | 高：Kiro IDE 和订阅/模型体系 | 中：GitHub/agent/CLI 流程绑定 |
| 总体推荐度 | 高：当前 POC 最平衡 | 中高：轻量备选 | 中低：适合 IDE 试验，不适合首选 | 中高：适合后续标准化 |

## 5. 最推荐方案

最推荐 POC 方案：

**以 OpenSpec 作为轻量 Spec 骨架，结合自定义数仓模板和 Jira 工作流做局部试点。**

为什么选 OpenSpec：

- 它足够轻，不会把 POC 变成流程改造项目。
- 它不要求替换 Jira，只需要在 Jira Story 中挂 Git spec 链接。
- 它不要求替换 SQL/ETL/调度平台，只是在开发前补一层规格。
- 它的 change folder 很适合数仓需求从“业务变更”走到“设计、开发、验收”。
- 它适合针对新增需求逐步补 specs，不要求全仓盘点历史链路。

为什么不优先选其他三个：

- LeanSpec：足够轻，但 Jira provider 仍是未来能力，现阶段 Jira 集成不如“OpenSpec + 手工 Jira Key”直接。
- Kiro：体验好，但 IDE 绑定与企业工具链迁移成本过高。
- Spec Kit：成熟，但第一轮 POC 容易过重，更适合作为后续组织级标准候选。

它如何和 Jira、Git、现有数仓流程并存：

- Jira Epic：代表业务主题或版本目标。
- Jira Story：代表一个数据需求或指标变更。
- OpenSpec change：用 Jira Key 命名，例如 `openspec/changes/DW-1234-customer-retention-metric/`。
- Git PR：同时包含 SQL/ETL 代码和 spec 变更。
- 发布流程：仍走现有上线流程，但增加 spec review 和 acceptance checklist。
- 验收：业务验收看指标口径和样例数据，技术验收看数据质量、调度、回填和 SLA。

适合试点的需求：

- 新增 ADS 报表表或指标宽表。
- 既有指标口径调整。
- 多下游消费的共享 DWS 表变更。
- 涉及字段新增、枚举扩展、维表映射变化的需求。
- 需要回填、灰度、双跑校验的中等复杂需求。

不适合第一轮试点的需求：

- 全仓模型重构。
- 涉及几十条链路的大型迁移。
- 纯修复类小 SQL bug。
- 没有明确业务 owner 的历史口径治理。
- 跨多个系统且权限审批复杂的高风险项目。

## 6. POC 实施方案

POC 周期：4-6 周

POC 目标：

- 验证 SDD 是否能减少需求返工。
- 验证指标口径和字段定义能否提前澄清。
- 验证开发任务拆解是否更清晰。
- 验证测试验收和上线治理是否更可控。
- 验证 Jira 与 Git spec 是否能形成可追踪闭环。

试点范围：

- 选择 2-3 个真实业务需求。
- 只覆盖一个主题域或一个业务线。
- 只对新增或中等复杂变更试点。
- 不要求补齐历史全量文档。

试点需求选择标准：

- 有明确业务 owner。
- 有明确下游消费者。
- 涉及指标口径或字段规则。
- 涉及至少一张 DWD/DWS/ADS 表。
- 有测试和验收价值。
- 不属于紧急救火需求。

参与角色：

- 产品/业务分析：确认业务目标、指标口径、验收标准。
- 数据架构师：确认分层、模型、依赖、治理要求。
- 数仓开发：实现 SQL/ETL、调度、回填。
- 测试/数据质量负责人：设计校验规则和样例对账。
- 项目经理/Scrum Master：维护 Jira 状态和节奏。
- 数据消费者代表：确认报表、接口或分析使用方式。

工作流：

1. Jira 需求进入：创建 Story，补充业务背景、优先级、owner、期望交付时间。
2. SDD spec 创建：创建 OpenSpec change folder，并在 Jira 中贴 Git 链接。
3. 业务口径澄清：完成 `proposal.md` 和 `requirements.md`，确认指标、字段、边界。
4. 技术设计：完成 `design.md`，确认分层、源表、目标表、调度、回填、权限。
5. task 拆解：完成 `tasks.md`，拆成建表、SQL、调度、测试、上线、验收任务。
6. SQL/ETL 实现：按 tasks 开发，PR 中同时带代码和 spec。
7. 测试与验收：按 `acceptance-checklist.md` 做样例数据、口径、质量、SLA 校验。
8. 上线与回填：按 `design.md` 的 rollout/backfill plan 执行。
9. 复盘与推广判断：统计返工、澄清次数、缺陷、上线问题和团队反馈。

产物模板：

- `proposal.md`
- `requirements.md`
- `design.md`
- `tasks.md`
- `data-contract.yaml`
- `acceptance-checklist.md`

验收指标：

- 需求澄清问题是否前置发现。
- 指标口径变更次数是否减少。
- 开发中断等待业务确认次数是否减少。
- 测试缺陷是否更早暴露。
- 上线后数据问题数量是否减少。
- Jira Story 与 Git spec 是否能一一追踪。
- 团队是否认为模板负担可接受。

风险与应对：

- 风险：模板太重，团队抵触。应对：第一轮只保留最少字段，禁止为了形式补文档。
- 风险：Spec 和 Jira 重复维护。应对：Jira 只保留管理字段，详细口径进入 Git spec。
- 风险：Spec 写完但无人评审。应对：把 spec review 作为开发前置条件。
- 风险：没有自动校验，spec 变成静态文档。应对：第二阶段增加字段、质量、SLA 检查脚本。
- 风险：AI 生成内容不可信。应对：AI 只能生成初稿，业务口径和验收必须人工确认。

## 7. 数仓专用模板建议

`proposal.md` 应包含：

- Jira Key
- 业务背景
- 业务目标
- 需求范围
- 非范围
- 影响系统
- 涉及主题域
- 下游消费者
- 成功标准
- 风险初判

`requirements.md` 应包含：

- 业务术语定义
- 指标名称
- 指标公式
- 统计周期
- 过滤条件
- 去重规则
- 字段口径
- 边界条件
- 样例数据
- 验收口径

`design.md` 应包含：

- 当前链路说明
- 目标链路说明
- 数仓分层落位
- 源表清单
- 目标表清单
- source-to-target mapping
- 主键和分区策略
- 增量逻辑
- 调度策略
- 权限和敏感字段
- 回填方案
- 回滚方案
- 上下游影响

`tasks.md` 应包含：

- 建表任务
- SQL/ETL 开发任务
- 调度配置任务
- 数据质量规则任务
- 单元测试或样例验证任务
- 回填任务
- 权限申请任务
- 上线任务
- 验收任务
- Jira Sub-task 映射

`data-contract.yaml` 应包含：

- dataset 名称
- owner
- consumer
- schema
- 字段类型
- 字段语义
- 主键/唯一性
- 非空约束
- 枚举约束
- 数据质量断言
- freshness / SLA
- 安全分级
- 使用限制
- 版本号

`acceptance-checklist.md` 应包含：

- 业务口径已确认
- 字段定义已确认
- 样例数据已对账
- 历史回填已验证
- 调度依赖已检查
- 数据质量规则已通过
- 权限已配置
- 下游报表或接口已验证
- SLA 已验证
- Jira 状态和 PR 状态已同步

## 8. 最终建议

最适合当前 POC 的工具：**OpenSpec**

推荐原因：

它最适合在大型传统数仓里做低风险敏捷试点。它足够轻，可以和 Jira、Git、SQL/ETL、现有调度平台并存；它又足够结构化，可以让需求、设计、任务、规格变更进入同一个 repo，减少口径误解和开发返工。

不推荐直接全仓推广的原因：

大型数仓历史链路多、口径复杂、owner 不一定清晰。如果一开始要求所有表、所有指标、所有链路都补 SDD，会迅速变成文档治理工程，团队容易抵触，收益也不容易量化。

后续从 POC 到团队级推广的条件：

- 试点需求确实减少了返工和口径争议。
- 业务、架构、开发都愿意使用模板。
- Jira 与 Git spec 的映射稳定。
- 至少核心字段、指标、质量、SLA 能进入自动校验。
- 形成一套轻量模板，不超过团队可维护负担。
- 明确哪些需求必须走 SDD，哪些小修复可以豁免。

需要提前避开的坑：

- 不要把 SDD 当成多写文档。
- 不要让 Jira 和 spec 写两套完整需求。
- 不要一开始追求全仓覆盖。
- 不要让 AI 直接决定业务口径。
- 不要只写 `proposal/design/tasks`，却不写验收标准。
- 不要忽略回填、重跑、权限、调度和 SLA，这些才是数仓上线的真实风险点。

## 9. 参考资料

- OpenSpec 官网：https://openspec.dev/
- OpenSpec Workflow：https://openspec.pro/workflow/
- OpenSpec GitHub：https://github.com/Fission-AI/OpenSpec
- LeanSpec 官网：https://www.lean-spec.dev/
- LeanSpec GitHub：https://github.com/codervisor/lean-spec
- Kiro Specs：https://kiro.dev/docs/specs/
- Kiro Best Practices：https://kiro.dev/docs/specs/best-practices/
- Kiro MCP Security：https://kiro.dev/docs/mcp/security/
- Spec Kit Docs：https://github.github.com/spec-kit/
- Spec Kit Installation：https://github.github.com/spec-kit/installation.html
- Spec Kit Quick Start：https://github.github.com/spec-kit/quickstart.html
- Spec Kit GitHub：https://github.com/github/spec-kit
- OpenMetadata Data Contracts：https://docs.open-metadata.org/v1.12.x/how-to-guides/data-contracts/spec
