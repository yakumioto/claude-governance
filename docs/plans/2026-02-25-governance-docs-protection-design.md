# Design: 治理文档保护规则

Date: 2026-02-25
Owner: yakumioto

---

## 背景说明

在执行 L2 变更（Docker 镜像名更新）时，错误地修改了 `docs/spec/` 和 `docs/tasks/` 下的文件。这些目录存放的是：
- `docs/spec/` — L3 规格说明（设计基准）
- `docs/tasks/` — L3 任务清单（执行记录）
- `docs/changes/` — L2 变更记录（变更历史）

这些是治理文档，代表设计基准和历史记录，不应被追溯修改。

---

## 目标

为治理框架增加**文档保护规则**，防止 L1/L2 变更误修改治理文档。

---

## 非目标

- 不改变 L3 变更流程（Spec/Tasks 阶段仍然可以生成这些文档）
- 不限制 README.md 等项目文档的修改
- 不引入强制校验机制（pre-commit hook 等）

---

## 设计方案（方案 A：最小化修改）

### 修改范围

| 文件 | 修改内容 |
|------|----------|
| `CLAUDE.md` | L1/L2 规则中增加文档保护限制 |
| `skills/execute/SKILL.md` | L2 执行规则中增加禁止修改治理文档 |

### 具体改动

#### 1. CLAUDE.md — L1 部分

```diff
  满足以下全部条件即可进入 L1：

  - 仅修改文档或注释（README、docs/**、*.md、代码注释）
+ - 仅修改文档或注释（README.md、docs/guide/**、*.md、代码注释）
+ - 禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）
  - 不改变代码运行行为
```

#### 2. CLAUDE.md — L2 部分（新增区块）

在 "执行流程" 之后、"要求" 之前新增：

```markdown
## 受保护目录（禁止修改）

- docs/spec/ — 设计基准，不应追溯修改
- docs/tasks/ — 执行记录，不应追溯修改
- docs/changes/ — 变更历史，仅可追加新文件
```

#### 3. skills/execute/SKILL.md — L2 执行规则

```diff
  ## L2 执行规则

  1. 严格按 Scope 修改代码。
  2. 禁止修改未声明模块。
  3. 禁止新增依赖。
  4. 禁止修改接口或默认值。
  5. 禁止顺手重构。
+ 6. 禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）。
```

---

## 受保护目录语义

| 目录 | 语义 | 修改权限 |
|------|------|----------|
| `docs/spec/` | L3 规格说明 | 仅 L3 Spec 阶段可创建/新增 |
| `docs/tasks/` | L3 任务清单 | 仅 L3 Tasks 阶段可创建/新增；Execute 阶段仅更新 Execution Record |
| `docs/changes/` | L2 变更记录 | 仅 L2 可追加新文件 |
| `README.md` 等 | 项目文档 | L1 可自由修改 |

---

## 约束

- 不改变现有 L3 流程
- 不影响正常的项目文档（README.md 等）修改
- 依赖 AI 遵守规则，无强制校验

---

## 验证方案

1. 审查 CLAUDE.md 和 execute/SKILL.md 的修改
2. 确认 L1/L2 规则中明确列出保护目录
3. 确认不影响 L3 流程

---

## Trade-offs

| 优点 | 缺点 |
|------|------|
| 改动最小，风险可控 | 依赖 AI 遵守规则 |
| 规则清晰明确 | 无强制校验机制 |
| 不影响现有 L3 流程 | — |
