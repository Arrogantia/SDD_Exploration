# 主流 SDD 工具与框架多维度对比报告

> 研究日期：2026-06-02
> 对比对象：OpenSpec、Spec Kit、BMAD、Superpowers、Kiro
> 说明：本报告已按要求移除 LeanSpec 分析。`Token 消耗量/成本` 为基于流程复杂度、上下文长度、Agent 调用次数、子任务拆分和商业计费方式的相对判断，不代表官方统一 benchmark。

## 1. 结论先行

如果目标是 **大型传统项目或传统数仓中的低风险 SDD POC**，第一推荐仍然是 **OpenSpec**。它足够轻、对存量项目侵入低、规格以 Markdown 留在仓库中，适合先把需求澄清、字段定义、指标口径、任务拆解和验收条件纳入变更流程。

如果目标是 **组织级 SDD 标准化和治理体系建设**，更值得进一步评估 **Spec Kit**。它的 constitution、clarify、checklist、plan、tasks、analyze、implement 形成了更完整的规格驱动链路，适合后续沉淀企业级规范，但用于第一轮 POC 时可能偏重。

如果目标是 **AI-native Agile 或复杂产品交付体系重塑**，可以重点研究 **BMAD**。它不是单一工具，而是一套带角色分工、PRD、架构、Story、QA 和测试策略的 AI 敏捷方法体系，适合复杂项目，不适合作为最小化试点起点。

如果目标是 **提高 AI 编码纪律、降低 Agent 直接乱写代码的概率**，可以引入 **Superpowers** 作为补充。它更像给 AI Agent 加上 brainstorm、plan、TDD、review、worktree 等工程习惯，不替代需求治理和项目管理。

如果目标是 **在一个 IDE/平台内完成规格、设计、任务、执行和自动化**，可以评估 **Kiro**。它产品化程度高，specs、steering、hooks、MCP 体验完整，但商业平台、账号、credit 和工具迁移成本需要提前评估。

## 2. 尽可能全面的对比维度清单

### 2.1 战略与方法论维度

| 维度 | 关注问题 |
|---|---|
| 核心哲学 | 工具认为 SDD 的核心是“规格治理”、“可执行规范”、“敏捷角色协作”、“TDD 纪律”还是“IDE 内闭环”？ |
| 方法论完整度 | 是否覆盖从需求、设计、任务、实现、测试、上线到复盘的完整链路？ |
| 控制模式 | 是轻量提醒、强制门禁、角色分工、自动化 hook，还是 IDE 平台控制？ |
| 流程刚性 | 是否强制固定阶段？是否允许团队只采用部分步骤？ |
| 人工参与点 | 哪些环节必须人工确认，例如需求澄清、设计评审、任务拆解、测试验收？ |
| 适用组织阶段 | 更适合个人、小团队、试点项目、平台团队，还是企业级推广？ |

### 2.2 工具与工程维度

| 维度 | 关注问题 |
|---|---|
| 工具形态 | CLI、IDE、插件、Agent 技能、模板框架、项目管理框架、商业平台。 |
| Git 友好度 | 规格是否可入库、可 diff、可 review、可回溯。 |
| CI/CD 适配 | 是否容易接入质量检查、测试、发布门禁。 |
| 存量项目适配 | 是否适合 brownfield 项目，是否要求重构目录和流程。 |
| 目录侵入性 | 是否需要新增复杂目录、配置文件和元数据。 |
| 局部 POC 能力 | 是否可只对一个需求、一个模块、一个指标或一个小链路试点。 |
| 退出成本 | 停用工具后，规格、文档、任务和流程是否仍可保留。 |
| IDE 依赖 | 是否必须迁移到指定 IDE 或平台。 |
| 模型/平台依赖 | 是否绑定 Claude、OpenAI、AWS、GitHub、特定 Agent 或云服务。 |

### 2.3 AI 与上下文维度

| 维度 | 关注问题 |
|---|---|
| Agent 支持范围 | 是否支持 Codex、Claude Code、Cursor、Copilot、Gemini CLI 等多种 Agent。 |
| MCP 支持 | 是否容易接入数据库、元数据、文档库、工单、搜索、CI 等上下文工具。 |
| 上下文管理 | 是否能避免 Agent 只看局部信息导致实现缺失。 |
| 子 Agent 支持 | 是否支持多角色、多阶段、多任务并行或分工。 |
| 澄清机制 | 是否有明确机制要求 Agent 在不确定时先提问。 |
| 一致性检查 | 是否能检查 spec、plan、tasks、代码之间的信息断层。 |
| 自动执行能力 | 是否能从任务直接生成代码、测试、配置或 issue。 |
| Token 消耗 | 是否容易因多轮澄清、长文档、子 Agent、TDD、review 造成高消耗。 |

