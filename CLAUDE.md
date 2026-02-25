# 默认工作流（Workflow）

- 所有回复在在保证专业的前提下尽可能使用中文回复。
- 所有变更在进入默认工作流前，必须先进行变更分级。

变更分级规则：

L1 — Docs / Trivial Changes  
L2 — Tiny / Controlled Changes  
L3 — Full Feature Changes  

L2 必须执行：

Change -> Execute

L3 必须执行完整流程：

Plan → Spec → Tasks → Execute

---

## 变更分级入口规则（Entry Gate）

- **L1** — 直接对话，无需调用技能
- **L2** — 必须使用 `/brainstorming` 技能作为入口
- **L3** — 必须使用 `/brainstorming` 技能作为入口

**禁止在直接对话模式下执行 L2/L3 变更。**

如果用户在直接对话中提出 L2/L3 需求，必须拒绝执行并引导用户：

```
这是一个 L2/L3 变更。请使用 /brainstorming <需求描述> 启动标准流程。
```

---

## 变更分级说明（Workflow Gate）

### L1 — Docs / Trivial Changes

满足以下全部条件即可进入 L1：

- 仅修改文档或注释（README.md、docs/guide/**、*.md、代码注释）
- 禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）
- 不改变代码运行行为
- 不改变公共接口
- 不改变默认值或配置语义
- 不新增或修改依赖

执行流程：

Edit → Preview

要求：

- 不生成 Spec
- 不生成 Tasks
- 不生成 Execution Record
- 提交说明必须包含：

docs: <summary>

What:
Why:
Risk:
Verify:

Done 定义：

- Markdown 渲染正常
- 示例命令验证通过（如有）

---

### L2 — Tiny / Controlled Changes

适用于：

- 小 bug 修复
- typo 修复
- 单文件逻辑修正
- 内部实现优化（不改变接口与语义）

执行流程：

Change → Record

## 受保护目录（禁止修改）

- docs/spec/ — 设计基准，不应追溯修改
- docs/tasks/ — 执行记录，不应追溯修改
- docs/changes/ — 变更历史，仅可追加新文件

要求：

- 不生成 Spec
- 不生成完整 Tasks
- 必须生成 Tiny Change Record

记录路径：

docs/changes/YYYYMMDD-<topic>.md

记录模板：

# Change: <title>

Date:
Owner:

Scope:
- files:

Behavior Impact:
- none / minimal

Acceptance:
- <command>

Result:
PASS / FAIL

Output Summary:

Done 定义：

- 代码完成
- 验收命令执行通过
- 记录文件存在

---

### L3 — Full Feature Changes

触发条件（命中任意一条必须走 L3）：

- 新功能开发
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块改动
- 架构调整
- 安全 / 加密 / 账务相关变更

执行流程：

Plan → Spec → Tasks → Execute

---

## 1. Spec 阶段

使用 Plan 模式生成：

docs/spec/<feature>.spec.md

要求：

- 明确目标与非目标
- 描述设计方案
- 定义接口与数据结构
- 列出边界情况
- 提供全局验证方案（Validation Plan）

限制：

- Spec 阶段禁止生成实现代码
- 禁止拆解 Tasks
- Spec 文件按功能独立创建，禁止覆盖或合并既有 Spec

---

## 2. Tasks 阶段

基于 Spec 生成：

docs/tasks/<feature>.tasks.md

要求：

- 拆分为多个可独立完成的任务
- 每个任务粒度 1–3 小时
- 每个任务必须包含可执行验收命令（Acceptance）
- Acceptance 必须为可直接执行的命令
- 最后一个 Task 必须包含全量回归验证命令
- Tasks 文件与 Spec 一一对应，禁止复用或覆盖其他功能 Tasks

---

## 3. Execute 阶段

执行规则：

- 一次只允许实现一个 Task
- 严格按照 Scope 修改代码
- 禁止提前实现未执行的 Tasks
- 完成后必须运行 Acceptance 命令
- 必须在 Tasks 文档中记录 PASS / FAIL 结果

---

## 4. 变更护栏

默认禁止：

- 大范围重构（除非 Spec 明确要求）
- 修改依赖文件（go.mod / package.json 等）
- 修改公共接口而不更新测试

---

## Task 完成定义（Definition of Done）

L3 定义：

- 代码完成
- 测试补充或更新
- 验收命令已执行
- 结果已记录
- 每个 Task 完成后必须填写 Execution Record（包含 Commands Run、Result、Output Summary）
- 未填写 Execution Record 不视为完成
- 建议在 tasks 文件顶部维护 Execution Timeline 表格，并在每次 Task 完成后更新对应行