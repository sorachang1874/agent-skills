# 真源、投影与 Runtime 合同

> 当同一业务内容被教师台、治理台、学生端、预览页或 A/B renderer 同时使用时，Gate A–C 必须补充本合同。它解决的不是“页面放什么字段”，而是：谁拥有事实、哪个版本被消费、同一事件在不同表面上怎样表达，以及评审原型如何保持真实。

## 1. Single source of truth 不是“只有一个页面”

Single source of truth 指每类事实只有一个权威所有者和一条明确的版本链，不等于所有角色共用一个编辑器。

| 层 | 回答 | 例子 | 禁止 |
|---|---|---|---|
| 业务规则真源 | 流程、顺序、不变量是什么 | rundown、批准的课程规则 | renderer 自己改流程 |
| 规范内容真源 | 正文、媒体、变体由谁维护 | ResourceVersion | 教师用法页同时改规范正文 |
| 用法 / 绑定 | 当前课程怎样使用规范内容 | 环节、对象、教师提示、选用版本 | 把用法变成第二份规范内容 |
| Session snapshot | 本次课堂锁定了什么 | 主角、四格故事、已批准资源版本 | 运行时自动漂移到最新草稿 |
| Runtime state | 孩子刚做了什么、系统处理到哪里 | 已提交原话、选择、生成结果 | 用静态说明伪造已发生事件 |
| Presentation projection | 不同角色 / renderer 如何看同一事实 | A 气泡、B 创作足迹、教师摘要 | 各 renderer 维护第二份 payload |
| Review evidence | 如何证明合同成立 | fixture、截图、浏览器断言 | 把评审说明写进产品 UI |

先填一张 `Source → Snapshot → Runtime → Projection → Evidence` 链；任何一段没有 owner、revision 或验收，都不能声称“同源”。

## 2. Canonical / Usage / Snapshot 的版本规则

同一内容在多个控制台出现时，至少登记：

| 问题 | 必答 |
|---|---|
| Canonical owner | 谁能修改规范内容，谁只能引用或建议修改 |
| Usage owner | 谁决定内容用在哪个环节、给谁看、如何引导 |
| Selected revision | 当前用法锁定哪个已批准版本 |
| Local change | 本地调整是覆盖、派生草稿还是新资源版本 |
| Upgrade | 新版本批准后是否手动升级；禁止默认自动漂移 |
| Runtime snapshot | 哪个时点锁定课堂输入；重进或中断怎样恢复 |

默认规则：教师选择规范内容；不修改则引用其批准版本。若正文发生实质变化，创建可追踪的派生草稿 / 新版本，并显式决定是否替换当前用法。治理台维护版本、来源、权利与发布；教师台维护教学用法，不形成第二个无版本正文真源。

## 3. 事件语义所有权

一次交互常同时产生“用户动作、系统结果、角色回应”，但它们不能挤进同一种组件。

| 信息类型 | 语义所有者 | 持久性 | 合适表面 |
|---|---|---|---|
| User utterance | 用户 | 持久 | 用户气泡 / 原话记录 |
| User committed action | 用户 | 持久 | 动作回执 / 创作足迹 |
| Agent utterance | AI / 角色 | 持久或当前轮 | AI 气泡 / 中央当前问句 |
| System outcome | 系统 | 持久状态 | 任务面、结果卡、状态栏 |
| Action echo | 本地交互 | 短暂 | toast / anchored echo；不进历史 |
| Comfort progress | 系统处理的儿童化投影 | 仅等待中 | thinking / 显影；完成即消失 |
| Review annotation | 评审者 | 不属于产品 | 产品框外 / 独立评审路由 |

判断句：如果一句话的主语不是“用户说了 / 选择了 / 点了”，它通常不应出现在用户回执中。系统不能借用户气泡宣布“照片已修改”“不用勉强满意”或“已进入下一课”。

## 4. Commit、Echo 与结果必须拆开

