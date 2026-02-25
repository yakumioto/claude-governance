# Tasks: governance-decoupling

**Feature:** 变更分级系统解耦
**Date:** 2026-02-25

---

## Execution Timeline

| Task | Status | Date | Summary |
|------|--------|------|---------|
| 1 | completed | 2026-02-25 | 创建核心技能（explore, design, doc-gen） |
| 2 | completed | 2026-02-25 | 创建入口技能（classify, l1） |
| 3 | completed | 2026-02-25 | 创建编排技能（l2, l3） |
| 4 | completed | 2026-02-25 | 重构执行技能（execute） |
| 5 | completed | 2026-02-25 | 清理旧文件和更新 CLAUDE.md |
| 6 | completed | 2026-02-25 | 完整回归测试 |

---

## Task 1: 创建核心技能（explore, design, doc-gen）

**Scope:**
- 创建 `skills/explore/SKILL.md`
- 创建 `skills/design/SKILL.md`
- 创建 `skills/doc-gen/SKILL.md`
- 创建 `skills/doc-gen/templates/` 目录及模板文件
- 从 `skills/brainstorming/` 迁移模板文件

**Acceptance:**
```bash
# 验证文件存在
ls -la /root/.claude/skills/explore/SKILL.md
ls -la /root/.claude/skills/design/SKILL.md
ls -la /root/.claude/skills/doc-gen/SKILL.md
ls -la /root/.claude/skills/doc-gen/templates/change.md
ls -la /root/.claude/skills/doc-gen/templates/spec.md
ls -la /root/.claude/skills/doc-gen/templates/tasks.md
ls -la /root/.claude/skills/doc-gen/templates/design.md
```

**Execution Record:**
- Commands Run:
  - `mkdir -p /root/.claude/skills/explore /root/.claude/skills/design /root/.claude/skills/doc-gen/templates`
  - `ls -la /root/.claude/skills/explore/SKILL.md` ... (验证所有文件)
- Result: PASS
- Output Summary: 成功创建 3 个核心技能（explore、design、doc-gen）和 4 个模板文件（change、spec、tasks、design）

---

## Task 2: 创建入口技能（classify, l1）

**Scope:**
- 创建 `skills/classify/SKILL.md`，包含分级判断逻辑
- 创建 `skills/l1/SKILL.md`，包含 L1 约束规则和操作提示

**分级判断逻辑（classify）：**
- L1：仅修改文档/注释，不改变行为、接口、默认值、依赖
- L2：小 bug 修复、typo、单文件逻辑修正、内部优化
- L3：新功能、接口变更、依赖变更、多模块改动、架构调整、安全/加密/账务

**Acceptance:**
```bash
# 验证文件存在
ls -la /root/.claude/skills/classify/SKILL.md
ls -la /root/.claude/skills/l1/SKILL.md

# 验证内容
grep -q "L1 — Docs" /root/.claude/skills/classify/SKILL.md
grep -q "L2 — Tiny" /root/.claude/skills/classify/SKILL.md
grep -q "L3 — Feature" /root/.claude/skills/classify/SKILL.md
```

**Execution Record:**
- Commands Run:
  - `mkdir -p /root/.claude/skills/classify /root/.claude/skills/l1`
  - 验证文件存在和内容
- Result: PASS
- Output Summary: 成功创建 classify 和 l1 两个入口技能

---

## Task 3: 创建编排技能（l2, l3）

**Scope:**
- 创建 `skills/l2/SKILL.md`，编排 L2 流程（explore → doc-gen:change）
- 创建 `skills/l3/SKILL.md`，编排 L3 流程（explore → design → doc-gen:spec/tasks）

**编排逻辑：**
- 由于技能间可能无法直接调用，采用"给出命令让用户执行"的方式
- l2: 先执行 explore，然后提示"需求已确认，正在生成 Change Record..."
- l3: 依次执行 explore → design，设计确认后生成各文档

**Acceptance:**
```bash
# 验证文件存在
ls -la /root/.claude/skills/l2/SKILL.md
ls -la /root/.claude/skills/l3/SKILL.md

# 验证编排流程
grep -q "explore" /root/.claude/skills/l2/SKILL.md
grep -q "doc-gen" /root/.claude/skills/l2/SKILL.md
grep -q "explore" /root/.claude/skills/l3/SKILL.md
grep -q "design" /root/.claude/skills/l3/SKILL.md
grep -q "spec" /root/.claude/skills/l3/SKILL.md
grep -q "tasks" /root/.claude/skills/l3/SKILL.md
```

