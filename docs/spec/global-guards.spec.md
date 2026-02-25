# Spec: 全局治理文档约束机制

**Feature:** global-guards
**Status:** Draft
**Date:** 2026-02-25

---

## 1. Goal

在 CLAUDE.md 中实现分层检查机制，让受保护目录规则真正全局生效。确保 AI 在处理任何请求前自动判断变更级别和受保护目录，防止用户绕过约束直接修改治理文档。

---

## 2. Non-Goals

- 不修改技能代码
- 不依赖系统级自动路由支持
- 不改变现有的 L1/L2/L3 判断标准
- 不影响非受保护目录的正常操作

---

## 3. Constraints

- 必须适用于任何项目（全局生效）
- 依赖 AI 遵守 CLAUDE.md 中的指令
- 受保护目录：`docs/spec/`、`docs/tasks/`、`docs/changes/`
- 保持现有 L1/L2/L3 流程不变

---

## 4. Interface Specification

### 4.1 入口检查接口

**触发时机：** 收到任何用户输入时

**输入：** 用户输入字符串

**处理逻辑：**

```python
def entry_check(user_input):
    # 解析意图
    intent = parse_intent(user_input)

    # 检查是否涉及受保护目录
    if mentions_protected_path(user_input):
        return {
            "allowed": False,
            "message": "检测到修改受保护目录。请使用 /classify <你的需求> 判断变更级别。"
        }

    # 检查是否创建受保护目录
    if is_creating_protected_doc(user_input):
        return {
            "allowed": False,
            "message": "治理文档只能由 L2/L3 流程创建。请使用 /l2 或 /l3。"
        }

    # 允许继续
    return {"allowed": True}
```

**输出：**
- 允许：继续处理
- 拒绝：返回错误消息

### 4.2 执行检查接口

**触发时机：** 准备执行 Edit/Write 操作时

**输入：** 目标文件路径

**处理逻辑：**

```python
def execution_check(file_path):
    # 检查路径是否包含受保护目录
    protected_dirs = ["docs/spec/", "docs/tasks/", "docs/changes/"]

    for protected_dir in protected_dirs:
        if protected_dir in file_path:
            return {
                "allowed": False,
                "message": f"目标路径 {file_path} 位于受保护目录。请使用正确的流程。"
            }

    return {"allowed": True}
```

**输出：**
- 允许：执行操作
- 拒绝：返回错误消息

### 4.3 受保护目录规则

**目录定义：**

| 目录 | 说明 | 创建权限 | 更新权限 | 删除权限 |
|------|------|----------|----------|----------|
| `docs/spec/` | 设计基准 | L3 | 禁止 | 禁止 |
| `docs/tasks/` | 执行记录 | L3 | execute (ER) | 禁止 |
| `docs/changes/` | 变更历史 | L2 | execute (ER) | 禁止 |

**注：** ER = Execution Record

---

## 5. Data Structures

### 5.1 检查结果结构

```json
{
  "allowed": boolean,
  "message": string,
  "suggested_action": string  // 可选：建议的操作
}
```

### 5.2 受保护目录配置

```yaml
protected_directories:
  - path: "docs/spec/"
    description: "设计基准"
    create_by: "l3"
    update_by: "none"
    delete_by: "none"

  - path: "docs/tasks/"
    description: "执行记录"
    create_by: "l3"
    update_by: "execute"
    update_scope: "execution_record_only"
    delete_by: "none"

  - path: "docs/changes/"
    description: "变更历史"
    create_by: "l2"
    update_by: "execute"
    update_scope: "execution_record_only"
    delete_by: "none"
```

---

## 6. Validation Plan

### 6.1 单元验证

