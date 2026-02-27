# 默认工作流（Workflow）

- 所有回复在保证专业的前提下尽可能使用中文回复。
- 所有变更在进入默认工作流前，必须先进行变更分级。

---

## 强制前置检查（Guards）

**重要：** 在处理任何请求前，必须执行以下检查。

### 第一层：请求入口检查

收到用户输入时，立即检查：

1. **检查是否涉及受保护目录**
   - 用户输入是否包含 `docs/spec/`、`docs/tasks/`、`docs/changes/`
   - 是否明确表达修改、创建、删除这些目录的意图

2. **如果涉及受保护目录**
   ```
   "检测到操作受保护目录（docs/spec/、docs/tasks/、docs/changes/）。

   治理文档必须通过 fix/feat 流程操作。

   请使用：
   - /fix <你的需求> — 小变更/修复
   - /feat <你的需求> — 功能开发"
   ```

3. **如果不涉及**
   - 继续正常处理

### 第二层：上下文恢复检查

收到用户输入时，评估当前会话是否已了解项目结构：

1. **判断是否需要读取 memory**
   - 会话是否首次与用户交互？
   - 用户是否提出了需要了解项目结构的问题？
   - 是否涉及不明确的功能模块或文件位置？

2. **如果需要读取 memory**
   - 检查 `docs/memories/memory.md` 是否存在
   - 如果存在，读取该文件恢复项目上下文
   - 如果不存在，可使用 `/project-memory` 初始化项目记忆

3. **明确项目结构后的优势**
   - 减少探索阶段的重复工作
   - 快速定位相关文件和模块
   - 理解已有的架构决策

### 受保护目录（全局生效）

以下目录在任何项目中都受保护：

| 目录 | 说明 | 创建权限 | 更新权限 | 删除权限 |
|------|------|----------|----------|----------|
| `docs/spec/` | 设计基准 | feat | 禁止 | 禁止 |
| `docs/tasks/` | 执行记录 | feat | execute (ER) | 禁止 |
| `docs/changes/` | 变更历史 | fix | execute (ER) | 禁止 |

**注：** ER = Execution Record（执行记录）

**规则：**
- ❌ 用户直接创建、修改、删除
- ✅ fix/feat 流程创建
- ✅ execute 更新 Execution Record

---

## 变更分级入口规则（Entry Gate）

**根据需求描述判断变更级别，然后使用对应的技能。**

```
用户需求
    │
    ▼
┌─────────────────────────────────────────┐
│ 第一步：检查是否修改治理文档？           │
│ (docs/spec/, docs/tasks/, docs/changes/) │
└─────────────────┬───────────────────────┘
                  │
         是 ──────┴────── 否
         │                │
         ▼                ▼
   提示使用          ┌─────────────────┐
   fix/feat          │ 第二步：检查   │
                     │ 触发 feat 条件 │
                     └────────┬────────┘
                              │
                    是 ───────┴────── 否
                      │              │
                      ▼              ▼
                    /feat         ┌─────────────┐
                                  │ 第三步：检查 │
                                  │ 仅修改文档？ │
                                  └──────┬──────┘
                                   是  │  否
                                    ▼  ▼
                                 直接对话 /fix
```

**feat 触发条件：**
- 新功能开发
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块改动
- 架构调整
- 安全 / 加密 / 账务相关变更

**fix 适用范围：**
- 小 bug 修复
- typo 修复
- 单文件逻辑修正
- 内部实现优化（不改变接口与语义）

