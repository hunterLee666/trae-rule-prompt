---
name: TRAE Context Layer
description: 上下文管理层 - 记忆系统、上下文加载、状态管理
version: "7.0"
priority: 95
alwaysApply: true
---

# 03-context/ 上下文管理层

## 职责范围

上下文管理层是TRAE系统的"记忆"，负责：
- **上下文管理** - AGENTS.md分层加载和合并
- **记忆系统** - 工作记忆、短期记忆、长期记忆
- **状态管理** - 对话状态追踪、话题管理
- **项目上下文** - 项目特定的知识和约定

---

## 核心组件

### 1. 上下文管理 (context-management.md)
基于OpenCode的AGENTS.md最佳实践：

```
上下文层级（从高到低优先级）：
Level 5: Conversation Context (当前对话历史)
Level 4: File Context (文件内注释)
Level 3: Directory Context (目录AGENTS.md)
Level 2: Project Context (项目根AGENTS.md)
Level 1: System Context (系统内置知识)
```

**加载顺序**：
1. 系统提示词（基础指令）
2. 项目AGENTS.md（项目级上下文）
3. 目录AGENTS.md（模块级上下文）
4. 文件特定上下文（文件头注释）
5. 用户查询（当前输入）

**合并规则**：
- 优先级：高优先级覆盖低优先级
- Frontmatter：按优先级覆盖
- 指令：连接并分隔
- 约束：所有约束的并集

### 2. 记忆系统 (memory-system.md)
三层记忆架构：

```yaml
Working Memory:
  容量: 有限（当前会话）
  内容: 当前任务、最近操作、临时数据
  生命周期: 会话期间
  
Short-term Memory:
  容量: 中等（最近N轮对话）
  内容: 对话历史、用户偏好、上下文线索
  生命周期: 跨会话，但有TTL
  
Long-term Memory:
  容量: 大（持久化）
  内容: 学习到的模式、用户画像、项目知识
  生命周期: 永久
```

### 3. 状态管理 (state-management.md)
对话状态追踪：

```yaml
State Components:
  topic_stack: []        # 话题栈（支持3-5个并行话题）
  intent_history: []     # 意图历史
  user_preferences: {}   # 用户偏好
  session_goals: []      # 会话目标
  emotional_state: {}    # 情感状态
```

**状态转换**：
- 话题切换：压栈/弹栈
- 意图演化：追踪意图变化
- 长期记忆整合：重要信息持久化

### 4. 项目上下文 (project-context.md)
项目特定知识管理：

```yaml
AGENTS.md Structure:
  ---
  name: Project Name
  description: 项目描述
  version: "1.0"
  priority: 100
  ---
  
  # 项目上下文
  
  ## 概览
  项目目标和范围
  
  ## 技术栈
  - 语言/框架
  - 依赖
  - 工具链
  
  ## 架构模式
  - 设计模式
  - 代码组织
  - 命名约定
  
  ## 编码标准
  - 风格指南
  - 最佳实践
  - 禁忌事项
  
  ## 常见任务
  - 开发流程
  - 构建命令
  - 测试策略
```

---

## 上下文模板 (templates/)

### agents-template.md
项目级AGENTS.md模板

### project-template.md
项目初始化模板

---

## 快速参考

```yaml
上下文加载:
  1. 解析frontmatter
  2. 按优先级合并
  3. 应用上下文约束
  4. 生成响应

记忆检索:
  Working → Short-term → Long-term
  
状态追踪:
  话题栈深度: 3-5
  历史窗口: 最近10轮
```

---

## 文件清单

| 文件 | 说明 | 来源 |
|------|------|------|
| README.md | 本文件 | 新建 |
| context-management.md | 上下文管理 | opencode-study/05-context-management.md |
| memory-system.md | 记忆系统 | 原01-core/memory.md |
| state-management.md | 状态管理 | 原06-interaction/engine.md |
| project-context.md | 项目上下文 | 新建 |
| templates/*.md | 上下文模板 | 新建 |

---

## 相关层

- ↑ 调用: 01-core/ (决策引擎)
- → 协作: 02-engine/ (执行引擎)
- → 协作: 06-wisdom/ (智慧框架)
- ↓ 被依赖: 所有其他层

---

## 集成OpenCode模式

本层整合了以下OpenCode最佳实践：
1. **分层上下文加载** - 系统→项目→目录→文件→对话
2. **AGENTS.md规范** - 项目特定知识管理
3. **上下文合并规则** - 优先级驱动的智能合并
4. **上下文大小管理** - 性能优化的上下文控制

---

> **核心原则**: "上下文为王，记忆铸就智能。"