| 测试用例 | 输入 | 预期输出 |
|----------|------|----------|
| 入口检查-修改 spec | "修改 docs/spec/xxx.md" | 拒绝，提示 /classify |
| 入口检查-创建 tasks | "创建 docs/tasks/test.md" | 拒绝，提示 /l3 |
| 入口检查-普通文件 | "修改 src/main.py" | 允许 |
| 入口检查-只读 | "查看 docs/spec/xxx.md" | 允许 |
| 执行检查-Edit spec | Edit docs/spec/xxx.md | 拒绝 |
| 执行检查-Write changes | Write docs/changes/xxx.md | 拒绝 |
| 执行检查-普通文件 | Edit src/main.py | 允许 |

### 6.2 集成验证

**场景 1：用户尝试直接修改受保护目录**
```
输入："更新 docs/spec/xxx.md"
预期：拒绝，提示使用 /classify 或 /l2
```

**场景 2：L2 完整流程**
```
1. /classify "修复 bug"
   输出：L2，使用 /l2

2. /l2 "修复 bug"
   输出：创建 docs/changes/xxx.md

3. /execute l2 docs/changes/xxx.md
   输出：执行成功，更新 Execution Record
```

**场景 3：L3 完整流程**
```
1. /classify "新增功能"
   输出：L3，使用 /l3

2. /l3 "新增功能"
   输出：创建 docs/spec/xxx.spec.md, docs/tasks/xxx.tasks.md

3. /execute l3 xxx:1
   输出：执行成功，更新 Execution Record + Timeline
```

### 6.3 回归验证

| 场景 | 验证方式 |
|------|----------|
| L1 流程不受影响 | "修改 README.md" → 允许 |
| 非受保护目录 | "修改 src/main.py" → 允许 |
| 只读操作 | "查看 docs/spec/xxx.md" → 允许 |

---

## 7. Error Handling

### 7.1 错误类型

| 错误类型 | 触发条件 | 处理方式 |
|----------|----------|----------|
| PROTECTED_PATH_MODIFY | 尝试修改受保护目录 | 拒绝，提示正确流程 |
| PROTECTED_PATH_CREATE | 尝试创建受保护文档 | 拒绝，提示 L2/L3 |
| PROTECTED_PATH_DELETE | 尝试删除受保护文档 | 拒绝，禁止操作 |
| AMBIGUOUS_PATH | 路径模糊，无法确定 | 询问具体路径 |

### 7.2 错误消息模板

```yaml
messages:
  protected_modify: |
    检测到修改受保护目录 {path}。

    受保护目录必须通过 L2/L3 流程操作。

    请使用：
    - /classify <你的需求> — 判断变更级别
    - /l2 <你的需求> — L2 变更
    - /l3 <你的需求> — L3 变更

  protected_create: |
    检测到创建受保护文档 {path}。

    治理文档只能由 L2/L3 流程创建。

    请使用：
    - /classify <你的需求> — 判断变更级别
    - /l2 <你的需求> — L2 变更
    - /l3 <你的需求> — L3 变更

  protected_delete: |
    检测到删除受保护文档 {path}。

    治理文档禁止删除。

  ambiguous_path: |
    路径 "{path}" 模糊。

    如果是受保护目录，需要走 L2/L3 流程：
    - docs/spec/ — 设计基准
    - docs/tasks/ — 执行记录
    - docs/changes/ — 变更历史

    请明确目标路径。
```

---

## 8. Acceptance Criteria

- [ ] CLAUDE.md 已更新，包含分层检查规则
- [ ] 用户直接修改受保护目录被拒绝
- [ ] 用户直接创建受保护目录被拒绝
- [ ] 用户直接删除受保护目录被拒绝
- [ ] L2 流程可以正常创建 docs/changes/
- [ ] L3 流程可以正常创建 docs/spec/ 和 docs/tasks/
- [ ] execute 可以正常更新 Execution Record
- [ ] 只读操作不受影响
- [ ] 非受保护目录不受影响
- [ ] 现有 L1 流程不受影响
- [ ] 错误消息清晰明确
- [ ] 回归测试全部通过
