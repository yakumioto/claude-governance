# Spec: 变更分级系统解耦

**Feature:** governance-decoupling
**Status:** Draft
**Date:** 2026-02-25

---

## 1. Goal

解耦变更分级系统与技能的耦合关系，建立清晰的分层架构：
- 分级定义独立于技能系统
- 技能职责单一、可复用
- 便于未来扩展新的变更级别

---

## 2. Non-Goals

- 不改变 L1/L2/L3 的触发条件和判断逻辑
- 不改变产出物路径和格式
- 不改变用户视角的使用流程体验

---

## 3. Constraints

- 技能间直接调用可能不支持，需通过"给出命令让用户执行"的方式实现编排
- 必须保持当前 L1/L2/L3 的所有约束和限制
- 产出物路径和格式必须与现有系统兼容

---

## 4. Interface Specification

### 4.1 技能接口

#### /classify

**输入：** 用户需求描述

**输出：** 分级结果 + 路由提示

```
输出格式：
"这是一个 L2 变更。
适用范围：[范围描述]
请使用 /l2 <需求描述> 继续流程。"
```

---

#### /l1

**输入：** 无（或任意输入）

**输出：** L1 约束说明 + 操作提示

```
输出格式：
"L1 变更无需使用技能。
L1 约束：
- 仅修改文档或注释
- 不改变代码运行行为
- ...

请直接描述你需要的修改。"
```

---

#### /l2

**输入：** 需求描述

**流程：**
1. 调用 /explore 进行需求澄清
2. 生成 Change Record（内部调用 /doc-gen change）

**输出：** Change Record 文件路径 + 下一步提示

```
输出格式：
"需求已确认：[摘要]
Change Record 已生成：docs/changes/YYYY-MM-DD-<topic>.md
请使用 /execute l2 docs/changes/YYYY-MM-DD-<topic>.md 执行变更。"
```

---

#### /l3

**输入：** 需求描述

**流程：**
1. 调用 /explore 进行需求澄清
2. 调用 /design 进行设计讨论
3. 生成设计文档（调用 /doc-gen design）
4. 生成 Spec（调用 /doc-gen spec）
5. 生成 Tasks（调用 /doc-gen tasks）

**输出：** 产出物路径 + 下一步提示

```
输出格式：
"设计、Spec 与 Tasks 已生成：
- docs/plans/YYYY-MM-DD-<topic>-design.md
- docs/spec/<feature>.spec.md
- docs/tasks/<feature>.tasks.md

请使用 /execute l3 <feature>:1 开始执行。"
```

---

#### /explore

**输入：** 需求描述

**输出：** 需求摘要

```
包含字段：
- 目标
- 非目标
- 约束
- 成功标准
```

---

#### /design

**输入：** 需求摘要（来自 /explore）

**流程：**
- 提供方案选项
- 分析 trade-offs
- 等待用户确认

**输出：** 设计方案

```
包含字段：
- 推荐方案
- 架构说明
- 模块划分
- 数据流
- 错误处理
- 测试策略
```

---

#### /doc-gen

**输入：**
- 类型参数：change | spec | tasks | design
- 对应的内容数据

**输出：** 文档文件路径

```
输出格式：
"<类型> 已生成：<文件路径>"
```

---

#### /execute

**输入：**
- 类型参数：l2 | l3
- L2: `<file>` (Change Record 路径)
- L3: `<task-id>` (任务编号)

**L2 执行流程：**
1. 读取 Change Record
2. 检查 Scope、Acceptance、Execution Record 区块
3. 执行变更
4. 运行 Acceptance 命令
5. 更新 Execution Record

**L3 执行流程：**
1. 读取 Tasks 文件
2. 定位指定 Task
3. 检查 Scope、Acceptance、Execution Record 区块
4. 执行变更
5. 更新测试
6. 运行 Acceptance 命令
7. 更新 Execution Record 和 Timeline

---

### 4.2 数据结构

#### Change Record (docs/changes/YYYY-MM-DD-<topic>.md)

```markdown
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
```

#### Spec (docs/spec/<feature>.spec.md)

```markdown
# Spec: <feature>

Goal:
Non-Goals:
Constraints:

Interfaces:
...

Validation Plan:
...
```

#### Tasks (docs/tasks/<feature>.tasks.md)

```markdown
# Tasks: <feature>

## Execution Timeline

| Task | Status | Date | Summary |
|------|--------|------|---------|

## Task 1

**Scope:**
...

**Acceptance:**
- <command>

**Execution Record:**
- Commands Run:
- Result:
- Output Summary:
```

---

## 5. Template Definitions

### 5.1 模板位置

```
skills/doc-gen/templates/
├── change.md
├── spec.md
├── tasks.md
└── design.md
```

### 5.2 模板格式

每个模板包含：
- 固定的文档结构
- 变量占位符（如 `{{title}}`, `{{date}}` 等）
- 示例内容

---

## 6. File Structure

### 6.1 新建文件

```
skills/
├── classify/SKILL.md
├── l1/SKILL.md
├── l2/SKILL.md
├── l3/SKILL.md
├── explore/SKILL.md
├── design/SKILL.md
├── doc-gen/
│   ├── SKILL.md
│   └── templates/
│       ├── change.md
│       ├── spec.md
│       ├── tasks.md
│       └── design.md
└── execute/SKILL.md
```

### 6.2 删除文件

```
skills/
├── brainstorming/SKILL.md  [删除]
└── execute/SKILL.md        [删除，重建]
```

### 6.3 更新文件

```
CLAUDE.md  [更新：简化分级说明，引用技能系统]
```

---

## 7. Error Handling

### 7.1 技能调用失败

当子技能调用失败时：
- 返回明确的错误信息
- 提示用户重试或手动调用子技能

```
示例：
"调用 /doc-gen 失败。
请手动执行：/doc-gen change <参数>"
```

### 7.2 参数验证

- /execute: 检查文件路径是否存在
- /doc-gen: 检查类型参数是否有效
- /l2 /l3: 检查必需的前置条件

### 7.3 级别边界检查

- /l2: 在 explore 阶段检查是否超出 L2 边界，如超出则提示升级到 L3
- /l3: 确认所有 L3 触发条件满足

---

## 8. Validation Plan

### 8.1 单元验证（每个技能）

| 技能 | 验证方式 |
|------|----------|
| /classify | 测试各类需求描述，确认分级正确 |
| /l1 | 确认输出 L1 约束说明 |
| /l2 | 完整走通 L2 流程，确认产出物正确 |
| /l3 | 完整走通 L3 流程，确认产出物正确 |
| /explore | 确认需求澄清完整 |
| /design | 确认设计讨论有效 |
| /doc-gen | 确认各类文档生成格式正确 |
| /execute l2 | 确认 L2 执行流程正确 |
| /execute l3 | 确认 L3 执行流程正确 |

### 8.2 集成验证（完整流程）

| 场景 | 流程 | 验收标准 |
|------|------|----------|
| L1 变更 | 直接对话 | 文档正常修改，无需技能 |
| L2 变更 | /classify → /l2 → /execute l2 | Change Record 生成且执行成功 |
| L3 变更 | /classify → /l3 → /execute l3 | Design/Spec/Tasks 生成且执行成功 |

### 8.3 产出物验证

| 产出物 | 验证内容 |
|--------|----------|
| Change Record | 格式与原 change.md 一致，路径正确 |
| Spec | 格式与原 spec.md 一致，包含所有必需字段 |
| Tasks | 格式与原 tasks.md 一致，包含 Timeline 和 Execution Record |
| Design Doc | 格式正确，包含设计要素 |

### 8.4 兼容性验证

- 确认现有 docs/spec/、docs/tasks/、docs/changes/ 目录结构不变
- 确认现有产出物可被新系统读取和执行
- 确认 CLAUDE.md 更新后用户仍能理解工作流

---

## 9. Acceptance Criteria

- [ ] 所有新技能文件已创建
- [ ] 所有旧技能文件已删除
- [ ] 模板文件已迁移到 doc-gen/templates/
- [ ] CLAUDE.md 已更新
- [ ] /classify 能正确判断 L1/L2/L3
- [ ] /l2 能完整走通并生成 Change Record
- [ ] /l3 能完整走通并生成 Design/Spec/Tasks
- [ ] /execute l2 能正确执行 L2 Change
- [ ] /execute l3 能正确执行 L3 Task
- [ ] 产出物格式与原系统一致
- [ ] 完整的 L2 回归测试通过
- [ ] 完整的 L3 回归测试通过
