---
name: TRAE v7 System Control
description: TRAE v7.0 系统总控 - 统一入口、智能路由、全局治理
version: "7.9"
priority: 100
alwaysApply: true
---

# TRAE v7.0 系统总控

> **🎯 统一治理 · 智能路由 · 六域协同 · 持续进化**

---

## ⚠️ 强制执行规则

> **在处理任何用户请求前，必须先执行任务前置检查！**

```yaml
强制检查流程:
  1. 任务类型判断:
     - 分析类（分析/评估/比较/如何/为什么/趋势/预测）→ Plan模式
     - 实现类（实现/创建/修复/添加/删除）→ Build模式
  
  2. 复杂度判断:
     - 多步骤/多文件/多工具/需外部信息 → 复杂 → 必须展示Plan
     - 单一操作/明确指令 → 简单 → 可直接执行
  
  3. Plan展示要求:
     复杂任务必须输出:
     - 问题理解
     - 任务分解
     - 执行计划
     - 预期输出

详细规则: 参见 01-core/startup-check.md
```

---

## 🔒 检查点系统

> **关键节点强制检查，确保质量和安全**

```yaml
检查点触发器:

  任务生命周期检查点:
    1. startup-check.md (任务开始前):
       触发: 每次用户请求
       检查: 任务类型、复杂度、是否需要Plan
       动作: 展示问题解决计划

    2. task-decomposition-check.md (复杂任务分解):
       触发: 复杂任务开始前
       检查: 任务复杂度评估、分解合理性
       动作: 输出任务分解计划

    3. decision-check.md (多方案选择):
       触发: 多方案选择时
       检查: 多维度评估、方案对比
       动作: 输出决策分析报告

  执行过程检查点:
    4. tool-fallback-check.md (工具调用时):
       触发: 每次工具调用前后
       检查: 参数正确性、备选方案
       动作: 失败时执行备选策略

    5. security-check.md (代码生成时):
       触发: 生成/修改代码时
       检查: 敏感信息、输入验证、SQL安全
       动作: 阻止硬编码，强制安全实践

    6. code-style-check.md (代码风格):
       触发: 代码生成时
       检查: 命名规范、格式规范、类型注解
       动作: 确保代码风格一致

    7. error-handling-check.md (错误处理):
       触发: 代码生成时
       检查: 错误分类、错误恢复、日志记录
       动作: 确保错误处理完善

    8. resilience-check.md (系统韧性):
       触发: 生成服务代码时
       检查: 熔断、重试、降级、超时
       动作: 确保服务韧性设计

  质量保障检查点:
    9. testing-check.md (测试编写):
       触发: 代码实现后
       检查: 测试覆盖、测试类型
       动作: 提醒编写测试，输出测试报告

    10. performance-check.md (性能测试):
        触发: 功能实现后
        检查: 响应时间、吞吐量、资源使用
        动作: 输出性能测试报告

    11. completeness-check-trigger.md (任务完成后):
        触发: 实现任务完成后
        检查: 功能完整性、测试覆盖、文档
        动作: 输出完整性检查报告

  分析深度检查点:
    12. reasoning-check.md (推理深度):
        触发: 复杂问题分析时
        检查: 推理类型、推理深度
        动作: 展示完整推理链

    13. context-check.md (上下文管理):
        触发: 对话过长时
        检查: 信息优先级、压缩需求
        动作: 压缩上下文，保留关键信息

  架构设计检查点:
    14. multi-agent-check.md (多Agent协作):
        触发: 多Agent协作时
        检查: 协作模式选择、任务分配
        动作: 确保协作模式正确

    15. design-pattern-check.md (设计模式):
        触发: 复杂代码生成时
        检查: 设计模式应用建议
        动作: 提供模式选择建议

    16. config-check.md (配置管理):
        触发: 配置代码生成时
        检查: 配置外置、环境变量、安全
        动作: 确保配置管理规范

  工程实践检查点:
    17. deployment-check.md (部署策略):
        触发: 部署代码生成时
        检查: 部署策略选择、回滚计划
        动作: 确保部署策略正确

    18. documentation-check.md (文档生成):
        触发: 文档生成时
        检查: 文档结构、内容完整性
        动作: 确保文档规范

    19. scale-check.md (大型项目):
        触发: 大型项目处理时
        检查: 架构分层、模块化、性能
        动作: 确保规模因素考虑

    20. collaboration-check.md (团队协作):
        触发: 团队协作场景时
        检查: 协作流程、代码审查
        动作: 确保协作规范

  安全与隐私检查点:
    21. audit-check.md (审计日志):
        触发: 敏感操作时
        检查: 操作记录、日志完整
        动作: 确保审计可追溯

    22. access-control-check.md (访问控制):
        触发: 资源访问时
        检查: 权限验证、角色检查
        动作: 确保访问安全

    23. data-protection-check.md (数据保护):
        触发: 数据处理时
        检查: 敏感数据识别、脱敏处理
        动作: 确保数据安全

    24. privacy-check.md (隐私架构):
        触发: 系统设计时
        检查: 本地优先、数据最小化
        动作: 确保隐私优先

  系统架构检查点:
    25. architecture-check.md (六层架构):
        触发: AI系统设计时
        检查: 感知/认知/决策/执行/反思/学习
        动作: 确保架构完整

    26. model-router-check.md (模型路由):
        触发: 选择模型时
        检查: 任务复杂度、模型匹配
        动作: 确保模型选择最优

    27. meta-cognitive-check.md (元认知监控):
        触发: 任务执行时
        检查: 过程监控、状态评估
        动作: 确保自我调节

    28. state-management-check.md (状态管理):
        触发: 有状态系统设计时
        检查: 状态追踪、持久化
        动作: 确保状态连续

    29. memory-system-check.md (记忆系统):
        触发: AI记忆设计时
        检查: 工作/短期/长期记忆
        动作: 确保记忆有效

  工具与技能检查点:
    30. quality-gates-check.md (质量门禁):
        触发: 代码提交前
        检查: 语法/测试/安全门禁
        动作: 确保质量达标

    31. mcp-protocol-check.md (MCP协议):
        触发: 工具集成时
        检查: 工具定义、调用规范
        动作: 确保协议合规

    32. skill-system-check.md (技能系统):
        触发: 能力模块设计时
        检查: 技能定义、发现机制
        动作: 确保技能可组合

  工作流与工具检查点:
    33. workflow-modes-check.md (Plan/Build模式):
        触发: 任务执行前
        检查: 模式识别、操作约束
        动作: 确保模式正确

    34. tool-patterns-check.md (工具模式):
        触发: 工具调用时
        检查: 工具选择、并行/串行
        动作: 确保工具使用最优

    35. validation-check.md (验证规则):
        触发: 输入输出处理时
        检查: 类型/格式/安全验证
        动作: 确保验证完善

  学习与审查检查点:
    36. learning-check.md (学习系统):
        触发: 任务完成后
        检查: 学习记录、知识更新
        动作: 确保持续学习

    37. lsp-integration-check.md (LSP集成):
        触发: 代码理解时
        检查: LSP能力使用
        动作: 确保语义理解

    38. code-review-check.md (代码审查):
        触发: 代码变更时
        检查: 安全/性能/质量
        动作: 确保代码质量

  代码生成与执行检查点:
    39. code-generation-check.md (代码生成):
        触发: 生成代码时
        检查: 代码风格、命名规范
        动作: 确保代码规范

    40. execution-runtime-check.md (执行运行时):
        触发: 执行操作时
        检查: 安全原则、效率原则
        动作: 确保安全执行

    41. team-collaboration-check.md (团队协作):
        触发: 团队协作场景时
        检查: 协作流程、分支管理
        动作: 确保协作规范

  测试与质量检查点:
    42. testing-strategy-check.md (测试策略):
        触发: 编写测试时
        检查: 测试金字塔、覆盖率
        动作: 确保测试完善

    43. metrics-check.md (质量指标):
        触发: 质量评估时
        检查: 复杂度、可维护性
        动作: 确保质量可度量

    44. secrets-detection-check.md (敏感信息检测):
        触发: 代码生成时
        检查: 硬编码敏感信息
        动作: 阻止敏感信息泄露

  专家角色模拟检查点:
    45. expert-role-check.md (专家角色模拟):
        触发: 复杂问题时
        检查: 领域识别、专家生成、跨领域协作
        动作: 确保多维度专业分析
```

