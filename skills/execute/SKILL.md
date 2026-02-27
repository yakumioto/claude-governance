---
name: execute
description: 执行器。根据参数类型执行 fix Change 或 feat Task。docs 禁用。
---

# 执行器（Execute）

本技能用于执行已定义的 fix Change 或 feat Task。

---

## 用法

```
/execute fix <file>       # 执行 fix Change Record
/execute feat <task-id>    # 执行 feat Task
```

**docs 禁用：** docs 级别变更无需使用此技能。

---

## Workflow Gate（适用范围）

执行前必须确认变更级别：

| 级别 | 处理方式 |
|------|----------|
| docs | 禁止使用本技能 |
| fix | 必须基于 `docs/changes/YYYY-MM-DD-<topic>.md` |
| feat | 必须基于 `docs/tasks/<feature>.tasks.md` |

若无法定位对应 Change 或 Task，禁止继续实现。

---

## fix 执行流程

### 输入格式

```
/execute fix docs/changes/YYYY-MM-DD-<topic>.md
```

### 执行步骤

1. **打开 Change Record**
   - 读取 `docs/changes/<file>.md`
   - 定位：Scope、Acceptance、Execution Record 区块
   - 若缺少任一区块 → 停止

2. **文件修改范围检查**
   - 输入文件即为允许修改的唯一文件
   - 验证修改是否超出输入文件范围
   - 若超出范围 → 拒绝执行并提示错误

3. **执行变更**
   - 严格按照 Scope 修改代码
   - 仅允许修改输入的治理文件 md 及非治理文档文件
   - 禁止修改其他任何治理文档（docs/spec/、docs/plans/、docs/tasks/、docs/changes/ 中非输入的文件）
   - 禁止修改未声明模块
   - 禁止新增依赖
   - 禁止修改接口或默认值
   - 禁止顺手重构

4. **运行验收**
   - 运行 Acceptance 中的全部命令

5. **更新记录**
   - 将以下内容写入 Change 文件中的 Execution Record：
     - Commands Run
     - Result（PASS / FAIL）
     - Output Summary
   - 若 Result 为 FAIL，必须记录原因

6. **完成提示**
   - 提示用户调用 `/project-memory` 更新项目记忆：
     ```
     建议调用 /project-memory 更新项目记忆：
     ```bash
     /project-memory docs/changes/<date>-<topic>.md
     ```
     ```

### fix 禁止操作

- 修改输入文件之外的任何治理文档（docs/spec/、docs/plans/、docs/tasks/、docs/changes/）
- 超出 Scope 的修改
- 新增或修改依赖

---

## feat 执行流程

### 输入格式

```
/execute feat <feature>:<task-number>
例如：/execute feat governance-decoupling:1
```

### 执行步骤

1. **打开 Tasks 文件**
   - 读取 `docs/tasks/<feature>.tasks.md`
   - 定位指定 Task 区块：Scope、Acceptance、Execution Record
   - 若不存在 → 停止

2. **文件修改范围检查**
   - 提取该 Task 关联的 spec/design 文件（从 Scope 或描述中识别）
   - 验证修改是否在关联的 spec/design 范围内
   - 若修改其他治理文档 → 拒绝执行并提示错误

3. **执行变更**
   - 只实现指定 Task
   - 严格按照 Scope 修改代码
   - 仅允许修改关联的 spec/design 文件及非治理文档文件
   - 禁止修改其他任何治理文档（docs/spec/、docs/plans/、docs/tasks/、docs/changes/ 中非关联的文件）
   - 禁止提前实现后续 Tasks
   - 不修改依赖文件（除非 Task 明确允许）
   - 禁止顺手重构

4. **补充测试**
   - 补充或更新测试

5. **运行验收**
   - 运行 Acceptance 中的所有命令

6. **更新记录**
   - 写入 Execution Record：
     - Commands Run
     - Result
     - Output Summary
   - 更新 Execution Timeline 表格

6. **完成提示**
   - 如果是最后一个 task，提示用户调用 `/project-memory` 更新项目记忆：
     ```
     最后一个 task 已完成。建议调用 /project-memory 更新项目记忆：
     ```bash
     /project-memory docs/tasks/<feature>.tasks.md docs/spec/<feature>.spec.md
     ```
     ```

### feat 禁止操作

- 修改关联文件之外的任何治理文档（docs/spec/、docs/plans/、docs/tasks/、docs/changes/）
- 超出 Scope 的修改
- 提前实现后续 Tasks
- 顺手重构

---

## 失败处理（统一规则）

- 任一 Acceptance 失败 → Result 必须为 FAIL
- 禁止假通过
- 必须写明失败原因与建议

---

## 完成定义（Definition of Done）

### fix

- 代码完成
- Acceptance 全部执行
- Change 文件 Execution Record 已更新

### feat

- 代码完成
- 测试更新
- Acceptance 全部执行
- Task Execution Record 已写入
- Timeline 已更新

**未写入 Execution Record 不视为完成。**
