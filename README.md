# Claude Governance Framework

Claude 技能导向治理框架 - 统一配置与工作流管理，实现可预测、可审计、可扩展的 AI 辅助开发。

## 概述

本框架通过定义清晰的**变更分级**、**工作流程**和**执行标准**，为 Claude AI 辅助开发提供结构化的治理机制。核心目标：

- **可预测性** - 通过标准化流程确保开发结果可预期
- **可审计性** - 每个变更都有完整的执行记录和追溯链
- **可扩展性** - 技能体系支持灵活的职责分工

## 目录结构

```
claude-governance/
├── .gitignore              # 反向忽略模式：仅跟踪治理框架文件
├── CLAUDE.md               # 项目级执行宪法（工作流与变更规范）
├── README.md               # 本文档
└── skills/                 # 技能定义目录
    ├── brainstorming/      # 需求探索与设计生成技能
    │   ├── SKILL.md        # 头脑风暴技能核心定义
    │   ├── changes.md      # L2 变更记录模板
    │   ├── spec.md         # L3 规格说明模板
    │   └── tasks.md        # L3 任务分解模板
    └── execute/            # 任务执行技能
        └── SKILL.md        # 执行技能核心定义
```

---

## 核心概念

### 变更分级（Workflow Gate）

所有变更在进入工作流前必须先进行分级，不同级别对应不同流程：

| 级别 | 类型 | 适用场景 | 工作流程 |
|:----:|------|----------|----------|
| **L1** | Docs / Trivial Changes | 仅修改文档或注释，不改变代码运行行为 | Edit → Preview |
| **L2** | Tiny / Controlled Changes | 小 bug 修复、typo 修复、单文件逻辑修正 | Change → Acceptance → Record |
| **L3** | Full Feature Changes | 新功能开发、接口变更、依赖变更、架构调整 | Plan → Spec → Tasks → Execute |

**L1 判定条件**（需全部满足）：
- 仅修改文档或注释
- 不改变代码运行行为
- 不改变公共接口
- 不改变默认值或配置语义
- 不新增或修改依赖

**L3 触发条件**（命中任意一条）：
- 新功能开发
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块改动
- 架构调整
- 安全/加密/账务相关变更

### 技能体系

| 技能 | 调用方式 | 职责 | 适用范围 |
|------|----------|------|----------|
| **brainstorming** | `/brainstorming <需求>` | 需求探索、设计确认、生成 Spec 与 Tasks | L2、L3 |
| **execute** | `/execute <task-id>` | 执行 L2 或 L3 的指定任务 | L2、L3 |

---

## 工作流程详解

### L1 - 文档变更

```
修改文档 → 预览效果 → 提交变更
```

**执行流程**：
1. 直接修改文档或注释
2. 预览变更效果（确保 Markdown 渲染正常）
3. 提交变更（commit 说明包含 `docs:` 前缀）

**完成定义**：
- Markdown 渲染正常
- 示例命令验证通过（如有）

---

### L2 - 小变更

```
/brainstorming → 生成 Change Record → /execute → 运行验收 → 记录结果
```

**执行流程**：
1. 使用 `/brainstorming <变更描述>` 生成变更记录
2. 使用 `/execute <change-file>` 执行变更
3. 运行验收命令
4. 记录执行结果

**输出文件**：
- `docs/changes/YYYYMMDD-<topic>.md`

**完成定义**：
- 代码完成
- 验收命令执行通过
- 变更记录文件存在且已更新执行结果

---

### L3 - 功能变更

```
/brainstorming → 设计探索 → Spec → Tasks → /execute 1 → /execute 2 → ... → 回归验证
```

**执行流程**：
1. 使用 `/brainstorming <功能需求>` 进行设计探索
2. 生成设计文档（可选）、Spec 和 Tasks
3. 使用 `/execute <task-number>` 按任务顺序逐个执行
4. 每个任务完成后运行验收命令并记录结果
5. 最后执行全量回归验证
6. 更新执行时间线

**输出文件**：
- `docs/plans/YYYY-MM-DD-<topic>-design.md`（可选）
- `docs/spec/<feature>.spec.md`
- `docs/tasks/<feature>.tasks.md`

**完成定义**：
- 代码完成
- 测试补充或更新
- 所有验收命令已执行
- 每个 Task 的 Execution Record 已填写
- Timeline 已更新

---

## 快速开始

### 本地使用

将本仓库链接到 `~/.claude`：

```bash
git clone <repo-url> ~/.claude
```

或使用符号链接：

```bash
ln -s /path/to/claude-governance ~/.claude
```

### 项目级覆盖

在项目根目录创建 `.claude/` 目录，可覆盖全局规则：

```bash
your-project/
├── .claude/
│   ├── CLAUDE.md              # 可选：项目级宪法（覆盖全局规则）
│   └── skills/                # 可选：项目级技能定义
└── ...
```

项目级规则优先级高于全局规则。

---

## 技能使用指南

### Brainstorming 技能

**用途**：在任何功能开发前必须使用。负责需求探索、设计确认、生成 Spec 与 Tasks。未经设计批准禁止进入实现。

**调用方式**：
```
/brainstorming <需求描述>
```

**工作流程**：

**L2（小变更）**：
1. 澄清变更动机、影响范围、成功标准
2. 生成 `docs/changes/YYYYMMDD-<topic>.md`
3. 提示进入 `/execute`

**L3（功能变更）**：
1. 探索项目结构与上下文
2. 提出设计方案（2-3 个方案 + trade-offs）
3. 设计批准后生成 Spec
4. 基于 Spec 生成 Tasks
5. 提示使用 `/execute 1` 开始执行

**注意**：
- Spec 阶段禁止生成实现代码
- 禁止提前拆解 Tasks

---

### Execute 技能

**用途**：执行 L2 或 L3 的指定任务。

**调用方式**：
```
/execute <task-identifier>
```

**输入**：
- L2：Change 文件路径（如 `docs/changes/20250225-fix-bug.md`）
- L3：Task 编号（如 `1`）+ feature 名称（或 tasks 文件路径）

**执行规则**：
- 严格按 Scope 修改代码
- 禁止提前实现未执行的任务
- L3 禁止顺手重构
- 必须运行验收命令并记录结果

**执行步骤**：
1. 读取任务定义
2. 实现代码
3. 补充或更新测试（L3）
4. 运行验收命令
5. 记录执行结果（Commands Run、Result、Output Summary）
6. 更新时间线（L3）

---

## 变更护栏

默认禁止的行为：
- 大范围重构（除非 Spec 明确要求）
- 修改依赖文件（go.mod / package.json 等）
- 修改公共接口而不更新测试
- L1 变更使用 brainstorming 或 execute 技能

---

## 设计原则

1. **稳定优先** - 所有变更以系统稳定为最高优先级
2. **架构一致** - 设计决策优先于实现细节
3. **最小风险** - 最小改动范围，限制变更影响
4. **可审计** - 所有决策可追溯，所有变更有来源
5. **可持续** - 支持长期演进，避免技术债务积累

---

## 许可

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request。

---

**Location**: `~/.claude/` | **Version**: 2.0
