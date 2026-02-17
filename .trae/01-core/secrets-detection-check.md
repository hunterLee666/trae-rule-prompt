---
name: Secrets Detection Check
description: 敏感信息检测检查触发器 - 代码生成时强制执行
version: "1.0"
priority: 100
alwaysApply: true
trigger: "during_code_generation"
---

# 敏感信息检测检查触发器

> **⚠️ 强制规则：代码中禁止硬编码敏感信息**

---

## 一、触发条件

```yaml
触发场景:
  - 生成代码时
  - 修改代码时
  - 创建配置文件时
  - 提交代码前

不触发场景:
  - 用户明确跳过
```

---

## 二、敏感信息分类

```yaml
P0 - 严重敏感信息:
  - API密钥 (API_KEY, API_SECRET)
  - 数据库密码 (DB_PASSWORD, DATABASE_URL)
  - 认证令牌 (AUTH_TOKEN, JWT_SECRET)
  - 加密密钥 (ENCRYPTION_KEY, SECRET_KEY)
  - 私钥文件内容 (RSA_PRIVATE_KEY, SSH_KEY)

P1 - 高敏感信息:
  - 用户密码
  - 访问令牌 (ACCESS_TOKEN, REFRESH_TOKEN)
  - OAuth密钥 (OAUTH_CLIENT_SECRET)
  - 云服务凭证 (AWS_SECRET_KEY, GCP_API_KEY)

P2 - 中敏感信息:
  - 内部IP地址
  - 端口号 (非标准端口)
  - 用户名 (DB_USER, ADMIN_USER)
  - 邮箱地址 (内部邮箱)

P3 - 低敏感信息:
  - 调试信息
  - 内部URL
  - 版本号
```

---

## 三、检测模式

### 3.1 硬编码密钥检测

```yaml
检测模式:
  直接赋值:
    模式: API_KEY = "sk-xxx"
    风险: P0
    处理: 阻止并提示使用环境变量
  
  字符串拼接:
    模式: password = "admin" + "123"
    风险: P0
    处理: 阻止并提示安全存储
  
  配置字典:
    模式: config = {"password": "xxx"}
    风险: P0
    处理: 阻止并提示配置文件外置

正则检测模式:
  API密钥:
    - "sk-[a-zA-Z0-9]{20,}"
    - "api[_-]?key[_-]?=.*['\"][a-zA-Z0-9]{16,}['\"]"
  
  数据库连接:
    - "mysql://[^:]+:[^@]+@"
    - "postgres://[^:]+:[^@]+@"
  
  AWS密钥:
    - "AKIA[0-9A-Z]{16}"
    - "aws[_-]?secret[_-]?access[_-]?key"
```

### 3.2 敏感变量名检测

```yaml
敏感变量名:
  密钥类:
    - password, passwd, pwd
    - secret, secret_key, secret_token
    - api_key, apikey, api_secret
    - access_token, auth_token
  
  认证类:
    - credential, credentials
    - private_key, privatekey
    - token, jwt, bearer
  
  连接类:
    - connection_string, conn_str
    - database_url, db_url
    - mongodb_uri, redis_url
```

---

## 四、安全替代方案

### 4.1 环境变量

```yaml
推荐做法:
  Python:
    import os
    api_key = os.environ.get("API_KEY")
  
  JavaScript:
    const apiKey = process.env.API_KEY;
  
  Go:
    apiKey := os.Getenv("API_KEY")

环境变量文件:
  创建: .env文件
  内容: API_KEY=your_key_here
  忽略: 添加到.gitignore
```

### 4.2 密钥管理服务

```yaml
云服务:
  AWS:
    - AWS Secrets Manager
    - AWS Parameter Store
  
  GCP:
    - Secret Manager
  
  Azure:
    - Key Vault

本地开发:
  - dotenv库
  - vault
  - pass
```

### 4.3 配置文件外置

```yaml
配置文件:
  格式:
    - YAML (.yaml, .yml)
    - JSON (.json)
    - TOML (.toml)
  
  位置:
    - /etc/app/config.yaml
    - ~/.app/config.yaml
    - ./config/local.yaml
  
  安全:
    - 不提交敏感配置
    - 使用模板文件
    - 环境变量覆盖
```

---

## 五、实现示例

