# governance-docs-protection 设计说明（Spec）

## 1. 目标（Goal）

为治理框架增加**文档保护规则**，防止 L1/L2 变更误修改治理文档（docs/spec/、docs/tasks/、docs/changes/）。

---

## 2. 非目标（Non-Goals）

- 不改变 L3 变更流程（Spec/Tasks 阶段仍然可以生成这些文档）
- 不限制 README.md 等项目文档的修改
- 不引入强制校验机制（pre-commit hook 等）

---

## 3. 约束条件（Constraints）

- 改动仅限于 `CLAUDE.md` 和 `skills/execute/SKILL.md`
- 不影响现有 L3 流程
- 依赖 AI 遵守规则，无强制校验

---

## 4. 设计方案（Proposed Design）

### 模块划分

| 模块 | 文件 | 改动内容 |
|------|------|----------|
| 治理规则 | `CLAUDE.md` | L1/L2 规则中增加文档保护限制 |
| 执行技能 | `skills/execute/SKILL.md` | L2 执行规则中增加禁止修改治理文档 |

### 接口设计（规则接口）

**L1 变更规则：**
```markdown
满足以下全部条件即可进入 L1：
- 仅修改文档或注释（README.md、docs/guide/**、*.md、代码注释）
- 禁止修改治理文档（docs/spec/、docs/tasks/、docs/changes/）
```

**L2 变更规则：**
```markdown
受保护目录（禁止修改）：
- docs/spec/ — 设计基准，不应追溯修改
- docs/tasks/ — 执行记录，不应追溯修改
- docs/changes/ — 变更历史，仅可追加新文件
```

**L2 执行规则：**
```markdown
禁止修改 docs/spec/、docs/tasks/ 目录（治理文档受保护）。
```

### 数据结构

**受保护目录语义表：**

| 目录 | 语义 | 修改权限 |
|------|------|----------|
| `docs/spec/` | L3 规格说明 | 仅 L3 Spec 阶段可创建/新增 |
| `docs/tasks/` | L3 任务清单 | 仅 L3 Tasks 阶段可创建/新增；Execute 阶段仅更新 Execution Record |
| `docs/changes/` | L2 变更记录 | 仅 L2 可追加新文件 |
| `README.md` 等 | 项目文档 | L1 可自由修改 |

---

## 5. 边界与异常情况（Edge Cases）

| 场景 | 处理方式 |
|------|----------|
| L3 Execute 阶段更新 Execution Record | 允许，这是执行记录的正常更新 |
| L3 创建新的 spec/tasks 文件 | 允许，这是 Spec/Tasks 阶段的正常产出 |
| L2 创建新的 change 文件 | 允许，这是变更记录的正常创建 |
| L1 修改 README.md | 允许，这是项目文档的正常修改 |
| L2 修改既有 spec/tasks 文件 | **禁止**，这是追溯修改设计基准 |

---

## 6. 验证方案（Validation Plan）

### 审查验证

- [ ] CLAUDE.md 中 L1 规则明确列出保护目录
- [ ] CLAUDE.md 中 L2 规则明确列出保护目录
- [ ] execute/SKILL.md 中 L2 执行规则包含保护规则

### 语义验证

- [ ] L1 可自由修改 README.md 等项目文档
- [ ] L2 可创建新的 docs/changes/ 文件
- [ ] L3 Spec 阶段可创建新的 docs/spec/ 文件
- [ ] L3 Tasks 阶段可创建新的 docs/tasks/ 文件
- [ ] L3 Execute 阶段可更新 Execution Record

### 回归验证

- [ ] 不影响现有 L3 流程
- [ ] 不影响正常的文档修改

---

## 7. 验收标准（Acceptance Criteria）

1. CLAUDE.md 中 L1 部分明确禁止修改治理文档
2. CLAUDE.md 中 L2 部分新增"受保护目录"区块
3. execute/SKILL.md 中 L2 执行规则包含保护规则
4. 以上修改不影响 L3 流程和正常文档修改