### 2.4 企业治理维度

| 维度 | 关注问题 |
|---|---|
| 标准化能力 | 是否支持组织级规范、模板、preset、constitution、checklist。 |
| 审计追踪 | 是否能追踪需求到设计、任务、代码、测试和验收。 |
| 权限与合规 | 是否适合企业内网、隐私、权限管控、敏感数据处理。 |
| 项目管理协同 | 是否能与现有项目管理、Issue、工单和变更流程并存。 |
| 跨团队协作 | 是否适合业务、架构、开发、测试、运维等多角色协作。 |
| 发布治理 | 是否能支持上线、回滚、灰度、数据修复、验收归档。 |
| 规范沉淀 | 是否能将团队约定固化为可复用资产。 |

### 2.5 数据工程/数仓维度

| 维度 | 关注问题 |
|---|---|
| SQL/ETL 适配 | 是否适合 SQL、ETL、调度、模型、脚本和数据平台开发。 |
| 指标口径 | 是否能清晰表达指标公式、粒度、统计窗口、过滤条件和去重规则。 |
| 字段定义 | 是否能管理字段含义、类型、来源、映射和兼容性。 |
| 数据契约 | 是否能表达输入输出表、schema、主键、约束、质量规则。 |
| 上下游影响 | 是否能要求 Agent 分析依赖表、消费方、报表和调度链路。 |
| 数据质量 | 是否能沉淀空值、重复、波动、对账、阈值等验收规则。 |
| 增量与回填 | 是否能表达增量逻辑、重跑、回填范围、幂等和回滚。 |
| SLA 与调度 | 是否能表达产出时间、依赖、失败重试、告警和运行窗口。 |

## 3. 主对比矩阵

