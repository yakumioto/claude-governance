# Memory Skill

为治理框架生成/更新项目记忆文件，解决项目过大导致的上下文不足问题。

---

## 用法

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

---

## 参数说明

| 参数 | 必需 | 类型 | 说明 |
|------|:----:|------|------|
| 文件路径 | 否 | string | 任意数量的文件路径 |

---

## 执行流程

### 1. 参数解析

解析传入的参数：
- 所有非选项参数作为文件路径
- 无参数时执行首次初始化逻辑

### 2. 文件类型识别

根据路径中的目录名称自动识别文件类型：

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

### 3. 目录检查

检查 `docs/memories/` 目录是否存在：
- 不存在时自动创建
- 存在时继续执行

---

### 4. 文件读取

**首次初始化（无参数）：**
- 扫描 `docs/spec/` 目录
- 扫描 `docs/tasks/` 目录
- 扫描 `docs/changes/` 目录
- 扫描 `docs/plans/` 目录

**增量更新（有参数）：**
- 读取现有 `docs/memories/memory.md`（如存在）
- 遍历传入的文件路径
- 读取每个传入的文件
- 验证传入的文件是否存在，不存在时报错并终止

---

### 5. 信息提取

根据文件类型提取信息：

| 文件类型 | 提取内容 | 更新位置 |
|----------|----------|----------|
| spec | 项目概述（Goal 部分） | 项目概述章节 |
| tasks | 已完成功能（Execution Record） | 已完成功能列表 |
| changes | 变更摘要（Scope/Execution Record） | 文件索引 Summary 列 |
| design | 架构要点、关键决策 | 架构要点、关键决策记录 |
| unknown | 警告但继续，仅更新文件索引 | 文件索引表格 |

---

### 6. 生成/更新 memory.md

**memory.md 结构：**

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
| docs/changes/xxx.md | changes | ... |
| docs/plans/xxx-design.md | design | ... |
```

**更新策略：**
- 根据传入的文件重新生成对应部分
- 更新文件索引表格（添加或更新传入文件的条目）

---

## 错误处理

| 错误场景 | 处理方式 |
|----------|----------|
| `docs/memories/` 目录不存在 | 自动创建目录 |
| `memory.md` 文件不存在 | 生成新文件 |
| 传入的文件不存在 | 报错并终止：<br>`错误：文件不存在<br>文件：<path><br>建议：检查文件路径是否正确` |
| 传入的文件解析失败 | 报错并终止：<br>`错误：文件解析失败<br>文件：<path><br>建议：检查文件格式是否正确` |
| 无法识别文件类型 | 警告但继续，仅更新文件索引 |
| memory.md 读取失败 | 报错但尝试生成新文件 |
| memory.md 写入失败 | 报错并保留原文件（如有）：<br>`错误：写入失败<br>文件：docs/memories/memory.md<br>建议：检查目录权限` |
| 无参数且目录无文件 | 生成空的 memory.md 模板 |

---

## 使用示例

### 示例 1：最后一个 task Acceptance 中调用（feat）

```markdown
### Acceptance

- [ ] 代码完成
- [ ] 测试通过
- [ ] 调用 /memory 更新项目记忆
  ```bash
  /memory docs/spec/<feature>.spec.md docs/tasks/<feature>.tasks.md
  ```
```

### 示例 2：fix 完成后调用

```markdown
### Acceptance

- [ ] 代码完成
- [ ] 验收通过
- [ ] 调用 /memory 更新项目记忆（文件索引）
  ```bash
  /memory docs/changes/<date>-<topic>.md
  ```
```

### 示例 3：首次初始化

```bash
/memory
```

---

## 注意事项

- 文件路径直接作为参数，无需 `--` 前缀
- 支持传入多个文件路径
- 根据路径中的目录名称自动识别文件类型
- 无参数时执行首次初始化，扫描所有文件
- memory.md 是**更新**而非追加，支持项目重构场景
- changes 文件只更新文件索引的 Summary 列
