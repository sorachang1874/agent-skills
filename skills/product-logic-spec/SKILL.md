---
name: product-logic-spec
description: 产品逻辑规格——页面规格之前的一层：从混合原始材料中推导角色能力边界、服务模型、对象模型（ORCA）、信息架构、行为状态、真源/版本链与页面注意力合同。只要任务涉及新页面/新功能原型、后台或 SaaS 信息架构、多个角色共用同一系统、教师/运营等非技术角色配置复杂业务，或出现“层次混乱/入口打架/两个页面都能改同一内容/A-B 或预览与真实端不一致/用户气泡混入系统判断/技术后台不像给目标用户使用”，都应使用本 skill。本 skill 止于 HTML 与视觉设计之前，下游移交 frontend-design。
version: "2.3-role-staffing-session"
---

# Product Logic Spec · 产品逻辑规格

> 定位：**业务原始材料 → 【本 skill】→ 页面规格（references/*.md）→ HTML 原型**。
> 它回答"页面应该长什么样"之前的所有逻辑问题；它不产出视觉、不写 HTML——那是 frontend-design 的事。

## 为什么需要这一层

原始输入（业务 rundown / 交付包 / 共享规范等）同时混合了业务目标、流程、角色分工、技术约束、数据与话术。团队生产规范要求"先设计后实现"，但没有规定**怎样从混合输入中推导出一致的对象模型、归属关系、导航结构和页面优先级**。Mermaid 只能把已经建好的逻辑画出来，不能替你建立逻辑。本 skill 强制完成这段推导：

```
业务原始材料
  ↓ ① 需求归一化（Requirement Ledger）
  ↓ ② 角色能力与服务边界（谁能理解、决定和维护什么）
  ↓ ③ 对象/关系/归属/状态/导航模型
  ↓ ④ 面向角色的页面注意力合同
页面规格 → HTML
```

## 工作流：三道 Review Gate

不做机械的"每层一停"。三个正式门，**不过夜、不跳级**：

| Gate | 名称 | 必须回答 | 产物 | 未通过禁止 |
|------|------|----------|------|-----------|
| **A** | 模型确认 | 真源与版本路由？核心场景与边界？主要/次要角色是谁？每个角色能理解、决定和维护什么？角色间如何交接？业务对象及关系？数据和规则归谁维护？系统底座与用户工作台如何分层？ | Requirement Ledger · Actor & Capability Contract · Role–Task–Surface Matrix · Service Blueprint（多角色功能强制）· Object Map / ORCA Matrix · IA Thesis | 写页面区块 |
| **B** | 行为确认 | 每个有生命周期的对象有哪些状态？谁触发变化？Guard？副作用？失败/超时/中断/恢复？浏览态如何进入创建/编辑态并安全返回？多端如何交接？ | State Model · Transition Table · Top-task Flows（必要时 Sequence Diagram） | 画线框/页面结构 |
| **C** | 页面合同确认 | 本页主要为哪个角色服务？该角色此刻懂什么、不该懂什么、愿意承担多少复杂度？页面唯一任务？主对象？第一/二/三注意力？主/次/低频操作？每项编辑由什么触发、承载在哪种交互表面、怎样提交/取消/恢复？技术细节如何隐藏或转交？每个组件映射到哪个对象、状态或动作？视图状态矩阵？哪些内容**明确不在本页**？ | Audience Contract + Page Hierarchy Contract + Interaction Surface Contract（页面逻辑规格文档） | 调用 frontend-design 或写 HTML |

每个 Gate 的通过形式：把产物列给用户 review，收到确认再进入下一道。检查清单见 [references/review-gates.md](references/review-gates.md)。

### Gate 重开纪律

- 发现“对象关系正确，但页面不像给目标用户使用”时，视为 **Gate A 的角色/服务边界失败**，不是视觉或中文化问题。
- 只重开受影响切片：可保留已验证的对象、状态与不变量，但旧 IA、页面拓扑和 Gate C 合同立即失效，直到角色边界重新确认。
- “同一账号兼任多个角色”不等于“把多个角色做进同一默认页面”。必须分别定义角色任务、权限、词汇与复杂度，再决定是否合并界面。
- Gate C 不得用角色名占位。若“课程设计者”可能同时指教研教师、平台配置人员或发布管理员，必须回 Gate A 拆分。

## 七张逻辑视图（不要试图用一张图表达所有逻辑）

| 视图 | 回答的问题 | 产物 | 方法参考 |
|------|-----------|------|----------|
| 角色能力层 | 谁在什么情境下完成什么结果；能理解哪些概念；可承受多少配置；超出边界交给谁 | Actor Cards + Role–Task–Object Matrix + Role–Surface Access Matrix | [references/role-capability-and-surface.md](references/role-capability-and-surface.md) |
| 服务协同层 | 谁在什么阶段，通过哪个端做什么；用户能看到什么，后台发生什么 | Service Blueprint | [references/service-blueprint.md](references/service-blueprint.md) |
| 领域对象层 | 系统里有哪些"东西"，谁拥有谁，谁依赖谁 | 对象关系图 + ORCA 对象卡 | [references/orca-object-model.md](references/orca-object-model.md)（OOUX/ORCA） |
| 信息架构层 | 什么放在哪里，全局/局部/上下文导航分别是什么 | IA Thesis + App Map + Navigation Role Matrix | [references/information-architecture.md](references/information-architecture.md) |
| 行为状态层 | 当前是什么状态，什么触发变化，副作用和兜底 | 状态图 + 转换表 + 关键任务流 | [references/state-and-flow.md](references/state-and-flow.md) |
| 页面注意力层 | 用户第一、第二、第三看到什么，什么动作最重要 | Page Hierarchy Contract | [references/page-hierarchy-contract.md](references/page-hierarchy-contract.md) |
| 交互表面层 | 浏览、查看、创建、编辑和确认怎样分层；什么触发，在哪完成，怎样安全退出 | Interaction Surface Contract | [references/interaction-surface-contract.md](references/interaction-surface-contract.md) |

横向贯穿两条链：

1. 每条需求挂一条追踪链（原始需求 → 产品规则 → 对象/状态 → 页面/组件 → 验收条件），模板见 [assets/traceability-matrix-template.md](assets/traceability-matrix-template.md)。
2. 同一内容跨角色、跨端或跨 renderer 时，再挂一条运行链（业务规则 / canonical → usage → session snapshot → runtime event → presentation projection → review evidence），方法见 [references/source-projection-and-runtime.md](references/source-projection-and-runtime.md)。

## Skill 路由（把行为路由到对应方法/skill）

| 情境 | 路由 |
|------|------|
| 角色名称存在但能力/任务边界含混；非技术用户看到 schema、端口、策略、发布清单 | **角色能力合同**（references/role-capability-and-surface.md）——强制先于 ORCA 与 IA；先分工作台，再分对象家园 |
| 多角色/多端/线上线下交接（课堂、后台线、打印发送） | **Service Blueprint**（references/service-blueprint.md ＋ 配套 skill `service-blueprint`）——强制 |
| 归属不清（"X 属于课程/场次/学生？""这是状态还是动作？"） | **ORCA 对象卡**（references/orca-object-model.md） |
| 导航/入口关系混乱（四入口 vs Tab、重复路径） | **IA 方法**（references/information-architecture.md ＋ 配套 skill `information-architecture`）：声明组织原则 + Navigation Role Matrix |
| 状态/流程已确认，要设计跨端流程与用例 | 状态转换表 + 任务流（references/state-and-flow.md ＋ 配套 skill `state-machine`）；图模式可参考 ux-flow-designer / user-flow-diagram 类图工具，**其 wireframe 产出不直接入仓** |
| 页面把展示、编辑器、选择器和所有异常态同时平铺；有按钮但没有打开/关闭/返回行为 | **交互表面合同**（references/interaction-surface-contract.md）：先定浏览态，再为每项动作选择 inline / popover / modal / drawer / wide overlay / full page，写清触发、提交、取消与恢复 |
| 同一正文在教师台与治理台都能改；A/B、预览与真实端内容不同；孩子气泡混入系统判断 | **真源、投影与 Runtime 合同**（references/source-projection-and-runtime.md）：拆 canonical / usage / snapshot，登记事件语义 owner，并保证多 renderer 共用 manifest 与 Commit |
| 全新业务方向、只有口头描述、需要澄清"为什么做" | JTBD / PRD 类方法（如 jobs-to-be-done / prd-skill）；**不为已有 rundown 的需求再造一份 PRD 真源** |
| Gate C 通过后 | **frontend-design**（视觉与 HTML） |
| 页面合同完成后需要设计评审 | 结构化设计评审方法（如 design-critique）；检查项已并入 review-gates.md Gate C 清单 |

## 产出落点（建议约定，可按仓库调整）

```
{模块}/references/models/        ← 跨页面模型（领域对象 + IA，非单页）
{模块}/references/{page}-logic.md ← 页面层级合同（本 skill 的 Gate C 产物）
{模块}/references/{page}.md       ← 页面规格（下游，含界面元素/数据字段）
{模块}/assets/…/*.html            ← 原型实现（frontend-design 之后）
```

页面级文档不承载跨页面逻辑——这是"文档很完整但层次仍表达不出来"的根因。

## 硬性纪律

1. **真源先行**：任何模型动作前，先按 [references/source-routing.md](references/source-routing.md) 确认采用哪些现行基准、哪些已退役；不引用存档/被取代文档作为依据。
2. **角色不是账号**：同一人可兼任角色，但模型必须保留各角色的目标、权限、知识和交接边界；不能用“一人多职”省略界面分层。
3. **领域模型不等于导航**：Program、FieldDefinition、Policy、Release 等可以是必要底座，但只有目标角色直接操控的对象才能成为其默认导航；其余通过自动编译、自然语言映射、只读摘要或专家入口承接。
4. **复杂度预算**：每个页面合同必须声明目标角色可承受的概念、配置步骤和技术证据；内部 ID、schema path、端口、校验码和策略表达式默认不进入非技术角色的主任务面。
5. **术语统一**：儿童端话术用儿童叙事词（无职业角色词/技术词）；成人端仍按角色使用业务语言，不以“成人后台语汇”为理由暴露工程术语；页面结构用「场景」，模块内导航用「tab」。
6. **产品态与评审证据分离**：可执行原型的默认产品框只显示 Default 状态；弹窗、抽屉、创建器和确认层必须由真实触发器打开并可关闭。为了覆盖状态而平铺的快照只能放在产品框外的评审区，或放在独立评审路由，不能伪装成默认产品 UI。
7. **减法原则**：每个 Gate 先问"删什么"，不堆砌入口与文档量；Mermaid 图按问题选型，不机械全画。
8. **边界声明**：页面合同必须写明"哪些内容明确不在本页"，同时写明“超出本角色边界后转交谁”。
9. **同源不等于同页编辑**：canonical 内容、课程用法与课堂 snapshot 必须各有 owner 和版本链；多个页面展示同一内容，不代表它们都能原地修改。
10. **反馈归属先于组件**：先判断一句反馈属于用户原话、用户动作、AI 回应、系统结果、短暂 Echo 还是等待安慰，再选择气泡、任务面或状态层；不能让用户回执替系统发言。
11. **呈现变体只改 projection**：A/B 或多端 renderer 必须共用 resolved manifest、事件 Guard、Commit 与恢复结果；不得以“另一个版本”为由复制业务 payload。
12. **评审帧仍要真实运行**：可以平铺场景用于审查，但帧内下一问、弹层、编辑与恢复必须由真实触发器出现；隐藏状态不能为了展示而默认展开。
13. **稳定角色与排班分离**：A/B/C、早班/晚班或“一人中途转后台”只可作为某次 staffing assignment，不得升格为 Actor、权限枚举或导航；Actor 按稳定责任建模，人员比例、责任组、有效时间与换岗交接另由 staffing snapshot / assignment 表达。
14. **Session 是定义与长期档案之间的运行层**：不同课程可产生不同 output port，但都应落到统一的运行层对象（示例命名：LearnerSession / ActivityRun / ValueRecord / Artifact）；跨课复用通过已发布 binding、提升与 lineage 表达，不为每节课建立专属控制台或“素材归档”体系。
