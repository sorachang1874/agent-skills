# Review Gates 检查清单（A 模型 / B 行为 / C 页面合同）

> 三道门的逐项检查。通过形式：产物列给用户 review 并确认。**不过夜、不跳级**——Gate A 未过不写页面区块，Gate B 未过不画结构，Gate C 未过不碰 HTML。

## Gate A · 模型确认

- [ ] §1 真源登记表完成：全部来源有版本/地位标注，退役文档零引用（见 source-routing.md）
- [ ] 冲突与内部矛盾已登记（原样还原 or 待业务方确认），无擅自修复
- [ ] 核心场景一句话说清（谁在什么时刻要解决什么问题；新方向附 JTBD）
- [ ] In/Out of Scope 成文，含"明确不做"
- [ ] 角色清单完整，含主要/次要角色；“教师/运营/课程设计者/管理员”等含混词已拆成 Actor Card
- [ ] 每个 Actor Card 写明 Top task、频率、专业能力、不应掌握的概念、决策权、复杂度预算与专家升级路径
- [ ] Role–Task–Object、Role–Surface Access、Intent–System Translation 三张矩阵完成
- [ ] 同一账号兼任多个角色时，界面和任务仍按角色分层，没有用“一人多职”合并默认页面
- [ ] 多角色/多端功能已画 Service Blueprint，交接点与降级路径标注
- [ ] 对象模型（ORCA）完成：每个对象有标识、所有者、关系（方向+基数）、CTA（挂状态）、不变量
- [ ] 无"动作被画成状态"（如"再玩一次"）
- [ ] 派生数据标注"派生自"，未混入实体
- [ ] IA Thesis 已声明：主组织原则 + 例外及理由；全局入口 ≤ 稳定少数
- [ ] IA 先按 audience/job 划分工作台，再在工作台内部选择 object-first/task-first/lifecycle；领域对象未直接冒充目标用户导航
- [ ] 跨角色 / 跨端内容已拆 canonical owner、usage owner 与 session snapshot；没有两个默认编辑器争夺同一事实
- [ ] 多 renderer / 预览已登记共用的 resolved manifest 与版本，不以页面手写内容代替真源

## Gate B · 行为确认

- [ ] 每个有生命周期的对象有状态模型（属于对象，不属于页面）
- [ ] 转换表完整：From/Trigger/**Guard**/To/**Side effect**/Recovery 六列无空缺
- [ ] Top 1-3 任务流有 UC 卡（前置/主流程/分支/后置）
- [ ] Browse / View / Create / Edit / Confirm 的进入与返回已写入流程；按钮不是无目标的占位动作
- [ ] 跨端交接有触发事件与降级路径；时间窗标注"设计参考"
- [ ] 失败/超时/中断/恢复路径存在（无失败态=孩子的体验，但后台必须有恢复路径）
- [ ] 每个事件已区分用户原话、用户动作、AI 回应、系统结果、短 Echo 与等待反馈；非 Commit 动作不进入持久历史
- [ ] sequential flow 的下一步由 Guard / Trigger 产生，没有把所有问题、答案与恢复态同时铺开
- [ ] Mermaid 图按问题选型，与表格内容一致（表格为准）

## Gate C · 页面合同确认

- [ ] 页面 Audience Contract 完成：主要角色唯一，次要角色、使用情境、知识假设、复杂度预算、允许/禁止术语、升级出口明确
- [ ] 页面唯一任务一句话、可验证
- [ ] 页面主对象明确（映射 ORCA 模型）
- [ ] 第一/第二/第三注意力排序成表；全页仅一个主按钮
- [ ] 操作三级分明（主按钮 / 文字链 / 低频位）
- [ ] 导航角色矩阵无重复路径（或重复路径已声明收益）
- [ ] 视图状态矩阵覆盖 Default/Empty/Loading/Error/Interrupted/Permission
- [ ] 每个组件能映射回对象/状态/动作；映射不上的已删或回 Gate A
- [ ] 每个添加 / 编辑 / 选择 / 复制 / 预览 / 确认 CTA 有 Action ID、触发器、承载表面、Commit、Cancel、Unsaved / Recovery 与焦点返回
- [ ] 创建、加入、复制、派生四类身份变化用词和副作用明确，没有共用一个模糊“添加 / 保存”动作
- [ ] 默认产品框未永久展开弹窗、抽屉、创建器或状态画廊；评审证据位于产品框外或独立评审路由
- [ ] 预览登记精确 source revision / perspective，并与真实消费端共用 resolved manifest / renderer contract
- [ ] `Source → Snapshot → Runtime → Projection → Evidence` 链可追踪；A/B 或多端只改变 presentation，不复制业务状态
- [ ] 用户回执只记录用户真实说过或做过的事；系统结果、安慰与评审说明使用各自表面
- [ ] **"明确不在本页"清单成文**
- [ ] 非技术角色的默认任务面不以 schema path、内部 ID、端口、Binding、checksum、策略表达式作为前置知识
- [ ] 错误与阻断先用角色任务语言说明“哪里未满足、去哪里修”；技术码仅作次级证据或专家详情
- [ ] 术语检查：儿童端用儿童话术；成人端按角色使用业务语言，不以“成人后台”作为暴露工程术语的理由；「场景/tab」术语统一
- [ ] Role–Surface Access Matrix 中不属于本页主要角色的对象已移除、只读或转专家入口
- [ ] 评审五问过（page-hierarchy-contract.md §3）

## 通过后的移交

- Gate C 通过 → 产物 = `references/models/`（跨页面）+ `references/{page}-logic.md`（页面合同）
- 调 **frontend-design** 做视觉与 HTML 实现；
- HTML 完成后按团队的 HTML 验收清单 + 本 skill 的合同逐项对照（组件映射表逐条核对）。
- 验收必须操作真实触发器并检查默认关闭、顺序、取消/Escape/焦点返回、响应式几何、内容换行、控制台与普通版/standalone；“构建成功”不等于通过。

## 何时必须重开 Gate A

- 页面对象关系自洽，但目标角色无法理解或不愿完成配置；
- 一个“课程设计者/运营/管理员”同时承担教学、数据、策略、审核或发布等互斥专业任务；
- 只有隐藏按钮，没有改变页面主体与导航，导致无权限角色仍落入专家工作台；
- 领域模型被直接翻译成导航，用户意图没有任务语言层。

重开时可保留已验证对象/状态，但旧 IA 与 Gate C 合同失效，直到角色/服务切片重新确认。
