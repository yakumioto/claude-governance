# Commit 技能规格说明

**Version**: 1.0
**Date**: 2026-02-25
**Status**: Approved

---

## 1. Goal

创建一个 `/commit` 技能，实现智能化的 Git 提交流程。技能将分析改动内容，使用 AI 推断提交类型和作用域，自动生成符合 Conventional Commits 规范的提交信息并执行提交。

---

## 2. Non-Goals

- 不支持交互式确认（全自动执行）
- 不支持 partial commit（必须提交所有改动）
- 不支持 amend、revert、cherry-pick 等高级 Git 操作
- 不支持多仓库操作

---

## 3. Constraints

| 约束 | 说明 |
|------|------|
| 执行环境 | 必须在 Git 仓库中执行 |
| Git 状态 | 不能有未解决的 merge conflict |
| 提交格式 | 遵循 Conventional Commits: `type(scope): description` |
| Type 类型 | 仅限: feat, fix, docs, style, refactor, test, chore |
| 字符限制 | 完整提交信息不超过 72 字符 |
| 语言 | description 使用中文（与项目提交历史保持一致） |

---

## 4. 接口定义

### 4.1 技能调用

```bash
/commit
```

**输入**：无参数

**输出**：执行结果或错误信息

### 4.2 执行流程

```
1. 环境检查
   ├─ 验证 Git 仓库
   └─ 检查是否有改动

2. 收集上下文
   ├─ git status（改动文件列表）
   ├─ git diff（改动内容）
   └─ git log --oneline -5（最近提交风格）

3. AI 分析
   ├─ 输入：files + diff + recent_commits
   └─ 输出：{ type, scope, description }

4. 生成提交信息
   └─ 格式：type(scope): description

5. 执行提交
   └─ git add . && git commit -m "<message>"
```

### 4.3 AI 分析接口

**输入格式**：
```json
{
  "files": [
    {"path": "skills/commit/SKILL.md", "status": "new"}
  ],
  "diff": "diff --git a/skills/commit/SKILL.md ...",
  "recent_commits": [
    "6943a91 docs: 更新 README 文档结构",
    "27c53e4 docs: 添加变更分级入口规则"
  ]
}
```

**输出格式**：
```json
{
  "type": "feat",
  "scope": "skills",
  "description": "添加 commit 智能提交技能"
}
```

---

## 5. 数据结构

### 5.1 Type 类型定义

| Type | 说明 | 判断依据 |
|------|------|----------|
| feat | 新功能 | 新增文件、新增功能点 |
| fix | Bug 修复 | 修复性改动、bugfix 关键词 |
| docs | 文档变更 | *.md 文件、README、文档目录 |
| style | 代码格式 | 格式化、缩进、空行（不影响逻辑） |
| refactor | 重构 | 结构调整、代码重组（不改变行为） |
| test | 测试相关 | *_test.go、测试目录、断言 |
| chore | 构建/工具链 | 配置文件、依赖、脚本 |

### 5.2 Scope 推断规则

| 文件路径 | Scope |
|----------|-------|
| skills/** | skills |
| CLAUDE.md | governance |
| README.md | docs |
| docs/** | docs |
| .claude/** | config |

---

## 6. 边界情况

| 情况 | 处理方式 |
|------|----------|
| 非 Git 仓库 | 报错：`错误：当前目录不是 Git 仓库` |
| 无改动 | 报错：`错误：没有需要提交的改动` |
| 有未暂存和已暂存改动 | 全部提交（git add .） |
| 单文件改动 | 推断 scope，无 scope 可省略 |
| 多文件不同模块 | scope 取主要模块或省略 |
| AI 分析失败 | 降级为默认值：`chore: update` |
| Git hook 拒绝 | 显示 hook 错误信息，不重试 |

---

## 7. 错误处理

### 7.1 错误类型

| 错误类型 | 错误信息 | 处理 |
|----------|----------|------|
| NOT_GIT_REPO | `错误：当前目录不是 Git 仓库` | 退出 |
| NO_CHANGES | `错误：没有需要提交的改动` | 退出 |
| AI_PARSE_ERROR | `警告：AI 分析失败，使用默认提交信息` | 降级继续 |
| GIT_ERROR | `错误：Git 执行失败 - <stderr>` | 退出 |

### 7.2 降级策略

当 AI 分析失败时：
1. 记录警告日志
2. 使用默认提交信息：`chore: update`
3. 继续执行提交

---

## 8. Validation Plan

### 8.1 功能验证

| 测试用例 | 操作 | 预期结果 |
|----------|------|----------|
| 新文件提交 | `touch test.md && /commit` | type=feat, 有描述 |
| 文档修改 | `echo "# test" >> README.md && /commit` | type=docs |
| 技能文件 | 在 skills/ 下新文件 | scope=skills |
| 空仓库 | 无改动时调用 `/commit` | 报错：无改动 |
| 非 Git 仓库 | 在 /tmp 下调用 `/commit` | 报错：非 Git 仓库 |

### 8.2 验收命令

```bash
# 创建测试环境
cd /tmp && mkdir commit-test && cd commit-test && git init

# 测试 1：新文件
echo "test" > test.txt && /commit
git log --oneline -1 | grep -E "feat|chore"

# 测试 2：文档修改
echo "# README" > README.md && /commit
git log --oneline -1 | grep "docs:"

# 测试 3：无改动
/commit
# 预期：报错

# 测试 4：提交信息格式
git log -1 --pretty=format:"%s" | grep -E "^[a-z]+(\([a-z]+\))?: .+$"
```

---

## 9. 文件清单

| 文件 | 路径 | 说明 |
|------|------|------|
| SKILL.md | ~/.claude/skills/commit/SKILL.md | 技能定义 |
| prompt.md | ~/.claude/skills/commit/prompt.md | AI 提示词模板 |
| types.md | ~/.claude/skills/commit/types.md | Type 类型参考 |

---

## 10. 完成定义（Definition of Done）

- [ ] SKILL.md 文件创建，包含完整执行流程
- [ ] prompt.md 文件创建，包含 AI 分析提示词
- [ ] types.md 文件创建，包含 Type 类型说明
- [ ] 所有测试用例通过
- [ ] 提交信息格式符合 Conventional Commits
- [ ] 错误处理覆盖所有边界情况
