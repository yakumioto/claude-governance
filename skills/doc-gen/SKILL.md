---
name: doc-gen
description: 文档生成器。根据类型生成 Change Record、Spec、Tasks 或设计文档。
---

# 文档生成器（Doc-Gen）

本技能用于生成各类治理文档，包括 Change Record、Spec、Tasks 和设计文档。

---

## 用法

```
/doc-gen <type> [参数]
```

**类型（type）：**
- `change` - 生成 L2 Change Record
- `spec` - 生成 L3 Spec
- `tasks` - 生成 L3 Tasks
- `design` - 生成设计文档

---

## 输入要求

### change
- 需求摘要（来自 `/explore`）
- 影响范围（文件/模块）
- 验收命令

### spec
- 需求摘要（来自 `/explore`）
- 设计方案（来自 `/design`）

### tasks
- Spec 文档内容
- Feature 名称

### design
- 设计方案内容（来自 `/design`）

---

## 产出路径

| 类型 | 路径 |
|------|------|
| change | `docs/changes/YYYY-MM-DD-<topic>.md` |
| spec | `docs/spec/<feature>.spec.md` |
| tasks | `docs/tasks/<feature>.tasks.md` |
| design | `docs/plans/YYYY-MM-DD-<topic>-design.md` |

---

## 模板位置

```
skills/doc-gen/templates/
├── change.md
├── spec.md
├── tasks.md
└── design.md
```

---

## 执行流程

1. **读取对应模板**
   - 优先读取 `docs/templates/<type>.md`
   - 否则读取 `skills/doc-gen/templates/<type>.md`

2. **填充内容**
   - 根据输入数据填充模板
   - 替换变量占位符

3. **生成文件**
   - 写入对应路径
   - 确保目录存在

4. **返回结果**
   - 输出文件路径
   - 提示下一步操作

---

## 输出格式

```
<类型> 已生成：<文件路径>

下一步：请使用 /execute <类型> <文件路径> 执行变更。
```

---

## 模板变量说明

### 通用变量
- `{{date}}` - 当前日期 YYYY-MM-DD
- `{{title}}` - 标题
- `{{topic}}` - 主题
- `{{feature}}` - 功能名

### change 模板
- `{{scope}}` - 影响范围
- `{{acceptance}}` - 验收命令

### spec 模板
- `{{goal}}` - 目标
- `{{non_goals}}` - 非目标
- `{{constraints}}` - 约束
- `{{design}}` - 设计方案

### tasks 模板
- `{{tasks}}` - 任务列表

### design 模板
- `{{architecture}}` - 架构设计
- `{{modules}}` - 模块划分
- `{{data_flow}}` - 数据流
- `{{error_handling}}` - 错误处理
- `{{test_strategy}}` - 测试策略
