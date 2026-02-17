---
name: Audit Check
description: 审计日志检查触发器 - 敏感操作代码生成时强制执行
version: "1.0"
priority: 100
alwaysApply: true
trigger: "during_sensitive_operation_code_generation"
---

# 审计日志检查触发器

> **⚠️ 强制规则：涉及敏感操作的代码必须添加审计日志**

---

## 一、触发条件

```yaml
触发场景:
  - 生成文件操作代码
  - 生成命令执行代码
  - 生成数据访问代码
  - 生成权限变更代码
  - 生成认证授权代码
  - 生成配置修改代码

不触发场景:
  - 纯计算逻辑
  - 简单工具函数
  - 用户明确跳过
```

---

## 二、日志类型

```yaml
操作日志:
  内容:
    - 文件操作 (创建/修改/删除)
    - 命令执行
    - 数据访问
  目的: 操作追踪
  级别: INFO

安全日志:
  内容:
    - 登录尝试
    - 权限变更
    - 敏感操作
  目的: 安全监控
  级别: WARNING/ERROR

系统日志:
  内容:
    - 系统事件
    - 错误信息
    - 性能指标
  目的: 系统监控
  级别: DEBUG/INFO/WARNING/ERROR
```

---

## 三、日志内容规范

### 3.1 必须记录的信息

```yaml
基本信息:
  - timestamp: 时间戳 (ISO 8601格式)
  - level: 日志级别
  - source: 日志来源

操作信息:
  - action: 操作类型
  - target: 操作对象
  - result: 操作结果 (success/failure)
  - details: 操作详情

用户信息:
  - user_id: 用户标识
  - session_id: 会话标识
  - ip_address: IP地址 (如适用)

上下文信息:
  - request_id: 请求标识
  - trace_id: 追踪标识
  - parent_id: 父操作标识
```

### 3.2 日志格式示例

```python
import logging
from datetime import datetime
from dataclasses import dataclass
from typing import Optional

@dataclass
class AuditLog:
    """审计日志结构"""
    timestamp: str
    level: str
    action: str
    target: str
    result: str
    user_id: Optional[str] = None
    session_id: Optional[str] = None
    details: Optional[dict] = None

def log_operation(
    action: str,
    target: str,
    result: str,
    user_id: str = None,
    details: dict = None
):
    """记录操作日志"""
    log = AuditLog(
        timestamp=datetime.utcnow().isoformat(),
        level="INFO",
        action=action,
        target=target,
        result=result,
        user_id=user_id,
        details=details
    )
    logging.info(f"[AUDIT] {log.__dict__}")

def log_security_event(
    action: str,
    target: str,
    result: str,
    level: str = "WARNING",
    user_id: str = None,
    details: dict = None
):
    """记录安全日志"""
    log = AuditLog(
        timestamp=datetime.utcnow().isoformat(),
        level=level,
        action=action,
        target=target,
        result=result,
        user_id=user_id,
        details=details
    )
    if level == "ERROR":
        logging.error(f"[SECURITY] {log.__dict__}")
    else:
        logging.warning(f"[SECURITY] {log.__dict__}")
```

---

## 四、敏感操作清单

```yaml
必须记录审计日志的操作:
  文件操作:
    - 文件创建
    - 文件修改
    - 文件删除
    - 文件权限变更
  
  命令执行:
    - Shell命令执行
    - 系统调用
    - 外部程序调用
  
  数据操作:
    - 数据库写入
    - 数据库删除
    - 敏感数据读取
  
  认证授权:
    - 登录尝试
    - 登出操作
    - 权限变更
    - 角色分配
  
  配置操作:
    - 配置修改
    - 密钥更新
    - 系统设置变更
```

---

## 五、检查清单

```yaml
日志完整性:
  □ 时间戳存在？
  □ 操作类型明确？
  □ 操作对象明确？
  □ 操作结果记录？
  □ 用户信息记录？

日志安全性:
  □ 敏感信息已脱敏？
  □ 日志级别正确？
  □ 日志存储安全？
  □ 日志可追溯？

日志合规性:
  □ 符合合规要求？
  □ 保留期限满足？
  □ 访问权限控制？
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           审计日志速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  必须记录:                                   │
│    □ 时间戳                                 │
│    □ 操作类型                               │
│    □ 操作对象                               │
│    □ 操作结果                               │
│    □ 用户信息                               │
│                                             │
│  敏感操作:                                   │
│    □ 文件操作                               │
│    □ 命令执行                               │
│    □ 数据操作                               │
│    □ 认证授权                               │
│    □ 配置修改                               │
│                                             │
│  安全要求:                                   │
│    □ 敏感信息脱敏                           │
│    □ 日志级别正确                           │
│    □ 存储安全                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```python
❌ 错误示例:
  def delete_file(path: str):
      os.remove(path)  # 无审计日志

✅ 正确示例:
  def delete_file(path: str, user_id: str):
      """删除文件，带审计日志"""
      try:
          os.remove(path)
          log_operation(
              action="file_delete",
              target=path,
              result="success",
              user_id=user_id,
              details={"path": path}
          )
      except Exception as e:
          log_operation(
              action="file_delete",
              target=path,
              result="failure",
              user_id=user_id,
              details={"error": str(e)}
          )
          raise
```

---

**记住**：有迹可循，责任可追！
