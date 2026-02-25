---
name: brainstorming
description: 在功能级或行为变更前必须使用。负责需求探索、设计确认、生成 Spec 与 Tasks。未经设计批准禁止进入实现。
---

# Workflow Gate（变更分级）

在启动 Brainstorming 前，必须先进行变更分类。

变更分级：

L1 — Docs / Trivial Changes  
L2 — Tiny / Controlled Changes  
L3 — Feature / Behavior Changes  

---

## L1 — Docs / Trivial Changes

满足全部条件：

- 仅修改 README / docs / *.md / 注释
- 不改变运行时行为
- 不改变接口
- 不改变默认值或语义承诺
- 不新增依赖

处理方式：

- 禁止使用 brainstorming
- 禁止生成 design / spec / tasks
- 直接修改并提交

---

## L2 — Tiny / Controlled Changes

适用于：

- 小 bug 修复
- typo 修复
- 单文件逻辑修正
- 内部重构（无接口变更）

处理方式：

- 禁止生成 design
- 禁止生成 spec
- 仅生成 Tiny Change
- 可直接进入 execute

---

## L3 — Feature / Behavior Changes

满足任一条件必须使用 Brainstorming：

- 新功能
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块影响
- 架构调整
- 安全 / 加密 / 账务相关逻辑

---

# Brainstorming Execution Flows

## L2 — Tiny / Controlled Changes

（以下流程仅适用于 L2）

### 目标（L2）

对小范围、低风险变更进行最小化澄清与审计记录，生成 Tiny Change 后直接进入执行阶段。

生成：

- docs/changes/YYYY-MM-DD-<topic>.md

然后提示进入 /execute 阶段。

---

### 约束（L2 边界）

L2 必须满足：

- 不改变公共接口（API/CLI/配置项对外契约）
- 不改变默认值或配置语义
- 不新增或升级依赖（go.mod / package.json 等）
- 不涉及跨模块架构调整
- 影响范围可在单个或少量文件内闭合

若对边界存在不确定，必须升级为 L3。

---

### HARD GATE（L2）

在 Change Record 写入并确认前，禁止：

- 编写任何代码
- 调用 execute
- 修改项目文件

---

### 阶段 1：澄清与确认（L2）

必须按顺序执行：

1. 简述本次变更动机（1–3 句）
2. 列出影响范围（文件/模块）
3. 明确成功标准（如何算修好）
4. 给出验证方式（Acceptance 命令）

若发现涉及接口/语义/依赖/架构 → 立即停止并升级为 L3。

---

### 阶段 2：生成 Change Record（L2）

变更确认后生成：

docs/changes/YYYY-MM-DD-<topic>.md

优先参考：

docs/templates/change.md

若不存在，则参考：

~/.claude/skills/brainstorming/change.md

若两者都不存在，则按内置结构生成。

---

### 结束状态（L2）

提示用户：

“Change Record 已生成。  
请使用 /execute 执行变更。”

## L3 — Feature / Behavior Changes

（以下流程仅适用于 L3）

### 目标（L3）

通过协作对话将想法转化为完整设计，并在批准后自动生成：

- docs/plans/YYYY-MM-DD-<topic>-design.md
- docs/spec/<feature>.spec.md
- docs/tasks/<feature>.tasks.md

然后提示进入 /execute 阶段。

---

### HARD GATE（L3）

在设计获得用户批准前，禁止：

- 编写任何代码
- 调用 execute
- 修改项目文件
- 生成 Spec 或 Tasks

---

### 阶段 1：探索与澄清（L3）

必须按顺序执行：

1. 探索当前项目结构与上下文
2. 每次只提一个问题
3. 明确：
   - 目标
   - 非目标
   - 约束
   - 成功标准

---

### 阶段 2：提出设计方案（L3）

- 提供 2–3 个方案
- 分析 trade-offs
- 给出推荐方案
- 分段展示设计（架构 / 模块 / 数据流 / 错误处理 / 测试策略）
- 每部分都需要用户确认

---

### 阶段 3：生成设计文档（L3）

设计批准后：

生成：

docs/plans/YYYY-MM-DD-<topic>-design.md

优先参考：

docs/templates/design.md

若不存在，则参考：

~/.claude/skills/brainstorming/design.md

若两者都不存在，则按内置结构生成。

---

### 阶段 4：生成 Spec（L3）

基于设计文档生成：

docs/spec/<feature>.spec.md

优先参考：

docs/templates/spec.md

若不存在，则参考：

~/.claude/skills/brainstorming/spec.md

若两者都不存在，则按内置结构生成。

要求：

- 包含 Goal / Non-Goals / Constraints
- 明确接口与数据结构
- 包含 Validation Plan
- 禁止实现代码

---

### 阶段 5：生成 Tasks（L3）

基于 Spec 生成：

docs/tasks/<feature>.tasks.md

优先参考：

docs/templates/tasks.md

若不存在，则参考：

~/.claude/skills/brainstorming/tasks.md

若两者都不存在，则按内置结构生成。

要求：

- 每个 Task 粒度 1–3 小时
- 每个 Task 必须包含：
  - Scope
  - Acceptance（可执行命令）
  - Execution Record 区块
- 最后一个 Task 必须包含全量回归命令
- 在文件顶部创建 Execution Timeline 表格

---

### 结束状态（L3）

提示用户：

“设计、Spec 与 Tasks 已生成。  
请使用 /execute 1 开始执行。”