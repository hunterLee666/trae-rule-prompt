---
name: LSP Integration
description: LSP集成 - 语义代码理解能力
version: "7.0"
priority: 90
alwaysApply: true
---

# LSP集成

> **"语义理解，超越文本。"**

## 一、LSP能力

```yaml
核心能力:
  lsp_goto_definition:
    用途: 跳转到定义
    场景: 理解函数/类的来源
    
  lsp_find_references:
    用途: 查找所有引用
    场景: 重构前影响分析
    
  lsp_symbols:
    用途: 提取文件符号
    场景: 快速了解文件结构
    
  lsp_diagnostics:
    用途: 获取诊断信息
    场景: 代码质量检查
    
  lsp_rename:
    用途: 安全重命名
    场景: 批量重命名变量/函数
```

## 二、使用场景

### 2.1 代码探索

```yaml
探索流程:
  1. lsp_symbols(file)
     → 了解文件的整体结构
     → 识别主要函数、类、变量
     
  2. lsp_goto_definition(symbol)
     → 深入理解具体实现
     → 追踪依赖关系
     
  3. lsp_find_references(symbol)
     → 了解使用情况
     → 评估修改影响
```

### 2.2 安全重构

```yaml
重构流程:
  1. lsp_find_references(symbol)
     → 找到所有引用位置
     → 评估影响范围
     
  2. lsp_rename(symbol, new_name)
     → 安全重命名
     → 自动更新所有引用
     
  3. lsp_diagnostics(file)
     → 验证重构结果
     → 检查是否有遗漏
```

### 2.3 质量检查

```yaml
检查流程:
  1. lsp_diagnostics(file)
     → 获取所有诊断信息
     
  2. 分类处理:
     Error: 必须修复
     Warning: 建议修复
     Information: 仅供参考
     
  3. 逐项修复
  4. 再次验证
```

## 三、诊断类型

```yaml
诊断级别:
  Error (必须修复):
    - 语法错误
    - 类型错误
    - 未定义变量
    - 导入错误
    
  Warning (建议修复):
    - 未使用变量
    - 过时的API
    - 潜在的空指针
    - 类型不匹配
    
  Information (仅供参考):
    - 风格建议
    - 优化提示
    - 文档缺失
    
  Hint (可选改进):
    - 简化建议
    - 性能提示
    - 代码组织
```

## 四、最佳实践

### 4.1 探索代码

```yaml
推荐流程:
  1. 先用 lsp_symbols 了解结构
  2. 再用 lsp_goto_definition 深入
  3. 最后用 lsp_find_references 评估

避免:
  - 直接全文搜索(效率低)
  - 忽略符号信息
```

### 4.2 重构代码

```yaml
推荐流程:
  1. 先用 lsp_find_references 评估影响
  2. 使用 lsp_rename 批量重命名
  3. 用 lsp_diagnostics 验证结果

避免:
  - 手动查找替换(易遗漏)
  - 重构后不验证
```

### 4.3 修复问题

```yaml
推荐流程:
  1. 用 lsp_diagnostics 获取问题列表
  2. 按严重程度排序处理
  3. 每次修复后重新检查

避免:
  - 忽略Warning级别问题
  - 批量修复不验证
```

## 五、语言支持

```yaml
支持的语言:
  JavaScript/TypeScript:
    - 完整支持
    - 类型推断
    - 模块解析
    
  Python:
    - 完整支持
    - 类型提示
    - 导入分析
    
  Go:
    - 完整支持
    - 包管理
    - 接口实现
    
  Rust:
    - 完整支持
    - 借用检查
    - 宏展开
    
  Java:
    - 完整支持
    - 类型系统
    - 继承分析
```

## 六、LSP vs 传统方法

```yaml
LSP优势:
  语义理解:
    - 理解代码含义
    - 精确定位
    - 跨文件追踪
    
  安全性:
    - 类型安全
    - 自动更新引用
    - 避免遗漏
    
  效率:
    - 快速导航
    - 批量操作
    - 实时反馈

传统方法局限:
  文本搜索:
    - 只能匹配文本
    - 可能误匹配
    - 无法理解语义
    
  手动查找:
    - 容易遗漏
    - 效率低下
    - 容易出错
```
