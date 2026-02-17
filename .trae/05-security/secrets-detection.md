---
name: Secrets Detection
description: 敏感信息检测规则 - 防止硬编码密钥、密码等敏感信息泄露
version: "1.0"
priority: 95
alwaysApply: true
tags: [security, secrets, detection]
---

# 敏感信息检测规则

## 一、检测原则

### 1.1 核心原则

```yaml
原则:
  1. 零容忍: 任何硬编码敏感信息都是安全漏洞
  2. 主动检测: 在代码生成阶段就阻止敏感信息硬编码
  3. 最佳实践: 提供安全替代方案
  4. 持续提醒: 在输出中提示安全最佳实践
```

### 1.2 敏感信息分类

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

## 二、检测模式

### 2.1 硬编码密钥检测

```yaml
检测模式:
  直接赋值:
    模式: "API_KEY = \"sk-xxx\""
    风险: P0
    处理: 阻止并提示使用环境变量
    
  字符串拼接:
    模式: "password = \"admin\" + \"123\""
    风险: P0
    处理: 阻止并提示安全存储
    
  配置字典:
    模式: "config = {\"password\": \"xxx\"}"
    风险: P0
    处理: 阻止并提示配置文件外置

正则检测模式:
  API密钥:
    - "sk-[a-zA-Z0-9]{20,}"
    - "api[_-]?key[_-]?=.*['\"][a-zA-Z0-9]{16,}['\"]"
    
  数据库连接:
    - "mysql://[^:]+:[^@]+@"
    - "postgres://[^:]+:[^@]+@"
    - "mongodb://[^:]+:[^@]+@"
    
  JWT密钥:
    - "jwt[_-]?secret[_-]?=.*['\"][a-zA-Z0-9]{16,}['\"]"
    - "SECRET_KEY.*=.*['\"][a-zA-Z0-9]{16,}['\"]"
    
  AWS密钥:
    - "AKIA[0-9A-Z]{16}"
    - "aws[_-]?secret[_-]?access[_-]?key"
```

### 2.2 代码生成检测

```yaml
生成代码时的检测:
  
  检测时机:
    - 生成配置代码时
    - 生成数据库连接代码时
    - 生成API调用代码时
    - 生成认证代码时
    
  检测动作:
    发现硬编码:
      动作: 立即停止
      替代: 使用环境变量或配置文件
      提示: 显示安全警告
      
    发现占位符:
      动作: 允许继续
      要求: 明确标注需要替换
      格式: "YOUR_API_KEY_HERE"
```

---

## 三、安全替代方案

### 3.1 环境变量方案

```python
import os
from typing import Optional

def get_api_key() -> str:
    key = os.environ.get("API_KEY")
    if not key:
        raise ValueError(
            "API_KEY environment variable not set. "
            "Please set it using: export API_KEY=your_key_here"
        )
    return key

def get_database_url() -> str:
    return os.environ.get(
        "DATABASE_URL",
        "postgresql://localhost:5432/dev_db"
    )
```

### 3.2 配置文件方案

```yaml
config.yaml:
  database:
    host: ${DB_HOST:localhost}
    port: ${DB_PORT:5432}
    name: ${DB_NAME:myapp}
    
  api:
    key: ${API_KEY}
    timeout: 30

Python加载:
  from pydantic import BaseSettings
  
  class Settings(BaseSettings):
      api_key: str
      db_host: str = "localhost"
      db_port: int = 5432
      
      class Config:
          env_file = ".env"
  
  settings = Settings()
```

### 3.3 密钥管理服务方案

```yaml
AWS Secrets Manager:
  import boto3
  import json
  
  def get_secret(secret_name: str) -> dict:
      client = boto3.client('secretsmanager')
      response = client.get_secret_value(SecretId=secret_name)
      return json.loads(response['SecretString'])

HashiCorp Vault:
  import hvac
  
  client = hvac.Client(url='https://vault.example.com')
  secret = client.secrets.kv.v2.read_secret_version(path='myapp')
```

---

## 四、输出规范

### 4.1 代码生成时的安全提示

```yaml
必须包含的提示:
  
  敏感信息处理:
    格式: |
      ⚠️ 安全提示:
      - 请勿将API密钥、密码等敏感信息硬编码
      - 建议使用环境变量: export API_KEY=your_key
      - 或使用配置文件: config.yaml (已添加到.gitignore)
      - 生产环境建议使用密钥管理服务 (AWS KMS, HashiCorp Vault)
  
  示例代码:
    要求: |
      # 正确示例
      API_KEY = os.environ.get("API_KEY")
      
      # 错误示例 (请勿使用)
      # API_KEY = "sk-xxxxx"  # ❌ 硬编码密钥
```

### 4.2 配置文件模板

```yaml
.env.example:
  API_KEY=your_api_key_here
  DATABASE_URL=postgresql://user:password@localhost:5432/db
  JWT_SECRET=your_jwt_secret_here
  AWS_ACCESS_KEY_ID=your_aws_key
  AWS_SECRET_ACCESS_KEY=your_aws_secret

.gitignore:
  .env
  .env.local
  .env.*.local
  config.local.yaml
  secrets.yaml
  *.pem
  *.key
```

