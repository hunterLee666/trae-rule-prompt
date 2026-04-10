# 项目 AI 协作核心宪法
> **Superpowers + Everything Claude Code 全局权责锁定规则（强制生效，不可突破）**

## 一、 核心插件定位声明
本项目同时启用 **Superpowers** 与 **Everything Claude Code (ECC)**。
- **Superpowers**：担任 **CTO/架构师/核心开发 Leader**。掌控开发**主链路**（需求→架构→TDD→交付）。
- **ECC**：担任 **专项专家团队/运维安全总监**。负责**辅链路**（安全、性能、文档、CI/CD、E2E）。

## 二、 权责绝对划分表（零重叠，禁止交叉调用）

### 1. Superpowers 独占领域（主链路强制锁定）
| 阶段 | 具体职责 | 强制触发命令（别名） | 禁止 ECC 介入声明 |
| :--- | :--- | :--- | :--- |
| **需求与规划** | 用户需求澄清、原子任务拆解、架构设计评审 | `/s-brainstorm`, `/s-plan`, `/s-architect` | 禁止 ECC 的 `architect` Agent 覆盖架构决策 |
| **TDD 开发** | 单元测试先行编写、业务代码实现、单测验证 | `/s-tdd`, `/s-test-unit` | **绝对禁止** ECC 编写单元测试逻辑 |
| **集成与验收** | 集成测试核心流程、交付验收、代码合入评审 | `/s-test-integration`, `/s-review`, `/s-accept` | 禁止 ECC 介入核心业务逻辑的 Code Review |
| **版本控制** | Git 分支规范、Commit Message 格式管控 | `/s-git` | / |

### 2. Everything Claude Code (ECC) 独占领域（辅链路专项锁定）
| 阶段 | 具体职责 | 强制触发命令（别名） | 禁止 Superpowers 介入声明 |
| :--- | :--- | :--- | :--- |
| **专项测试** | E2E 测试、性能压测、安全渗透测试、兼容性测试 | `/e-test-e2e`, `/e-test-perf`, `/e-test-sec` | **绝对禁止** Superpowers 编写 UI/性能/安全测试 |
| **代码保障** | 安全漏洞扫描、多语言语法规范校验、依赖漏洞修复 | `/e-security`, `/e-audit` | 禁止 Superpowers 修改依赖版本或安全配置 |
| **文档与记忆** | API 文档、架构图、CHANGELOG、项目记忆持久化 | `/e-docs`, `/e-memory` | / |
| **运维与优化** | CI/CD 脚本、性能热点优化、第三方 SDK 集成 | `/e-optimize`, `/e-cicd` | / |

### 3. 测试能力专属划分细则（特别强化）
| 测试类型 | 权责归属 | 执行铁律 |
| :--- | :--- | :--- |
| **单元测试 (Unit)** | **Superpowers** | 必须由 `/s-tdd` 驱动，先写测试，后写实现。ECC **禁止触碰**。 |
| **集成测试 (Integration)** | **Superpowers (主导)** | 核心链路验证归 Superpowers；复杂环境搭积木可申请 ECC 辅助脚本。 |
| **端到端测试 (E2E)** | **ECC** | 全链路用户场景模拟，Superpowers **禁止生成** Playwright/Cypress 代码。 |
| **性能与安全测试** | **ECC** | 压测、负载、漏洞扫描，Superpowers **仅可查看报告**。 |

## 三、 命令冲突强制规避与别名总表
- **优先级原则**：流程类默认 Superpowers，专项类默认 ECC。
- **固定别名**：为杜绝 AI 随机选择，**强制使用以下别名进行调度**：

| 功能 | 别名 (唯一入口) | 实际调用的命名空间 |
| :--- | :--- | :--- |
| 需求澄清 / 头脑风暴 | `/s-brainstorm` | `/superpowers:brainstorming` |
| 任务拆解与计划 | `/s-plan` | `/superpowers:writing-plans` |
| **TDD 微循环 / 单元测试** | **`/s-tdd`** | `/superpowers:test-driven-development` |
| 代码评审 (业务逻辑) | `/s-review` | `/superpowers:code-review` |
| 安全检查 | `/e-security` | `/everything-claude-code:security-scanner` |
| **E2E 测试编写** | **`/e-test-e2e`** | `/everything-claude-code:e2e-tester` |
| 性能优化 | `/e-optimize` | `/everything-claude-code:performance-optimizer` |
| 生成 API 文档 | `/e-docs` | `/everything-claude-code:doc-writer` |

## 四、 Token 与上下文优化铁律
1.  **懒加载原则**：仅当用户输入 `/s-*` 或 `/e-*` 时，才激活对应插件的特定技能。**严禁全量预加载**。
2.  **上下文隔离**：Superpowers 仅保留 **“任务与流程记忆”** ；ECC 仅保留 **“环境元数据与漏洞库”** 。禁止互相读取/写入对方上下文。
3.  **轻量模式**：若用户未显式调用别名，而是直接提问修改单行代码，默认仅启用 **Superpowers 基础规则**，ECC 保持静默。

## 五、 兜底原则与故障恢复
- **冲突仲裁**：若出现规则歧义，**Superpowers 的核心流程具有一票否决权**。系统应暂停 ECC 任务，优先保障核心开发链路不中断。
- **异常上报**：若发现 AI 违规交叉调用（例如 Superpowers 试图写 E2E 测试），应立即停止生成并提示：“违反权责锁定规则 [Rule-403]”。

---
**规则版本：v2.0 (整合强化版)**
**生效时间：立即生效 | 适用环境：Claude Code 全模式**