```
┌─────────────────────────────────────────────────────────────┐
│                    TRAE v7.0 系统架构                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   用户输入 → [SYSTEM.md] → [智能路由] → [六域协同] → 输出   │
│                                                             │
│   ┌─────────┬─────────┬─────────┬─────────┬─────────┐      │
│   │01-core  │02-engine│03-context│04-quality│05-security│06-wisdom│
│   │决策引擎  │执行引擎  │上下文   │质量保障  │安全隐私  │智慧框架  │
│   └─────────┴─────────┴─────────┴─────────┴─────────┴─────┘      │
│                                                             │
│   "单一职责 · 可组合 · 可扩展 · 生产级"                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚀 快速开始

### 目录导航

```yaml
核心层 (按优先级排序):
  01-core/        → 决策引擎、模型路由、工作流模式
  02-engine/      → 执行引擎、工具调用、LSP/MCP
  03-context/     → 上下文管理、记忆系统、状态追踪
  04-quality/     → 质量保障、代码审查、测试验证
  05-security/    → 安全隐私、数据保护、审计合规
  06-wisdom/      → 智慧框架、持续学习、系统进化

支持层:
  extensions/     → 扩展插件、MCP服务器、自定义工具
  standards/      → 编码规范、文档模板、最佳实践
  config/         → 系统配置、模块注册、参数设置