| 动作 | 是否 Commit | 允许持久回执 | 系统结果 |
|---|---:|---|---|
| 预览、放大、重播、点错、听提示 | 否 | 否；只给短 Echo | 不改变业务对象 |
| 语音提交、明确拍板、确认修改 | 是 | 记录用户真实动作 / 原话 | 另在结果表面说明改变了什么 |
| 取消、关闭、Escape | 否 | 否 | 恢复原对象、草稿与焦点语境 |

不要用“回执”掩盖业务提交。Action ID 仍必须声明 Guard、Commit、Side effect、Recovery；回执只是其投影。

## 5. 多 renderer / A-B 变体合同

PresentationVariant 只能改变信息组织和视觉语言，不能偷偷复制业务状态。

```text
Business rule / resolved manifest
              ↓
       shared action core
          ↙       ↘
   renderer A   renderer B
```

必须共享：对象身份、fixture / snapshot revision、事件顺序、Guard、Commit、副作用、恢复结果和最终产出。

允许不同：当前信息的空间位置、历史是否保留、对话或沉浸式表达、动效和局部组件。

测试同一动作时，应同时断言：A/B 改变的是同一对象、得到相同结果、只在 projection 上不同。不能仅比较两个页面“看起来都完整”。

## 6. 评审帧不等于把状态全部平铺

静态课程原型可以平铺多个场景帧，便于对照 rundown；但每个帧内部仍应像真实产品：

- 默认只出现当前状态；下一问、弹层、修正结果由真实触发器出现。
- sequential flow 用状态迁移演示，不把三个问题、三次回答和所有恢复态一次铺开。
- Modal、drawer、wide overlay 默认 Closed；能取消、Escape、恢复焦点。
- 产品框外可以有场景跳转和评审控件，但不得冒充孩子 / 教师真实操作。

## 7. 预览必须消费真实来源

教师或治理页面中的“学生端预览”不能手写一张近似卡片。先登记：

1. 精确 canonical / snapshot revision；
2. 与真实学生端共用的 renderer 或 component contract；
3. 全局运行控件（如唯一语音入口）与内容资源的边界；
4. 年龄、声学、渠道变体怎样解析；
5. 来源更新后的 stale / rebuild 行为。

预览中只显示孩子真实会看到和听到的内容。产品说明、教师提示、系统字段和“这里将会……”类实现注释留在预览外。

## 8. 反馈进入下一轮的 Delta 流程

用户指出局部问题时，先判断它属于哪一层：

| 现象 | 应重开 / 更新 |
|---|---|
| 角色看不懂、两个控制台争夺同一编辑权 | Gate A：角色、owner、真源 |
| 顺序、提交、恢复或跨端交接不对 | Gate B：状态与事件 |
| 所有内容平铺、反馈放错组件、触发方式缺失 | Gate C：表面与投影 |
| 对象/行为正确，但对齐、颜色、溢出或动效错误 | frontend-design + 视觉验收 |

每次 Delta 依次完成：来源登记 → 归一化规则 → 同类影响搜索 → 更新 ledger / 合同 → 实现 → 普通版与派生构建 → 浏览器回归。不要只修被点名的一个 DOM 节点。

## 9. Gate C / 实现移交检查

- [ ] 每类事实有唯一 canonical owner，usage 与 snapshot 不冒充 canonical。
- [ ] `Source → Snapshot → Runtime → Projection → Evidence` 可逐段追踪。
- [ ] 每个事件区分用户动作、系统结果、AI 回应、短 Echo 与等待反馈。
- [ ] 非 Commit 动作不写入持久历史；Commit 说明改变了哪个对象。
- [ ] 多 renderer 共用 manifest / action core，差异只在 projection。
- [ ] sequential flow 由触发器推进；隐藏层默认关闭并能安全返回。
- [ ] 预览复用真实消费端来源，不渲染产品说明。
- [ ] 验收同时覆盖语义、顺序、几何、内容尺寸、焦点、控制台、响应式和派生构建。
