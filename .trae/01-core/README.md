---
name: TRAE Core Layer
description: 智能体核心层 - 决策引擎、模型路由、工作流模式
version: "7.0"
priority: 100
alwaysApply: true
---

# 01-core/ 智能体核心层

## 职责范围

智能体核心层是TRAE系统的"大脑"，负责：
- **智能体定义** - 定义智能体的能力、角色和行为
- **决策引擎** - 基于多维度因素做出智能决策
- **模型路由** - 根据任务复杂度、成本、延迟选择最优模型
- **工作流模式** - Plan/Build双模式工作流管理

---

## 核心组件

### 1. 智能体架构 (architecture.md)
六层智能体架构的完整定义：
- 感知层 → 认知层 → 决策层 → 执行层 → 反思层 → 学习层

### 2. 决策引擎 (decision-engine.md)
多因素决策系统，整合10位专家系统的智慧：
- 复杂度评估
- 风险评估
- 资源分配
- 策略选择

### 3. 模型路由器 (model-router.md)
基于OpenCode最佳实践的多模型路由：
- **quick**: 简单修复、拼写错误
- **unspecified-low**: 中等任务
- **unspecified-high**: 复杂任务
- **ultrabrain**: 架构设计、复杂逻辑
- **visual-engineering**: 前端UI/UX
- **artistry**: 创造性任务
- **writing**: 文档编写

### 4. 工作流模式 (workflow-modes.md)
Plan/Build双模式工作流：
- **Plan Mode**: 只读规划模式，用于分析、设计、计划
- **Build Mode**: 执行模式，用于实施、验证、交付

---

## 快速参考

```yaml
模型选择决策树:
  complexity < 0.3 AND cost_sensitive:
    → quick
  complexity > 0.8 OR architecture_involved:
    → ultrabrain
  frontend_ui_ux_task:
    → visual-engineering
  documentation_task:
    → writing
  complexity > 0.6:
    → unspecified-high
  else:
    → unspecified-low

工作流模式检测:
  Plan Mode触发词: plan, analyze, design, how should
  Build Mode触发词: implement, fix, add, create
```

---

## 文件清单

| 文件 | 说明 | 来源 |
|------|------|------|
| README.md | 本文件 | 新建 |
| architecture.md | 六层架构详解 | 原01-core/agent.md |
| decision-engine.md | 决策引擎 | 原05-intelligence/engine.md |
| model-router.md | 模型路由器 | opencode-study/03-model-routing.md |
| workflow-modes.md | 工作流模式 | opencode-study/01-workflow-modes.md |
| multi-agent.md | 多智能体协调 | 原02-execution/core-guide.md |
| meta-cognitive.md | 元认知监控 | 原09-enhancements/meta-cognitive-deepening.md |
| agent.yaml | 智能体配置 | 新建 |

---

## 相关层

- ↑ 调用: SYSTEM.md (统一治理层)
- → 协作: 02-engine/ (执行引擎层)
- → 协作: 03-context/ (上下文管理层)
- ↓ 依赖: 05-security/ (安全隐私层)

---

## 集成OpenCode模式

本层整合了以下OpenCode最佳实践：
1. **模型路由策略** - 根据任务特征智能选择模型
2. **Plan/Build双模式** - 规划与执行分离，确保安全性
3. **多智能体协调** - 复杂任务的分布式决策

---

> **核心原则**: "合适的模型，恰当的时机，正确的决策。"
