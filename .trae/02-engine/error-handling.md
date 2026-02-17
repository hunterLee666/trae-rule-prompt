---
name: Error Handling Rules
description: 错误处理规则 - 错误分类、恢复策略、日志规范、追踪机制
version: "9.4"
priority: 90
alwaysApply: true
---

# 错误处理规则

> **"错误可预期，恢复有策略。"**

## 一、错误分类体系

### 1.1 错误级别

```yaml
Critical (致命):
  定义: 系统无法继续运行
  示例: 
    - 数据库连接失败
    - 核心服务不可用
    - 内存耗尽
  处理: 立即终止，通知管理员
  响应时间: 立即

Error (错误):
  定义: 严重问题，需要立即处理
  示例: 
    - 文件不存在
    - 权限不足
    - 配置错误
  处理: 终止当前操作，报告用户
  响应时间: 1分钟内

Warning (警告):
  定义: 潜在问题，建议处理
  示例:
    - 弃用的API
    - 性能警告
    - 兼容性问题
  处理: 记录日志，继续执行
  响应时间: 1小时内

Info (信息):
  定义: 正常操作信息
  示例:
    - 操作完成
    - 状态变更
    - 进度更新
  处理: 记录日志
  响应时间: 无需响应

Debug (调试):
  定义: 调试信息
  示例:
    - 变量值
    - 执行路径
    - 性能数据
  处理: 仅开发环境记录
  响应时间: 无需响应
```

### 1.2 错误类型分类

```yaml
文件系统错误:
  FileNotFoundError:
    原因: 文件不存在
    恢复: 搜索相似文件，询问用户，创建文件
    
  PermissionError:
    原因: 权限不足
    恢复: 请求权限，降级为只读，报告用户
    
  IsADirectoryError:
    原因: 期望文件但得到目录
    恢复: 列出目录内容，询问用户选择
    
  FileTooLargeError:
    原因: 文件过大
    恢复: 分块读取，使用流式处理

网络错误:
  ConnectionError:
    原因: 无法建立连接
    恢复: 重试，检查网络，使用离线模式
    
  TimeoutError:
    原因: 请求超时
    恢复: 增加超时时间，重试，使用缓存
    
  HTTPError:
    原因: HTTP错误响应
    恢复: 检查状态码，重试，使用备用URL
    
  SSLError:
    原因: SSL证书问题
    恢复: 跳过验证(不推荐)，更新证书

数据解析错误:
  JSONDecodeError:
    原因: JSON格式错误
    恢复: 定位错误位置，尝试修复，使用宽松解析
    
  UnicodeDecodeError:
    原因: 编码问题
    恢复: 尝试不同编码，使用错误处理策略
    
  ValueError:
    原因: 值格式不正确
    恢复: 验证输入，转换格式，报告用户

工具调用错误:
  ToolNotFoundError:
    原因: 工具不存在
    恢复: 搜索相似工具，建议替代方案
    
  InvalidParameterError:
    原因: 参数无效
    恢复: 验证参数，提供正确格式示例
    
  ToolExecutionError:
    原因: 工具执行失败
    恢复: 重试，使用备选工具，降级处理

类型错误:
  TypeError:
    原因: 类型不匹配
    恢复: 检查类型，转换或修复
    
  AttributeError:
    原因: 属性不存在
    恢复: 检查对象类型，使用getattr默认值
    
  KeyError:
    原因: 键不存在
    恢复: 使用get默认值，检查字典内容
```

### 1.3 错误严重程度评估

```yaml
评估维度:
  影响范围:
    局部: 1分 - 仅影响当前操作
    模块: 2分 - 影响整个模块
    系统: 3分 - 影响整个系统
    
  恢复难度:
    自动: 1分 - 可自动恢复
    手动: 2分 - 需要用户干预
    专家: 3分 - 需要技术支持
    
  数据影响:
    无: 1分 - 无数据影响
    部分: 2分 - 部分数据丢失
    严重: 3分 - 数据损坏或丢失
    
  频率:
    首次: 1分 - 首次出现
    偶发: 2分 - 偶尔出现
    频繁: 3分 - 频繁出现

严重程度计算:
  总分 4-6: 低严重度
  总分 7-9: 中严重度
  总分 10-12: 高严重度
```

---

## 二、恢复策略

### 2.1 自动恢复

```yaml
重试策略:
  简单重试:
    次数: 3次
    间隔: 固定1秒
    
  指数退避:
    次数: 5次
    间隔: 1s, 2s, 4s, 8s, 16s
    
  抖动重试:
    次数: 5次
    间隔: 基础时间 + 随机抖动

降级策略:
  功能降级:
    - 使用简化版本
    - 返回缓存数据
    - 返回默认值
    
  服务降级:
    - 关闭非核心功能
    - 限流保护
    - 返回友好提示
```

### 2.2 手动恢复