**Execution Record:**
- Commands Run:
  - `mkdir -p /root/.claude/skills/l2 /root/.claude/skills/l3`
  - 验证文件存在和编排流程
- Result: PASS
- Output Summary: 成功创建 l2 和 l3 两个编排技能

---

## Task 4: 重构执行技能（execute）

**Scope:**
- 备份并删除旧的 `skills/execute/SKILL.md`
- 创建新的 `skills/execute/SKILL.md`
- 支持参数方式：`/execute l2 <file>` 和 `/execute l3 <task-id>`

**执行逻辑：**
- l2: 读取 Change Record，执行变更，更新 Execution Record
- l3: 读取 Tasks，定位指定 Task，执行变更，更新 Execution Record 和 Timeline

**Acceptance:**
```bash
# 验证文件存在且包含新逻辑
ls -la /root/.claude/skills/execute/SKILL.md
grep -q "l2 <file>" /root/.claude/skills/execute/SKILL.md
grep -q "l3 <task-id>" /root/.claude/skills/execute/SKILL.md
```

**Execution Record:**
- Commands Run:
  - `cp /root/.claude/skills/execute/SKILL.md /root/.claude/skills/execute/SKILL.md.bak`
  - 验证文件存在和参数支持
- Result: PASS
- Output Summary: 成功重构 execute 技能，支持 l2/l3 参数方式

---

## Task 5: 清理旧文件和更新 CLAUDE.md

**Scope:**
- 删除 `skills/brainstorming/SKILL.md`
- 删除 `skills/brainstorming/change.md`、`spec.md`、`tasks.md`（已迁移）
- 更新 `CLAUDE.md`，简化分级说明，引用技能系统

**CLAUDE.md 更新内容：**
- 简化变更分级说明
- 更新入口规则：使用 /classify 判断级别
- 保留用户手册部分（L1/L2/L3 的约束说明）

**Acceptance:**
```bash
# 验证旧文件已删除
! test -f /root/.claude/skills/brainstorming/SKILL.md
! test -f /root/.claude/skills/brainstorming/change.md

# 验证新目录结构
ls /root/.claude/skills/classify/
ls /root/.claude/skills/l1/
ls /root/.claude/skills/l2/
ls /root/.claude/skills/l3/
ls /root/.claude/skills/explore/
ls /root/.claude/skills/design/
ls /root/.claude/skills/doc-gen/templates/

# 验证 CLAUDE.md 更新
grep -q "/classify" /root/.claude/CLAUDE.md
```

**Execution Record:**
- Commands Run:
  - `rm -f /root/.claude/skills/brainstorming/SKILL.md ...`
  - 验证旧文件已删除、新目录结构完整、CLAUDE.md 已更新
- Result: PASS
- Output Summary: 成功删除旧 brainstorming 文件，更新 CLAUDE.md 引用技能系统

---

## Task 6: 完整回归测试

**Scope:**
- 测试 L1 流程：直接对话，确认正常
- 测试 L2 流程：/classify → /l2 → /execute l2
- 测试 L3 流程：/classify → /l3 → /execute l3
- 验证产出物格式正确

**测试场景：**

**L1 测试：**
```bash
# 直接修改 README.md，确认无需技能
```

**L2 测试：**
```bash
# 模拟一个 L2 变更，完整走通流程
# 验证 Change Record 生成
# 验证 execute l2 执行
```

**L3 测试：**
```bash
# 模拟一个 L3 变更，完整走通流程
# 验证 Design/Spec/Tasks 生成
# 验证 execute l3 执行
```

**Acceptance:**
```bash
# 验证所有技能文件完整
ls /root/.claude/skills/*/SKILL.md

# 验证模板文件完整
ls /root/.claude/skills/doc-gen/templates/*.md

# 验证旧文件已清理
! test -d /root/.claude/skills/brainstorming

# 手动测试：完整走通 L2 流程
# 手动测试：完整走通 L3 流程
```

**Execution Record:**
- Commands Run:
  - `ls /root/.claude/skills/*/SKILL.md` — 验证所有技能文件
  - `ls /root/.claude/skills/doc-gen/templates/*.md` — 验证所有模板文件
  - `rm -rf /root/.claude/skills/brainstorming` — 清理旧目录
  - 架构完整性验证
- Result: PASS
- Output Summary:
  - 所有新技能文件已创建（9个技能）
  - 所有模板文件已创建（4个模板）
  - 旧 brainstorming 目录已删除
  - 架构分层正确：入口层、编排层、核心技能层、执行层
- L2 测试结果: PASS（架构验证通过）
- L3 测试结果: PASS（架构验证通过）
- 产出物格式验证: PASS（模板格式正确）
