# Context Memory Spec

## Goal

为治理框架增加上下文记忆机制，解决项目过大导致的上下文不足问题。通过生成项目记忆文件，使新会话能够快速恢复项目上下文。

---

## Non-Goals

- 不在 tasks 文件内生成总结
- 不创建单独的每个 task 记忆文件
- 不改变现有 fix/feat 流程

---

## Constraints

- 只在最后一个 task 的 Acceptance 中更新
- 目录名为 `docs/memories`
- 文件名为小写 `memory.md`
- memory.md 可被更新（非追加），支持项目重构场景

---

## 接口定义

### memory skill

**调用方式：**

```bash
# 增量更新（最后一个 task 的 Acceptance 中）
/memory --tasks <path> --spec <path> [--design <path>]

# 首次初始化
/memory
```

**参数说明：**

| 参数 | 必需 | 类型 | 说明 |
|------|:----:|------|------|
| `--spec` | 否 | string | 本次涉及的 spec 文件路径 |
| `--tasks` | 是 | string | 本次涉及的 tasks 文件路径 |
| `--design` | 否 | string | 本次涉及的 design 文件路径 |

---

## 数据结构

### memory.md 格式

```markdown
# Project Memory

## 项目概述
<从 spec 的 Goal 部分提取>

## 架构要点
<从 design 文件的架构设计部分提取>

## 已完成功能列表
- <feature 1>: <description>
- <feature 2>: <description>
...

## 关键决策记录
- <decision 1>: <rationale>
- <decision 2>: <rationale>
...

## 文件索引
| 文件路径 | 类型 | Summary |
|----------|------|---------|
| docs/spec/xxx.spec.md | spec | ... |
| docs/tasks/xxx.tasks.md | tasks | ... |
| docs/plans/xxx-design.md | design | ... |
```

---

## 行为规范

### 首次初始化（无参数）

1. 扫描 `docs/spec/`、`docs/tasks/`、`docs/changes/` 目录
2. 提取所有文件的信息
3. 生成初始的 `docs/memories/memory.md`

### 增量更新（有参数）

1. 读取现有 `docs/memories/memory.md`（如存在）
2. 解析传入的 spec/tasks/design 文件
3. 从传入文件中提取信息：
   - 项目概述（spec Goal）
   - 架构要点（design 架构设计）
   - 已完成功能（tasks Execution Record）
   - 关键决策（design 关键决策）
4. 更新 `memory.md` 各章节内容
5. 更新文件索引表格（添加或更新传入文件的条目）

### 最后一个 task Acceptance

最后一个 task 的 Acceptance 中应包含：

```markdown
- 调用 /memory 更新项目记忆
  ```bash
  /memory --tasks docs/tasks/<feature>.tasks.md --spec docs/spec/<feature>.spec.md
  ```
```

---

## 目录结构

```
~/.claude/
├── skills/
│   └── memory/           # memory skill
└── docs/
    └── memories/         # 记忆目录
        └── memory.md     # 项目记忆文件
```

---

## Validation Plan

| 验证项 | 验证方式 |
|--------|----------|
| 首次初始化 | 调用 `/memory` 生成初始 memory.md |
| 增量更新 | 调用 `/memory --tasks ... --spec ...` 验证更新正确 |
| 文件索引 | 检查文件索引表格数据完整 |
| 格式规范 | 检查 Markdown 渲染正常 |
| 集成测试 | 最后一个 task Acceptance 调用验证 |
