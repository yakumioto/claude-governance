---
name: fix
description: fix 流程编排。执行 explore、doc-gen 生成 Change Record。
---

# fix 流程编排（fix）

本技能负责编排 fix 级别变更的完整流程。

---

## fix 变更适用范围

fix 变更适用于：
- 小 bug 修复
- typo 修复
- 单文件逻辑修正
- 内部实现优化（不改变接口和语义）

### fix 边界约束

必须满足：
- 不改变公共接口（API/CLI/配置项对外契约）
- 不改变默认值或配置语义
- 不新增或升级依赖
- 不涉及跨模块架构调整
- 影响范围可在单个或少量文件内闭合

若对边界存在不确定，必须升级为 feat。

---

## 执行流程

### 阶段 1：需求探索

首先执行需求探索：

```
请先执行：/explore <需求描述>
```

等待 `/explore` 完成并获得需求摘要。

---

### 阶段 2：确认需求

基于需求摘要，确认以下内容：
- 变更动机（1-3 句）
- 影响范围（文件/模块）
- 成功标准
- 验证方式（Acceptance 命令）

若发现涉及接口/语义/依赖/架构 → 立即停止并升级为 feat。

---

### 阶段 3：生成 Change Record

需求确认后，调用 `/doc-gen change` 生成 Change Record：

"需求已确认，正在生成 Change Record..."

使用 doc-gen 生成 `docs/changes/YYYY-MM-DD-<topic>.md`

---

### 阶段 4：完成提示

Change Record 生成后，提示用户：

```
Change Record 已生成：docs/changes/YYYY-MM-DD-<topic>.md

请使用 /execute fix docs/changes/YYYY-MM-DD-<topic>.md 执行变更。
```

---

## 禁止操作

在 Change Record 写入并确认前，禁止：
- 编写任何代码
- 调用 execute
- 修改项目文件

---

## fix 执行规则

执行变更时必须遵守：
1. 严格按 Scope 修改代码
2. 禁止修改未声明模块
3. 禁止新增依赖
4. 禁止修改接口或默认值
5. 禁止顺手重构
6. 禁止修改治理文档（docs/spec/、docs/tasks/）
