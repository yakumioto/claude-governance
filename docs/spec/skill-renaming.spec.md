# 技能重命名 Spec

## 目标（Goal）

将技能命名从 `l1/l2/l3` 重命名为 `docs/fix/feat`，提升命令可理解性，让用户无需学习框架即可理解命令用途。

---

## 非目标（Non-Goals）

- 不改变技能的核心逻辑和执行流程
- 不保留旧命令别名
- 不改变已存在的治理文档（docs/spec/、docs/tasks/、docs/changes/）

---

## 约束（Constraints）

- 必须全面更新所有相关文件中的术语
- 使用 `git mv` 保留文件历史
- 保持技能功能不变
- 旧命令 `/l1`、`/l2`、`/l3` 不再可用

---

## 术语映射

| 旧术语 | 新术语 |
|--------|--------|
| L1 | Docs / Trivial Changes |
| L2 | Tiny / Controlled Changes |
| L3 | Full Feature Changes |
| `/l1` | `/docs` |
| `/l2` | `/fix` |
| `/l3` | `/feat` |

---

## 接口与数据结构

### 目录结构变更

```
# 变更前
skills/l1/
skills/l2/
skills/l3/

# 变更后
skills/docs/
skills/fix/
skills/feat/
```

### 命令接口变更

```
# 变更前
/l1 <需求>
/l2 <需求>
/l3 <需求>

# 变更后
/docs <需求>
/fix <需求>
/feat <需求>
```

---

## 变更范围

### 必须修改的文件

1. **目录重命名**
   - `skills/l1/` → `skills/docs/`
   - `skills/l2/` → `skills/fix/`
   - `skills/l3/` → `skills/feat/`

2. **文档更新**
   - `CLAUDE.md`
   - `README.md`

3. **技能内部更新**
   - `skills/classify/SKILL.md` - 输出格式中的命令提示
   - `skills/execute/SKILL.md` - 命令用法说明
   - `skills/docs/SKILL.md`（原 l1）
   - `skills/fix/SKILL.md`（原 l2）
   - `skills/feat/SKILL.md`（原 l3）

4. **模板更新**
   - `skills/doc-gen/templates/change.md`
   - `skills/doc-gen/templates/spec.md`
   - `skills/doc-gen/templates/tasks.md`
   - `skills/doc-gen/templates/design.md`

### 禁止修改

- `docs/spec/` 目录下的已存在文件
- `docs/tasks/` 目录下的已存在文件
- `docs/changes/` 目录下的已存在文件

---

## 边界情况

1. **交叉引用**：技能间互相调用的地方需要更新命令引用
2. **示例代码**：README 中的命令示例需要更新
3. **注释中的引用**：代码注释中如有旧术语也需要更新

---

## 验证方案（Validation Plan）

### 结构验证

```bash
# 确认目录结构
ls -la skills/ | grep -E "docs|fix|feat"

# 确认旧目录不存在
ls -la skills/ | grep -E "l1|l2|l3"  # 应无输出
```

### 内容验证

```bash
# 搜索旧术语（应该只出现在本变更文档中）
grep -r "L1\|L2\|L3" --include="*.md" --exclude-dir="docs/spec" --exclude-dir="docs/tasks" --exclude-dir="docs/changes"
grep -r "/l1\|/l2\|/l3" --include="*.md" --exclude-dir="docs/spec" --exclude-dir="docs/tasks" --exclude-dir="docs/changes"
```

### 功能验证

- 新命令 `/docs`、`/fix`、`/feat` 可被 Skill tool 调用
- 旧命令 `/l1`、`/l2`、`/l3` 不再可用

### 文档验证

- CLAUDE.md 渲染正常
- README.md 渲染正常
- 各技能 SKILL.md 渲染正常
