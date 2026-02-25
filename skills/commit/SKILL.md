---
name: commit
description: 自动分析改动并生成符合 Conventional Commits 规范的提交信息，执行 Git 提交。
---

# Commit 智能提交技能

用途：
分析 Git 改动内容，使用 AI 推断提交类型和作用域，自动生成符合 Conventional Commits 规范的提交信息并执行提交。

---

## Workflow Gate（执行前置条件）

执行前必须确认：

- 当前目录在 Git 仓库中
- 有需要提交的改动（staged 或 unstaged）

若不满足条件，禁止继续执行。

---

## 调用方式

```
/commit
```

无参数，自动处理当前所有改动。

---

## 提交格式

遵循 Conventional Commits 规范：

```
type(scope): description
```

| 字段 | 说明 | 可选 |
|------|------|------|
| type | 提交类型 | 必填 |
| scope | 作用域 | 可选 |
| description | 简洁描述（中文，不超过 50 字符） | 必填 |

---

## Type 类型定义

| Type | 说明 | 判断依据 |
|------|------|----------|
| feat | 新功能 | 新增文件、新增功能点 |
| fix | Bug 修复 | 修复性改动、bugfix 关键词 |
| docs | 文档变更 | *.md 文件、README、文档目录 |
| style | 代码格式 | 格式化、缩进、空行（不影响逻辑） |
| refactor | 重构 | 结构调整、代码重组（不改变行为） |
| test | 测试相关 | *_test.go、测试目录、断言 |
| chore | 构建/工具链 | 配置文件、依赖、脚本 |

---

# 执行流程

## 步骤 1：环境检查

验证 Git 仓库状态：

```bash
# 确认在 Git 仓库中
git rev-parse --git-dir > /dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "错误：当前目录不是 Git 仓库"
  exit 1
fi

# 检查是否有改动
CHANGES=$(git status --porcelain)
if [ -z "$CHANGES" ]; then
  echo "错误：没有需要提交的改动"
  exit 1
fi
```

---

## 步骤 2：收集上下文

获取必要信息用于 AI 分析：

```bash
# 改动文件列表（带状态）
FILES=$(git status --short)

# 改动内容
DIFF=$(git diff)

# 最近提交风格（保持一致）
RECENT_COMMITS=$(git log --oneline -5)
```

---

## 步骤 3：AI 分析

使用 AI 分析改动并推断提交信息：

**输入**：
- 改动文件列表（files）
- 改动内容（diff）
- 最近提交历史（recent_commits）

**输出**：
```json
{
  "type": "feat",
  "scope": "skills",
  "description": "添加 commit 智能提交技能"
}
```

**AI Prompt 模板**（参考 prompt.md）

---

## 步骤 4：生成提交信息

根据 AI 分析结果生成提交信息：

```bash
# 如果有 scope
if [ -n "$SCOPE" ]; then
  MESSAGE="${TYPE}(${SCOPE}): ${DESCRIPTION}"
else
  MESSAGE="${TYPE}: ${DESCRIPTION}"
fi
```

---

## 步骤 5：执行提交

执行 Git 提交：

```bash
# 添加所有改动
git add .

# 提交
git commit -m "$MESSAGE"

# 显示结果
echo "✓ 提交成功: $MESSAGE"
git log --oneline -1
```

---

# 错误处理

| 错误类型 | 错误信息 | 处理 |
|----------|----------|------|
| NOT_GIT_REPO | `错误：当前目录不是 Git 仓库` | 退出 |
| NO_CHANGES | `错误：没有需要提交的改动` | 退出 |
| AI_PARSE_ERROR | `警告：AI 分析失败，使用默认提交信息` | 降级为 `chore: update` |
| GIT_ERROR | `错误：Git 执行失败 - <stderr>` | 退出 |

**降级策略**：
当 AI 分析失败时：
1. 记录警告日志
2. 使用默认提交信息：`chore: update`
3. 继续执行提交

---

## Scope 推断规则

| 文件路径 | Scope |
|----------|-------|
| skills/** | skills |
| CLAUDE.md | governance |
| README.md | docs |
| docs/** | docs |
| .claude/** | config |

---

## 完成定义（Definition of Done）

- [ ] 环境检查通过
- [ ] 上下文收集完成
- [ ] AI 分析生成 type/scope/description
- [ ] Git 提交成功执行
- [ ] 提交信息格式符合 Conventional Commits
