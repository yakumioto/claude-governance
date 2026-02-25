# Tasks：重构治理框架入口 - 去掉 classify 技能

**相关 Spec：** `docs/spec/remove-classify-skill.spec.md`
**创建日期：** 2026-02-25

---

## Execution Timeline

| Task ID | 状态 | 执行者 | 完成日期 |
|---------|------|--------|---------|
| 1 | completed | Claude | 2026-02-25 |
| 2 | completed | Claude | 2026-02-25 |
| 3 | completed | Claude | 2026-02-25 |
| 4 | completed | Claude | 2026-02-25 |
| 5 | completed | Claude | 2026-02-25 |
| 6 | completed | Claude | 2026-02-25 |

---

## Task 1：更新 CLAUDE.md - 整合决策树

**Scope：**
- 更新 "变更分级入口规则" 部分
- 将现有的分级规则整合为决策树形式
- 统一命名为 docs/fix/feat（小写）

**Acceptance：**
- [ ] 决策树使用 ASCII Art 呈现
- [ ] 包含三级判断流程（治理文档检查 → feat 条件检查 → docs 条件检查）
- [ ] 所有 "Docs" 替换为 "docs"
- [ ] feat 触发条件完整列出
- [ ] fix 适用范围完整列出
- [ ] docs 约束完整列出

**Execution Record：**
```
执行者：Claude
完成日期：2026-02-25
变更说明：
- 更新 "变更分级入口规则" 为决策树形式（ASCII Art）
- 将 "Docs" 级别更名为 "docs"（小写）
- 移除所有 /classify 技能引用
- 移除 "技能系统 → 入口层" 中的 /classify
- 更新 "第一层：请求入口检查" 中的提示信息

Commands Run:
- grep -n "docs" /root/.claude/CLAUDE.md | head -20
- grep -n "Docs" /root/.claude/CLAUDE.md
- grep -n "classify" /root/.claude/CLAUDE.md
- grep -A 35 "变更分级入口规则" /root/.claude/CLAUDE.md

Result: PASS
Output Summary: 决策树正确呈现，所有级别命名统一为小写 docs/fix/feat，classify 引用已全部移除
```

---

## Task 2：更新 fix/SKILL.md - 添加边界检查

**Scope：**
- 在 fix/SKILL.md 顶部添加边界检查逻辑
- 实现两阶段检查（启动时 + 探索时）
- 添加自动路由提示

**Acceptance：**
- [ ] 阶段 1 检查位于文件顶部
- [ ] 阶段 1 检查包含所有 feat 触发条件
- [ ] 阶段 1 检测到 feat 时输出路由提示
- [ ] 阶段 2 检查说明在 /explore 阶段的处理
- [ ] 阶段 2 检测到超出边界时输出路由提示
- [ ] 所有 "Docs" 替换为 "docs"

**Execution Record：**
```
执行者：Claude
完成日期：2026-02-25
变更说明：
- 在 fix/SKILL.md 顶部添加边界检查逻辑
- 阶段 1 检查：7 个 feat 触发条件
- 阶段 2 检查：4 个 fix 边界条件
- 两个阶段都输出路由提示

Commands Run:
- head -50 /root/.claude/skills/fix/SKILL.md
- grep -n "阶段 1\|阶段 2\|feat 条件" /root/.claude/skills/fix/SKILL.md
- grep -n "Docs" /root/.claude/skills/fix/SKILL.md

Result: PASS
Output Summary: 边界检查已添加到文件顶部，两阶段检查完整，无 Docs 大写残留
```

---

## Task 3：更新 feat/SKILL.md - 添加边界检查

**Scope：**
- 在 feat/SKILL.md 顶部添加边界检查逻辑
- 实现启动时检查（检测 docs 级别）
- 添加自动路由提示

**Acceptance：**
- [ ] 阶段 1 检查位于文件顶部
- [ ] 阶段 1 检查包含 docs 级别条件
- [ ] 阶段 1 检测到 docs 时输出提示
- [ ] 所有 "Docs" 替换为 "docs"

**Execution Record：**
```
执行者：Claude
完成日期：2026-02-25
变更说明：
- 在 feat/SKILL.md 顶部添加边界检查逻辑
- 阶段 1 检查：6 个 docs 级别条件
- 检测到 docs 时输出提示

Commands Run:
- head -35 /root/.claude/skills/feat/SKILL.md
- grep -n "docs 级别\|阶段 1\|仅修改文档" /root/.claude/skills/feat/SKILL.md | head -5
- grep -n "Docs" /root/.claude/skills/feat/SKILL.md

Result: PASS
Output Summary: 边界检查已添加到文件顶部，docs 级别条件完整，无 Docs 大写残留
```

