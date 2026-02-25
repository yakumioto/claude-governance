# Commit 技能设计文档

**Date**: 2026-02-25
**Status**: Approved
**Owner**: yakumioto

---

## 1. 概述

### 1.1 目标

创建一个 `/commit` 技能，实现智能化的 Git 提交流程。技能将分析改动内容，自动推断提交类型和作用域，生成符合 Conventional Commits 规范的提交信息。

### 1.2 非目标

- ❌ 不支持交互式确认（全自动执行）
- ❌ 不支持 partial commit（必须提交所有改动）
- ❌ 不支持 amend/revert 等高级 Git 操作

---

## 2. 需求规格

### 2.1 调用方式

```
/commit
```

无参数，自动处理当前所有改动。

### 2.2 提交格式

遵循 Conventional Commits 规范：

```
type(scope): description
```

| 字段 | 说明 | 示例 |
|------|------|------|
| type | 提交类型 | feat, fix, docs, style, refactor, test, chore |
| scope | 作用域（可选） | skills, governance, agent 等 |
| description | 简洁描述 | 不超过 50 字符 |

### 2.3 Type 类型定义

| Type | 说明 | 示例 |
|------|------|------|
| feat | 新功能 | feat: add commit skill |
| fix | Bug 修复 | fix: resolve scope inference |
| docs | 文档变更 | docs: update README |
| style | 代码格式（不影响逻辑） | style: format code |
| refactor | 重构 | refactor: simplify flow |
| test | 测试相关 | test: add unit tests |
| chore | 构建/工具链 | chore: update config |

---

## 3. 设计方案

### 3.1 架构设计

```
┌─────────────────────────────────────────────────────────┐
│  /commit 调用                                           │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  1. 环境检查                                            │
│     - 确认在 Git 仓库中                                 │
│     - 检查是否有改动 (git status)                       │
└────────────────────┬────────────────────────────────────┘
                     │ 有改动
                     ▼
┌─────────────────────────────────────────────────────────┐
│  2. 收集上下文                                          │
│     - git status (改动文件列表)                         │
│     - git diff (改动内容)                               │
│     - git log --oneline -5 (最近提交风格)               │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  3. AI 分析                                             │
│     - 输入：文件列表 + diff 内容 + 提交历史             │
│     - 输出：type, scope, description                    │
│     - 约束：遵循项目提交风格                            │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  4. 生成提交信息                                        │
│     type(scope): description                            │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  5. 执行提交                                            │
│     git add . && git commit -m "<message>"              │
└─────────────────────────────────────────────────────────┘
```

### 3.2 模块划分

| 模块 | 职责 |
|------|------|
| 环境检查 | 验证 Git 状态，检查改动存在 |
| 上下文收集 | 获取 status、diff、log |
| AI 分析器 | 分析改动，推断 type/scope，生成 description |
| 执行器 | 执行 git add + git commit |

### 3.3 数据流

**输入数据**：
- 改动文件列表（git status）
- 改动内容（git diff）
- 最近提交历史（git log --oneline -5）

**AI Prompt**：
```markdown
# 任务：生成 Conventional Commit 信息

## 改动文件
{{files}}

## 改动内容
{{diff}}

## 最近提交风格（请保持一致）
{{recent_commits}}

## 输出要求
1. 分析改动类型：feat|fix|docs|style|refactor|test|chore
2. 推断作用域（基于文件路径）
3. 生成简洁描述（不超过 50 字符）

## 输出格式（JSON）
{
  "type": "...",
  "scope": "...",
  "description": "..."
}
```

**输出数据**：
```json
{
  "type": "feat",
  "scope": "skills",
  "description": "添加 commit 智能提交技能"
}
```

### 3.4 错误处理

| 场景 | 处理方式 |
|------|----------|
| 非 Git 仓库 | 报错：`Not a Git repository` |
| 无改动 | 报错：`No changes to commit` |
| AI 分析失败 | 使用默认值：`chore: update` |
| Git 执行失败 | 显示错误，不重试 |

---

## 4. 文件结构

```
~/.claude/skills/commit/
├── SKILL.md              # 技能定义（执行流程）
├── prompt.md             # AI 分析提示词模板
└── types.md              # Type 类型定义参考
```

---

## 5. 测试策略

| 场景 | 描述 | 验证点 |
|------|------|--------|
| 新文件提交 | 添加新技能文件 | type: `feat` |
| 文档修改 | 修改 README/CLAUDE.md | type: `docs` |
| Bug 修复 | 修复代码问题 | type: `fix` |
| 重构 | 代码结构调整 | type: `refactor` |
| 多文件改动 | 同时修改多个模块 | scope: 主要模块 |
| 无改动 | 空仓库调用 | 报错提示 |
| 非 Git 仓库 | 在非 Git 目录调用 | 报错提示 |

---

## 6. 约束条件

- 必须在 Git 仓库中执行
- 不能有未解决的 merge conflict
- 提交信息不超过 72 字符（type + scope + description）
- description 使用中文（与项目提交历史保持一致）