```python
import re
from dataclasses import dataclass
from typing import List, Dict, Optional, Tuple
from enum import Enum

class Severity(Enum):
    """严重程度"""
    P0 = "critical"
    P1 = "high"
    P2 = "medium"
    P3 = "low"

@dataclass
class SecretFinding:
    """敏感信息发现"""
    file: str
    line: int
    severity: Severity
    type: str
    match: str
    suggestion: str

class SecretsDetector:
    """敏感信息检测器"""
    
    PATTERNS = {
        Severity.P0: [
            (r'api[_-]?key[_-]?=\s*["\'][^"\']{16,}["\']', "API密钥硬编码"),
            (r'secret[_-]?key[_-]?=\s*["\'][^"\']{16,}["\']', "密钥硬编码"),
            (r'password[_-]?=\s*["\'][^"\']+["\']', "密码硬编码"),
            (r'token[_-]?=\s*["\'][^"\']{16,}["\']', "令牌硬编码"),
            (r'sk-[a-zA-Z0-9]{20,}', "OpenAI API密钥"),
            (r'AKIA[0-9A-Z]{16}', "AWS访问密钥"),
            (r'-----BEGIN (RSA |DSA |EC |OPENSSH )?PRIVATE KEY-----', "私钥文件"),
        ],
        Severity.P1: [
            (r'access[_-]?token[_-]?=\s*["\'][^"\']{16,}["\']', "访问令牌硬编码"),
            (r'oauth[_-]?secret[_-]?=\s*["\'][^"\']+["\']', "OAuth密钥硬编码"),
            (r'aws[_-]?secret[_-]?access[_-]?key[_-]?=\s*["\'][^"\']+["\']', "AWS密钥硬编码"),
        ],
        Severity.P2: [
            (r'\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b', "IP地址"),
            (r':\d{4,5}', "非标准端口"),
            (r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}', "邮箱地址"),
        ],
    }
    
    SENSITIVE_NAMES = [
        "password", "passwd", "pwd", "secret", "secret_key",
        "api_key", "apikey", "api_secret", "access_token",
        "auth_token", "private_key", "credential", "token"
    ]
    
    def scan_code(self, code: str, file_path: str) -> List[SecretFinding]:
        """扫描代码"""
        findings = []
        lines = code.split('\n')
        
        for i, line in enumerate(lines, 1):
            for severity, patterns in self.PATTERNS.items():
                for pattern, desc in patterns:
                    matches = re.finditer(pattern, line, re.IGNORECASE)
                    for match in matches:
                        findings.append(SecretFinding(
                            file=file_path,
                            line=i,
                            severity=severity,
                            type=desc,
                            match=self._mask_secret(match.group()),
                            suggestion=self._get_suggestion(desc)
                        ))
        
        return findings
    
    def scan_variable_names(self, code: str, 
                           file_path: str) -> List[SecretFinding]:
        """扫描敏感变量名"""
        findings = []
        lines = code.split('\n')
        
        for i, line in enumerate(lines, 1):
            for name in self.SENSITIVE_NAMES:
                pattern = rf'\b{name}\s*=\s*["\'][^"\']+["\']'
                if re.search(pattern, line, re.IGNORECASE):
                    findings.append(SecretFinding(
                        file=file_path,
                        line=i,
                        severity=Severity.P0,
                        type=f"敏感变量赋值: {name}",
                        match=line.strip()[:50],
                        suggestion="使用环境变量存储敏感信息"
                    ))
        
        return findings
    
    def _mask_secret(self, secret: str) -> str:
        """脱敏显示"""
        if len(secret) <= 8:
            return "***"
        return secret[:4] + "***" + secret[-4:]
    
    def _get_suggestion(self, issue_type: str) -> str:
        """获取建议"""
        suggestions = {
            "API密钥硬编码": "使用环境变量: os.environ.get('API_KEY')",
            "密钥硬编码": "使用密钥管理服务存储密钥",
            "密码硬编码": "使用环境变量或配置文件",
            "令牌硬编码": "使用OAuth流程获取令牌",
            "私钥文件": "将私钥存储在安全位置，使用环境变量指定路径",
        }
        return suggestions.get(issue_type, "使用安全的密钥管理方式")

class SecretsPreventer:
    """敏感信息阻止器"""
    
    def __init__(self):
        self.detector = SecretsDetector()
    
    def check_and_prevent(self, code: str, 
                         file_path: str) -> Tuple[bool, List[SecretFinding]]:
        """检查并阻止"""
        findings = []
        
        findings.extend(self.detector.scan_code(code, file_path))
        findings.extend(self.detector.scan_variable_names(code, file_path))
        
        critical_findings = [
            f for f in findings 
            if f.severity in [Severity.P0, Severity.P1]
        ]
        
        return len(critical_findings) == 0, findings
    
    def generate_safe_code(self, original: str, 
                          findings: List[SecretFinding]) -> str:
        """生成安全代码"""
        safe_code = original
        
        for finding in findings:
            if finding.severity == Severity.P0:
                safe_code = self._replace_with_env_var(safe_code, finding)
        
        return safe_code
    
    def _replace_with_env_var(self, code: str, 
                             finding: SecretFinding) -> str:
        """替换为环境变量"""
        lines = code.split('\n')
        if finding.line <= len(lines):
            line = lines[finding.line - 1]
            
            if '=' in line:
                var_name = line.split('=')[0].strip()
                lines[finding.line - 1] = f"{var_name} = os.environ.get('{var_name.upper()}')"
        
        return '\n'.join(lines)
```

---

## 六、检查清单

```yaml
P0级检查:
  □ 无硬编码API密钥？
  □ 无硬编码密码？
  □ 无硬编码令牌？
  □ 无私钥文件内容？

P1级检查:
  □ 无硬编码访问令牌？
  □ 无硬编码OAuth密钥？
  □ 无硬编码云服务凭证？

安全实践:
  □ 使用环境变量？
  □ 使用密钥管理服务？
  □ 配置文件已忽略？
  □ 敏感信息已脱敏？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           敏感信息检测速查表                 │
├─────────────────────────────────────────────┤
│                                             │
│  P0级 (禁止):                               │
│    □ API密钥                                │
│    □ 数据库密码                             │
│    □ 认证令牌                               │
│    □ 加密密钥                               │
│    □ 私钥文件                               │
│                                             │
│  安全替代:                                   │
│    环境变量: os.environ.get("KEY")          │
│    密钥服务: AWS Secrets Manager            │
│    配置外置: .env文件 (gitignore)           │
│                                             │
│  检测模式:                                   │
│    api_key = "xxx" → 阻止                   │
│    password = "xxx" → 阻止                  │
│    secret = "xxx" → 阻止                    │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：零容忍，主动检测，安全替代！
