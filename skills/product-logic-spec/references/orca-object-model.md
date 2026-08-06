# ORCA 对象模型（OOUX）

> 领域对象层的核心方法（源自 OOUX/ORCA）：把产品结构拆为 **Objects（对象）/ Relationships（关系）/ Calls-to-Action（动作）/ Attributes（属性）**，在视觉与交互设计之前建立稳定的共享语言。
> 专治本仓库的高发问题："作品到底属于课程、场次还是学生？""进度是实体还是派生视图？""再玩一次是状态还是动作？""线索属于资产、课程结构还是课堂执行？"

## 1. 四要素

| 要素 | 回答 | 规则 |
|------|------|------|
| **Object** | 系统里有哪些"东西" | 名词；必须有唯一标识与所有者 |
| **Relationship** | 谁拥有谁、谁依赖谁 | 写明基数（1:1 / 1:N / N:M）与方向 |
| **CTA** | 用户能对每个对象做什么 | 动词；挂在对象的状态上（某状态下可用的动作） |
| **Attribute** | 对象有哪些字段 | 数据/元数据分开；派生属性必须标注"派生自" |

**铁律**：动作不是状态。"再玩一次"不是第五种状态，而是 `completed` 状态下可执行的 CTA；执行后产生什么副作用，写在转换表里（见 state-and-flow.md）。

## 2. 对象卡模板

```
### Object: {名称}
- 标识：{唯一键，如 studentId + courseId}
- 所有者：{谁拥有它}
- 定义：{一句话}
- Relationships：
  - 属于 {对象}（基数）
  - 拥有 {对象}（基数）
- CTAs（按状态）：
  - {状态A}: {动作} / {动作}
  - {状态B}: {动作}
- Attributes：
  - {字段}: {说明}
- Invariants（不变量，任何情况下都必须成立）：
  - {规则 1}
  - {规则 2}
- Permissions：{谁可见/谁可操作}
```

## 3. 核心对象示例（某儿童课程产品实战固化）

```
STUDENT ||--o{ COURSE_PROGRESS : owns
SEASON  ||--o{ COURSE          : contains
COURSE  ||--o{ COURSE_PROGRESS : is_tracked_by
COURSE_PROGRESS ||--o{ SESSION : accumulates
SESSION ||--o{ ARTIFACT        : produces
STUDENT ||--o{ ACHIEVEMENT     : earns
```

### Object: CourseProgress（学生学习进度 · 单课粒度）
- 标识：studentId + courseId
- 状态：locked / available / in_progress / completed
- 关系：属于 Student；对应 Course；拥有 0..N Session
- CTAs：locked=无 ｜ available=start ｜ in_progress=continue / replay / viewArtifacts ｜ completed=replay / viewArtifacts
- Invariants：① replay 新建 Session；② replay 不删除旧 Session；③ replay 不把 completed 改回 in_progress；④ 历史 Artifact 持续累加不覆盖

### Object: Session（场次）
- 定义：一次完整的课程体验（从进入到完成/中断）
- 关系：属于某 CourseProgress；产生 0..N Artifact
- 作用：作品挂场次——"重玩后作品累加、不覆盖"由此天然成立；与后台"开课后场次锁定快照"同构

### Object: Artifact（作品）
- 关系：属于 Session（原始归属）；可按 Course/Student 聚合**展示**（聚合是视图，不是归属）
- 类型：image（图片）/ video-short（短视频）/ video-long（长视频）

### 派生视图（不是对象）
- **学习进度** = CourseProgress 集合的派生统计（已完成数/进行中数/作品数）——不做成实体，只做只读看板；
- **徽章** = 由 CourseProgress/Session 事件触发的 Achievement。

## 4. 另一个已固化范例：内容的三层分离

| 层 | 对象 | 职责 |
|----|------|------|
| 资产层 | Clue（线索） | 内容、版本、状态机、校验 |
| 结构层 | LessonSlot（课程槽位） | 绑定哪条线索（结构层不编辑内容） |
| 执行层 | Session（场次） | 阶段推进时生效，快照锁定 |

——当新对象出现时，先问它属于哪一层、归谁维护，再进模型。

## 5. 建模检查清单

- [ ] 每个对象有唯一标识与所有者
- [ ] 每个动作挂在对象状态上，没有被画成状态
- [ ] 派生数据标注"派生自"，没有混进实体表
- [ ] 每个关系有方向与基数
- [ ] 每条不变量是一句可以当测试断言的判断句
