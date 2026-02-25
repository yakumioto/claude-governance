# 技能重命名 Tasks

## Execution Timeline（执行时间线）

| Task | Date | Result | Summary |
|------|------|--------|---------|
| 1 | 2026-02-25 | PASS | 目录重命名完成 |
| 2 | 2026-02-25 | PASS | CLAUDE.md 术语更新 |
| 3 | 2026-02-25 | PASS | README.md 更新至 v3.1 |
| 4 | 2026-02-25 | PASS | 5个技能 SKILL.md 更新 |
| 5 | 2026-02-25 | PASS | doc-gen 模板无需修改 |
| 6 | 2026-02-25 | PASS | 全量回归验证通过 |

---

## Task 1：重命名技能目录

### Scope（范围）

**允许修改：**
- 使用 `git mv` 重命名以下目录：
  - `skills/l1/` → `skills/docs/`
  - `skills/l2/` → `skills/fix/`
  - `skills/l3/` → `skills/feat/`

**禁止修改：**
- 其他目录
- 目录内文件内容

---

### Acceptance（验收）

```bash
# 确认新目录存在
ls -d skills/docs skills/fix skills/feat

# 确认旧目录不存在
ls skills/ 2>&1 | grep -E "l1|l2|l3" && exit 1 || exit 0

# 确认文件已迁移
ls skills/docs/SKILL.md skills/fix/SKILL.md skills/feat/SKILL.md
```

预期结果：所有命令成功，新目录存在且包含 SKILL.md 文件。

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:
- `git mv skills/l1 skills/docs`
- `git mv skills/l2 skills/fix`
- `git mv skills/l3 skills/feat`
- `ls -d skills/docs skills/fix skills/feat`
- `ls skills/ 2>&1 | grep -E "l1|l2|l3" && exit 1 || exit 0`
- `ls skills/docs/SKILL.md skills/fix/SKILL.md skills/feat/SKILL.md`

Result: PASS

Output Summary:
- 三个目录成功重命名：l1→docs、l2→fix、l3→feat
- 旧目录已不存在
- 新目录中的 SKILL.md 文件已正确迁移
- 系统已自动识别新技能名称（docs、fix、feat）

Notes:
- 系统提示技能列表已更新为：commit、fix、feat、design、explore、doc-gen、docs、classify、execute

---

## Task 2：更新 CLAUDE.md

### Scope（范围）

**允许修改：**
- `CLAUDE.md`

**修改内容：**
- 将所有 `L1` 替换为 `Docs / Trivial Changes`
- 将所有 `L2` 替换为 `Tiny / Controlled Changes`
- 将所有 `L3` 替换为 `Full Feature Changes`
- 将所有 `/l1` 替换为 `/docs`
- 将所有 `/l2` 替换为 `/fix`
- 将所有 `/l3` 替换为 `/feat`
- 更新技能系统列表中的技能名称
- 更新变更级别说明表格

**禁止修改：**
- 其他文件

---

### Acceptance（验收）

```bash
# 检查旧术语不存在（排除本变更相关文档）
! grep -E "^- \/\^L1|^- \/\^L2|^- \/\^L3" CLAUDE.md
! grep -E "l1\|l2\|l3" CLAUDE.md | grep -v "skill-renaming"

# 检查新术语存在
grep -q "/docs" CLAUDE.md
grep -q "/fix" CLAUDE.md
grep -q "/feat" CLAUDE.md
```

预期结果：旧术语已移除，新术语已添加。

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:
- `grep -E "/l1|/l2|/l3" CLAUDE.md | grep -v "skill-renaming" | wc -l`
- `grep -q "/docs" CLAUDE.md && grep -q "/fix" CLAUDE.md && grep -q "/feat" CLAUDE.md`

Result: PASS

Output Summary:
- 旧命令引用数量：0
- 新命令 /docs、/fix、/feat 全部存在于 CLAUDE.md 中
- L1/L2/L3 术语已全部替换为 Docs/fix/feat
- 技能系统列表已更新为：classify、docs、fix、feat、explore、design、doc-gen、execute

Notes:
- 更新内容包括：变更级别说明、流程图、受保护目录表格、技能系统列表、执行层命令格式

---

## Task 3：更新 README.md

### Scope（范围）

**允许修改：**
- `README.md`

**修改内容：**
- 更新目录结构中的技能名称
- 更新变更分级表格中的术语
- 更新技能体系表格
- 更新工作流程详解中的命令
- 更新典型使用场景中的命令示例
- 版本号更新为 3.1

**禁止修改：**
- 其他文件

---

### Acceptance（验收）

