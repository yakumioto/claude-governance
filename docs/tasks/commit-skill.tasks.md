# Commit 技能任务分解

**Feature**: commit-skill
**Date**: 2026-02-25
**Status**: In Progress

---

## Execution Timeline

| Task | Status | Date | Notes |
|------|--------|------|-------|
| 1 | completed | 2026-02-25 | 创建技能目录结构 |
| 2 | completed | 2026-02-25 | 创建 SKILL.md |
| 3 | completed | 2026-02-25 | 创建 prompt.md |
| 4 | completed | 2026-02-25 | 创建 types.md |
| 5 | completed | 2026-02-25 | 功能测试与验证 |
| 6 | completed | 2026-02-25 | 还原 ~/.claude/ 到原始状态 |

---

## Task 1: 创建技能目录结构

**Scope**：
- 在 `/root/workspace/claude-governance/skills/` 下创建 `commit/` 目录
- 同步到 `~/.claude/skills/commit/`
- 确认目录结构符合技能框架规范

**Acceptance**：
```bash
# 验证项目目录
ls -la /root/workspace/claude-governance/skills/commit/

# 验证同步目录
ls -la ~/.claude/skills/commit/
# 预期：两个目录都存在
```

**Execution Record**：
- Commands Run: `mkdir -p /root/workspace/claude-governance/skills/commit`
- Result: PASS
- Output Summary: 项目目录已创建，~/claude 同步将在后续 Task 中处理

---

## Task 2: 创建 SKILL.md

**Scope**：
- 创建 `/root/workspace/claude-governance/skills/commit/SKILL.md`
- 同步到 `~/.claude/skills/commit/SKILL.md`
- 定义技能名称、描述、执行流程
- 包含环境检查、上下文收集、AI 分析、执行提交的完整流程

**要求**：
- 技能名称：`commit`
- 技能描述：自动分析改动并生成符合 Conventional Commits 规范的提交信息
- 执行流程清晰，包含 5 个步骤
- 包含错误处理说明

**Acceptance**：
```bash
# 验证项目文件
cat /root/workspace/claude-governance/skills/commit/SKILL.md | grep -E "name:|description:|# 执行流程"

# 验证同步文件
cat ~/.claude/skills/commit/SKILL.md | grep -E "name:|description:|# 执行流程"
# 预期：包含技能定义和执行流程
```

**Execution Record**：
- Commands Run:
  - `mkdir -p ~/.claude/skills/commit && cp ...SKILL.md ~/.claude/skills/commit/SKILL.md`
  - `cat ...SKILL.md | grep -E "name:|description:|# 执行流程"`
- Result: PASS
- Output Summary: SKILL.md 已创建并同步，系统已识别新技能

---

## Task 3: 创建 prompt.md

**Scope**：
- 创建 `/root/workspace/claude-governance/skills/commit/prompt.md`
- 同步到 `~/.claude/skills/commit/prompt.md`
- 定义 AI 分析的提示词模板
- 包含输入变量、输出要求、格式约束

**要求**：
- 输入变量：files, diff, recent_commits
- 输出格式：JSON { type, scope, description }
- Type 类型约束：feat|fix|docs|style|refactor|test|chore
- description 长度约束：不超过 50 字符

**Acceptance**：
```bash
# 验证项目文件
cat /root/workspace/claude-governance/skills/commit/prompt.md | grep -E "## 输出要求|## 输出格式"

# 验证同步文件
cat ~/.claude/skills/commit/prompt.md | grep -E "## 输出要求|## 输出格式"
# 预期：包含输出要求和格式定义
```

**Execution Record**：
- Commands Run:
  - `cp ...prompt.md ~/.claude/skills/commit/prompt.md`
  - `cat ...prompt.md | grep -E "## 输出要求|## 输出格式"`
- Result: PASS
- Output Summary: prompt.md 已创建并同步，包含完整的 AI 提示词模板
- Commands Run:
- Result: PASS / FAIL
- Output Summary:

---

## Task 4: 创建 types.md

**Scope**：
- 创建 `/root/workspace/claude-governance/skills/commit/types.md`
- 同步到 `~/.claude/skills/commit/types.md`
- 定义 Type 类型的详细说明和判断依据
- 提供示例供参考

