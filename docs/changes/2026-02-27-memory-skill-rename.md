# Change Record: memory Skill 重命名

**Date:** 2026-02-27
**Type:** fix
**Topic:** memory-skill-rename

---

## Motivation

Claude Code CLI 内置了 `/memory` 命令，与自定义的 memory skill 命名冲突。需要将 skill 重命名以避免冲突。

---

## Scope

### 需要修改的文件（不包含受保护目录）

| 文件路径 | 修改内容 |
|----------|----------|
| `skills/memory/skill.md` | 移动到 `skills/project-memory/SKILL.md` |
| `CLAUDE.md` | 更新技能列表中的 `/memory` 为 `/project-memory` |
| `README.md` | 更新 memory skill 说明和调用示例 |
| `docs/memories/memory.md` | 更新文件索引和描述（skill 引用） |
| `skills/execute/SKILL.md` | 更新提示信息 |
| `skills/doc-gen/templates/tasks.md` | 更新模板 |
| `skills/doc-gen/templates/change.md` | 更新模板 |

**注意：** docs/spec/ 和 docs/tasks/ 是受保护的历史文档，本次变更不修改其中的内容。

### 新名称

- 技能命令：`/project-memory`
- 目录名：`skills/project-memory/`
- 文件名：`SKILL.md`
- 调用格式：`/project-memory docs/spec/xxx.spec.md`

---

## Execution Plan

1. 创建 `skills/project-memory/` 目录
2. 移动并重命名 `skills/memory/skill.md` → `skills/project-memory/SKILL.md`
3. 删除 `skills/memory/` 目录
4. 更新 skill 文件内的名称引用
5. 更新 CLAUDE.md 技能列表
6. 更新 README.md 文档
7. 更新 skills/execute/SKILL.md
8. 更新 skills/doc-gen/templates/
9. 更新 docs/memories/memory.md

**不修改 docs/spec/ 和 docs/tasks/（受保护目录）**

---

## Execution Record

| 时间 | 执行者 | 摘要 |
|------|:------:|------|
| 2026-02-27 | Claude | 创建 `skills/project-memory/` 目录，移动 skill 文件 |
| 2026-02-27 | Claude | 更新 CLAUDE.md 技能列表：`/memory` → `/project-memory` |
| 2026-02-27 | Claude | 更新 README.md 文档中的 skill 说明和调用示例 |
| 2026-02-27 | Claude | 更新 skills/execute/SKILL.md 提示信息 |
| 2026-02-27 | Claude | 更新 skills/doc-gen/templates/tasks.md 模板 |
| 2026-02-27 | Claude | 更新 skills/doc-gen/templates/change.md 模板 |
| 2026-02-27 | Claude | 更新 docs/memories/memory.md 文件索引 |

---

## Acceptance

- [x] skill 文件已移动到 `skills/project-memory/SKILL.md`
- [x] 原 `skills/memory/` 目录已删除
- [x] CLAUDE.md 技能列表已更新
- [x] README.md 文档已更新
- [x] skills/execute/SKILL.md 已更新
- [x] skills/doc-gen/templates/ 已更新
- [x] docs/memories/memory.md 已更新

**未修改 docs/spec/ 和 docs/tasks/（受保护目录）**
