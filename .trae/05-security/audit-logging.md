---
name: Audit Logging
description: 审计日志 - 操作记录与合规追踪
version: "7.0"
priority: 100
alwaysApply: true
---

# 审计日志

> **"有迹可循，责任可追。"**

## 一、日志类型

```yaml
操作日志:
  内容:
    - 文件操作
    - 命令执行
    - 数据访问
  目的: 操作追踪

安全日志:
  内容:
    - 登录尝试
    - 权限变更
    - 敏感操作
  目的: 安全监控

系统日志:
  内容:
    - 系统事件
    - 错误信息
    - 性能指标
  目的: 系统监控
```

## 二、日志内容

```yaml
基本信息:
  - 时间戳
  - 日志级别
  - 日志来源

操作信息:
  - 操作类型
  - 操作对象
  - 操作结果
  - 操作详情

用户信息:
  - 用户标识
  - 会话标识
  - 来源IP
  - 用户代理

上下文信息:
  - 任务ID
  - 请求ID
  - 关联ID
```

## 三、日志格式

```yaml
标准格式:
  timestamp: ISO8601时间戳
  level: 日志级别
  source: 日志来源
  operation: 操作类型
  object: 操作对象
  result: 操作结果
  user: 用户标识
  session: 会话标识
  details: 详细信息

示例:
  timestamp: "2026-02-16T10:00:00Z"
  level: "INFO"
  source: "file-operation"
  operation: "read"
  object: "/path/to/file"
  result: "success"
  user: "user123"
  session: "session456"
  details:
    lines: 100
    duration: 50ms
```

## 四、日志级别

```yaml
DEBUG:
  用途: 调试信息
  内容: 详细执行过程
  保留: 开发环境

INFO:
  用途: 正常操作
  内容: 关键操作记录
  保留: 所有环境

WARNING:
  用途: 警告信息
  内容: 潜在问题
  保留: 所有环境

ERROR:
  用途: 错误信息
  内容: 错误详情
  保留: 所有环境

CRITICAL:
  用途: 严重错误
  内容: 安全事件
  保留: 永久
```

## 五、日志管理

```yaml
存储策略:
  热数据:
    - 最近7天
    - 快速查询
    
  温数据:
    - 30天内
    - 正常查询
    
  冷数据:
    - 30天以上
    - 归档存储

清理策略:
  - DEBUG: 保留7天
  - INFO: 保留30天
  - WARNING: 保留90天
  - ERROR: 保留1年
  - CRITICAL: 永久保留

查询能力:
  - 按时间范围查询
  - 按日志级别查询
  - 按操作类型查询
  - 按用户查询
```

## 六、日志分析

```yaml
实时分析:
  - 异常检测
  - 安全告警
  - 性能监控

定期分析:
  - 操作统计
  - 趋势分析
  - 合规报告

分析报告:
  - 操作频率统计
  - 异常行为识别
  - 安全事件追踪
  - 合规性检查
```
