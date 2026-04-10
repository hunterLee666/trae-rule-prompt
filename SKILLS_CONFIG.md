# BI-Agent 项目技能配置

## 安装概览

本项目已成功安装以下技能包：

### Everything Claude Code (ECC)
- **安装时间**: 2026-04-10
- **版本**: 最新版本
- **安装内容**:
  - 79 个命令
  - 47 个智能体
  - **180 个技能**
  - 89 个规则
- **目录位置**: `.trae/skills/ecc-skills/`

### Superpowers
- **安装时间**: 2026-04-10
- **版本**: 最新版本
- **安装内容**:
  - 3 个命令 (brainstorm, execute-plan, write-plan)
  - 1 个智能体 (code-reviewer)
  - **14 个技能包**
- **目录位置**: `.trae/skills/superpowers-skills/`

---

## 目录结构

```
.trae/
├── skills/
│   ├── superpowers-skills/        # Superpowers 技能（14个）
│   │   ├── brainstorming/
│   │   ├── test-driven-development/
│   │   ├── writing-plans/
│   │   ├── executing-plans/
│   │   ├── verification-before-completion/
│   │   ├── systematic-debugging/
│   │   ├── dispatching-parallel-agents/
│   │   ├── finishing-a-development-branch/
│   │   ├── receiving-code-review/
│   │   ├── requesting-code-review/
│   │   ├── subagent-driven-development/
│   │   ├── using-git-worktrees/
│   │   ├── using-superpowers/
│   │   └── writing-skills/
│   │
│   └── ecc-skills/                # ECC 技能（180个）
│       ├── python-patterns/
│       ├── golang-patterns/
│       ├── security-review/
│       ├── e2e-testing/
│       ├── backend-patterns/
│       ├── frontend-patterns/
│       └── ... (其他175个技能)
│
├── commands/                      # 命令
├── agents/                        # 智能体
└── rules/                         # 规则
```

---

## 核心技能列表

### Superpowers 核心技能（主链路）

| 技能名称 | 描述 | 使用场景 |
|---------|------|---------|
| `brainstorming` | 头脑风暴和需求澄清 | 在任何创造性工作之前必须使用 |
| `test-driven-development` | TDD 测试驱动开发 | 实现任何功能或修复 bug 之前 |
| `writing-plans` | 编写实施计划 | 多步骤任务，在编写代码之前 |
| `executing-plans` | 执行实施计划 | 有明确的实施计划需要执行时 |
| `verification-before-completion` | 完成前验证 | 声称工作完成之前必须验证 |
| `systematic-debugging` | 系统化调试 | 遇到 bug、测试失败或意外行为时 |
| `dispatching-parallel-agents` | 并行代理调度 | 面临 2+ 个独立任务时 |

### ECC 核心技能（辅链路）

| 技能名称 | 描述 | 使用场景 |
|---------|------|---------|
| `python-patterns` | Python 最佳实践 | 编写、审查或重构 Python 代码 |
| `golang-patterns` | Go 语言模式 | 编写、审查或重构 Go 代码 |
| `security-review` | 安全审查 | 添加认证、处理用户输入、处理机密时 |
| `e2e-testing` | E2E 测试 | Playwright 端到端测试 |
| `backend-patterns` | 后端架构模式 | Node.js、Express、Next.js API 路由 |
| `frontend-patterns` | 前端开发模式 | React、Next.js、状态管理 |

---

## 使用方法

### 1. 查看所有可用技能

在 Trae IDE 中输入 `/` 查看所有可用命令和技能。

### 2. 使用 Superpowers 技能

Superpowers 技能主要用于主开发链路：

```bash
# 头脑风暴
/brainstorm

# TDD 开发
/test-driven-development

# 编写计划
/write-plan

# 执行计划
/execute-plan
```

### 3. 使用 ECC 技能

ECC 技能主要用于专项工作：

```bash
# Python 最佳实践
/python-patterns

# 安全审查
/security-review

# E2E 测试
/e2e-testing
```

---

## 验证技能环境

运行验证脚本确认所有技能正确安装：

```bash
cd /Users/htli/Desktop/workspace/BI-Agent/.trae
python3 verify_skills.py
```

---

## 权责划分

根据项目核心宪法，Superpowers 和 ECC 有明确的权责划分：

### Superpowers（主链路）
- ✅ 需求澄清与规划
- ✅ TDD 开发（单元测试先行）
- ✅ 集成测试
- ✅ 代码评审（业务逻辑）
- ✅ 版本控制

### ECC（辅链路）
- ✅ E2E 测试
- ✅ 性能测试
- ✅ 安全测试
- ✅ 代码保障（漏洞扫描、依赖修复）
- ✅ 文档与记忆
- ✅ 运维与优化

**重要**: 严禁交叉调用！Superpowers 禁止编写 E2E/性能/安全测试，ECC 禁止编写单元测试。

---

## 更新技能

如需更新技能，请重新克隆仓库并安装：

### 更新 ECC 技能

```bash
cd /Users/htli/Desktop/workspace/BI-Agent/.trae/skills
git clone https://github.com/affaan-m/everything-claude-code.git temp-ecc
cd temp-ecc
./install.sh
# 安装完成后删除临时目录
cd ..
rm -rf temp-ecc
```

### 更新 Superpowers 技能

```bash
cd /Users/htli/Desktop/workspace/BI-Agent/.trae/skills
git clone https://github.com/obra/superpowers.git temp-sp
# 手动复制更新的技能到 superpowers-skills/
rm -rf temp-sp
```

---

## 技能统计

- **总技能数**: 194 个
- **Superpowers**: 14 个
- **ECC**: 180 个
- **验证状态**: ✅ 所有技能配置正确

---

**最后更新**: 2026-04-10  
