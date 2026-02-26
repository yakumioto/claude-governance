# Memory Skill 参数简化 Tasks

**相关 Spec：** `docs/spec/memory-params.spec.md`
**创建日期:** 2026-02-26

---

## Execution Timeline

| Task ID | Status | Date |
|:--------|:------:|------:|
| memory-params:1 | completed | 2026-02-26 |
| memory-params:2 | completed | 2026-02-26 |
| memory-params:3 | completed | 2026-02-26 |
| memory-params:4 | completed | 2026-02-26 |
| memory-params:5 | completed | 2026-02-26 |
| memory-params:6 | completed | 2026-02-26 |

---

## Task 1: 更新 memory skill

### Scope

更新 `skills/memory/skill.md`，将参数从 `--spec`、`--tasks`、`--design` 改为直接接受文件路径。

**修改文件：**
- `skills/memory/skill.md`

**修改内容：**
- 移除 `--spec`、`--tasks`、`--design` 参数说明
- 添加直接文件路径参数说明
- 添加文件类型识别逻辑（spec/tasks/changes/plans）
- 更新调用示例

### Acceptance

- [ ] memory skill 参数格式已更新
- [ ] 文件类型识别逻辑已添加
- [ ] 调用示例已更新
- [ ] 无参数时的首次初始化行为保持不变

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 memory skill：改为直接文件路径参数，添加文件类型识别逻辑（spec/tasks/changes/plans），更新调用示例 |

---

## Task 2: 更新 doc-gen tasks 模板

### Scope

更新 `doc-gen` 的 tasks 模板，使用新的 memory 调用格式。

**修改文件：**
- `skills/doc-gen/templates/tasks.md`

**修改内容：**
- 更新 Acceptance 中的 memory 调用说明
- 改为直接文件路径格式

### Acceptance

- [ ] tasks 模板已更新
- [ ] memory 调用格式为直接文件路径

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 tasks 模板：memory 调用格式改为直接文件路径 `/memory docs/spec/<feature>.spec.md docs/tasks/<feature>.tasks.md` |

---

## Task 3: 更新 doc-gen change 模板

### Scope

更新 `doc-gen` 的 change 模板，添加 memory 调用说明。

**修改文件：**
- `skills/doc-gen/templates/change.md`

**修改内容：**
- 在 Acceptance 部分添加 memory 调用说明
- 格式为直接文件路径

### Acceptance

- [ ] change 模板已添加 memory 调用说明
- [ ] memory 调用格式为直接文件路径

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 change 模板：添加 memory 调用说明，格式为 `/memory docs/changes/<date>-<topic>.md` |

---

## Task 4: 更新文档

### Scope

更新 README.md 和 CLAUDE.md，反映新的 memory 调用格式。

**修改文件：**
- `README.md`
- `CLAUDE.md`

**修改内容：**
- 更新 memory skill 的调用方式说明
- 更新项目记忆章节的示例

### Acceptance

- [ ] README.md 已更新
- [ ] CLAUDE.md 已更新
- [ ] 调用方式说明准确

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 README.md：memory 调用方式改为直接文件路径格式；更新 CLAUDE.md：memory skill 说明 |

---

## Task 5: 测试验证

### Scope

测试新的 memory skill 参数格式。

**测试场景：**
1. 单个 spec 文件
2. 单个 tasks 文件
3. 单个 changes 文件
4. 单个 design 文件
5. 多个文件组合
6. 无参数（首次初始化）

### Acceptance

- [ ] 单个 spec 文件测试通过
- [ ] 单个 tasks 文件测试通过
- [ ] 单个 changes 文件测试通过
- [ ] 单个 design 文件测试通过
- [ ] 多个文件组合测试通过
- [ ] 无参数测试通过

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 验证 memory skill：参数解析、文件类型识别、调用示例全部正确 |

---

## Task 6: 全量回归

### Scope

运行完整的回归测试，确保新功能不影响现有流程。

**验证项：**
- /fix 流程正常
- /feat 流程正常
- /execute 正常执行
- /memory 正常工作
- memory.md 生成正确

### Acceptance

- [ ] /fix 流程正常
- [ ] /feat 流程正常
- [ ] /execute 正常执行
- [ ] /memory 正常工作
- [ ] memory.md 生成正确

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 全量回归测试通过。/fix 流程正常、/feat 流程正常、/execute 正常执行、/memory 正常工作、memory.md 生成正确 |

---

**Total Tasks:** 6
