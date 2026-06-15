最终结论：**只选 1-2 个 SDD 框架即可，不建议再引入 BMAD、Kiro、Superpowers 作为第一阶段方案。**

最适合你们大型传统数仓的组合是：

> **OpenSpec 作为主框架 + Spec Kit 作为治理增强框架**

如果只能选一个：**选 OpenSpec**。  
如果允许两个结合：**OpenSpec + Spec Kit** 是最合理组合。

**推荐分工**

| 框架 | 定位 | 在你们项目中的作用 |
|---|---|---|
| OpenSpec | 主 SDD 流程框架 | 管每个需求的 `proposal`、`design`、`tasks`、规格变更、归档 |
| Spec Kit | 治理增强框架 | 提供 constitution、checklist、clarify、analyze 思路，用于补齐规范和质量门禁 |

**为什么 OpenSpec 做主框架**

OpenSpec 更适合你们当前目标：快速应用到现有大型数仓项目中，提高开发效率，而不是重塑整个研发体系。

原因：

- 适合存量项目，不要求全仓重构。
- 规格文件可以直接放进现有 Git 仓库。
- 可以围绕单个 Jira/Excel/邮件需求创建变更。
- 对 Snowflake SQL、Informatica、Autosys 配置都不强侵入。
- 学习成本低，适合快速 POC。
- 更容易和现有需求、代码、测试、上线流程并存。

**为什么 Spec Kit 不做主框架，而是做增强**

Spec Kit 的结构更完整，但第一阶段直接全量使用会偏重。它更适合提炼成你们的数仓治理规则。

建议只借用这些能力：

| Spec Kit 能力 | 在数仓中的用途 |
|---|---|
| constitution | 定义数仓开发原则、分层规范、字段规则、回填规则 |
| checklist | 检查需求是否补齐口径、字段、源表、目标表、验收 |
| clarify | 针对不完整 Jira/Excel/邮件需求做澄清 |
| analyze | 检查需求、设计、任务之间是否有遗漏 |

不要第一阶段完整套用 Spec Kit 的所有命令链，否则会增加流程成本。

**最终推荐工作流**

```text
Jira / Excel / Email 需求
  ↓
OpenSpec proposal.md
  ↓
Spec Kit 风格 checklist / clarify
  ↓
OpenSpec design.md
  ↓
OpenSpec tasks.md
  ↓
SQL / Informatica / Autosys 开发
  ↓
SQL 对账 / 抽样 / 自动化测试 / 业务确认
  ↓
OpenSpec archive
```

**最重要的边界**

| 问题 | 决策 |
|---|---|
| 谁是主流程 | OpenSpec |
| 谁是需求变更主记录 | OpenSpec change |
| 谁是任务唯一来源 | OpenSpec tasks |
| 谁负责治理规则 | Spec Kit constitution/checklist |
| 是否引入 BMAD | 第一阶段不引入 |
| 是否引入 Kiro | 第一阶段不引入 |
| 是否引入 Superpowers | 暂不作为框架引入，可后续参考其 TDD/review 思路 |

**最终建议**

你们第一阶段应该采用：

> **OpenSpec + 轻量 Spec Kit 治理模板**

而不是：

> BMAD + Kiro + Superpowers + Spec Kit + OpenSpec 全部叠加

这样最符合你们的目标：**快速应用、低侵入、基于现有代码和需求开发、逐步提升需求澄清、开发拆解、测试验收和上线治理效率。**
