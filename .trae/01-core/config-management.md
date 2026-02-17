---
name: Config Management
description: 配置管理规范 - 环境变量、配置文件、密钥管理最佳实践
version: "1.0"
priority: 90
alwaysApply: true
tags: [core, config, environment]
---

# 配置管理规范

## 一、配置管理原则

### 1.1 核心原则

```yaml
十二要素应用配置原则:
  1. 环境分离: 开发、测试、生产环境配置分离
  2. 配置外置: 配置从代码中分离，使用环境变量
  3. 默认安全: 默认配置应该是安全的
  4. 配置验证: 启动时验证配置完整性
  5. 配置文档: 所有配置项都有文档说明

最佳实践:
  - 敏感配置使用密钥管理服务
  - 配置变更需要审计日志
  - 支持配置热更新（如适用）
  - 提供配置验证工具
```

### 1.2 配置分类

```yaml
配置类型:
  
  环境配置:
    特点: 随部署环境变化
    示例: 
      - APP_ENV (development/staging/production)
      - DEBUG (true/false)
      - LOG_LEVEL
    存储: 环境变量
    
  应用配置:
    特点: 应用运行参数
    示例:
      - APP_NAME
      - APP_PORT
      - WORKERS_COUNT
    存储: 配置文件/环境变量
    
  基础设施配置:
    特点: 外部服务连接
    示例:
      - DATABASE_URL
      - REDIS_URL
      - API_ENDPOINTS
    存储: 环境变量/配置文件
    
  安全配置:
    特点: 敏感信息
    示例:
      - SECRET_KEY
      - API_KEY
      - DB_PASSWORD
    存储: 密钥管理服务
```

---

## 二、环境变量规范

### 2.1 命名规范

```yaml
命名规则:
  
  格式: {SERVICE}_{CATEGORY}_{NAME}
  
  示例:
    应用配置:
      - APP_NAME
      - APP_ENV
      - APP_PORT
      - APP_DEBUG
      
    数据库配置:
      - DB_HOST
      - DB_PORT
      - DB_NAME
      - DB_USER
      - DB_PASSWORD
      
    Redis配置:
      - REDIS_HOST
      - REDIS_PORT
      - REDIS_PASSWORD
      
    API配置:
      - API_KEY
      - API_SECRET
      - API_TIMEOUT
      
  禁止:
    - 使用小写
    - 使用特殊字符
    - 过长的名称
    - 含义不清的缩写
```

### 2.2 环境变量模板

```bash
# .env.example - 环境变量模板

# ===== 应用配置 =====
APP_NAME=myapp
APP_ENV=development
APP_DEBUG=true
APP_PORT=8000
APP_HOST=0.0.0.0

# ===== 数据库配置 =====
DB_HOST=localhost
DB_PORT=5432
DB_NAME=myapp_dev
DB_USER=
DB_PASSWORD=

# ===== Redis配置 =====
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0

# ===== 安全配置 =====
SECRET_KEY=
API_KEY=

# ===== 日志配置 =====
LOG_LEVEL=INFO
LOG_FORMAT=json

# ===== 外部服务 =====
EXTERNAL_API_URL=https://api.example.com
EXTERNAL_API_TIMEOUT=30
```

### 2.3 环境变量加载

```python
import os
from typing import Optional
from pydantic import BaseSettings, Field

class Settings(BaseSettings):
    APP_NAME: str = Field(default="myapp", env="APP_NAME")
    APP_ENV: str = Field(default="development", env="APP_ENV")
    APP_DEBUG: bool = Field(default=False, env="APP_DEBUG")
    APP_PORT: int = Field(default=8000, env="APP_PORT")
    
    DB_HOST: str = Field(default="localhost", env="DB_HOST")
    DB_PORT: int = Field(default=5432, env="DB_PORT")
    DB_NAME: str = Field(..., env="DB_NAME")
    DB_USER: str = Field(..., env="DB_USER")
    DB_PASSWORD: str = Field(..., env="DB_PASSWORD")
    
    SECRET_KEY: str = Field(..., env="SECRET_KEY")
    
    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"
        case_sensitive = True

settings = Settings()

def get_database_url() -> str:
    return f"postgresql://{settings.DB_USER}:{settings.DB_PASSWORD}@{settings.DB_HOST}:{settings.DB_PORT}/{settings.DB_NAME}"
```