---

## 五、检测清单

### 5.1 代码审查清单

```yaml
敏感信息检测清单:
  
  源代码检查:
    - [ ] 无硬编码API密钥
    - [ ] 无硬编码数据库密码
    - [ ] 无硬编码JWT密钥
    - [ ] 无硬编码用户密码
    - [ ] 无明文存储敏感数据
    
  配置文件检查:
    - [ ] 敏感配置使用环境变量
    - [ ] .env文件已添加.gitignore
    - [ ] 提供.env.example模板
    - [ ] 无敏感信息提交到版本控制
    
  日志检查:
    - [ ] 日志不输出敏感信息
    - [ ] 错误信息不暴露敏感数据
    - [ ] 调试信息已脱敏
    
  文档检查:
    - [ ] README无敏感信息
    - [ ] 示例代码使用占位符
    - [ ] 注释无敏感信息
```

### 5.2 自动检测脚本

```python
import re
from pathlib import Path
from typing import List, Tuple

SENSITIVE_PATTERNS = [
    (r'API_KEY\s*=\s*["\'][^"\']{10,}["\']', "API密钥硬编码"),
    (r'PASSWORD\s*=\s*["\'][^"\']+["\']', "密码硬编码"),
    (r'SECRET_KEY\s*=\s*["\'][^"\']{10,}["\']', "密钥硬编码"),
    (r'["\']sk-[a-zA-Z0-9]{20,}["\']', "OpenAI API密钥"),
    (r'["\']AKIA[0-9A-Z]{16}["\']', "AWS访问密钥"),
    (r'mysql://[^:]+:[^@]+@', "MySQL连接字符串"),
    (r'postgresql://[^:]+:[^@]+@', "PostgreSQL连接字符串"),
]

def scan_file(file_path: Path) -> List[Tuple[int, str, str]]:
    issues = []
    content = file_path.read_text()
    
    for pattern, description in SENSITIVE_PATTERNS:
        for match in re.finditer(pattern, content, re.IGNORECASE):
            line_num = content[:match.start()].count('\n') + 1
            issues.append((line_num, description, match.group()))
    
    return issues

def scan_directory(directory: Path) -> dict:
    results = {}
    for file_path in directory.rglob("*.py"):
        issues = scan_file(file_path)
        if issues:
            results[str(file_path)] = issues
    return results
```

---

## 六、违规处理

### 6.1 处理级别

```yaml
处理级别:
  
  P0级别违规:
    动作: 立即阻止代码生成
    提示: 显示严重安全警告
    要求: 必须使用安全替代方案
    
  P1级别违规:
    动作: 警告并建议修改
    提示: 显示安全建议
    要求: 推荐使用安全替代方案
    
  P2级别违规:
    动作: 提示注意
    建议: 考虑使用配置外部化
    要求: 可选改进
    
  P3级别违规:
    动作: 记录日志
    建议: 建议最佳实践
    要求: 可选改进
```

### 6.2 输出模板

```yaml
发现敏感信息时的输出:
  
  格式: |
    🚨 安全警告: 检测到敏感信息硬编码
    
    检测内容: {detected_content}
    风险级别: {risk_level}
    位置: {file_path}:{line_number}
    
    ⚠️ 请勿将敏感信息硬编码在源代码中！
    
    推荐替代方案:
    1. 环境变量: export {var_name}=your_value
    2. 配置文件: 使用.env文件 (已添加到.gitignore)
    3. 密钥管理: 使用AWS Secrets Manager或HashiCorp Vault
    
    示例代码:
    ```python
    import os
    {var_name} = os.environ.get("{var_name}")
    ```
```

---

## 七、最佳实践

### 7.1 开发环境

```yaml
本地开发:
  1. 使用.env文件存储敏感信息
  2. 确保.env在.gitignore中
  3. 提供.env.example作为模板
  4. 使用python-dotenv加载配置

示例:
  .env:
    API_KEY=sk-test-xxxxx
    DB_PASSWORD=local_dev_password
  
  .gitignore:
    .env
    .env.local
```

### 7.2 生产环境

```yaml
生产部署:
  1. 使用密钥管理服务
  2. 启用密钥轮换
  3. 实施最小权限原则
  4. 启用审计日志

推荐服务:
  AWS: Secrets Manager + KMS
  GCP: Secret Manager
  Azure: Key Vault
  自建: HashiCorp Vault
```

### 7.3 CI/CD环境

```yaml
CI/CD配置:
  1. 使用CI/CD平台的密钥管理
  2. 不在日志中输出敏感信息
  3. 使用掩码处理敏感输出

GitHub Actions:
  env:
    API_KEY: ${{ secrets.API_KEY }}
  
GitLab CI:
  variables:
    API_KEY:
      value: ""
      masked: true
```

---

## 八、版本历史

```yaml
版本记录:
  - v1.0 (2026-02-16): 初始版本
    - 定义敏感信息分类
    - 建立检测模式
    - 提供安全替代方案
```
