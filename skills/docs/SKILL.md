---
name: docs
description: docs 级别变更约束说明。记录 docs 的限制规则，提示用户直接对话。
---

# docs 级别变更约束说明

本技能用于说明 docs 级别变更的约束和操作方式。

---

## docs 变更定义

docs 级别变更是指仅修改文档或注释，不改变代码运行行为的变更。

---

## docs 约束规则

满足以下**全部**条件才能作为 docs 处理：

### 允许的修改

- 仅修改文档或注释：
  - README.md
  - docs/guide/**
  - 任意 *.md 文件
  - 代码注释（// 或 /* */）

### 禁止的修改

- 不改变代码运行行为
- 不改变公共接口
- 不改变默认值或配置语义
- 不新增或修改依赖（go.mod / package.json 等）
- 不修改治理文档（docs/spec/、docs/tasks/、docs/changes/）

---

## 处理方式

**docs 级别变更无需使用技能，请直接描述你需要的修改。**

---

## 提交说明格式

docs 级别变更的提交说明必须包含：

```
docs: <summary>

What:
- <修改了什么文档>

Why:
- <为什么修改>

Risk:
- <风险说明，Docs 通常为 none>

Verify:
- <如何验证，如：Markdown 渲染正常>
```

---

## Done 定义

docs 级别变更完成的标准：

- Markdown 文档渲染正常
- 示例命令验证通过（如有）
- 提交说明符合格式要求

---

## 注意事项

- 如果需要修改治理文档（docs/spec/、docs/tasks/、docs/changes/），这不属于 docs 级别
- 如果修改涉及代码行为变更，即使很小也应升级到 fix 或 feat
- 当不确定是否属于 docs 时，参考 CLAUDE.md 中的变更分级入口规则