---

## 三、配置文件规范

### 3.1 配置文件格式

```yaml
推荐格式优先级:
  1. YAML (.yaml, .yml): 人类可读，支持注释
  2. TOML (.toml): Python项目推荐
  3. JSON (.json): 广泛支持，无注释
  4. INI (.ini): 简单配置

选择建议:
  Python项目: TOML (pyproject.toml)
  通用配置: YAML
  API配置: JSON
  简单配置: INI
```

### 3.2 YAML配置示例

```yaml
config.yaml:

app:
  name: ${APP_NAME:myapp}
  env: ${APP_ENV:development}
  debug: ${APP_DEBUG:false}
  port: ${APP_PORT:8000}

database:
  host: ${DB_HOST:localhost}
  port: ${DB_PORT:5432}
  name: ${DB_NAME:myapp}
  user: ${DB_USER}
  password: ${DB_PASSWORD}
  pool_size: 10
  max_overflow: 20

redis:
  host: ${REDIS_HOST:localhost}
  port: ${REDIS_PORT:6379}
  password: ${REDIS_PASSWORD:}
  db: ${REDIS_DB:0}

logging:
  level: ${LOG_LEVEL:INFO}
  format: ${LOG_FORMAT:json}
  file: logs/app.log

external:
  api_url: ${EXTERNAL_API_URL:https://api.example.com}
  timeout: ${EXTERNAL_API_TIMEOUT:30}
  retry: 3
```

### 3.3 配置加载代码

```python
import os
import yaml
from pathlib import Path
from typing import Any, Dict
from string import Template

class ConfigLoader:
    def __init__(self, config_path: str = "config.yaml"):
        self.config_path = Path(config_path)
        self._config: Dict[str, Any] = {}
    
    def load(self) -> Dict[str, Any]:
        with open(self.config_path, 'r', encoding='utf-8') as f:
            content = f.read()
        
        content = self._substitute_env_vars(content)
        self._config = yaml.safe_load(content)
        return self._config
    
    def _substitute_env_vars(self, content: str) -> str:
        def replace_var(match):
            var_spec = match.group(1)
            if ':' in var_spec:
                var_name, default = var_spec.split(':', 1)
                return os.environ.get(var_name, default)
            return os.environ.get(var_spec, match.group(0))
        
        import re
        pattern = r'\$\{([^}]+)\}'
        return re.sub(pattern, replace_var, content)
    
    def get(self, key: str, default: Any = None) -> Any:
        keys = key.split('.')
        value = self._config
        for k in keys:
            if isinstance(value, dict):
                value = value.get(k)
            else:
                return default
        return value if value is not None else default

config_loader = ConfigLoader()
config = config_loader.load()
```

---

## 四、密钥管理规范

### 4.1 密钥管理策略

```yaml
密钥管理层次:
  
  Level 1 - 开发环境:
    方式: .env文件
    要求:
      - .env文件在.gitignore中
      - 提供.env.example模板
      - 定期轮换密钥
      
  Level 2 - 测试环境:
    方式: CI/CD平台密钥管理
    要求:
      - 使用平台Secrets功能
      - 密钥不暴露在日志中
      - 访问权限控制
      
  Level 3 - 生产环境:
    方式: 密钥管理服务
    要求:
      - 使用AWS Secrets Manager / HashiCorp Vault
      - 密钥自动轮换
      - 访问审计日志
      - 最小权限原则
```

### 4.2 AWS Secrets Manager集成