| 维度 | OpenSpec | Spec Kit | BMAD | Superpowers | Kiro |
|---|---|---|---|---|---|
| 核心哲学 | 轻量规格先行。原因：以 proposal、design、tasks、spec delta 管理变更，强调先对齐再实现。 | 规格即执行入口。原因：constitution、spec、plan、tasks、analyze、implement 串起完整工程链路。 | AI-native Agile。原因：用 Analyst、PM、Architect、Dev、QA 等角色模拟敏捷团队。 | Agent 工程纪律。原因：强调 brainstorm、plan、TDD、review，让 Agent 不直接开写。 | IDE 内规格闭环。原因：requirements、design、tasks、steering、hooks 都集成在产品工作流中。 |
| 工具形态 | 开源 CLI + Markdown 规范层。适合作为仓库内轻量治理层。 | GitHub 开源 Toolkit + CLI + 模板命令。适合标准化工程骨架。 | 方法论 + 多 Agent 工作流 + 模板体系。更像研发操作系统。 | Agent 技能/行为框架。更像增强 AI 编码习惯的插件层。 | 商业 Agentic IDE/CLI/Web。更像完整开发平台。 |
| 控制模式 | 中等控制。人工审 proposal/tasks，再执行变更。 | 强控制。多阶段命令和质量门禁减少信息断层。 | 很强控制。角色、阶段、story、QA 都被流程化。 | 很强控制。TDD、worktree、review 对 Agent 行为约束明显。 | 强控制。通过 IDE specs、steering、hooks 管控上下文和动作。 |
| 标准流程 | Proposal → Design → Tasks → Spec Delta → Apply → Archive。流程短，适合小步迭代。 | Constitution → Specify → Clarify → Checklist → Plan → Tasks → Analyze → Implement。流程完整但更重。 | Brief/Research → PRD → Architecture → Epics/Stories → Dev → QA。适合复杂交付。 | Brainstorm → Design → Plan → Worktree → TDD → Review → Finish。适合编码质量控制。 | Requirements → Design → Tasks → Execute，并结合 Steering/Hooks/MCP。适合 IDE 内闭环。 |
| 核心产物 | `proposal.md`、`design.md`、`tasks.md`、`spec.md`。产物轻，易审查。 | `constitution.md`、`spec.md`、`plan.md`、`tasks.md`、checklist。产物完整，利于治理。 | PRD、Architecture、Epics、Stories、QA 报告。产物重，适合复杂项目。 | Design、Plan、测试、review、worktree 分支。更偏开发过程记录。 | `.kiro/specs`、`.kiro/steering`、hooks 配置。平台内体验强。 |
| 需求澄清能力 | 中。模板可要求澄清，但不默认强制复杂澄清流程。 | 强。`clarify` 和 checklist 明确用于消除歧义。 | 强。Analyst/PM 流程适合复杂需求挖掘。 | 中。可 brainstorm，但不主打正式需求治理。 | 强。spec 流程会生成需求并引导 refinement。 |
| 设计深度 | 中。适合变更级设计，不追求完整企业架构。 | 强。plan 可包含技术上下文、研究、数据模型和约束。 | 很强。Architecture 是核心产物之一。 | 中。设计以能指导实现和测试为主。 | 中强。design.md 能在 IDE 内承载架构与组件说明。 |
| 任务拆解 | 中强。tasks 简洁，适合 POC 和小步交付。 | 强。tasks 可从 plan 派生，适合并行、依赖和测试拆解。 | 很强。story/epic/sprint 粒度适合敏捷团队。 | 很强。强调把工作拆成小任务并由子 Agent 执行。 | 强。任务在 IDE 内可逐项执行和跟踪。 |
| 实施自动化 | 中。依赖外部 Agent 或命令执行任务。 | 强。`implement` 可按任务推进实现。 | 强。Dev Agent 按 story 执行，QA 可介入。 | 很强。子 Agent、TDD、review 推动实现。 | 很强。IDE 内直接执行 spec tasks。 |
| 质量门禁 | 中。需要团队自定义 checklist 和审查规则。 | 强。checklist/analyze 是显著优势。 | 很强。QA 与测试模块较完整。 | 很强。TDD + review 是核心设计。 | 强。hooks 可自动触发检查。 |
| 测试策略 | 中。可自定义验收模板，但不是内置核心。 | 强。测试可作为 checklist/tasks/analyze 的一部分。 | 很强。BMAD 有 QA 和测试策略扩展。 | 很强。TDD 是核心哲学。 | 中强。可通过 tasks/hooks/MCP 实现自动测试。 |
| 存量项目适配 | 强。轻量目录和 Markdown 产物对 brownfield 友好。 | 中强。支持现有项目，但初始化和规范更完整。 | 强但较重。brownfield 可做，但产物和角色成本高。 | 中。能用于已有代码，但偏编码纪律，不偏需求体系。 | 中。可打开现有项目，但价值依赖 Kiro 环境。 |
| 局部 POC 能力 | 很强。可只选一个需求或链路试点。 | 中强。可 POC，但流程较完整，需要裁剪。 | 中。小 POC 容易过度工程。 | 强。可对某个任务或模块引入。 | 中。需要团队接受 Kiro 工具链。 |
| 实施成本 | 低。主要是模板设计和团队习惯调整。 | 中。需要初始化、constitution、命令和质量门禁。 | 高。需要理解角色、流程和产物体系。 | 中。工具成本低，但 TDD/review 执行成本高。 | 中高。涉及商业账号、IDE、credit 和安全评估。 |
| 技术复杂度 | 低到中。Markdown + CLI，理解门槛低。 | 中到高。命令链和 artifact 较多。 | 高。多 Agent、多角色、多阶段。 | 中。概念简单，但执行纪律细。 | 中到高。平台能力多，配置和集成更复杂。 |
| 学习曲线 | 低。团队容易理解 proposal/design/tasks。 | 中。要理解 constitution、clarify、analyze 等概念。 | 高。需要学习完整 BMAD 方法论。 | 中。需要适应 TDD、worktree、review 习惯。 | 中。需要学习 Kiro specs、steering、hooks、MCP。 |
| Token 消耗量 | 低到中。原因：产物轻、阶段少，上下文较短。 | 中到高。原因：clarify、plan、analyze 会增加上下文和轮次。 | 高。原因：多角色、多文档、多阶段交互明显增加消耗。 | 高。原因：子 Agent、TDD、review、小任务多轮调用。 | 中到高。原因：spec refinement、task execution、hooks 和 credit 模型都会产生成本。 |
| 成本可控性 | 强。开源，成本主要来自所选 Agent。 | 中强。开源，但流程越完整 token 越高。 | 中。方法重，人力和 token 都较高。 | 中。开源，但频繁 Agent 调用可能贵。 | 中。商业 credit 机制，需要预算管理。 |
| IDE 依赖 | 低。可与多种编辑器/Agent 并存。 | 低到中。支持多 Agent，但需要初始化到工作区。 | 中。依赖 AI IDE/Agent 执行角色流程。 | 中。依赖 Agent 技能运行环境。 | 高。核心体验绑定 Kiro。 |
| 模型/平台锁定 | 低。Markdown 产物可迁移。 | 低到中。开源，但具体体验取决于 Agent 集成。 | 中。流程习惯和 Agent 角色会形成锁定。 | 中。技能生态和 Agent 支持会形成锁定。 | 高。商业平台、账号、IDE 和计费模型锁定明显。 |
| MCP/工具扩展 | 中。可通过外部 Agent 接入上下文工具。 | 强。多 Agent、extensions/presets 更适合扩展。 | 中强。可结合 Agent/MCP/模块扩展。 | 中。技能可组合，但不是平台级治理框架。 | 强。MCP 是 Kiro 重要扩展方式。 |
| 企业治理潜力 | 中。需要自建模板、审计和门禁。 | 强。constitution、presets、extensions 适合组织规范沉淀。 | 强。角色、PRD、架构、QA 适合流程型组织。 | 中。更偏工程纪律，不偏企业治理。 | 强。产品化能力和企业版更适合统一管控。 |
| 安全与隐私 | 中。开源本地文件友好，但实际取决于所用 Agent。 | 中。框架开源，风险取决于模型、插件和执行环境。 | 中。多 Agent 调用可能扩大上下文暴露面。 | 中。插件和 Agent 权限需治理。 | 中到强。产品化安全能力较好，但需单独核验企业版数据策略。 |
| 开源透明度 | 强。源码和规范透明。 | 强。GitHub 官方开源。 | 强。开源且文档体系完整。 | 强。开源，技能内容可审查。 | 弱到中。文档公开，但核心平台非纯开源。 |
| 社区成熟度 | 中强。适合关注版本活跃度和 issue。 | 强。GitHub 官方项目，社区关注度高。 | 强。文档、模块、社区较活跃。 | 强。社区关注高，适合 AI coding 群体。 | 强。官方产品化和文档完整。 |
| 标准化能力 | 中。模板可标准化，但需团队自建。 | 很强。constitution/preset/extension 是核心优势。 | 很强。流程天然标准化。 | 中。标准化在开发纪律层，不在业务规格层。 | 强。steering/hooks/specs 可沉淀团队规则。 |
| 项目管理适配 | 中。可通过变更 ID、Git 分支或工单号映射。 | 中强。可转 issue，但外部 PM 工具需集成。 | 强。epic/story/sprint 天然接近敏捷项目管理。 | 弱到中。不负责正式项目管理。 | 中。IDE 内强，外部项目管理需集成。 |
| Git/CI 适配 | 强。规格进仓库，便于 diff/review。 | 强。specs 进仓库，可与 issue/CI 协作。 | 强。产物可随仓库管理，但目录和文档较多。 | 很强。worktree/branch/review 是核心实践。 | 中强。`.kiro` 文件可进仓库，但体验依赖 Kiro。 |
| 数仓/数据工程适配 | 强。原因：低侵入，适合定制字段、口径、回填、验收模板。 | 强。原因：适合沉淀数据契约、质量 checklist、口径 constitution。 | 中强。原因：适合复杂数据产品，但普通数仓需求偏重。 | 中。原因：适合 SQL 实施纪律，不擅长数据治理。 | 中强。原因：适合 SQL/平台开发，但需要接入元数据和权限上下文。 |
| 指标口径治理 | 中强。可用 spec delta 管理口径变化。 | 强。constitution + checklist 可作为口径门禁。 | 中。PRD/story 可表达，但非专门数据治理。 | 弱到中。需额外模板约束。 | 中。可写入 steering/spec，但平台绑定。 |
| 字段定义与数据契约 | 中强。可通过自定义模板补齐。 | 强。适合定义 contract、schema、验收规则。 | 中。架构文档可承载，但不是默认核心。 | 弱到中。需要自行补模板。 | 中。可在 spec/design 中表达，需 MCP 或数据库上下文增强。 |
| 上下游影响分析 | 中。需要模板要求 Agent 搜索 SQL、依赖和消费方。 | 中强。plan/analyze 可纳入影响检查。 | 中强。架构/story 可纳入影响分析。 | 中。可在 review 阶段要求检查。 | 中强。MCP 可接元数据、数据库和文档工具。 |
| 回填/重跑/SLA | 中强。适合在 proposal/design/tasks 中强制表达。 | 强。适合写入 constitution/checklist/tasks。 | 中。可纳入故事验收，但需模板化。 | 弱到中。需额外 prompt 和任务约束。 | 中。可通过 spec/hooks 管理，但需要定制。 |
| 适用场景 | 存量项目小步 POC、传统数仓变更、低侵入治理。 | 组织级 SDD、可执行规格、质量门禁、数据契约。 | 复杂产品、平台工程、AI 敏捷团队重塑。 | AI 编码纪律、TDD、防止 Agent 乱写。 | IDE 内闭环、团队愿意采用 Kiro、强自动化。 |
| 不适合场景 | 需要完整企业权限、审批、审计平台的场景。 | 只想极轻量试点或团队不愿维护 artifact。 | 小需求、短周期、单 SQL 变更。 | 正式需求治理、跨部门口径治理。 | 不愿换工具、内网合规限制严格、预算不确定。 |
| 核心优势 | 轻、快、brownfield 友好、易退出。 | 结构完整、质量门禁强、标准化潜力高。 | 全流程、角色清晰、复杂项目组织能力强。 | TDD/review 强，能约束 AI 编码行为。 | 产品化体验好，spec/steering/hooks/MCP 集成强。 |
| 主要局限性 | 企业治理需自建，规格可能停留在文档层。 | 试点偏重，上下文不足时仍会生成缺信息代码。 | 启动成本高，容易过度工程。 | Token 高，不解决业务规格治理。 | 锁定风险、credit 成本、安全评估成本高。 |
| 退出成本 | 低。Markdown 留在仓库，可继续手工维护。 | 中。artifact 可保留，但流程迁移需整理。 | 中高。团队流程和角色习惯迁移成本高。 | 中。技能可卸载，但 TDD/review 流程会影响习惯。 | 高。IDE/平台能力迁出成本较高。 |
| 综合推荐 | 适合第一轮低风险 SDD POC。 | 适合第二阶段标准化治理。 | 适合复杂项目和研发体系升级。 | 适合作为 AI 编码质量增强组件。 | 适合 IDE 内全链路 SDD 平台化尝试。 |

