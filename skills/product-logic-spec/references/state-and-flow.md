# 状态与任务流（State & Flow）

> 行为状态层（Gate B）的产物：状态模型 + 转换表 + 关键任务流。回答"当前是什么状态、什么触发变化、副作用与兜底"。

## 1. 状态模型（按对象建，不按页面建）

状态属于**对象**，不属于页面。每个有生命周期的对象一张：

```
CourseProgress: locked → available → in_progress → completed
Session:        in_progress → completed / interrupted
Clue:           草稿 → 试讲中 → 启用 → 停用（历史保留）
```

## 2. 转换表（核心交付，比图更重要）

| From | Trigger | Guard（前置条件） | To | Side effect（副作用） | Recovery（失败/中断恢复） |
|------|---------|-------------------|----|----------------------|---------------------------|
| available | 用户点"开始玩" | 课程已启用 | in_progress | 新建 Session（in_progress） | 中断：Session 保留，回来"接着玩" |
| in_progress | 完成课程 | — | completed | Session→completed；触发 Artifact 生成队列；解锁下一课 available | 生成未就绪走降级链（孩子无感） |
| completed | 用户点"再玩一次" | 确认弹窗通过 | completed（**不变**） | 新建 Session（N+1）；路由到课程开头 | 历史 Session/Artifact 全部保留 |
| in_progress | 用户点"再玩一次" | 确认弹窗通过 | in_progress | 新建 Session（N+1）；旧 Session 标记 interrupted | 旧 Session 的作品保留 |

**Guard 与副作用是必填项**——只画箭头不写这两列，等于没建模型。

## 3. 关键任务流（Top-task UC）

只写 Top 1-3 任务，不穷尽。模板：

```
### UC-01 重新体验已完成课程
- Actor：孩子（学生端）
- Preconditions：CourseProgress(课1)=completed；课程处于启用状态
- Main Flow：
  1. 课程 tab → 课 1 卡 → 点「↻ 再玩一次」
  2. 系统弹确认弹窗（"作品都在，不会丢"）
  3. 确认 → 新建 Session → 进入课程开头
- Alternate Flow：取消 → 回到课程 tab，无变化
- Postconditions：Session 数 +1；CourseProgress 仍为 completed；历史作品可见且未变
```

## 4. Mermaid 使用纪律（不机械全画）

| 问题 | 用什么图 |
|------|----------|
| 有限状态对象的生命周期 | `stateDiagram-v2` |
| 对象关系/归属 | `erDiagram` 或对象关系简图 |
| 跨端/跨角色交接的时序 | `sequenceDiagram` |
| 用户任务的步骤分支 | `flowchart` |

规则：
1. **按问题选型**，不是每个用例三张图；
2. 图是转换表/对象卡的**可视化副本**，内容必须与表一致（表为准）；
3. 图超过 ~12 个节点就拆分或降级为表。

## 5. 多端交接检查（与 service-blueprint 联动）

凡跨端交接（如"收敛确认 → 后台预生成"、"拍板 → 生成队列"），在转换表中单独标注触发事件与降级路径；时间窗一律标注"设计参考，非调度计划"。
