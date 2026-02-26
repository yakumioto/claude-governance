# Memory Skill 参数简化 Spec

## Goal

简化 memory skill 的参数设计，改为直接接受文件路径，提升易用性和灵活性，支持 fix 流程完成后更新文件索引。

---

## Non-Goals

- 不改变 memory.md 的结构和内容
- 不改变无参数时的首次初始化行为

---

## Constraints

- 支持传入多个文件路径
- 根据路径自动识别文件类型（spec/tasks/changes/plans）
- 保持与现有技能体系的一致性
- 只修改指定的文件

---

## 接口定义

### memory skill

**新的调用方式：**

```bash
# 单个文件
/memory docs/spec/xxx.spec.md
/memory docs/tasks/xxx.tasks.md
/memory docs/changes/xxx.md

# 多个文件
/memory docs/spec/xxx.spec.md docs/tasks/xxx.tasks.md
/memory docs/spec/xxx.spec.md docs/tasks/xxx.tasks.md docs/plans/xxx-design.md

# 首次初始化（无参数）
/memory
```

**参数说明：**

| 参数 | 必需 | 类型 | 说明 |
|------|:----:|------|------|
| 文件路径 | 否 | string | 任意数量的文件路径 |

---

## 数据结构

### 文件类型映射

| 路径包含 | 类型 | 提取信息 | 更新位置 |
|----------|------|----------|----------|
| `docs/spec/` | spec | 项目概述（Goal 部分） | 项目概述章节 |
| `docs/tasks/` | tasks | 已完成功能（Execution Record） | 已完成功能列表 |
| `docs/changes/` | changes | 变更摘要（Scope/Execution Record） | 文件索引 Summary 列 |
| `docs/plans/` | design | 架构要点、关键决策 | 架构要点、关键决策记录 |

---

## 行为规范

### 首次初始化（无参数）

1. 扫描 `docs/spec/`、`docs/tasks/`、`docs/changes/` 目录
2. 提取所有文件的信息
3. 生成初始的 `docs/memories/memory.md`

### 增量更新（有参数）

1. 读取现有 `docs/memories/memory.md`（如存在）
2. 遍历传入的文件路径
3. 识别每个文件的类型
4. 从传入文件中提取信息：
   - spec: 项目概述
   - tasks: 已完成功能
   - changes: 变更摘要
   - design: 架构要点、关键决策
5. 更新 `memory.md` 各章节内容
6. 更新文件索引表格

### 文件类型识别

根据路径中的目录名称自动识别：

```python
def identify_file_type(path):
    if '/spec/' in path:
        return 'spec'
    elif '/tasks/' in path:
        return 'tasks'
    elif '/changes/' in path:
        return 'changes'
    elif '/plans/' in path:
        return 'design'
    else:
        return 'unknown'
```

---

## 错误处理

### 错误类型

| 错误类型 | 触发条件 | 处理方式 |
|----------|----------|----------|
| FILE_NOT_FOUND | 传入的文件不存在 | 报错并终止 |
| UNKNOWN_TYPE | 无法识别文件类型 | 警告但继续，仅更新文件索引 |
| WRITE_FAILED | memory.md 写入失败 | 报错并保留原文件（如有） |

---

## Validation Plan

| 验证项 | 验证方式 |
|--------|----------|
| 单个 spec 文件 | 调用 `/memory docs/spec/xxx.spec.md` 验证项目概述更新 |
| 单个 tasks 文件 | 调用 `/memory docs/tasks/xxx.tasks.md` 验证已完成功能更新 |
| 单个 changes 文件 | 调用 `/memory docs/changes/xxx.md` 验证文件索引更新 |
| 单个 design 文件 | 调用 `/memory docs/plans/xxx-design.md` 验证架构/决策更新 |
| 多个文件组合 | 调用 `/memory docs/spec/xxx.spec.md docs/tasks/xxx.tasks.md` 验证多文件处理 |
| 无参数 | 调用 `/memory` 验证首次初始化正常 |
| 类型识别 | 传入各种路径验证类型识别正确 |
