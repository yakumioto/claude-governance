# Change Record: Execute Skill 增加文件修改范围限制

**Date:** 2026-02-27
**Type:** fix
**Topic:** execute-skill-file-scope-limit

---

## Motivation

当前 execute skill 缺少文件修改范围限制，可能导致在执行 fix/feat 时不小心修改了其他治理文档，污染历史记录。需要增加范围检查，确保只修改指定的文件。

---

## Scope

### 需要修改的文件

| 文件路径 | 修改内容 |
|----------|----------|
| `skills/execute/SKILL.md` | 增加文件修改范围检查逻辑 |

### 限制规则

**fix 执行：**
- 输入：`/execute fix docs/changes/xxx.md`
- 允许修改：仅限输入的 md 文件
- 禁止修改：其他任何文件

**feat 执行：**
- 输入：`/execute feat <feature>.tasks.md:N`
- 允许修改：该 task 关联的 spec/design 文件
- 禁止修改：其他任何治理文档

---

## Execution Plan

1. 读取当前 `skills/execute/SKILL.md`
2. 在 execute 流程中添加文件修改范围检查：
   - fix：验证目标文件是否为输入的文件
   - feat：提取 task 关联的 spec/design，验证修改范围
3. 超出范围时拒绝执行并提示错误

---

## Acceptance

- [x] execute skill 已增加文件修改范围检查
- [x] fix 执行时只允许修改输入的文件及非治理文档
- [x] feat 执行时只允许修改关联的 spec/design 文件及非治理文档
- [x] 超出范围时正确拒绝

---

## Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-27 | Claude | 在 fix 执行流程中添加文件修改范围检查步骤 |
| 2026-02-27 | Claude | 在 feat 执行流程中添加文件修改范围检查步骤 |
| 2026-02-27 | Claude | 更新 fix/feat 禁止操作，明确禁止修改其他治理文档 |
| 2026-02-27 | Claude | 在 fix 执行完成提示中添加 /project-memory 调用说明 |
