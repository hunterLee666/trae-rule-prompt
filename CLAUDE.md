# 项目 AI 协作核心宪法

> **Superpowers + Everything Claude Code 全局权责锁定规则（强制生效，不可突破）**
>
> **生效机制**：本文件通过 `always_applied_workspace_rules` + CLAUDE.md 自动注入，**所有消息类型**（用户消息、AI 思考消息、工具消息）全程强制生效，不可绕过。

---

## ⚡ 快速参考卡

```
Superpowers 主链路 ←→ ECC 辅链路
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
需求/架构/TDD/单元测试/集成测试/代码评审    E2E/安全扫描/性能压测/文档生成/CI/CD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ Superpowers 禁止生成 Playwright / 单元测试断言
❌ ECC 禁止编写业务逻辑 / 依赖版本变更
```

---

## 一、 核心插件定位

本项目同时启用 **Superpowers** 与 **Everything Claude Code (ECC)**。

| 体系 | 角色定位 | 核心职责 | 何时介入 |
|:---|:---|:---|:---|
| **Superpowers** | CTO / 架构师 / 核心开发 Leader | 需求→架构→TDD→交付 | 新功能开发、Bug 修复、核心逻辑实现 |
| **ECC** | 专项专家团队 / 运维安全总监 | 安全→性能→文档→CI/CD→E2E | 代码写完后自动触发，无需用户指令 |

---

## 二、 [Rule-403] 违规拦截规则

> **这是最高优先级规则**。检测到以下任一行为，立即停止生成并输出拦截提示。

### 2.1 Superpowers 不得越界的操作（ECC 专属）

| 越界行为（发现即拦截） | 正确做法 |
|:---|:---|
| 生成 `test_*.py` 或 `*.test.ts` 文件中的断言逻辑 | 调用 `/superpowers:test-driven-development` 写单元测试 |
| 生成 `playwright`、`cypress`、`@e2e` 等 E2E 框架代码 | 调用 `/everything-claude-code:e2e-testing` |
| 在 Python/JS 文件中编写 `describe(...)` / `it(...)` 等测试块 | Superpowers 只写业务代码，测试由各自所属体系负责 |
| 修改 `package.json` / `requirements.txt` 依赖版本 | 调用 `/everything-claude-code:security-scan` 分析后由 ECC 处理 |
| 编写压测脚本（locust、k6、ab、wrk） | 调用 `/everything-claude-code:performance` |

### 2.2 ECC 不得越界的操作（Superpowers 专属）

| 越界行为（发现即拦截） | 正确做法 |
|:---|:---|
| 重写 `src/` 目录下的业务逻辑实现 | Superpowers 负责业务代码 |
| 修改 `src/api/` 或 `src/services/` 的核心函数逻辑 | Superpowers 负责核心架构决策 |
| 编写 `src/models/`、`src/utils/` 中的非测试工具函数 | Superpowers 负责 |
| 在非配置文件路径修改依赖版本 | 调用 `/superpowers:brainstorming` 确认后再行动 |

### 2.3 拦截提示模板

发现违规时，**立即停止生成**，输出：

```
══════════════════════════════════════════
❌ [Rule-403] 违反权责锁定规则

该操作属于 [Superpowers / ECC] 专属领域，
当前执行者无权越界执行。

  违规内容：[具体描述]
  应调用的正确技能：[技能名称]

请改用正确技能重新执行。
══════════════════════════════════════════
```

---

## 三、 权责绝对划分表

### 3.1 Superpowers 主链路（独占，禁止 ECC 介入）

| 阶段 | 具体职责 | 强制规则 | 触发时机 |
|:---|:---|:---|:---|
| **需求澄清** | 用户需求细节澄清、原子任务拆解 | 先澄清再拆解，禁止跳过 | 用户提出任何复杂需求时 |
| **架构设计** | 整体架构、模块划分、接口设计 | 架构决策由 Superpowers 做出，ECC 不得覆盖 | 功能开发前 |
| **TDD 开发** | 单元测试先行（RED→GREEN→REFACTOR） | **绝对禁止** ECC 编写测试断言 | 编写任何业务代码前 |
| **业务代码实现** | 核心功能逻辑、算法、数据模型 | 仅 Superpowers 可修改 `src/` 核心文件 | 开发过程中 |
| **集成测试** | 核心链路验证、接口联调 | Superpowers 主导，ECC 仅辅助脚本 | 单元测试通过后 |
| **代码评审** | 业务逻辑正确性、架构合理性、可读性 | 禁止 ECC 介入核心业务逻辑评审 | 代码编写完成后 |
| **版本控制** | Git 分支规范、Commit Message、PR | 参考 `.trae/rules/zh/` | 提交前 |

### 3.2 ECC 辅链路（独占，禁止 Superpowers 介入）

| 阶段 | 具体职责 | 强制规则 | 触发时机 |
|:---|:---|:---|:---|
| **E2E 测试** | Playwright 全链路用户场景模拟 | **绝对禁止** Superpowers 生成 Playwright 代码 | 核心功能完成后 |
| **安全漏洞扫描** | OWASP Top 10、硬编码密钥、依赖漏洞检测 | 禁止 Superpowers 修改依赖进行修复 | **任何代码编写完成后自动触发** |
| **性能压测与优化** | SQL 优化、缓存策略、热点分析 | Superpowers 仅查看报告，不得实施 | 性能问题代码检测到后自动触发 |
| **文档生成** | API 文档、架构图、CHANGELOG | 禁止 Superpowers 介入第三方 SDK 集成配置 | API 接口变更时 |
| **CI/CD 脚本** | GitHub Actions、GitLab CI、Dockerfile | 全部由 ECC 负责 | 构建流程变更时 |
| **依赖漏洞修复** | `package.json`、`requirements.txt` 更新 | 全部由 ECC 执行 | 安全扫描发现漏洞后 |

