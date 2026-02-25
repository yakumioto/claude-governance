# Conventional Commits 类型定义

本文档定义了 commit 技能使用的 Conventional Commits 类型规范。

---

## Type 类型定义

### feat

**说明**：新功能

**判断依据**：
- 新增文件
- 添加新的功能点或特性
- 新增 API 或接口
- 添加新的配置项

**示例**：
```
feat(skills): 添加 commit 智能提交技能
feat(agent): 新增多轮对话支持
feat(api): 添加用户认证接口
```

---

### fix

**说明**：Bug 修复

**判断依据**：
- 修复性改动
- 包含 "fix"、"修复"、"bug" 等关键词
- 纠正错误行为
- 修复崩溃或异常

**示例**：
```
fix(skills): 修复 scope 推断逻辑错误
fix(governance): 修复 fix 变更流程漏洞
```

---

### docs

**说明**：文档变更

**判断依据**：
- 修改 `*.md` 文件
- README、CHANGELOG、LICENSE 等
- 文档目录下的改动
- 注释修改（不伴随代码逻辑变更）

**示例**：
```
docs: 更新 README 文档结构
docs(governance): 补充 feat 流程说明
docs(skills): 更新 brainstorming 技能文档
```

---

### style

**说明**：代码格式（不影响代码运行的变动）

**判断依据**：
- 代码格式化
- 缩进、空行调整
- 分号、括号风格统一
- 不改变代码逻辑的格式调整

**示例**：
```
style: 格式化代码
style(skills): 统一使用 4 空格缩进
```

---

### refactor

**说明**：重构（既不是新功能也不是修复Bug的代码变动）

**判断依据**：
- 代码结构调整
- 变量重命名
- 代码组织优化
- 不改变外部行为的内部重构

**示例**：
```
refactor(skills): 简化技能加载流程
refactor(governance): 重构变更分级逻辑
```

---

### test

**说明**：测试相关

**判断依据**：
- 测试文件（如 `*_test.go`）
- 测试目录下的改动
- 添加或修改测试用例
- 添加断言或 mock

**示例**：
```
test(skills): 添加 commit 技能单元测试
test: 修复 CI 测试失败
```

---

### chore

**说明**：构建过程或辅助工具的变动

**判断依据**：
- 配置文件修改
- 依赖更新
- 脚本修改
- 构建工具配置
- CI/CD 配置

**示例**：
```
chore: 更新依赖版本
chore: 添加 pre-commit hook
chore(ci): 配置 GitHub Actions
```

---

## Scope 推断规则

基于文件路径自动推断作用域：

| 文件路径模式 | Scope | 说明 |
|-------------|-------|------|
| `skills/**` | `skills` | 技能相关 |
| `CLAUDE.md` | `governance` | 治理规则 |
| `README.md` | - | 文档，通常省略 scope |
| `docs/**` | `docs` | 文档目录 |
| `.claude/**` | `config` | 配置相关 |
| `*.go` | 项目名 | Go 模块名 |
| `*.py` | 项目名 | Python 包名 |
| `Makefile` | `build` | 构建相关 |
| `Dockerfile` | `docker` | Docker 相关 |
| `.github/**` | `ci` | CI/CD 相关 |

---

## Type 选择决策树

```
改动是否影响代码运行逻辑？
├─ 否 → style 或 docs
│      └─ 是否是文档文件？
│             ├─ 是 → docs
│             └─ 否 → style
└─ 是 → 是否是新增功能？
       ├─ 是 → feat
       └─ 否 → 是否修复 Bug？
              ├─ 是 → fix
              └─ 否 → 是否是测试？
                     ├─ 是 → test
                     └─ 否 → 是否是重构？
                            ├─ 是 → refactor
                            └─ 否 → chore
```

---

## 完整示例

### 示例 1：新技能开发
改动：`skills/commit/SKILL.md`（新文件）
```
feat(skills): 添加 commit 智能提交技能
```

### 示例 2：修复文档
改动：`README.md` 内容更新
```
docs: 更新项目说明文档
```

### 示例 3：修复 Bug
改动：`execute/SKILL.md` 中的 feat 执行逻辑修复
```
fix(execute): 修复 Task 执行顺序错误
```

### 示例 4：重构代码
改动：重新组织技能目录结构
```
refactor(skills): 简化技能加载流程
```

### 示例 5：添加测试
改动：`skills/commit/testCommit.sh`（新测试文件）
```
test(commit): 添加提交信息格式验证
```

### 示例 6：更新配置
改动：`.claude/config.json`
```
chore(config): 更新技能搜索路径
```

### 示例 7：格式化代码
改动：统一缩进风格
```
style: 统一使用 4 空格缩进
```
