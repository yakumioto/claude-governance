# governance-docs-protection 任务拆解（Tasks）

## Execution Timeline（执行时间线）

| Task | Date | Result |
|------|------|--------|
| 1 | 2026-02-25 | PASS |
| 2 | 2026-02-25 | PASS |
| 3 | 2026-02-25 | PASS |

---

## Task 1：修改 CLAUDE.md 增加文档保护规则

### Scope（范围）

允许修改：

- `CLAUDE.md` — L1 部分的文档修改条件
- `CLAUDE.md` — L2 部分的执行流程

禁止修改：

- 其他章节
- 其他文件

---

### 实现要点

**L1 部分修改：**
1. 将 "仅修改文档或注释（README、docs/**、*.md、代码注释）" 修改为：
   - "仅修改文档或注释（README.md、docs/guide/**、*.md、代码注释）"
   - 新增："禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）"

**L2 部分修改：**
1. 在 "执行流程：Change → Record" 之后，"要求：" 之前，新增区块：
   ```markdown
   ## 受保护目录（禁止修改）

   - docs/spec/ — 设计基准，不应追溯修改
   - docs/tasks/ — 执行记录，不应追溯修改
   - docs/changes/ — 变更历史，仅可追加新文件
   ```

---

### Acceptance（验收）

执行以下命令：

```bash
# 验证 L1 部分包含保护规则
grep -A 3 "禁止修改治理文档" CLAUDE.md

# 验证 L2 部分包含受保护目录区块
grep -A 3 "受保护目录" CLAUDE.md
```

预期结果：

- L1 部分包含 "禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）"
- L2 部分包含 "受保护目录（禁止修改）" 区块

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:

- `grep -A 3 "禁止修改治理文档" CLAUDE.md`
- `grep -A 3 "受保护目录" CLAUDE.md`

Result: PASS

Output Summary:

- L1 部分成功添加 "禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）"
- L2 部分成功添加 "受保护目录（禁止修改）" 区块
- docs/spec/ — 设计基准，不应追溯修改
- docs/tasks/ — 执行记录，不应追溯修改
- docs/changes/ — 变更历史，仅可追加新文件

Notes:

- 无

---

## Task 2：修改 execute/SKILL.md 增加 L2 执行保护规则

### Scope（范围）

允许修改：

- `skills/execute/SKILL.md` — L2 执行规则部分

禁止修改：

- L3 执行规则
- 其他章节
- 其他文件

---

### 实现要点

**L2 执行规则修改：**
1. 在 "L2 执行规则" 部分，最后一条规则后新增：
   ```markdown
   6. 禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）。
   ```

---

### Acceptance（验收）

执行以下命令：

```bash
# 验证 L2 执行规则包含保护规则
grep "禁止修改 docs/spec/" skills/execute/SKILL.md
```

预期结果：

- L2 执行规则包含 "禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）"

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:

- `grep "禁止修改 docs/spec/" skills/execute/SKILL.md`

Result: PASS

Output Summary:

- L2 执行规则成功添加第 6 条："禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）"

Notes:

- 无

---

## Task 3：全量回归验证

### Scope（范围）

验证范围：

- CLAUDE.md 和 execute/SKILL.md 的修改正确
- 不影响 L3 流程
- 不影响正常文档修改

---

### 实现要点

1. 验证 CLAUDE.md 中 L1 和 L2 规则修改正确
2. 验证 execute/SKILL.md 中 L2 执行规则修改正确
3. 确认不影响 L3 流程
4. 确认不影响正常文档修改（README.md 等）

---

### Acceptance（验收）

执行以下命令：

```bash
# 1. 验证 L1 规则
grep -A 5 "满足以下全部条件即可进入 L1" CLAUDE.md | grep "禁止修改治理文档"

# 2. 验证 L2 受保护目录区块
grep -A 5 "受保护目录" CLAUDE.md | grep "docs/spec/"

# 3. 验证 execute skill L2 规则
grep "禁止修改 docs/spec/" skills/execute/SKILL.md

# 4. 验证 L3 流程不受影响
grep -A 3 "## 1. Spec 阶段" CLAUDE.md | grep "docs/spec/"
```

预期结果：

- 所有验证命令输出包含预期内容
- L3 流程说明仍然正确

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:

- `grep -A 5 "满足以下全部条件即可进入 L1" CLAUDE.md | grep "禁止修改治理文档"`
- `grep -A 5 "受保护目录" CLAUDE.md | grep "docs/spec/"`
- `grep "禁止修改 docs/spec/" skills/execute/SKILL.md`
- `grep -A 3 "## 1. Spec 阶段" CLAUDE.md | grep "docs/spec/"`

Result: PASS

Output Summary:

- L1 规则：✓ 包含 "禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）"
- L2 受保护目录：✓ 包含 "docs/spec/ — 设计基准，不应追溯修改"
- execute skill L2 规则：✓ 包含 "禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）"
- L3 流程：✓ Spec 阶段仍然使用 docs/spec/，流程不受影响

Notes:

- 全量回归验证通过，治理文档保护规则已成功部署
- 不影响 L3 流程（Spec/Tasks 阶段仍可正常生成对应文档）
- 不影响正常文档修改（README.md 等项目文档仍可 L1 修改）
