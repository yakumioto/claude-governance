# Design: 变更分级系统解耦

**Date:** 2026-02-25
**Status:** Draft
**Owner:** Claude

---

## 1. Goal

解耦变更分级系统与技能的耦合关系，实现：
- 分级定义独立于技能系统
- 技能职责单一、可复用
- 便于未来扩展新的变更级别

---

## 2. Non-Goals

- 不改变 L1/L2/L3 的触发条件和判断逻辑
- 不改变产出物路径和格式
- 不改变用户视角的使用流程

---

## 3. Current Architecture Analysis

### 3.1 当前耦合点

| 耦合点 | 当前状态 | 问题 |
|--------|----------|------|
| 分级定义重复 | CLAUDE.md 和 brainstorming/SKILL.md 各有一份 | 修改需同步两处 |
| 入口规则硬编码 | "L2/L3 必须使用 /brainstorming" 写在两处 | 改入口需改多处 |
| 流程与级别绑定 | 执行流程直接写在级别定义中 | 扩展困难 |
| 技能职责混杂 | brainstorming 承担分级判断+需求探索+设计 | 职责过重 |

### 3.2 当前流程

```
用户需求
    ↓
直接对话（L1）或 /brainstorming（L2/L3）
    ↓
[brainstorming 内部判断级别]
    ↓
L2: explore → change-record
L3: explore → design → spec → tasks
    ↓
/execute（内部判断 L2/L3）
```

---

## 4. New Architecture Design

### 4.1 整体架构

```
【入口层】
┌─────────────────┐
│   /classify     │ ← 分级判断 + 路由提示
└────────┬────────┘
         │
         ├─────────→ /l1 (提示直接对话)
         ├─────────→ /l2 (L2 编排)
         └─────────→ /l3 (L3 编排)

【编排层】
┌─────────────────┐       ┌─────────────────┐
│       /l2       │       │       /l3       │
│  explore →      │       │  explore →      │
│  doc-gen:change │       │  design →       │
└─────────────────┘       │  doc-gen:spec/  │
                          │  tasks          │
                          └─────────────────┘

【核心技能层】
┌─────────────────┐       ┌─────────────────┐
│    /explore     │       │    /design      │
│  需求探索与澄清  │       │   设计方案讨论   │
└─────────────────┘       └─────────────────┘
                          ┌─────────────────┐
                          │    /doc-gen     │
                          │  文档生成器      │
                          │  - change       │
                          │  - spec         │
                          │  - tasks        │
                          │  - design       │
                          └─────────────────┘

【执行层】
┌─────────────────────────────────────────┐
│            /execute                      │
│  - l2 <file>  → 执行 L2 Change           │
│  - l3 <task-id> → 执行 L3 Task           │
└─────────────────────────────────────────┘
```

### 4.2 技能清单

| 技能 | 职责 | 参数/输出 |
|------|------|-----------|
| `/classify` | 分级判断，路由到 /l1 /l2 /l3 | 无 |
| `/l1` | 记录 L1 约束，提示直接对话 | 无 |
| `/l2` | 编排 L2 流程 | 生成 Change Record |
| `/l3` | 编排 L3 流程 | 生成 Design + Spec + Tasks |
| `/explore` | 需求探索与澄清 | 返回需求摘要 |
| `/design` | 设计方案讨论 | 返回设计方案 |
| `/doc-gen change` | 生成 Change Record | `docs/changes/YYYY-MM-DD-<topic>.md` |
| `/doc-gen spec` | 生成 Spec | `docs/spec/<feature>.spec.md` |
| `/doc-gen tasks` | 生成 Tasks | `docs/tasks/<feature>.tasks.md` |
| `/doc-gen design` | 生成设计文档 | `docs/plans/YYYY-MM-DD-<topic>-design.md` |
| `/execute l2 <file>` | 执行 L2 Change | 更新 Execution Record |
| `/execute l3 <task-id>` | 执行 L3 Task | 更新 Execution Record |

### 4.3 目录结构