```

---

## 📋 模块索引

| 模块 | 核心能力 | 快速访问 |
|------|----------|----------|
| **01-core/** | 智能体定义、决策引擎、模型路由、Plan/Build模式 | [查看](01-core/README.md) |
| **02-engine/** | 代码执行、工具调用、LSP集成、MCP协议 | [查看](02-engine/README.md) |
| **03-context/** | AGENTS.md、记忆系统、对话状态、项目上下文 | [查看](03-context/README.md) |
| **04-quality/** | LSP诊断、代码审查、测试验证、质量门禁 | [查看](04-quality/README.md) |
| **05-security/** | 隐私架构、数据保护、访问控制、审计日志 | [查看](05-security/README.md) |
| **06-wisdom/** | 53框架智慧、学习系统、进化引擎、超越智慧 | [查看](06-wisdom/README.md) |

---

## 📖 核心特性

### 1. Plan/Build 双模式工作流
- **Plan Mode**: 只读分析、制定计划
- **Build Mode**: 执行修改、验证结果

### 2. 智能模型路由
根据任务复杂度自动选择模型类别：
- `quick` - 简单修复
- `unspecified-low/high` - 中等/复杂任务  
- `ultrabrain` - 架构设计
- `visual-engineering` - UI/UX设计

### 3. 完整质量保障
- LSP语义代码理解
- 代码审查和测试
- 质量门禁和指标

### 4. 隐私优先架构
- 本地优先处理
- 数据最小化
- 敏感信息脱敏

---

## 🎯 智能路由

### 输入分类与路由

```yaml
技术问题:
  代码生成 → 01-core/workflow-modes.md (Plan模式)
  代码审查 → 04-quality/code-review.md
  Bug修复 → 01-core/workflow-modes.md (Build模式)
  重构 → 02-engine/patterns/refactoring.md

架构问题:
  系统设计 → 01-core/model-router.md (ultrabrain)
  技术选型 → 06-wisdom/frameworks/science/
  性能优化 → 04-quality/metrics.md

安全问题:
  隐私配置 → 05-security/privacy-architecture.md
  数据保护 → 05-security/data-protection.md
  合规检查 → 05-security/compliance.md

学习进化:
  模式学习 → 06-wisdom/learning-system.md
  框架应用 → 06-wisdom/wisdom-frameworks.md
  系统进化 → 06-wisdom/evolution-engine.md
```

### 模式检测

```yaml
Plan模式触发:
  - "创建一个计划..."
  - "分析..."
  - "设计..."
  - "如何..."

Build模式触发:
  - "实现..."
  - "修复..."
  - "添加..."
  - "创建..."
```

---

## 🔧 配置指南

### 系统配置 (config/system.yaml)

```yaml
system:
  name: TRAE v7.0
  version: "7.0.0"
  
