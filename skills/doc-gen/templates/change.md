# Change: {{title}}

**创建日期:** {{date}}

---

## Scope

**影响范围：**

{{scope}}

**允许修改：**
- 上述范围内的文件

**禁止修改：**
- 未在上述范围声明的文件
- 公共接口（除非 Scope 明确允许）
- 依赖文件（go.mod / package.json 等）
- 治理文档（docs/spec/、docs/tasks/）

---

## Behavior Impact

- **影响程度：** none / minimal

---

## Acceptance

{{acceptance}}

---

**注意：** Acceptance 中应包含：

```markdown
- 调用 /memory 更新项目记忆（文件索引）
  ```bash
  /memory docs/changes/<date>-<topic>.md
  ```
```

**说明：** fix 完成后会创建 changes 文件，需要更新 memory.md 中的文件索引。

---

## Execution Record

**Executor:**
**Date:**

**Commands Run:**
-

**Result:**
PASS / FAIL

**Output Summary:**
