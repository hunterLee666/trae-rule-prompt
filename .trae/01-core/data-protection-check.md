---
name: Data Protection Check
description: 数据保护检查触发器 - 处理敏感数据时强制执行
version: "1.0"
priority: 100
alwaysApply: true
trigger: "during_sensitive_data_handling"
---

# 数据保护检查触发器

> **⚠️ 强制规则：处理敏感数据时必须脱敏和保护**

---

## 一、触发条件

```yaml
触发场景:
  - 处理用户个人信息
  - 处理API密钥/密码
  - 处理财务数据
  - 处理商业机密
  - 日志记录敏感信息
  - 数据传输和存储

不触发场景:
  - 公开数据
  - 用户明确跳过
```

---

## 二、敏感数据分类

```yaml
凭证类 (最高敏感度):
  - API密钥
  - 访问令牌
  - 密码
  - 私钥
  - 证书
  处理: 加密存储、不记录日志、传输加密

个人信息 (高敏感度):
  - 姓名
  - 邮箱
  - 电话
  - 地址
  - 身份证号
  处理: 脱敏显示、访问控制、加密存储

商业敏感 (中敏感度):
  - 商业机密
  - 财务数据
  - 客户数据
  - 算法实现
  处理: 访问控制、审计日志

内部数据 (低敏感度):
  - 配置信息
  - 系统日志
  - 性能数据
  处理: 访问控制
```

---

## 三、脱敏规则

### 3.1 脱敏模式

```yaml
完全隐藏:
  适用: 密码、密钥、令牌
  示例: "password" → "***"

部分隐藏:
  适用: 邮箱、电话、身份证
  示例:
    - "user@example.com" → "u***@example.com"
    - "13812345678" → "138****5678"
    - "110101199001011234" → "110101********1234"

哈希处理:
  适用: 需要比对但不需原文
  示例: "password" → "5f4dcc3b5aa765d61d8327deb882cf99"

加密存储:
  适用: 需要还原的敏感数据
  示例: 使用AES/RSA加密
```

### 3.2 脱敏实现

```python
import re
import hashlib
from typing import Optional

def mask_email(email: str) -> str:
    """邮箱脱敏"""
    if not email or "@" not in email:
        return email
    name, domain = email.split("@", 1)
    masked_name = name[0] + "***" if len(name) > 1 else "***"
    return f"{masked_name}@{domain}"

def mask_phone(phone: str) -> str:
    """电话脱敏"""
    if not phone or len(phone) < 7:
        return phone
    return phone[:3] + "****" + phone[-4:]

def mask_id_card(id_card: str) -> str:
    """身份证脱敏"""
    if not id_card or len(id_card) < 8:
        return id_card
    return id_card[:6] + "********" + id_card[-4:]

def mask_password(password: str) -> str:
    """密码完全隐藏"""
    return "***"

def hash_value(value: str, salt: str = "") -> str:
    """哈希处理"""
    return hashlib.sha256((value + salt).encode()).hexdigest()

def sanitize_log(data: dict, sensitive_keys: list) -> dict:
    """日志脱敏"""
    result = data.copy()
    for key in sensitive_keys:
        if key in result:
            result[key] = "***"
    return result

SENSITIVE_KEYS = [
    "password", "passwd", "pwd",
    "api_key", "apikey", "key", "secret",
    "token", "access_token", "refresh_token",
    "credit_card", "card_number",
    "ssn", "id_card"
]

def safe_log(message: str, data: dict = None):
    """安全日志记录"""
    if data:
        data = sanitize_log(data, SENSITIVE_KEYS)
    print(f"[LOG] {message}: {data}")
```

---

## 四、检测规则

```yaml
文件名检测:
  敏感文件:
    - .env
    - .env.*
    - *.key
    - *.pem
    - credentials*
    - secrets*
    - password*
  
  处理: 警告用户、禁止提交

内容模式检测:
  API密钥:
    - /[a-zA-Z0-9]{32,}/
    - /sk-[a-zA-Z0-9]{20,}/
  
  密码:
    - /password\s*=\s*["'][^"']+["']/
    - /passwd\s*=\s*["'][^"']+["']/
  
  令牌:
    - /token\s*=\s*["'][^"']+["']/
    - /Bearer\s+[a-zA-Z0-9]+/
  
  处理: 脱敏或移除
```

---

## 五、检查清单

```yaml
数据识别:
  □ 敏感数据类型识别？
  □ 敏感度级别评估？
  □ 数据流向追踪？

数据处理:
  □ 脱敏规则应用？
  □ 加密措施实施？
  □ 访问控制设置？

数据传输:
  □ 使用HTTPS？
  □ 传输加密？
  □ 证书验证？

数据存储:
  □ 加密存储？
  □ 访问控制？
  □ 备份安全？

日志记录:
  □ 敏感信息脱敏？
  □ 日志访问控制？
  □ 日志保留期限？
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           数据保护速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  敏感数据类型:                               │
│    □ 凭证类 (密钥/密码/令牌)                │
│    □ 个人信息 (姓名/电话/身份证)            │
│    □ 商业敏感 (财务/客户/机密)              │
│                                             │
│  脱敏规则:                                   │
│    密码 → ***                               │
│    邮箱 → u***@example.com                  │
│    电话 → 138****5678                       │
│    身份证 → 110101********1234              │
│                                             │
│  检查要点:                                   │
│    □ 日志脱敏                               │
│    □ 传输加密                               │
│    □ 存储加密                               │
│    □ 访问控制                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```python
❌ 错误示例:
  def login(username: str, password: str):
      print(f"Login: {username}, password: {password}")  # 日志泄露密码
      return authenticate(username, password)

✅ 正确示例:
  def login(username: str, password: str):
      """登录 - 安全处理密码"""
      safe_log("Login attempt", {"username": username})  # 密码不记录
      try:
          result = authenticate(username, password)
          log_security_event("login", username, "success")
          return result
      except Exception as e:
          log_security_event("login", username, "failure")
          raise
```

---

**记住**：数据安全，责任重大！