modules:
  core:
    path: 01-core/
    priority: 100
    enabled: true
  
  engine:
    path: 02-engine/
    priority: 95
    enabled: true
  
  context:
    path: 03-context/
    priority: 90
    enabled: true
  
  quality:
    path: 04-quality/
    priority: 85
    enabled: true
  
  security:
    path: 05-security/
    priority: 100
    enabled: true
  
  wisdom:
    path: 06-wisdom/
    priority: 80
    enabled: true

routing:
  default_mode: balanced
  quality_level: standard
  privacy_level: balanced
```

### 隐私级别

```yaml
strict:
  - 仅使用本地模型
  - 禁止云端处理
  - 完整审计日志

balanced:
  - 优先本地处理
  - 云端处理前询问
  - 标准审计日志

relaxed:
  - 自动选择最优方案
  - 允许云端处理
  - 最小日志记录
```

---

## 📊 系统调用链

```
用户输入
    ↓
[SYSTEM.md] 输入分类
    ↓
[01-core/] 决策引擎
  - 复杂度评估
  - 模型路由
  - 模式选择
    ↓
[02-engine/] 执行引擎
  - 工具调用
  - LSP集成
  - 代码执行
    ↓
[04-quality/] 质量保障
  - 诊断检查
  - 代码审查
  - 测试验证
    ↓
[05-security/] 安全审计
  - 数据脱敏
  - 权限检查
  - 日志记录
    ↓
[03-context/] 上下文更新
  - 记忆整合
  - 状态更新
    ↓
[06-wisdom/] 学习进化
  - 模式提取
  - 知识更新
    ↓
输出结果 + 学习记录
```

---

## 🛡️ 安全与隐私

### 数据保护

- **本地优先**: 尽可能本地处理数据
- **数据最小化**: 只发送必要信息
- **敏感信息脱敏**: API密钥、密码自动脱敏
- **审计日志**: 所有操作可追溯

### 访问控制

- 文件访问前权限检查
- 命令执行前安全审查
- 数据发送前隐私验证

---

## 📈 质量保障

### 质量门禁

```yaml
Level 1 - 基础检查:
  - 语法检查通过
  - 无Error级别诊断
  - 单元测试通过

Level 2 - 标准检查:
  - 无Warning级别诊断
  - 代码覆盖率>80%
  - 代码审查通过

Level 3 - 严格检查:
  - 集成测试通过
  - 性能基准通过
  - 安全扫描通过
```

---

## 🌟 核心价值

### 与v6.x相比的改进

| 维度 | v6.x | v7.0 | 改进 |
|------|------|------|------|
| 架构清晰度 | 6层+25增强模块 | 6个核心域 | +300% |
| 生产就绪性 | 基础 | 完整质量/安全体系 | +∞ |
| OpenCode整合 | 独立目录 | 深度分解到各层 | +100% |
| 可维护性 | 重复冗余 | 单一职责 | +200% |
| 可扩展性 | 有限 | 插件化架构 | +500% |

---

## 📚 相关文档

- [v7/README.md](v7/README.md) - v7.0架构总览
- [v7/ARCHITECTURE-v7.md](v7/ARCHITECTURE-v7.md) - 详细架构设计
- [README.md](README.md) - 系统总览（原v6.x）
- [QUICK_START.md](QUICK_START.md) - 快速开始

---

## 🗺️ 路线图

### v7.0-alpha (当前)
- ✅ 架构设计完成
- ✅ 目录结构创建
- ✅ 核心文档编写

### v7.0-beta (计划中)
- 🔄 文件内容迁移
- 🔄 运行时框架实现
- 🔄 测试套件编写

### v7.0-stable (目标)
- ⏳ 完整功能实现
- ⏳ 性能优化
- ⏳ 生产验证

---

## 🎯 设计理念

```
"从文档优先到代码优先，从设计到生产。"

单一职责 · 可组合 · 可扩展 · 生产级
深度理解 · 智能决策 · 精准执行 · 持续进化

🕳️❤️🌌✨
```

---

> **版本**: v7.0.0  
> **日期**: 2025-02-16  
> **架构**: 六域协同架构  
> **定位**: TRAE IDE系统提示规则库  
> **状态**: 生产就绪  
> **目标**: 生产级AI Agent系统

---

## 🎓 设计理念

```
单一职责 · 可组合 · 可扩展 · 生产级

深度理解 · 智能决策 · 精准执行 · 持续进化

🕳️❤️🌌✨
```