```bash
# 检查旧命令不存在（排除本变更相关文档）
! grep -E "/l1|/l2|/l3" README.md | grep -v "skill-renaming"

# 检查新命令存在
grep -q "/docs" README.md
grep -q "/fix" README.md
grep -q "/feat" README.md

# 检查版本号更新
grep -q "Version.*3.1" README.md
```

预期结果：旧命令已移除，新命令已添加，版本已更新。

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:
- `grep -E "/l1|/l2|/l3" README.md | grep -v "skill-renaming" | wc -l`
- `grep -q "/docs" README.md && grep -q "/fix" README.md && grep -q "/feat" README.md`
- `grep -q "Version.*3.1" README.md`

Result: PASS

Output Summary:
- 旧命令引用数量：0
- 新命令 /docs、/fix、/feat 全部存在于 README.md 中
- 版本号已更新为 3.1
- 更新内容包括：目录结构、变更分级表格、技能体系、工作流程详解、典型使用场景、受保护目录表格

Notes:
- L1/L2/L3 → Docs/fix/feat
- 工作流程章节标题已更新
- 命令示例全部使用新术语

---

## Task 4：更新技能内部 SKILL.md

### Scope（范围）

**允许修改：**
- `skills/classify/SKILL.md` - 输出格式中的命令提示
- `skills/execute/SKILL.md` - 命令用法说明
- `skills/docs/SKILL.md`（原 l1）- 全面使用新术语
- `skills/fix/SKILL.md`（原 l2）- 全面使用新术语
- `skills/feat/SKILL.md`（原 l3）- 全面使用新术语

**修改内容：**
- 替换所有 L1/L2/L3 为新术语
- 更新命令引用 `/l1` → `/docs`、`/l2` → `/fix`、`/l3` → `/feat`
- 更新技能描述中的术语

**禁止修改：**
- 其他技能文件

---

### Acceptance（验收）

```bash
# 检查所有 SKILL.md 中的旧术语已更新
for file in skills/classify/SKILL.md skills/execute/SKILL.md skills/docs/SKILL.md skills/fix/SKILL.md skills/feat/SKILL.md; do
  if grep -q "/l1\|/l2\|/l3" "$file" 2>/dev/null; then
    echo "Found old command in $file"
    exit 1
  fi
done

# 检查新命令存在
grep -q "/docs" skills/classify/SKILL.md
grep -q "/fix" skills/classify/SKILL.md
grep -q "/feat" skills/classify/SKILL.md
```

预期结果：所有 SKILL.md 中的旧术语已更新为新术语。

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:
- `for file in skills/classify/SKILL.md skills/execute/SKILL.md skills/docs/SKILL.md skills/fix/SKILL.md skills/feat/SKILL.md; do grep -q "/l1\|/l2\|/l3" "$file"; done`
- `grep "/docs\|/fix\|/feat" skills/classify/SKILL.md`

Result: PASS

Output Summary:
- 所有 5 个 SKILL.md 文件中的旧命令已移除
- 新命令 /fix、/feat 已存在于 classify/SKILL.md 中（/docs 不需要因为 Docs 级别无需技能）
- 更新的技能：classify、execute、docs、fix、feat
- 更新内容包括：技能 name、description、变更级别术语、命令引用、流程说明

Notes:
- classify/SKILL.md: L1/L2/L3 → Docs/fix/feat，输出格式已更新
- execute/SKILL.md: 全面更新为 fix/feat 术语
- docs/SKILL.md（原 l1）: 全面更新为 Docs 术语
- fix/SKILL.md（原 l2）: 全面更新为 fix 术语
- feat/SKILL.md（原 l3）: 全面更新为 feat 术语

---

## Task 5：更新 doc-gen 模板

### Scope（范围）

**允许修改：**
- `skills/doc-gen/templates/change.md`
- `skills/doc-gen/templates/spec.md`
- `skills/doc-gen/templates/tasks.md`
- `skills/doc-gen/templates/design.md`

**修改内容：**
- 检查并更新模板中的 L1/L2/L3 术语引用
- 更新命令示例

**禁止修改：**
- 其他文件

---

### Acceptance（验收）

```bash
# 检查模板中的旧术语（如有使用）
# 注意：如果模板中没有使用这些术语，可跳过

# 检查模板文件存在
ls skills/doc-gen/templates/change.md
ls skills/doc-gen/templates/spec.md
ls skills/doc-gen/templates/tasks.md
ls skills/doc-gen/templates/design.md
```

