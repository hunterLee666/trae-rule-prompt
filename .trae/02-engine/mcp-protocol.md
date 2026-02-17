---
name: MCP Protocol
description: MCP协议 - 标准化工具集成协议
version: "7.0"
priority: 90
alwaysApply: true
---

# MCP协议

> **"标准接口，无限扩展。"**

## 一、协议概述

```yaml
MCP (Model Context Protocol):
  定义: 标准化的工具集成协议
  目标: 统一工具调用接口
  优势: 可扩展、可组合、可维护

核心概念:
  Tool:
    - 单一功能单元
    - 标准输入输出
    - 独立可测试
    
  Server:
    - 工具集合
    - 资源管理
    - 能力声明
    
  Client:
    - 工具发现
    - 工具调用
    - 结果处理
```

## 二、工具定义

```yaml
工具结构:
  name: 工具名称
  description: 工具描述
  inputSchema: 输入参数模式
  outputSchema: 输出结果模式

示例:
  name: read_file
  description: 读取文件内容
  inputSchema:
    type: object
    properties:
      file_path:
        type: string
        description: 文件路径
      limit:
        type: integer
        description: 读取行数
    required: [file_path]
```

## 三、调用流程

```yaml
标准流程:
  1. 发现工具:
     - 列出可用工具
     - 获取工具描述
     - 理解工具能力
     
  2. 准备参数:
     - 验证参数类型
     - 填充必需参数
     - 设置可选参数
     
  3. 调用工具:
     - 发送调用请求
     - 等待执行结果
     - 处理超时情况
     
  4. 处理结果:
     - 解析返回数据
     - 验证结果格式
     - 处理错误情况
```

## 四、错误处理

```yaml
错误类型:
  参数错误:
    - 缺少必需参数
    - 参数类型错误
    - 参数值无效
    
  执行错误:
    - 工具执行失败
    - 资源不可用
    - 权限不足
    
  超时错误:
    - 执行超时
    - 连接超时
    - 响应超时

错误处理:
  - 返回标准错误格式
  - 包含错误代码和描述
  - 提供恢复建议
```

## 五、最佳实践

```yaml
工具设计:
  - 单一职责
  - 清晰的输入输出
  - 完善的错误处理
  - 详细的文档说明

调用实践:
  - 验证参数有效性
  - 设置合理的超时
  - 处理所有错误情况
  - 记录调用日志

安全考虑:
  - 验证调用权限
  - 限制资源使用
  - 敏感数据保护
  - 审计日志记录
```