## 4. 按场景选择的推荐

| 场景 | 第一推荐 | 第二推荐 | 原因 |
|---|---|---|---|
| 大型传统数仓低风险 POC | OpenSpec | Spec Kit | OpenSpec 轻、易退出；Spec Kit 治理更强但启动更重。 |
| 组织级 SDD 标准化 | Spec Kit | BMAD | Spec Kit 有 constitution/preset/extension；BMAD 适合更完整的敏捷组织改造。 |
| AI 辅助代码质量控制 | Superpowers | BMAD | Superpowers 强在 TDD 和 review；BMAD 强在角色化协作。 |
| IDE 内规格驱动开发 | Kiro | Spec Kit | Kiro 产品化闭环最好；Spec Kit 可通过多 Agent 支持实现类似流程。 |
| 复杂数据产品或平台工程 | BMAD | Spec Kit | BMAD 的 PRD/Architecture/Story/QA 更适合复杂交付。 |
| 数据契约和指标治理 | Spec Kit | OpenSpec | Spec Kit 标准化和门禁更强；OpenSpec 更适合轻量变更管理。 |
| 快速验证单个需求 | OpenSpec | Superpowers | OpenSpec 管规格，Superpowers 管编码纪律。 |
| 企业内网/低锁定要求 | OpenSpec | Spec Kit | 两者开源透明，文件可入库，退出成本低于 Kiro。 |
| 强自动化和产品体验 | Kiro | Spec Kit | Kiro specs/steering/hooks/MCP 集成度更高，但锁定也更高。 |

