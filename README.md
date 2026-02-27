# Claude Governance Framework

Claude 技能导向治理框架 - 统一配置与工作流管理，实现可预测、可审计、可扩展的 AI 辅助开发。

## 概述

本框架通过定义清晰的**变更分级**、**工作流程**和**执行标准**，为 Claude AI 辅助开发提供结构化的治理机制。核心目标：

- **可预测性** - 通过标准化流程确保开发结果可预期
- **可审计性** - 每个变更都有完整的执行记录和追溯链
- **可扩展性** - 技能体系支持灵活的职责分工

---

## 目录结构

```
claude-governance/
├── CLAUDE.md               # 全局执行宪法（工作流与变更规范）
├── README.md               # 本文档
├── skills/                 # 技能定义目录
│   ├── explore/            # 需求探索与澄清技能
│   ├── design/             # 设计方案讨论技能
│   ├── doc-gen/            # 文档生成器技能
│   ├── execute/            # 执行器技能
│   ├── memory/             # 记忆技能
│   ├── commit/             # Git 提交技能
│   ├── docs/               # Docs 级别约束说明技能
│   ├── fix/                # fix 流程编排技能
│   └── feat/               # feat 流程编排技能
└── docs/
    ├── spec/               # 设计基准
    ├── tasks/              # 执行记录
    ├── changes/            # 变更历史
    ├── plans/              # 设计文档
    └── memories/           # 项目记忆
        └── memory.md       # 项目记忆文件
```

---

## 核心概念

### 变更分级（Workflow Gate）

所有变更在进入工作流前必须先进行分级，不同级别对应不同流程：

| 级别 | 类型 | 适用场景 | 工作流程 |
|:----:|------|----------|----------|
| **docs** | Docs / Trivial Changes | 仅修改文档或注释，不改变代码运行行为 | Edit → Preview |
| **fix** | Tiny / Controlled Changes | 小 bug 修复、typo 修复、单文件逻辑修正 | explore → doc-gen → execute |
| **feat** | Full Feature Changes | 新功能开发、接口变更、依赖变更、架构调整 | explore → design → doc-gen → execute |

**docs 判定条件**（需全部满足）：
- 仅修改文档或注释
- 不改变代码运行行为
- 不改变公共接口
- 不改变默认值或配置语义
- 不新增或修改依赖
- 不修改治理文档（docs/spec/、docs/tasks/、docs/changes/）

**fix 适用范围**（满足任一条件，且不涉及 feat）：
- 小 bug 修复
- typo 修复
- 单文件逻辑修正
- 内部实现优化（不改变接口与语义）

**feat 触发条件**（命中任意一条）：
- 新功能开发
- 公共接口变更
- 默认值或配置语义变更
- 依赖变更
- 多模块改动
- 架构调整
- 安全/加密/账务相关变更

---

## 技能体系

### 入口层

| 技能 | 调用方式 | 职责 |
|------|----------|------|
| **docs** | `/docs` | 显示 Docs 级别约束说明 |

### 编排层

| 技能 | 调用方式 | 职责 |
|------|----------|------|
| **fix** | `/fix <需求>` | fix 流程编排（explore → doc-gen） |
| **feat** | `/feat <需求>` | feat 流程编排（explore → design → doc-gen） |

### 核心技能层

| 技能 | 调用方式 | 职责 |
|------|----------|------|
| **explore** | `/explore <需求>` | 需求探索与澄清 |
| **design** | `/design` | 设计方案讨论 |
| **doc-gen** | `/doc-gen <type>` | 生成治理文档 |
| **project-memory** | `/project-memory [文件路径...]` | 生成/更新项目记忆文件 |
| **commit** | `/commit` | 自动生成提交信息 |

### 执行层

| 技能 | 调用方式 | 职责 |
|------|----------|------|
| **execute** | `/execute fix <file>` / `/execute feat <task-id>` | 执行 fix Change 或 feat Task |

---

## 工作流程详解

