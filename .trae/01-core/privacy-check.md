---
name: Privacy Check
description: 隐私架构检查触发器 - 系统设计时强制执行
version: "1.0"
priority: 100
alwaysApply: true
trigger: "during_system_design"
---

# 隐私架构检查触发器

> **⚠️ 强制规则：系统设计必须遵循隐私优先原则**

---

## 一、触发条件

```yaml
触发场景:
  - 设计系统架构
  - 设计数据处理流程
  - 设计用户交互
  - 设计数据存储
  - 设计API接口

不触发场景:
  - 简单工具函数
  - 用户明确跳过
```

---

## 二、隐私原则

```yaml
核心原则:
  1. 本地优先处理:
     - 尽可能在本地处理数据
     - 使用本地模型 (Ollama, LM Studio)
     - 最小化网络请求
  
  2. 数据最小化:
     - 只收集必要数据
     - 只发送必要代码片段
     - 敏感信息脱敏
  
  3. 用户控制:
     - 用户决定数据共享范围
     - 可配置隐私级别
     - 随时撤销授权
  
  4. 透明度:
     - 清晰说明数据流向
     - 公开数据处理政策
     - 提供审计能力
```

---

## 三、隐私级别

```yaml
strict (严格):
  特点:
    - 仅使用本地模型
    - 禁止云端处理
    - 完整审计日志
  适用:
    - 敏感项目
    - 合规要求高
    - 数据不可外传

balanced (平衡):
  特点:
    - 本地优先
    - 敏感数据本地处理
    - 非敏感数据可云端
  适用:
    - 常规项目
    - 平衡性能与隐私

relaxed (宽松):
  特点:
    - 允许云端处理
    - 敏感数据脱敏后发送
    - 基本审计日志
  适用:
    - 非敏感项目
    - 性能优先场景
```

---

## 四、数据处理规范

### 4.1 数据分类

```yaml
公开数据:
  定义: 可公开访问的数据
  处理: 无限制
  示例: 公开文档、开源代码

内部数据:
  定义: 组织内部数据
  处理: 访问控制
  示例: 内部文档、配置文件

敏感数据:
  定义: 需要保护的数据
  处理: 加密、脱敏、审计
  示例: 用户信息、商业数据

机密数据:
  定义: 高度敏感数据
  处理: 本地处理、禁止外传
  示例: 密钥、财务数据
```

### 4.2 数据流向控制

```yaml
本地处理优先:
  规则: 优先在本地处理数据
  实现:
    - 使用本地模型
    - 本地缓存
    - 离线处理

最小化传输:
  规则: 只传输必要数据
  实现:
    - 代码片段而非完整文件
    - 脱敏后传输
    - 压缩传输

传输加密:
  规则: 传输必须加密
  实现:
    - HTTPS
    - 端到端加密
    - 证书验证
```

---

## 五、实现示例

```python
from enum import Enum
from typing import Optional, Dict, Any

class PrivacyLevel(Enum):
    """隐私级别"""
    STRICT = "strict"
    BALANCED = "balanced"
    RELAXED = "relaxed"

class PrivacyManager:
    """隐私管理器"""
    
    def __init__(self, level: PrivacyLevel = PrivacyLevel.BALANCED):
        self.level = level
        self.audit_log = []
    
    def should_process_locally(self, data_type: str) -> bool:
        """判断是否应该本地处理"""
        if self.level == PrivacyLevel.STRICT:
            return True
        
        sensitive_types = ["password", "api_key", "token", "secret"]
        if data_type in sensitive_types:
            return True
        
        return False
    
    def sanitize_for_remote(self, data: Dict[str, Any]) -> Dict[str, Any]:
        """为远程处理脱敏数据"""
        sensitive_keys = [
            "password", "api_key", "token", "secret",
            "email", "phone", "id_card"
        ]
        
        result = data.copy()
        for key in sensitive_keys:
            if key in result:
                result[key] = "***"
        
        return result
    
    def log_data_access(self, action: str, data_type: str, destination: str):
        """记录数据访问"""
        import datetime
        self.audit_log.append({
            "timestamp": datetime.datetime.utcnow().isoformat(),
            "action": action,
            "data_type": data_type,
            "destination": destination
        })

def process_data(data: Dict[str, Any], privacy: PrivacyManager) -> Dict[str, Any]:
    """处理数据 - 遵循隐私原则"""
    data_type = data.get("type", "unknown")
    
    if privacy.should_process_locally(data_type):
        privacy.log_data_access("process_local", data_type, "local")
        return process_locally(data)
    else:
        sanitized = privacy.sanitize_for_remote(data)
        privacy.log_data_access("process_remote", data_type, "cloud")
        return process_remotely(sanitized)
```

---

## 六、检查清单

```yaml
隐私设计:
  □ 遵循本地优先原则？
  □ 数据最小化原则？
  □ 用户可控？
  □ 透明度足够？

数据处理:
  □ 数据分类正确？
  □ 敏感数据识别？
  □ 脱敏规则应用？
  □ 传输加密？

隐私控制:
  □ 隐私级别可配置？
  □ 用户可撤销授权？
  □ 审计日志完整？
  □ 合规要求满足？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           隐私架构速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  核心原则:                                   │
│    □ 本地优先处理                           │
│    □ 数据最小化                             │
│    □ 用户控制                               │
│    □ 透明度                                 │
│                                             │
│  隐私级别:                                   │
│    strict: 仅本地                           │
│    balanced: 本地优先                       │
│    relaxed: 允许云端                        │
│                                             │
│  检查要点:                                   │
│    □ 敏感数据识别                           │
│    □ 脱敏规则应用                           │
│    □ 传输加密                               │
│    □ 审计日志                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 八、违规示例

```python
❌ 错误示例:
  def analyze_code(code: str):
      # 直接发送到云端，无隐私检查
      return cloud_api.analyze(code)

✅ 正确示例:
  def analyze_code(code: str, privacy: PrivacyManager):
      """分析代码 - 遵循隐私原则"""
      # 检查是否包含敏感信息
      if contains_sensitive_data(code):
          privacy.log_data_access("analyze", "sensitive", "local")
          return local_model.analyze(code)
      
      # 脱敏后发送
      sanitized = privacy.sanitize_for_remote({"code": code})
      privacy.log_data_access("analyze", "code", "cloud")
      return cloud_api.analyze(sanitized)
```

---

**记住**：隐私优先，安全为本！