## 5. 针对传统数仓的排序建议

| 排名 | 工具 | 推荐理由 | 主要注意事项 |
|---|---|---|---|
| 1 | OpenSpec | 最适合低风险 POC。可把指标口径、字段映射、数据契约、增量回填、验收 SQL 写进轻量模板，不要求重构全仓流程。 | 需要自建数仓模板和验收门禁，否则规格可能只是文档。 |
| 2 | Spec Kit | 适合 POC 后的标准化扩展。constitution/checklist/analyze 能沉淀统一口径、数据质量和发布规则。 | 第一轮试点需要裁剪，否则流程和 token 成本偏高。 |
| 3 | Kiro | 适合希望在 IDE/Web/CLI 中做规格、设计、任务和自动化闭环的团队。 | 需评估账号、credit、安全、内网、Snowflake/元数据接入和工具迁移。 |
| 4 | BMAD | 适合复杂数据平台、数据产品、跨团队交付。 | 对普通数仓需求过重，实施成本和学习成本较高。 |
| 5 | Superpowers | 适合作为 AI 编码纪律增强层，降低 Agent 直接乱写 SQL/代码的风险。 | 不能单独承担指标口径、数据契约和上线治理。 |

## 6. 对 Token 成本的进一步说明

Token 消耗不是单纯由工具决定，而是由以下因素共同决定：