---

## Task 4：更新 docs/SKILL.md - 统一命名

**Scope：**
- 更新 docs/SKILL.md 中的命名
- 统一使用小写 "docs"

**Acceptance：**
- [ ] 所有 "Docs 级别" 替换为 "docs 级别"
- [ ] 所有 "Docs — Docs / Trivial Changes" 替换为 "docs — Docs / Trivial Changes"
- [ ] 描述内容保持一致，仅修改命名

**Execution Record：**
```
执行者：Claude
完成日期：2026-02-25
变更说明：
- 将所有 "Docs 级别" 替换为 "docs 级别"（7 处）
- 移除对 /classify 的引用
- 更新注意事项，引导用户参考 CLAUDE.md 中的决策树

Commands Run:
- grep -n "Docs 级别\|Docs 变更\|Docs 约束\|Docs 的限制" /root/.claude/skills/docs/SKILL.md
- grep -n "classify" /root/.claude/skills/docs/SKILL.md
- grep -n "docs 级别" /root/.claude/skills/docs/SKILL.md | head -5

Result: PASS
Output Summary: 所有 Docs 已统一为小写 docs，/classify 引用已移除
```

---

## Task 5：删除 classify 技能

**Scope：**
- 删除 skills/classify/ 目录及所有文件

**Acceptance：**
- [ ] skills/classify/ 目录已删除
- [ ] skills/classify/SKILL.md 已删除
- [ ] 其他技能中不再引用 classify

**Execution Record：**
```
执行者：Claude
完成日期：2026-02-25
变更说明：
- 删除 skills/classify/ 目录及 SKILL.md 文件
- 验证其他技能中无 classify 引用

Commands Run:
- ls -la /root/.claude/skills/classify/
- rm -rf /root/.claude/skills/classify/
- ls /root/.claude/skills/classify/ 2>&1
- grep -r "classify" /root/.claude/skills/*/SKILL.md 2>/dev/null

Result: PASS
Output Summary: classify 目录已完全删除，其他技能中无残留引用
```

---

## Task 6：回归测试

**Scope：**
- 执行完整回归测试
- 验证所有功能正常

**Acceptance：**
- [ ] 决策树规则完整性测试通过（手动遍历）
- [ ] fix 边界检查测试通过（/fix "修复 bug" ✓，/fix "新增 API" ✗）
- [ ] feat 边界检查测试通过（/feat "新增功能" ✓，/feat "修改 README" ✗）
- [ ] /explore 行为不变（执行正常流程）
- [ ] /design 行为不变（执行正常流程）
- [ ] /doc-gen 行为不变（执行正常流程）
- [ ] /execute 行为不变（执行正常流程）
- [ ] 受保护目录规则不变（尝试修改 docs/spec/，确认拒绝）

**Execution Record：**
```
执行者：Claude
完成日期：2026-02-25
变更说明：
- 执行完整回归测试
- 修复 execute/SKILL.md 中的 "Docs" 大写残留
- 清理备份文件

Commands Run:
- cat /root/.claude/CLAUDE.md | grep -A 30 "变更分级入口规则"
- cat /root/.claude/skills/fix/SKILL.md | head -30
- cat /root/.claude/skills/feat/SKILL.md | head -30
- ls /root/.claude/skills/classify/ 2>&1 | grep "No such file"
- grep -r "Docs 禁用\|Docs 级别" /root/.claude/skills/ /root/.claude/CLAUDE.md 2>/dev/null
- grep -r "classify" /root/.claude/skills/ /root/.claude/CLAUDE.md 2>/dev/null
- ls -la /root/.claude/skills/ | grep -E "^d"

Result: PASS
Output Summary:
- 决策树正确呈现
- fix/feat 边界检查已添加
- classify 目录已删除
- 所有命名已统一为小写 docs/fix/feat
- 无 classify 残留引用
- 技能列表：commit, design, doc-gen, docs, execute, explore, feat, fix
```

---

## 全量回归命令

```bash
# 验证决策树可读性
cat /root/.claude/CLAUDE.md | grep -A 30 "变更分级入口规则"

# 验证 fix 技能边界检查
cat /root/.claude/skills/fix/SKILL.md | head -30

# 验证 feat 技能边界检查
cat /root/.claude/skills/feat/SKILL.md | head -30

# 验证 classify 已删除
ls /root/.claude/skills/classify/ 2>&1 | grep "No such file"

# 验证命名统一
grep -r "Docs 级别" /root/.claude/skills/ /root/.claude/CLAUDE.md || echo "已统一为小写"
```