**docs 约束：**
- 仅修改文档或注释（README.md、docs/guide/**、*.md、代码注释）
- 不修改治理文档（docs/spec/、docs/tasks/、docs/changes/）

---

**禁止在直接对话模式下执行 fix/feat 变更。**

如果用户在直接对话中提出 fix/feat 需求，必须拒绝执行并引导用户：

```
这是一个 fix/feat 变更。请根据上述决策树判断级别后使用对应的技能：
- /fix <需求描述> — 小变更/修复
- /feat <需求描述> — 功能开发
```

---

## 变更级别说明

### docs — Docs / Trivial Changes

满足以下**全部**条件：

- 仅修改文档或注释（README.md、docs/guide/**、*.md、代码注释）
- 禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）
- 不改变代码运行行为
- 不改变公共接口
- 不改变默认值或配置语义
- 不新增或修改依赖

**处理方式：** 直接对话，无需技能

**提交格式：**

```
docs: <summary>

What:
Why:
Risk:
Verify:
```

---

### fix — Tiny / Controlled Changes

满足以下**任一**条件，且**不涉及** feat 触发条件：

- 小 bug 修复
- typo 修复
- 单文件逻辑修正
- 内部实现优化（不改变接口与语义）

**处理方式：** `/fix <需求描述>`

**执行流程：** explore → doc-gen:change → execute fix

**产出物：** `docs/changes/YYYY-MM-DD-<topic>.md`

**边界约束：**
- 不改变公共接口（API/CLI/配置项对外契约）
- 不改变默认值或配置语义
- 不新增或升级依赖
- 不涉及跨模块架构调整

---

### feat — Full Feature Changes

满足以下**任一**条件：

- 新功能开发
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块改动
- 架构调整
- 安全 / 加密 / 账务相关变更

**处理方式：** `/feat <需求描述>`

**执行流程：** explore → design → doc-gen:spec/tasks → execute feat

**产出物：**
- `docs/plans/YYYY-MM-DD-<topic>-design.md`
- `docs/spec/<feature>.spec.md`
- `docs/tasks/<feature>.tasks.md`

---

## 操作执行检查（Execution Guards）

**第二层检查：在 Edit/Write 操作前**

在准备执行 Edit 或 Write 操作时，必须检查目标文件路径。

### 检查规则

1. **检查目标文件路径**
   - 路径是否包含 `docs/spec/`
   - 路径是否包含 `docs/tasks/`
   - 路径是否包含 `docs/changes/`

2. **如果目标路径在受保护目录**
   ```
   "检测到目标路径 {path} 位于受保护目录。

   治理文档只能由以下方式操作：
   - 创建：fix/feat 流程
   - 更新：仅限 execute 更新 Execution Record

   如果需要修改设计内容，请使用：
   - /fix <你的需求> — 补充说明
   - /feat <你的需求> — 设计变更

   如果是执行任务，请使用：
   - /execute fix <file> — fix 执行
   - /execute feat <task-id> — feat 执行"
   ```

3. **如果目标路径不在受保护目录**
   - 正常执行操作

### 只读操作例外

Read 操作不受此限制：
- 查看受保护目录的文件是允许的
- 不触发执行检查

---

## 受保护目录

以下目录在任何项目中都受保护：

### 生命周期规则

| 目录 | 说明 | 创建权限 | 更新权限 | 删除权限 |
|------|------|----------|----------|----------|
| `docs/spec/` | 设计基准 | feat 流程 | 禁止 | 禁止 |
| `docs/tasks/` | 执行记录 | feat 流程 | execute (ER) | 禁止 |
| `docs/changes/` | 变更历史 | fix 流程 | execute (ER) | 禁止 |

**注：** ER = Execution Record（执行记录）

### 规则说明

**❌ 禁止操作：**
- 用户直接创建、修改、删除受保护目录的文件

**✅ fix/feat 创建：**
- 只能由 fix/feat 流程通过 doc-gen 创建
- fix 创建 docs/changes/
- feat 创建 docs/spec/ 和 docs/tasks/

**✅ execute 更新：**
- 只能由 execute 更新 Execution Record
- 更新 docs/tasks/ 和 docs/changes/ 中的 Execution Record

**✅ 读取不受限：**
- Read 操作不受限制

---

## 技能系统

### 入口层
- `/docs` — docs 级别约束说明

### 编排层
- `/fix` — fix 流程编排
- `/feat` — feat 流程编排

### 核心技能层
- `/explore` — 需求探索与澄清
- `/design` — 设计方案讨论
- `/doc-gen` — 文档生成器（change/spec/tasks/design）
- `/project-memory` — 生成/更新项目记忆文件（支持直接文件路径）
- `/commit` — 自动生成提交信息

### 执行层
- `/execute fix <file>` — 执行 fix Change
- `/execute feat <task-id>` — 执行 feat Task

---

## Task 完成定义（Definition of Done）

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

**未填写 Execution Record 不视为完成。**