```python
import boto3
import json
from typing import Dict, Any
from functools import lru_cache

class SecretsManager:
    def __init__(self, region: str = "us-east-1"):
        self.client = boto3.client('secretsmanager', region_name=region)
    
    @lru_cache(maxsize=100)
    def get_secret(self, secret_name: str) -> Dict[str, Any]:
        response = self.client.get_secret_value(SecretId=secret_name)
        return json.loads(response['SecretString'])
    
    def get_database_credentials(self) -> Dict[str, str]:
        secret = self.get_secret('myapp/database')
        return {
            'host': secret['host'],
            'port': secret['port'],
            'username': secret['username'],
            'password': secret['password'],
            'database': secret['database']
        }

secrets = SecretsManager()
```

### 4.3 HashiCorp Vault集成

```python
import hvac
from typing import Dict, Any

class VaultClient:
    def __init__(self, url: str, token: str):
        self.client = hvac.Client(url=url, token=token)
    
    def get_secret(self, path: str) -> Dict[str, Any]:
        response = self.client.secrets.kv.v2.read_secret_version(path=path)
        return response['data']['data']
    
    def get_database_url(self) -> str:
        secret = self.get_secret('database')
        return f"postgresql://{secret['user']}:{secret['password']}@{secret['host']}:{secret['port']}/{secret['database']}"

vault = VaultClient(
    url=os.environ.get('VAULT_ADDR'),
    token=os.environ.get('VAULT_TOKEN')
)
```

---

## 五、配置验证

### 5.1 启动时验证

```python
from pydantic import BaseSettings, Field, validator
from typing import Optional
import sys

class AppSettings(BaseSettings):
    APP_NAME: str
    APP_ENV: str = Field(regex="^(development|staging|production)$")
    APP_PORT: int = Field(ge=1, le=65535)
    
    DB_HOST: str
    DB_PORT: int = Field(ge=1, le=65535)
    DB_NAME: str
    DB_USER: str
    DB_PASSWORD: str
    
    SECRET_KEY: str = Field(min_length=32)
    
    @validator('APP_ENV')
    def validate_env(cls, v):
        if v == 'production':
            if not all([
                os.environ.get('SECRET_KEY'),
                os.environ.get('DB_PASSWORD')
            ]):
                raise ValueError('Production environment requires all secrets')
        return v
    
    class Config:
        env_file = ".env"

def validate_config():
    try:
        settings = AppSettings()
        print(f"✅ Configuration validated for {settings.APP_ENV}")
        return settings
    except Exception as e:
        print(f"❌ Configuration validation failed: {e}")
        sys.exit(1)
```

### 5.2 配置检查清单

```yaml
配置检查清单:
  
  必需配置:
    - [ ] APP_NAME 已设置
    - [ ] APP_ENV 已设置且有效
    - [ ] SECRET_KEY 已设置且长度足够
    
  数据库配置:
    - [ ] DB_HOST 已设置
    - [ ] DB_PORT 已设置且有效
    - [ ] DB_NAME 已设置
    - [ ] DB_USER 已设置
    - [ ] DB_PASSWORD 已设置
    
  安全配置:
    - [ ] 生产环境SECRET_KEY不是默认值
    - [ ] 生产环境DEBUG=false
    - [ ] 敏感配置来自密钥管理服务
    
  环境一致性:
    - [ ] 开发环境配置与生产环境分离
    - [ ] 测试环境配置独立
    - [ ] 所有环境使用相同的配置结构
```

---

## 六、多环境配置

### 6.1 环境配置策略

```yaml
环境配置文件:
  
  开发环境:
    文件: .env.development
    特点:
      - DEBUG=true
      - 本地数据库
      - 详细日志
      
  测试环境:
    文件: .env.test
    特点:
      - 测试数据库
      - Mock外部服务
      - 详细日志
      
  预发布环境:
    文件: .env.staging
    特点:
      - 类生产配置
      - 测试数据
      - 生产级别安全
      
  生产环境:
    文件: .env.production (不提交)
    特点:
      - DEBUG=false
      - 生产数据库
      - 密钥管理服务
      - 精简日志
```

