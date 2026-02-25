---
name: execute
description: 执行 L2 或 L3 的指定任务。L1 禁用。未按验收与记录流程完成不视为完成。
---

# 执行任务（L2 / L3）

用途：  
执行已定义的 L2 Change 或 L3 Task。

---

## Workflow Gate（适用范围）

执行前必须确认变更分级：

- L1 — Docs / Trivial Changes  
  禁止使用本 skill。  

- L2 — Tiny / Controlled Changes  
  必须基于 docs/changes/YYYY-MM-DD-<topic>.md 执行。  

- L3 — Full Feature Changes  
  必须基于 docs/tasks/<feature>.tasks.md 执行。  

若无法定位对应 Change 或 Task，禁止继续实现。

---

## 输入

- L2：Change 文件路径  
- L3：Task 编号 + feature 名称（或 tasks 文件路径）

---

# L2 执行流程（Tiny Change）

1. 打开对应 docs/changes/<file>.md
2. 定位：
   - Scope
   - Acceptance
   - Execution Record 区块
3. 若缺少 Acceptance 或 Execution Record 区块 → 停止

---

## L2 执行规则

1. 严格按 Scope 修改代码。
2. 禁止修改未声明模块。
3. 禁止新增依赖。
4. 禁止修改接口或默认值。
5. 禁止顺手重构。
6. 禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）。

---

## L2 执行步骤

1. 实现变更。
2. 运行 Acceptance 中的全部命令。
3. 将以下内容写入 Change 文件中的 Execution Record：
   - Commands Run
   - Result（PASS / FAIL）
   - Output Summary
4. 若 Result 为 FAIL，必须记录原因。

---

# L3 执行流程（Full Task）

1. 打开 docs/tasks/<feature>.tasks.md
2. 定位指定 Task 区块：
   - Scope
   - Acceptance
   - Execution Record
3. 若不存在 → 停止

---

## L3 执行规则

1. 只实现指定 Task。
2. 严格按照 Scope 修改代码。
3. 禁止提前实现后续 Tasks。
4. 不修改依赖文件（除非 Task 明确允许）。
5. 禁止顺手重构。

---

## L3 执行步骤

1. 实现代码。
2. 补充或更新测试。
3. 运行 Acceptance 中的所有命令。
4. 写入 Execution Record：
   - Commands Run
   - Result
   - Output Summary
5. 更新 Execution Timeline 表格。

---

## 失败处理（统一规则）

- 任一 Acceptance 失败 → Result 必须为 FAIL
- 禁止假通过
- 必须写明失败原因与建议

---

## 完成定义（Definition of Done）

L2：

- 代码完成
- Acceptance 全部执行
- Change 文件 Execution Record 已更新

L3：

- 代码完成
- 测试更新
- Acceptance 全部执行
- Task Execution Record 已写入
- Timeline 已更新

未写入 Execution Record 不视为完成。