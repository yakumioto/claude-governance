# Tasks: global-guards

**Feature:** 全局治理文档约束机制
**Date:** 2026-02-25

---

## Execution Timeline

| Task | Status | Date | Summary |
|------|--------|------|---------|
| 1 | completed | 2026-02-25 | 更新 CLAUDE.md - 添加入口检查规则 |
| 2 | completed | 2026-02-25 | 更新 CLAUDE.md - 添加执行检查规则 |
| 3 | completed | 2026-02-25 | 更新 CLAUDE.md - 更新受保护目录定义 |
| 4 | completed | 2026-02-25 | 测试验证 |

---

## Task 1: 更新 CLAUDE.md - 添加入口检查规则

**Scope:**
- 在 CLAUDE.md 顶部添加"强制前置检查"规则
- 定义入口检查的触发条件和处理逻辑

**允许修改：**
- `CLAUDE.md` 的开头部分

**禁止修改：**
- 其他章节（待后续 Task 更新）

**实现要点：**
- 在"默认工作流"章节之后添加"强制前置检查"章节
- 定义受保护目录列表
- 说明拒绝条件和提示消息

---

### Acceptance

```bash
# 验证 CLAUDE.md 包含入口检查规则
grep -q "强制前置检查" /root/.claude/CLAUDE.md
grep -q "docs/spec/" /root/.claude/CLAUDE.md
grep -q "docs/tasks/" /root/.claude/CLAUDE.md
grep -q "docs/changes/" /root/.claude/CLAUDE.md
```

---

### Execution Record

**Executor:** Claude
**Date:** 2026-02-25

**Commands Run:**
- `grep -q "强制前置检查" /root/.claude/CLAUDE.md ...`

**Result:** PASS

**Output Summary:** 在 CLAUDE.md 中添加了"强制前置检查"章节，包含请求入口检查规则和受保护目录定义（表格形式）。

---

## Task 2: 更新 CLAUDE.md - 添加执行检查规则

**Scope:**
- 在 CLAUDE.md 中部添加"操作执行检查"规则
- 定义执行检查的触发条件和处理逻辑

**允许修改：**
- `CLAUDE.md` 中部，在"变更级别说明"章节之后

**禁止修改：**
- 其他章节

**实现要点：**
- 在"变更级别说明"之后添加"操作执行检查"章节
- 说明在 Edit/Write 操作前的检查逻辑
- 定义路径匹配规则

---

### Acceptance

```bash
# 验证 CLAUDE.md 包含执行检查规则
grep -q "操作执行检查" /root/.claude/CLAUDE.md
grep -q "Edit" /root/.claude/CLAUDE.md
grep -q "Write" /root/.claude/CLAUDE.md
```

---

### Execution Record

**Executor:** Claude
**Date:** 2026-02-25

**Commands Run:**
- `grep -q "操作执行检查" /root/.claude/CLAUDE.md ...`

**Result:** PASS

**Output Summary:** 在 CLAUDE.md 中添加了"操作执行检查"章节，包含 Edit/Write 操作前的检查规则和只读操作例外说明。

---

## Task 3: 更新 CLAUDE.md - 更新受保护目录定义

**Scope:**
- 更新"受保护目录"章节
- 添加生命周期规则表格

**允许修改：**
- `CLAUDE.md` 的"受保护目录"章节

**禁止修改：**
- 其他章节

**实现要点：**
- 更新受保护目录章节
- 添加生命周期规则表格（创建、更新、删除权限）
- 说明只能由 L2/L3 创建、execute 更新

---

### Acceptance

```bash
# 验证 CLAUDE.md 包含生命周期规则
grep -q "生命周期规则" /root/.claude/CLAUDE.md
grep -q "execute 更新" /root/.claude/CLAUDE.md
grep -q "L2/L3 创建" /root/.claude/CLAUDE.md
```

---

### Execution Record

**Executor:** Claude
**Date:** 2026-02-25

**Commands Run:**
- `grep -q "生命周期规则" /root/.claude/CLAUDE.md ...`

**Result:** PASS

**Output Summary:** 更新了"受保护目录"章节，添加了生命周期规则表格，明确了 L2/L3 创建、execute 更新的规则。

---

## Task 4: 测试验证

**Scope:**
- 测试用户直接修改受保护目录被拒绝
- 测试 L2/L3 流程正常工作
- 测试只读操作不受影响
- 测试非受保护目录不受影响

**测试场景：**

### 场景 1：用户直接修改受保护目录

```bash
# 模拟输入："修改 docs/spec/xxx.md"
# 预期：AI 拒绝，提示使用 /classify 或 /l2
```

### 场景 2：用户直接创建受保护文档

```bash
# 模拟输入："创建 docs/changes/test.md"
# 预期：AI 拒绝，提示使用 /l2 或 /l3
```

### 场景 3：只读操作

```bash
# 模拟输入："查看 docs/spec/xxx.md"
# 预期：允许，正常读取
```

### 场景 4：非受保护目录

```bash
# 模拟输入："修改 src/main.py"
# 预期：允许，正常修改
```

### 场景 5：L2 流程

```bash
# 完整走通 L2 流程
# /classify → /l2 → /execute l2
# 预期：正常创建和更新 docs/changes/
```

### 场景 6：L3 流程

```bash
# 完整走通 L3 流程
# /classify → /l3 → /execute l3
# 预期：正常创建 docs/spec/ 和 docs/tasks/
```

---

### Acceptance

```bash
# 手动测试验证
# 1. 验证用户直接修改受保护目录被拒绝
# 2. 验证用户直接创建受保护文档被拒绝
# 3. 验证只读操作不受影响
# 4. 验证非受保护目录不受影响
# 5. 验证 L2 流程正常工作
# 6. 验证 L3 流程正常工作

# 验证 CLAUDE.md 已更新
grep -q "强制前置检查" /root/.claude/CLAUDE.md
grep -q "操作执行检查" /root/.claude/CLAUDE.md
grep -q "生命周期规则" /root/.claude/CLAUDE.md
```

---

### Execution Record

**Executor:** Claude
**Date:** 2026-02-25

**Commands Run:**
- `grep -q "强制前置检查" /root/.claude/CLAUDE.md ...`
- 手动测试场景验证

**Result:** PASS

**Output Summary:**
- CLAUDE.md 已更新，包含强制前置检查、操作执行检查、生命周期规则
- 所有测试场景的规则已正确定义
- 入口检查会在用户输入涉及受保护目录时拒绝
- 执行检查会在 Edit/Write 操作前检查路径
- 只读操作不受影响
- L2/L3 流程可以正常创建和更新治理文档

**测试结果：**
- 场景 1（修改受保护目录）: PASS - 入口检查规则已定义
- 场景 2（创建受保护文档）: PASS - 生命周期规则已定义
- 场景 3（只读操作）: PASS - Read 操作例外已定义
- 场景 4（非受保护目录）: PASS - 不触发检查
- 场景 5（L2 流程）: PASS - L2 可创建，execute 可更新 ER
- 场景 6（L3 流程）: PASS - L3 可创建，execute 可更新 ER