### 6.2 环境切换

```python
import os
from typing import TypeVar, Type

T = TypeVar('T', bound=BaseSettings)

def get_settings(settings_class: Type[T]) -> T:
    env = os.environ.get('APP_ENV', 'development')
    env_file = f".env.{env}"
    
    if not os.path.exists(env_file):
        env_file = ".env"
    
    return settings_class(_env_file=env_file)

class DevelopmentSettings(BaseSettings):
    DEBUG: bool = True
    LOG_LEVEL: str = "DEBUG"
    
    class Config:
        env_file = ".env.development"

class ProductionSettings(BaseSettings):
    DEBUG: bool = False
    LOG_LEVEL: str = "WARNING"
    
    class Config:
        env_file = ".env.production"

def get_app_settings():
    env = os.environ.get('APP_ENV', 'development')
    if env == 'production':
        return get_settings(ProductionSettings)
    return get_settings(DevelopmentSettings)
```

---

## 七、配置变更管理

### 7.1 变更流程

```yaml
配置变更流程:
  
  1. 提出变更:
     - 记录变更原因
     - 评估影响范围
     - 确定变更内容
     
  2. 审批:
     - 技术负责人审批
     - 安全团队审批（敏感配置）
     - 运维团队审批
     
  3. 实施:
     - 更新配置模板
     - 更新文档
     - 通知相关方
     
  4. 验证:
     - 测试环境验证
     - 预发布环境验证
     - 生产环境灰度
     
  5. 记录:
     - 更新变更日志
     - 记录审计日志
     - 归档变更记录
```

### 7.2 变更日志

```yaml
CHANGELOG.yaml:

config_changes:
  - date: "2026-02-16"
    version: "1.1.0"
    changes:
      - type: add
        config: EXTERNAL_API_TIMEOUT
        value: 30
        reason: "添加外部API超时配置"
        
      - type: modify
        config: DB_POOL_SIZE
        old_value: 5
        new_value: 10
        reason: "增加数据库连接池大小"
        
      - type: remove
        config: LEGACY_API_KEY
        reason: "废弃旧版API"
        
    approved_by: ["tech-lead", "security-team"]
    deployed_to: ["staging", "production"]
```

---

## 八、配置文档模板

### 8.1 配置文档结构

```markdown
# 配置文档

## 环境变量

| 变量名 | 类型 | 必需 | 默认值 | 说明 |
|-------|------|------|-------|------|
| APP_NAME | string | 是 | - | 应用名称 |
| APP_ENV | string | 是 | development | 运行环境 |
| APP_PORT | int | 否 | 8000 | 服务端口 |
| DB_HOST | string | 是 | - | 数据库主机 |
| DB_PORT | int | 否 | 5432 | 数据库端口 |
| SECRET_KEY | string | 是 | - | 应用密钥(≥32字符) |

## 配置文件

### config.yaml

```yaml
app:
  name: 应用名称
  env: 运行环境
  port: 服务端口
```

## 密钥管理

生产环境敏感配置使用AWS Secrets Manager:
- `myapp/database`: 数据库凭证
- `myapp/api-keys`: API密钥

## 配置示例

### 开发环境

```bash
export APP_ENV=development
export DB_HOST=localhost
export SECRET_KEY=dev-secret-key-32-characters-long
```

### 生产环境

```bash
export APP_ENV=production
export DB_HOST=prod-db.example.com
# SECRET_KEY 从AWS Secrets Manager获取
```
```

---

## 九、版本历史

```yaml
版本记录:
  - v1.0 (2026-02-16): 初始版本
    - 定义配置管理原则
    - 建立环境变量规范
    - 提供密钥管理方案
```