```yaml
用户干预:
  权限请求:
    场景: 权限不足
    操作: 请求用户授权
    
  配置修正:
    场景: 配置错误
    操作: 引导用户修正
    
  资源准备:
    场景: 资源不存在
    操作: 引导用户创建

开发者干预:
  代码修复:
    场景: 语法错误、逻辑错误
    操作: 提供修复建议
    
  架构调整:
    场景: 设计问题
    操作: 提供重构方案
```

---

## 三、错误传播

### 3.1 传播规则

```yaml
向上传播:
  场景: 子模块无法处理的错误
  规则: 包装错误，添加上下文
  示例: 
    原始: "File not found"
    传播: "ConfigLoader: Failed to load config.json - File not found"

跨模块传播:
  场景: 模块间调用失败
  规则: 转换为模块特定错误
  示例:
    原始: NetworkError
    转换: DataSyncError

边界传播:
  场景: 系统边界错误
  规则: 脱敏敏感信息
  示例:
    内部: "Database connection failed: postgres://user:pass@host"
    外部: "Service temporarily unavailable"
```

### 3.2 错误链

```yaml
错误链结构:
  root_cause: 原始错误
  context: 错误上下文
  actions: 已尝试的操作
  suggestions: 建议操作

示例:
  ErrorChain:
    error: "BuildFailed"
    cause: 
      error: "DependencyNotFound"
      cause:
        error: "NetworkError"
        message: "Connection timeout"
    context:
      file: "package.json"
      dependency: "lodash@4.17.21"
    actions:
      - "Retried 3 times"
      - "Checked registry availability"
    suggestions:
      - "Check network connection"
      - "Try alternative registry"
```

---

## 四、日志规范

### 4.1 日志格式

```yaml
标准格式:
  timestamp: ISO8601时间戳
  level: 日志级别
  module: 模块名称
  message: 日志消息
  context: 上下文信息
  trace_id: 追踪ID (分布式系统)

示例:
  {
    "timestamp": "2024-01-15T10:30:00Z",
    "level": "ERROR",
    "module": "FileHandler",
    "message": "Failed to read file",
    "context": {
      "file": "/path/to/file.txt",
      "error": "Permission denied"
    },
    "trace_id": "abc123"
  }
```

### 4.2 日志级别使用

```yaml
ERROR:
  使用场景: 需要立即关注的错误
  内容: 错误详情、影响范围、恢复建议
  示例: "Failed to connect to database: connection refused"

WARN:
  使用场景: 潜在问题、弃用警告
  内容: 问题描述、影响、建议操作
  示例: "API v1 is deprecated, please migrate to v2"

INFO:
  使用场景: 重要操作、状态变更
  内容: 操作描述、关键参数、结果
  示例: "User login successful: user_id=123"

DEBUG:
  使用场景: 调试信息、性能数据
  内容: 详细状态、变量值、执行路径
  示例: "Processing item: id=456, status=pending"
```

### 4.3 敏感信息处理

```yaml
脱敏规则:
  密码:
    原始: "password=secret123"
    脱敏: "password=****"
    
  API密钥:
    原始: "api_key=sk-abc123xyz"
    脱敏: "api_key=sk-***xyz"
    
  个人信息:
    原始: "email=user@example.com"
    脱敏: "email=u***@example.com"
    
  IP地址:
    原始: "ip=192.168.1.100"
    脱敏: "ip=192.168.***.***"
```

---

## 五、错误报告

### 5.1 用户报告

```yaml
报告内容:
  简洁描述: 发生了什么
  影响说明: 对用户的影响
  建议操作: 用户可以做什么
  
示例:
  "无法保存文件。请检查文件是否被其他程序占用，
  或尝试保存到其他位置。"
```

### 5.2 开发者报告

```yaml
报告内容:
  错误类型: 异常类型
  错误消息: 详细消息
  堆栈跟踪: 调用栈
  上下文: 相关变量
  环境信息: 版本、配置
  复现步骤: 如何复现
  
示例:
  Error: Failed to parse JSON
  at JSON.parse (<anonymous>)
  at loadConfig (config.js:25)
  
  Context:
    file: "/path/to/config.json"
    content: "{invalid json}"
  
  Environment:
    node: v18.0.0
    os: macOS 13.0
```

---

## 六、最佳实践

### 6.1 错误处理原则

```yaml
快速失败:
  - 尽早发现问题
  - 不要隐藏错误
  - 明确错误原因

优雅降级:
  - 提供替代方案
  - 保持部分功能
  - 友好提示用户

可恢复性:
  - 提供恢复路径
  - 保存中间状态
  - 支持重试操作

可观测性:
  - 详细日志记录
  - 错误追踪
  - 性能监控
```

### 6.2 常见反模式

```yaml
反模式:
  吞掉异常:
    错误: catch (e) { /* ignore */ }
    正确: catch (e) { logger.error(e); handle(e); }
    
  过于宽泛:
    错误: catch (Exception e)
    正确: catch (SpecificException e)
    
  暴露敏感信息:
    错误: throw new Error("Password: " + password)
    正确: throw new Error("Authentication failed")
    
  无上下文:
    错误: throw new Error("Failed")
    正确: throw new Error("Failed to load config from " + path)
```