```
skills/
├── classify/
│   └── SKILL.md
├── l1/
│   └── SKILL.md
├── l2/
│   └── SKILL.md
├── l3/
│   └── SKILL.md
├── explore/
│   └── SKILL.md
├── design/
│   └── SKILL.md
├── doc-gen/
│   ├── SKILL.md
│   └── templates/
│       ├── change.md
│       ├── spec.md
│       ├── tasks.md
│       └── design.md
└── execute/
    └── SKILL.md
```

---

## 5. Data Flow

### 5.1 L2 流程

```
用户: /classify "修复 xxx bug"
    ↓
classify: 判断为 L2，提示使用 /l2
    ↓
用户: /l2 "修复 xxx bug"
    ↓
l2: 调用 /explore
    ↓
explore: 返回需求摘要
    ↓
l2: 调用 /doc-gen change
    ↓
doc-gen: 生成 docs/changes/2026-02-25-fix-xxx.md
    ↓
l2: 提示使用 /execute l2 docs/changes/2026-02-25-fix-xxx.md
```

### 5.2 L3 流程

```
用户: /classify "新增 xxx 功能"
    ↓
classify: 判断为 L3，提示使用 /l3
    ↓
用户: /l3 "新增 xxx 功能"
    ↓
l3: 调用 /explore
    ↓
explore: 返回需求摘要
    ↓
l3: 调用 /design
    ↓
design: 讨论设计方案，用户确认
    ↓
l3: 调用 /doc-gen design
    ↓
l3: 调用 /doc-gen spec
    ↓
l3: 调用 /doc-gen tasks
    ↓
l3: 提示使用 /execute l3 xxx-feature:1
```

### 5.3 技能调用方式

由于当前可能不支持技能直接调用，采用"给出命令让用户执行"的方式：

```
[l2 内部]
/explore 完成
↓
"需求已确认，正在生成 Change Record..."
↓
[内部调用 /doc-gen change 或直接生成]
↓
"Change Record 已生成：docs/changes/xxx.md"
↓
"请使用 /execute l2 docs/changes/xxx.md 执行变更"
```

---

## 6. Migration Plan

### 6.1 新建技能

| 顺序 | 技能 | 来源 |
|------|------|------|
| 1 | classify | 新建（分级判断逻辑） |
| 2 | l1 | 新建（L1 约束规则） |
| 3 | explore | 从 brainstorming 拆分 |
| 4 | design | 从 brainstorming 拆分 |
| 5 | doc-gen | 从 brainstorming 拆分（含模板） |
| 6 | l2 | 新建（编排层） |
| 7 | l3 | 新建（编排层） |
| 8 | execute | 从 execute 拆分重构 |

### 6.2 删除文件

- `skills/brainstorming/SKILL.md`
- `skills/execute/SKILL.md`

### 6.3 模板迁移

```
skills/brainstorming/change.md → skills/doc-gen/templates/change.md
skills/brainstorming/spec.md → skills/doc-gen/templates/spec.md
skills/brainstorming/tasks.md → skills/doc-gen/templates/tasks.md
```

### 6.4 CLAUDE.md 更新

- 简化变更分级说明（引用技能系统）
- 保留用户手册部分
- 更新入口规则说明

---

## 7. Constraints

- 当前逻辑保持不变
- 产出物路径和格式不变
- 技能间调用可能不支持，需给出命令让用户执行

---

## 8. Validation Plan

### 8.1 功能验证

| 场景 | 验证方式 |
|------|----------|
| L1 变更 | 直接对话，确认正常修改 |
| L2 变更 | /classify → /l2 → /execute l2，确认流程完整 |
| L3 变更 | /classify → /l3 → /execute l3，确认流程完整 |
| 独立技能调用 | 直接调用 /explore、/doc-gen 等，确认功能正常 |

### 8.2 产出物验证

| 产出物 | 验证方式 |
|--------|----------|
| Change Record | 格式与原 `change.md` 一致 |
| Spec | 格式与原 `spec.md` 一致 |
| Tasks | 格式与原 `tasks.md` 一致 |
| Design Doc | 格式与原 `design.md` 一致 |

### 8.3 回归测试

- 执行一个完整的 L2 变更
- 执行一个完整的 L3 变更
- 确认产出物路径、格式、内容符合预期
