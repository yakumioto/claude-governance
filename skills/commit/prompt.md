# AI 分析提示词模板

用于分析 Git 改动并生成 Conventional Commits 格式的提交信息。

---

## 提示词模板

```markdown
# 任务：生成 Conventional Commit 提交信息

## 改动文件
{{FILES}}

## 改动内容
{{DIFF}}

## 最近提交风格（请保持一致）
{{RECENT_COMMITS}}

---

## 输出要求

1. **分析改动类型**（type）：
   - `feat` - 新功能
   - `fix` - Bug 修复
   - `docs` - 文档变更
   - `style` - 代码格式（不影响逻辑）
   - `refactor` - 重构
   - `test` - 测试相关
   - `chore` - 构建/工具链

2. **推断作用域**（scope）：
   - 基于改动文件路径推断
   - 常见作用域：skills, docs, governance, config, agent 等
   - 如果无法确定或不重要，可省略

3. **生成简洁描述**（description）：
   - 使用中文
   - 简洁明了，不超过 50 字符
   - 描述做了什么，而不是为什么
   - 使用祈使句（如"添加"、"修复"、"更新"）

## 输出格式

请**仅**输出 JSON 格式，不要包含任何其他文字：

```json
{
  "type": "feat",
  "scope": "skills",
  "description": "添加 commit 智能提交技能"
}
```

如果省略 scope：

```json
{
  "type": "docs",
  "scope": "",
  "description": "更新 README 文档"
}
```

---

## 示例

### 示例 1：新增技能文件
```
改动：skills/commit/SKILL.md（新文件）
输出：{"type": "feat", "scope": "skills", "description": "添加 commit 智能提交技能"}
```

### 示例 2：修改文档
```
改动：README.md 更新
输出：{"type": "docs", "scope": "", "description": "更新项目说明文档"}
```

### 示例 3：修复 Bug
```
改动：修复 scope 推断逻辑
输出：{"type": "fix", "scope": "skills", "description": "修复 scope 推断错误"}
```

### 示例 4：重构代码
```
改动：调整技能目录结构
输出：{"type": "refactor", "scope": "skills", "description": "简化技能加载流程"}
```

---

## 变量替换

在使用时，将以下变量替换为实际值：

| 变量 | 来源 | 格式 |
|------|------|------|
| {{FILES}} | `git status --short` | 每行一个文件，带状态标识 |
| {{DIFF}} | `git diff` | 完整的 diff 内容 |
| {{RECENT_COMMITS}} | `git log --oneline -5` | 最近 5 条提交，每行一条 |