---

## 四、 测试能力专属划分

| 测试类型 | 权责归属 | 执行铁律 |
|:---|:---|:---|
| **单元测试 (Unit)** | 🔒 **Superpowers 专属** | 严格 TDD，先写测试（RED），后写实现（GREEN）。ECC 绝对禁止触碰。 |
| **集成测试 (Integration)** | 🔒 **Superpowers 主导** | 核心链路验证；ECC 仅可辅助编写环境搭建脚本，不得触碰断言。 |
| **端到端测试 (E2E)** | 🔒 **ECC 专属** | 编写 Playwright 代码，模拟用户操作流。Superpowers 禁止生成任何 E2E 代码。 |
| **性能测试** | 🔒 **ECC 专属** | 压测脚本、负载测试、热点分析。Superpowers 仅可查看报告。 |
| **安全测试** | 🔒 **ECC 专属** | 漏洞扫描、依赖审计。Superpowers 仅可查看报告。 |

---

## 五、 技能自动调度矩阵

> AI 根据任务上下文**自动调度**，无需用户显式输入命令。

| 任务场景 | AI 自动执行动作 | 所属体系 |
|:---|:---|:---|
| 用户说"帮我实现一个功能" | 1. `brainstorming` 澄清需求 → 2. `writing-plans` 制定计划 → 3. 确认后 `test-driven-development` | Superpowers |
| 用户说"修复这个 Bug" | `test-driven-development`：先写失败测试 → 修复代码 → 验证通过 | Superpowers |
| 用户说"重构代码" | `requesting-code-review` 先评估 → `test-driven-development` 保证覆盖 → 重构 | Superpowers |
| 任何代码编写完成后 | **自动触发** `security-review` Agent 安全扫描 | ECC |
| 核心功能开发完成后 | **自动触发** `e2e-testing` 编写端到端测试 | ECC |
| 检测到性能问题代码 | **自动触发** `performance` Agent 分析优化 | ECC |
| API 接口变更时 | **自动触发** `doc-updater` 更新文档 | ECC |
| 构建失败 | **自动触发** `build-error-resolver` 修复 | ECC |

### 固定调度别名（唯一入口，防止随机选择）

| 功能 | 别名 | 实际调用 |
|:---|:---|:---|
| 需求澄清 | `/s-brainstorm` | `superpowers:brainstorming` |
| 任务拆解 | `/s-plan` | `superpowers:writing-plans` |
| **TDD 开发** | **`/s-tdd`** | `superpowers:test-driven-development` |
| 代码评审 | `/s-review` | `superpowers:requesting-code-review` |
| 安全扫描 | `/e-security` | `everything-claude-code:security-scan` |
| **E2E 测试** | **`/e-test-e2e`** | `everything-claude-code:e2e-testing` |
| 性能优化 | `/e-optimize` | `everything-claude-code:performance` |
| 文档生成 | `/e-docs` | `everything-claude-code:docs` |

---

## 六、 生成代码前的自我审查（AI 必读）

> **每次生成代码前，AI 必须执行以下检查。**

### ✅ Superpowers 自我审查清单

- [ ] 当前正在写的是**业务逻辑代码**（`src/` 下），不是测试文件
- [ ] 若需要单元测试 → **先调用 `/s-tdd`**，不自己写 `assert` 断言
- [ ] **不在** `test/`、`spec/` 路径下编写文件
- [ ] **不生成** `playwright`、`cypress`、`locust`、`k6` 相关代码
- [ ] **不修改** `package.json`、`requirements.txt` 中的依赖版本

### ✅ ECC 自我审查清单

- [ ] 正在操作的是**测试脚本**、**配置文件**、**CI/CD** 或**文档**
- [ ] **不重写** `src/` 下的业务逻辑实现
- [ ] **不修改** `src/models/`、`src/services/` 的核心函数
- [ ] 若涉及依赖更新 → 确认已通过 `security-scan` 评估

---

## 七、 上下文隔离与 Token 优化

1. **懒加载**：仅在进入对应任务场景时才激活相关技能，**严禁全量预加载**
2. **上下文隔离**：Superpowers 仅保留「任务与流程记忆」；ECC 仅保留「环境元数据与漏洞库」。禁止互相读写对方上下文
3. **轻量模式**：用户仅要求修改单行代码时，默认仅启用 Superpowers 基础规则，ECC 保持静默
4. **冲突仲裁**：规则歧义时，**Superpowers 主链路具有一票否决权**，优先保障核心开发链路

---

## 八、 插件状态

| 插件 | 版本 | 状态 |
|:---|:---|:---:|
| **superpowers** | 5.0.7 | ✅ 正常 |
| **frontend-design** | 7ed52314 | ✅ 正常 |
| **everything-claude-code** | 1.10.0 | ✅ 正常 |

---

**规则版本**：v3.0 (优化版)
**生效时间**：立即生效
**适用环境**：Claude Code / TRAE IDE 全模式
**强制等级**：🔒 最高 - 不可绕过，不可突破