- 规格产物长度：constitution、PRD、architecture、plan 越长，单次上下文成本越高。
- 流程轮次：clarify、checklist、analyze、review、QA 越多，调用次数越多。
- Agent 数量：BMAD 和 Superpowers 如果启用多 Agent/子 Agent，成本会明显上升。
- 任务颗粒度：任务越细，单次风险越低，但总调用次数越多。
- 上下文接入：接入代码库、数据库 schema、历史 SQL、测试结果会增加上下文，但能提升正确率。
- 自动执行范围：从生成文档扩展到生成代码、测试、修复和 review，成本会成倍增加。
- 商业计费方式：Kiro 使用 credit/套餐模型，需要额外做预算测算。

因此，对传统数仓 POC 更建议：

1. 第一阶段只选 1-2 个真实需求，不做全仓推广。
2. 每个需求限制规格长度，优先表达指标口径、字段映射、输入输出、回填、验收。
3. 不让 Agent 一次生成完整链路，而是按模型、SQL、质量检查、回填脚本、上线清单分段执行。
4. 对 OpenSpec/Spec Kit 做同需求对照，比较规格完整度、返工率、生成代码缺失信息数量和验收通过率。

## 7. 最终建议

若当前目标是 **从多个主流 SDD 框架中选择一个做大型传统数仓 POC**，建议采用：

> **OpenSpec 作为第一轮 POC 主框架，Spec Kit 作为第二阶段治理化候选，Superpowers 作为 AI 编码纪律补充，Kiro 作为平台化 IDE 方案观察，BMAD 用于复杂数据产品或组织级敏捷改造评估。**

不建议一开始就选择最重的框架做全流程替换。传统数仓已有历史链路、开发规范、调度体系、权限流程和上线治理，SDD 的第一价值不是“换工具”，而是把需求口径、字段定义、上下游影响、回填逻辑和验收标准前置并可追踪。

POC 成功后，再判断是否需要引入 Spec Kit 的 constitution、checklist、analyze 和 preset 机制，把试点经验沉淀成团队标准。

## 8. 参考资料

- OpenSpec 官方文档：https://openspec.dev/
- OpenSpec GitHub：https://github.com/Fission-AI/OpenSpec
- Spec Kit GitHub：https://github.com/github/spec-kit
- Spec Kit 官方站点：https://github.github.io/spec-kit/
- BMAD Method 文档：https://docs.bmad-method.org/
- BMAD Method GitHub：https://github.com/bmad-code-org/BMAD-METHOD
- BMAD Testing Options：https://docs.bmad-method.org/reference/testing/
- Superpowers GitHub：https://github.com/obra/superpowers
- Kiro 官方文档：https://kiro.dev/docs/
- Kiro Specs：https://kiro.dev/docs/specs/
- Kiro Steering：https://kiro.dev/docs/steering/
- Kiro Hooks：https://kiro.dev/docs/hooks/
- Kiro MCP：https://kiro.dev/docs/mcp/
- Kiro Pricing：https://kiro.dev/pricing/
