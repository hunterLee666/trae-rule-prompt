---
name: TRAE Engine Layer
description: 执行引擎层 - 代码执行、工具调用、技能管理、LSP集成
version: "7.0"
priority: 95
alwaysApply: true
---

# 02-engine/ 执行引擎层

## 职责范围

执行引擎层是TRAE系统的"手脚"，负责：
- **执行运行时** - 代码执行、命令运行、结果捕获
- **工具调用** - 文件操作、系统命令、网络请求
- **技能管理** - 技能的注册、调用、组合
- **LSP集成** - 语义代码理解、导航、诊断
- **MCP协议** - 标准化工具集成协议

---

## 核心组件

### 1. 执行运行时 (execution-runtime.md)
统一的代码执行环境：
- 文件操作 (read/write/edit/glob/grep)
- 命令执行 (bash/interactive_bash)
- 代码分析 (ast_grep/lsp_*)
- 网络请求 (webfetch/websearch)

### 2. 工具调用模式 (tool-patterns.md)
基于OpenCode的工具调用最佳实践：
- 工具选择策略
- 并行vs串行执行
- 错误处理与恢复
- 性能优化

### 3. LSP集成 (lsp-integration.md)
语义代码理解能力：
- `lsp_goto_definition` - 跳转到定义
- `lsp_find_references` - 查找引用
- `lsp_symbols` - 符号提取
- `lsp_diagnostics` - 诊断检查
- `lsp_rename` - 安全重命名

### 4. MCP协议 (mcp-protocol.md)
Model Context Protocol标准集成：
- 内置MCP服务器 (filesystem, terminal, git)
- 外部MCP服务器 (database, browser, API)
- 自定义MCP服务器
- 能力发现与安全控制

### 5. 技能系统 (skill-system.md)
技能注册与管理：
- 技能定义
- 技能组合
- 技能调用
- 技能学习

---

## 执行模式库 (patterns/)

### exploration.md - 探索模式
用于理解代码库的结构和逻辑：
```yaml
模式:
  1. glob查找相关文件
  2. read读取关键文件
  3. lsp_symbols映射组件
  4. lsp_goto_definition深入理解
```

### refactoring.md - 重构模式
安全重构代码的标准流程：
```yaml
模式:
  1. lsp_find_references影响分析
  2. read理解当前实现
  3. edit进行修改
  4. lsp_diagnostics验证
  5. 运行测试确认
```

### debugging.md - 调试模式
系统性调试问题的方法：
```yaml
模式:
  1. 收集错误信息
  2. lsp_goto_definition定位问题
  3. lsp_find_references追踪影响
  4. 分析根因
  5. edit修复
  6. 验证修复
```

### generation.md - 生成模式
代码生成的标准流程：
```yaml
模式:
  1. 理解需求
  2. 设计接口
  3. 实现代码
  4. 添加测试
  5. 文档说明
```

---

## 工具注册表 (tool-registry.yaml)

```yaml
categories:
  file_operations:
    - read
    - write
    - edit
    - glob
    - grep
  
  code_analysis:
    - lsp_goto_definition
    - lsp_find_references
    - lsp_symbols
    - lsp_diagnostics
    - ast_grep_search
  
  system_commands:
    - bash
    - interactive_bash
  
  web_operations:
    - webfetch
    - websearch
  
  agent_operations:
    - delegate_task
    - background_output
    - background_cancel
```

---

## 快速参考

```yaml
LSP工具使用:
  理解代码: lsp_goto_definition
  重构: lsp_find_references + lsp_rename
  验证: lsp_diagnostics
  探索: lsp_symbols

工具执行原则:
  独立操作 → 并行执行
  依赖操作 → 串行执行
  修改后 → 立即验证
```

---

## 文件清单

| 文件 | 说明 | 来源 |
|------|------|------|
| README.md | 本文件 | 新建 |
| execution-runtime.md | 执行运行时 | 原02-execution/core-guide.md |
| tool-patterns.md | 工具调用模式 | opencode-study/06-tool-patterns.md |
| tool-registry.yaml | 工具注册表 | 新建 |
| lsp-integration.md | LSP集成 | opencode-study/02-lsp-integration.md |
| mcp-protocol.md | MCP协议 | opencode-study/04-mcp-protocol.md |
| skill-system.md | 技能系统 | 原01-core/skills.md |
| code-quality.md | 代码质量 | 原02-execution/code-patterns.md |
| patterns/*.md | 执行模式库 | 原02-execution分解 |

---

## 相关层

- ↑ 调用: 01-core/ (决策引擎)
- → 协作: 04-quality/ (质量保障)
- → 协作: 05-security/ (安全隐私)
- ↓ 依赖: 03-context/ (上下文管理)

---

## 集成OpenCode模式

本层整合了以下OpenCode最佳实践：
1. **LSP语义导航** - 超越文本搜索的代码理解
2. **MCP协议** - 标准化的工具集成
3. **工具调用模式** - 并行、验证、错误处理
4. **代码质量** - SOLID原则、重构模式

---

> **核心原则**: "工欲善其事，必先利其器。"
