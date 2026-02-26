# Project Memory

## 项目概述

Claude Governance Framework - 技能导向治理框架，统一配置与工作流管理，实现可预测、可审计、可扩展的 AI 辅助开发。

核心目标：
- **可预测性** - 通过标准化流程确保开发结果可预期
- **可审计性** - 每个变更都有完整的执行记录和追溯链
- **可扩展性** - 技能体系支持灵活的职责分工

---

## 架构要点

### 技能体系分层

```
入口层
  └── /docs

编排层
  ├── /fix
  └── /feat

核心技能层
  ├── /explore
  ├── /design
  ├── /doc-gen     # change / spec / tasks / design
  ├── /memory      # 上下文记忆机制（支持直接文件路径）
  └── /commit

执行层
  └── /execute
```

### 目录结构

```
~/.claude/
├── CLAUDE.md               # 全局执行宪法
├── README.md               # 框架说明
├── skills/                 # 技能定义
│   ├── explore/
│   ├── design/
│   ├── doc-gen/
│   ├── execute/
│   ├── memory/             # 记忆技能
│   ├── commit/
│   ├── docs/
│   ├── fix/
│   └── feat/
└── docs/
    ├── spec/               # 设计基准（feat 创建，禁止更新/删除）
    ├── tasks/              # 执行记录（feat 创建，execute 更新 ER，禁止删除）
    ├── changes/            # 变更历史（fix 创建，execute 更新 ER，禁止删除）
    ├── plans/              # 设计文档
    └── memories/           # 项目记忆
        └── memory.md
```

### 变更分级

| 级别 | 适用场景 | 工作流程 |
|------|----------|----------|
| **docs** | 仅修改文档/注释 | 直接修改 |
| **fix** | 小 bug/typo/单文件逻辑 | /fix → /explore → /doc-gen → /execute |
| **feat** | 新功能/接口变更/架构调整 | /feat → /explore → /design → /doc-gen → /execute |

---

## 已完成功能列表

- **global-guards** - 全局治理文档约束机制
  - 在 CLAUDE.md 中实现分层检查机制
  - 受保护目录：docs/spec/、docs/tasks/、docs/changes/
  - 入口检查：请求涉及受保护目录时拒绝并提示正确流程
  - 执行检查：Edit/Write 操作前检查路径

- **governance-decoupling** - 治理框架解耦

- **governance-docs-protection** - 治理文档保护机制

- **remove-classify-skill** - 移除 classify 技能

- **skill-renaming** - 技能重命名（L1/L2/L3 → docs/fix/feat）

- **commit-skill** - Git 提交技能

- **context-memory** - 上下文记忆机制
  - 新增 memory skill（方案 B），职责单一、灵活易测试
  - 使用单一 memory.md 文件，简化维护
  - 更新而非追加，支持项目重构场景
  - 在最后一个 task Acceptance 中自动提示调用 /memory

- **memory-params** - Memory Skill 参数简化
  - 简化 memory skill 参数设计，改为直接接受文件路径
  - 支持传入多个文件路径
  - 根据路径中的目录名称自动识别文件类型（spec/tasks/changes/plans）
  - 更新 doc-gen 的 tasks 和 change 模板，添加 memory 调用说明
  - 更新 README.md 和 CLAUDE.md 中的 memory skill 说明

---

## 关键决策记录

- **global-guards** - 选择在 CLAUDE.md 中实现检查规则而非系统级路由
- **skill-renaming** - 将 L1/L2/L3 重命名为 docs/fix/feat 提升可理解性
- **context-memory** - 新增 memory skill（方案 B），职责单一、灵活易测试
- **context-memory** - 使用单一 memory.md 文件，简化维护
- **context-memory** - 更新而非追加，支持项目重构场景
- **memory-params** - 简化 memory skill 参数为直接文件路径，提升易用性和灵活性

---

## 文件索引

| 文件路径 | 类型 | Summary |
|----------|------|---------|
| docs/spec/global-guards.spec.md | spec | 全局治理文档约束机制 - 在 CLAUDE.md 中实现分层检查机制 |
| docs/spec/governance-decoupling.spec.md | spec | 治理框架解耦 |
| docs/spec/governance-docs-protection.spec.md | spec | 治理文档保护机制 |
| docs/spec/remove-classify-skill.spec.md | spec | 移除 classify 技能 |
| docs/spec/skill-renaming.spec.md | spec | 技能重命名（L1/L2/L3 → docs/fix/feat） |
| docs/spec/commit-skill.spec.md | spec | Git 提交技能 |
| docs/spec/context-memory.spec.md | spec | 上下文记忆机制 - 解决项目过大导致的上下文不足问题 |
| docs/spec/memory-params.spec.md | spec | Memory Skill 参数简化 - 改为直接文件路径，提升易用性 |
| docs/tasks/global-guards.tasks.md | tasks | 全局治理文档约束机制任务执行记录（7个任务全部完成） |
| docs/tasks/governance-decoupling.tasks.md | tasks | 治理框架解耦任务执行记录 |
| docs/tasks/governance-docs-protection.tasks.md | tasks | 治理文档保护机制任务执行记录 |
| docs/tasks/remove-classify-skill.tasks.md | tasks | 移除 classify 技能任务执行记录 |
| docs/tasks/skill-renaming.tasks.md | tasks | 技能重命名任务执行记录 |
| docs/tasks/commit-skill.tasks.md | tasks | Git 提交技能任务执行记录 |
| docs/tasks/context-memory.tasks.md | tasks | 上下文记忆机制任务执行记录（7个任务全部完成） |
| docs/tasks/memory-params.tasks.md | tasks | Memory Skill 参数简化任务执行记录（6个任务全部完成） |
| docs/changes/2026-02-26-tasks-template-summary.md | changes | tasks 模板表格列名调整：执行者改为摘要 |
| docs/plans/2026-02-26-context-memory-design.md | design | 上下文记忆机制设计文档 |
| docs/plans/2026-02-26-memory-params-design.md | design | Memory Skill 参数简化设计文档 |

---

**最后更新：** 2026-02-26