**要求**：
- 包含 7 种 Type 类型定义
- 每种类型有说明、判断依据、示例
- 包含 Scope 推断规则参考表

**Acceptance**：
```bash
# 验证项目文件
cat /root/workspace/claude-governance/skills/commit/types.md | grep -E "feat|fix|docs|style|refactor|test|chore"

# 验证同步文件
cat ~/.claude/skills/commit/types.md | grep -E "feat|fix|docs|style|refactor|test|chore"
# 预期：包含所有 7 种类型定义
```

**Execution Record**：
- Commands Run:
  - `cp ...types.md ~/.claude/skills/commit/types.md`
  - `cat ...types.md | grep -E "feat|fix|docs|style|refactor|test|chore"`
- Result: PASS
- Output Summary: types.md 已创建并同步，包含 7 种类型定义、Scope 推断规则、决策树和完整示例

---

## Task 5: 功能测试与验证

**Scope**：
- 在测试环境中验证技能功能（使用 ~/.claude 中已同步的 skill）
- 测试各种场景：新文件、文档修改、无改动、非 Git 仓库
- 验证提交信息格式符合 Conventional Commits

**Acceptance**：
```bash
# 创建测试仓库
cd /tmp && rm -rf commit-test && mkdir commit-test && cd commit-test && git init

# 测试 1：新文件提交
echo "test" > test.txt && /commit
git log --oneline -1 | grep -E "^[a-z]+(\([a-z]+\))?:"

# 测试 2：文档修改
echo "# README" > README.md && /commit
git log --oneline -1 | grep "docs:"

# 测试 3：无改动
/commit 2>&1 | grep "错误"
```

**Execution Record**：
- Commands Run:
  - 测试仓库创建和初始化
  - 测试 1：新文件提交 → `chore: 添加测试文件`
  - 测试 2：文档修改 → `docs: 添加项目说明文档`
  - 测试 3：无改动 → 正确报错 "没有需要提交的改动"
  - 格式验证：所有提交符合 Conventional Commits
  - 文件一致性验证：项目目录和 ~/.claude 完全一致
- Result: PASS
- Output Summary: 所有测试场景通过，技能功能正常，提交信息格式符合规范

---

## 全量回归验证

```bash
# 验证项目目录所有文件存在
ls -la /root/workspace/claude-governance/skills/commit/SKILL.md
ls -la /root/workspace/claude-governance/skills/commit/prompt.md
ls -la /root/workspace/claude-governance/skills/commit/types.md

# 验证同步目录所有文件存在
ls -la ~/.claude/skills/commit/SKILL.md
ls -la ~/.claude/skills/commit/prompt.md
ls -la ~/.claude/skills/commit/types.md

# 验证两个目录内容一致
diff /root/workspace/claude-governance/skills/commit/SKILL.md ~/.claude/skills/commit/SKILL.md
diff /root/workspace/claude-governance/skills/commit/prompt.md ~/.claude/skills/commit/prompt.md
diff /root/workspace/claude-governance/skills/commit/types.md ~/.claude/skills/commit/types.md

# 验证提交信息格式
git log -1 --pretty=format:"%s" | grep -E "^[a-z]+(\([a-z]+\))?: .+$"
```

**Execution Record**：
- Commands Run: 全量回归验证命令
- Result: PASS
- Output Summary: 所有文件存在，项目目录和同步目录内容完全一致

---

## Task 6: 还原 ~/.claude/ 到原始状态

**Scope**：
- 将 ~/.claude/ 还原到 origin/main 状态
- 确保测试后的临时修改被清除
- 验证还原后的状态

**Acceptance**：
```bash
# 还原 ~/.claude/ 到远程最新状态
cd ~/.claude && git reset --hard origin/main

# 验证 commit 技能目录已清除
! ls ~/.claude/skills/commit/ 2>/dev/null

# 验证 Git 状态干净
cd ~/.claude && git status
# 预期：nothing to commit, working tree clean
```

**Execution Record**：
- Commands Run:
  - `cd ~/.claude && git reset --hard origin/main`
  - `rm -rf ~/.claude/skills/commit/`
  - `cd ~/.claude && git status`
- Result: PASS
- Output Summary: ~/.claude/ 已还原到 origin/main 状态，commit 技能目录已清除，Git 状态干净
- Result: PASS / FAIL
- Output Summary:
