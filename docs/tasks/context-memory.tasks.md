# Context Memory Tasks

## Execution Timeline

| Task ID | Status | Date |
|:--------|:------:|------:|
| context-memory:1 | completed | 2026-02-26 |
| context-memory:2 | completed | 2026-02-26 |
| context-memory:3 | completed | 2026-02-26 |
| context-memory:4 | completed | 2026-02-26 |
| context-memory:5 | completed | 2026-02-26 |
| context-memory:6 | completed | 2026-02-26 |
| context-memory:7 | completed | 2026-02-26 |

---

## Task 1: 创建 memory skill 文件

### Scope

创建 `skills/memory/` 目录及 skill 文件。

**创建文件：**
- `skills/memory/skill.md` - memory skill 定义

**文件内容要求：**
- 支持参数解析：`--spec`、`--tasks`、`--design`
- 实现首次初始化逻辑（无参数时扫描所有文件）
- 实现增量更新逻辑（有参数时更新对应部分）
- 生成/更新 `docs/memories/memory.md`
- 包含错误处理逻辑

### Acceptance

- [ ] `skills/memory/` 目录存在
- [ ] `skills/memory/skill.md` 文件存在且格式正确
- [ ] skill 文件包含参数解析逻辑
- [ ] skill 文件包含错误处理说明

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 创建 `skills/memory/` 目录及 `skill.md` 文件，包含参数解析、执行流程、错误处理说明 |

---

## Task 2: 实现 memory skill 逻辑

### Scope

实现 memory skill 的核心逻辑。

**实现功能：**
1. 参数解析（`--spec`、`--tasks`、`--design`）
2. 读取现有 `memory.md`（如存在）
3. 解析传入的 spec/tasks/design 文件
4. 提取信息：
   - 项目概述（spec Goal）
   - 架构要点（design 架构设计）
   - 已完成功能（tasks Execution Record）
   - 关键决策（design 关键决策）
5. 生成/更新 `memory.md`
6. 更新文件索引表格

**错误处理：**
- 目录不存在时自动创建
- 文件不存在时生成新文件
- 传入文件不存在时报错并终止

### Acceptance

- [ ] 参数解析正确
- [ ] 能够读取现有 memory.md
- [ ] 能够从 spec/tasks/design 提取信息
- [ ] 能够生成符合格式的 memory.md
- [ ] 错误处理符合规范

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | skill.md 已包含完整逻辑：参数解析、文件读取、信息提取、memory.md 生成、错误处理。技能通过文档化指导 Claude 执行，验证通过。 |

---

## Task 3: 更新 doc-gen skill 模板

### Scope

更新 `doc-gen` skill，使其生成的 tasks 模板在最后一个 task 的 Acceptance 中包含 memory skill 调用。

**修改文件：**
- `skills/doc-gen/skill.md`

**修改内容：**
- 在 tasks 模板的最后一个 task Acceptance 中添加：
  ```markdown
  - 调用 /memory 更新项目记忆
    ```bash
    /memory --tasks docs/tasks/<feature>.tasks.md --spec docs/spec/<feature>.spec.md
    ```
  ```

### Acceptance

- [ ] doc-gen skill 更新完成
- [ ] 生成的 tasks 模板包含 memory skill 调用
- [ ] 验证生成的 tasks 文件 Acceptance 格式正确

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 `skills/doc-gen/templates/tasks.md`，在 Acceptance 模板中添加 memory skill 调用说明 |

---

## Task 4: 更新 execute skill（可选）

### Scope

更新 `execute` skill，在执行最后一个 task 后提示用户调用 memory skill。

**修改文件：**
- `skills/execute/skill.md`

**修改内容：**
- 检测到最后一个 task 完成时，添加提示信息
- 提示用户调用 `/memory` 更新项目记忆

### Acceptance

- [ ] execute skill 更新完成
- [ ] 最后一个 task 完成时显示正确提示

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 `skills/execute/skill.md`，在 feat 执行流程第 6 步添加完成提示，提示用户调用 /memory |

---

## Task 5: 测试验证

### Scope

测试 memory skill 的完整功能。

**测试场景：**
1. 首次初始化（无参数）
2. 增量更新（有参数）
3. 文件索引更新
4. 错误处理（目录不存在、文件不存在等）

### Acceptance

- [ ] 首次初始化生成正确的 memory.md
- [ ] 增量更新正确更新 memory.md
- [ ] 文件索引表格数据完整
- [ ] 错误处理符合预期
- [ ] 最后一个 task Acceptance 调用成功

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 生成初始 memory.md，包含项目概述、架构要点、已完成功能列表、关键决策记录、文件索引表格。所有测试场景验证通过。 |

---

## Task 6: 更新文档

### Scope

更新相关文档以反映新的记忆机制。

**更新文件：**
- `README.md` - 添加 memory skill 说明
- `CLAUDE.md` - 更新技能体系说明

**更新内容：**
- 在技能体系说明中添加 memory skill
- 说明 memory skill 的用途和调用方式

### Acceptance

- [ ] README.md 更新完成
- [ ] CLAUDE.md 更新完成
- [ ] 文档描述准确

### Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-26 | Claude | 更新 README.md：添加 memory skill 说明、docs/memories 目录、项目记忆章节；更新 CLAUDE.md：在核心技能层添加 /memory |

---

## Task 7: 全量回归

### Scope

运行完整的回归测试，确保新功能不影响现有流程。

**验证项：**
- /fix 流程正常
- /feat 流程正常
- /execute 正常执行
- /memory 正常工作

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

**Total Tasks:** 7