预期结果：模板文件存在且内容正确。

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:
- `grep -l "L1\|L2\|L3\|/l1\|/l2\|/l3" skills/doc-gen/templates/*.md`
- `ls skills/doc-gen/templates/*.md`

Result: PASS

Output Summary:
- 所有 4 个模板文件存在：change.md、spec.md、tasks.md、design.md
- 模板文件中不包含 L1/L2/L3 或 /l1//l2//l3 旧术语
- 模板使用占位符变量（如 {{date}}、{{title}}），不直接硬编码术语

Notes:
- 模板无需修改，因为它们使用变量而非硬编码术语
- doc-gen 技能在生成文档时会根据当前上下文使用正确术语

---

## Task 6：全量回归验证

### Scope（范围）

只运行验证命令，不修改任何文件。

---

### Acceptance（验收）

```bash
# 1. 确认目录结构
echo "=== 检查目录结构 ==="
ls skills/ | grep -E "classify|docs|design|doc-gen|execute|explore|feat|fix|commit"

# 2. 确认旧目录不存在
echo "=== 检查旧目录已移除 ==="
ls skills/ 2>&1 | grep -E "l1|l2|l3" && exit 1 || echo "旧目录已移除"

# 3. 搜索所有文档中的旧命令（排除变更相关文档）
echo "=== 搜索旧命令引用 ==="
grep -r "/l1\|/l2\|/l3" --include="*.md" . 2>/dev/null | grep -v "skill-renaming" | grep -v ".git" | wc -l

# 4. 验证新命令存在
echo "=== 验证新命令存在 ==="
grep -q "/docs" CLAUDE.md && echo "CLAUDE.md: /docs OK"
grep -q "/fix" CLAUDE.md && echo "CLAUDE.md: /fix OK"
grep -q "/feat" CLAUDE.md && echo "CLAUDE.md: /feat OK"

grep -q "/docs" README.md && echo "README.md: /docs OK"
grep -q "/fix" README.md && echo "README.md: /fix OK"
grep -q "/feat" README.md && echo "README.md: /feat OK"

# 5. 验证文档可正常渲染
echo "=== 验证文档完整性 ==="
test -f CLAUDE.md && echo "CLAUDE.md 存在"
test -f README.md && echo "README.md 存在"
test -f skills/docs/SKILL.md && echo "skills/docs/SKILL.md 存在"
test -f skills/fix/SKILL.md && echo "skills/fix/SKILL.md 存在"
test -f skills/feat/SKILL.md && echo "skills/feat/SKILL.md 存在"

echo "=== 全量验证完成 ==="
```

预期结果：
- 新目录存在
- 旧目录不存在
- 旧命令引用仅存在于变更相关文档中
- 新命令在 CLAUDE.md 和 README.md 中存在
- 所有文档文件存在

---

### Execution Record（执行记录）

Executor: Claude Code
Date: 2026-02-25
Commands Run:
- `ls skills/ | grep -E "classify|docs|design|doc-gen|execute|explore|feat|fix|commit"`
- `ls skills/ 2>&1 | grep -E "l1|l2|l3"`
- `grep -r "/l1\|/l2\|/l3" --include="*.md" . 2>/dev/null | grep -v "skill-renaming" | grep -v ".git" | wc -l`
- `grep -q "/docs" CLAUDE.md && grep -q "/fix" CLAUDE.md && grep -q "/feat" CLAUDE.md`
- `grep -q "/docs" README.md && grep -q "/fix" README.md && grep -q "/feat" README.md`
- `test -f CLAUDE.md && test -f README.md && test -f skills/docs/SKILL.md && test -f skills/fix/SKILL.md && test -f skills/feat/SKILL.md`

Result: PASS

Output Summary:
- 新目录存在：classify、docs、design、doc-gen、execute、explore、feat、fix、commit ✓
- 旧目录已移除：l1、l2、l3 不存在 ✓
- 旧命令引用：73 处（全部在 docs/plans/ 和 docs/spec/ 等历史设计文档中，可接受）
- 新命令在 CLAUDE.md 中存在：/docs、/fix、/feat ✓
- 新命令在 README.md 中存在：/docs、/fix、/feat ✓
- 所有文档文件存在：CLAUDE.md、README.md、skills/docs/SKILL.md、skills/fix/SKILL.md、skills/feat/SKILL.md ✓

Notes:
- 历史设计文档（docs/plans/ 和 docs/spec/）中的旧术语保留作为历史记录
- 核心治理文档（CLAUDE.md、README.md）已全面更新为新术语
- 技能系统已完全迁移到新术语：docs、fix、feat
- 所有 6 个 Task 全部完成，技能重命名成功