### docs - 文档变更

```
直接修改 → 预览效果 → 提交变更
```

**执行流程**：
1. 直接修改文档或注释
2. 预览变更效果
3. 提交变更（使用 `/commit` 或手动提交）

**完成定义**：
- Markdown 渲染正常
- 示例命令验证通过（如有）

---

### fix - 小变更

```
/fix → /explore → /doc-gen change → /execute fix → 验收 → 记录
```

**执行流程**：
1. 使用 `/fix <变更描述>` 启动流程
2. 执行需求探索（`/explore`）
3. 生成 Change Record（`/doc-gen change`）
4. 执行变更（`/execute fix <file>`）
5. 运行验收命令
6. 记录执行结果

**输出文件**：
- `docs/changes/YYYY-MM-DD-<topic>.md`

**完成定义**：
- 代码完成
- Acceptance 全部执行
- Execution Record 已更新

---

### feat - 功能变更

```
/feat → /explore → /design → /doc-gen spec → /doc-gen tasks → /execute feat
```

**执行流程**：
1. 使用 `/feat <功能需求>` 启动流程
2. 执行需求探索（`/explore`）
3. 进行设计方案讨论（`/design`）
4. 生成 Spec（`/doc-gen spec`）
5. 生成 Tasks（`/doc-gen tasks`）
6. 按任务顺序执行（`/execute feat <task-id>`）
7. 更新执行时间线

**输出文件**：
- `docs/plans/YYYY-MM-DD-<topic>-design.md`（可选）
- `docs/spec/<feature>.spec.md`
- `docs/tasks/<feature>.tasks.md`

**完成定义**：
- 代码完成
- 测试更新
- Acceptance 全部执行
- Task Execution Record 已填写
- Timeline 已更新

---

## 项目记忆（Memory）

为了解决项目过大导致的上下文不足问题，框架提供了项目记忆机制。

### project-memory skill

**调用方式：**

```bash
# 单个文件
/project-memory docs/spec/xxx.spec.md
/project-memory docs/tasks/xxx.tasks.md
/project-memory docs/changes/xxx.md

# 多个文件
/project-memory docs/spec/xxx.spec.md docs/tasks/xxx.tasks.md
/project-memory docs/spec/xxx.spec.md docs/tasks/xxx.tasks.md docs/plans/xxx-design.md

# 首次初始化
/project-memory
```

**memory.md 内容：**
- 项目概述
- 架构要点
- 已完成功能列表
- 关键决策记录
- 文件索引（spec/tasks/changes/plans）

新会话开始时，可以通过读取 `docs/memories/memory.md` 快速恢复项目上下文。

---

## 受保护目录

以下目录受治理框架保护：

| 目录 | 说明 | 创建权限 | 更新权限 | 删除权限 |
|------|------|----------|----------|----------|
| `docs/spec/` | 设计基准 | feat 流程 | 禁止 | 禁止 |
| `docs/tasks/` | 执行记录 | feat 流程 | execute (ER) | 禁止 |
| `docs/changes/` | 变更历史 | fix 流程 | execute (ER) | 禁止 |

**注：** ER = Execution Record（执行记录）

**规则**：
- ❌ 用户直接创建、修改、删除
- ✅ fix/feat 流程创建
- ✅ execute 更新 Execution Record

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

## 典型使用场景

### 场景 1：修复一个小 bug

```bash
# 1. 启动 fix 流程
/fix 修复登录页面验证逻辑错误

# 2. 按流程执行，最后
/execute fix docs/changes/2026-02-25-fix-login-validation.md
```

### 场景 2：新增一个功能

```bash
# 1. 启动 feat 流程
/feat 新增用户导出功能

# 2. 按流程执行，最后
/execute feat user-export:1
```

### 场景 3：修改文档

```bash
# 直接修改，无需技能
# 修改完成后使用 /commit 提交
/commit
```

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

---

**Location**: `~/.claude/` | **Version**: 3.1
