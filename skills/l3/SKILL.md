---
name: l3
description: L3 变更流程编排。执行需求探索、设计方案讨论、文档生成。
---

# L3 变更流程编排（L3）

本技能负责编排 L3 变更的完整流程。

---

## L3 变更适用范围

L3 变更满足以下任一条件：
- 新功能开发
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块改动
- 架构调整
- 安全 / 加密 / 账务相关变更

---

## 执行流程

### 阶段 1：需求探索

首先执行需求探索：

```
请先执行：/explore <需求描述>
```

等待 `/explore` 完成并获得需求摘要。

**目标：** 明确目标、非目标、约束、成功标准

---

### 阶段 2：设计方案讨论

需求确认后，执行设计方案讨论：

"需求已确认，正在进行设计方案讨论..."

使用 `/design` 进行：
- 提供 2-3 个方案
- 分析 trade-offs
- 给出推荐方案
- 分段展示设计（架构/模块/数据流/错误处理/测试策略）

每部分都需要用户确认。

---

### 阶段 3：生成设计文档

设计批准后，生成设计文档：

"设计方案已确认，正在生成设计文档..."

生成 `docs/plans/YYYY-MM-DD-<topic>-design.md`

---

### 阶段 4：生成 Spec

基于设计文档，生成 Spec：

"正在生成 Spec..."

生成 `docs/spec/<feature>.spec.md`

**要求：**
- 包含 Goal / Non-Goals / Constraints
- 明确接口与数据结构
- 包含 Validation Plan
- 禁止实现代码

---

### 阶段 5：生成 Tasks

基于 Spec，生成 Tasks：

"正在生成 Tasks..."

生成 `docs/tasks/<feature>.tasks.md`

**要求：**
- 每个 Task 粒度 1-3 小时
- 每个 Task 必须包含 Scope、Acceptance、Execution Record
- 最后一个 Task 必须包含全量回归命令
- 在文件顶部创建 Execution Timeline 表格

---

### 阶段 6：完成提示

所有文档生成后，提示用户：

```
设计、Spec 与 Tasks 已生成：
- docs/plans/YYYY-MM-DD-<topic>-design.md
- docs/spec/<feature>.spec.md
- docs/tasks/<feature>.tasks.md

请使用 /execute l3 <feature>:1 开始执行。
```

---

## 禁止操作

在设计获得用户批准前，禁止：
- 编写任何代码
- 调用 execute
- 修改项目文件
- 生成 Spec 或 Tasks

---

## L3 执行规则

执行变更时必须遵守：
1. 只实现指定 Task
2. 严格按照 Scope 修改代码
3. 禁止提前实现后续 Tasks
4. 不修改依赖文件（除非 Task 明确允许）
5. 禁止顺手重构
