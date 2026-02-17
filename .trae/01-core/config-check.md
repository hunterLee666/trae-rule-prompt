---
name: Config Check
description: 配置管理检查触发器 - 配置代码生成时强制执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_config_code_generation"
---

# 配置管理检查触发器

> **⚠️ 强制规则：生成配置代码时必须遵循配置管理规范**

---

## 一、触发条件

```yaml
触发场景:
  - 生成配置文件代码
  - 生成环境变量相关代码
  - 生成数据库连接代码
  - 生成API配置代码
  - 生成应用初始化代码

不触发场景:
  - 简单常量定义
  - 用户明确跳过
```

---

## 二、配置管理原则

### 2.1 核心原则

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

### 2.2 配置分类

```yaml
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

## 三、环境变量规范

### 3.1 命名规范

```yaml
命名规则:
  格式: {SERVICE}_{CATEGORY}_{NAME}
  
  示例:
    APP_ENV                    # 应用环境
    APP_DEBUG                  # 调试模式
    APP_PORT                   # 应用端口
    
    DB_HOST                    # 数据库主机
    DB_PORT                    # 数据库端口
    DB_NAME                    # 数据库名称
    DB_USER                    # 数据库用户
    DB_PASSWORD                # 数据库密码
    
    REDIS_HOST                 # Redis主机
    REDIS_PORT                 # Redis端口
    
    API_KEY                    # API密钥
    API_TIMEOUT                # API超时

禁止:
  - 使用小写
  - 使用特殊字符
  - 过长的名称
```

### 3.2 配置加载示例

```python
import os
from dataclasses import dataclass
from typing import Optional

@dataclass
class Config:
    """应用配置"""
    
    # 环境
    ENV: str = "development"
    DEBUG: bool = False
    LOG_LEVEL: str = "INFO"
    
    # 应用
    APP_NAME: str = "myapp"
    APP_PORT: int = 8000
    
    # 数据库
    DB_HOST: str = "localhost"
    DB_PORT: int = 5432
    DB_NAME: str = "mydb"
    DB_USER: str = "user"
    DB_PASSWORD: str = ""
    
    # Redis
    REDIS_HOST: str = "localhost"
    REDIS_PORT: int = 6379
    
    @classmethod
    def from_env(cls) -> "Config":
        """从环境变量加载配置"""
        return cls(
            ENV=os.getenv("APP_ENV", "development"),
            DEBUG=os.getenv("APP_DEBUG", "false").lower() == "true",
            LOG_LEVEL=os.getenv("APP_LOG_LEVEL", "INFO"),
            
            APP_NAME=os.getenv("APP_NAME", "myapp"),
            APP_PORT=int(os.getenv("APP_PORT", "8000")),
            
            DB_HOST=os.getenv("DB_HOST", "localhost"),
            DB_PORT=int(os.getenv("DB_PORT", "5432")),
            DB_NAME=os.getenv("DB_NAME", "mydb"),
            DB_USER=os.getenv("DB_USER", "user"),
            DB_PASSWORD=os.getenv("DB_PASSWORD", ""),
            
            REDIS_HOST=os.getenv("REDIS_HOST", "localhost"),
            REDIS_PORT=int(os.getenv("REDIS_PORT", "6379")),
        )
    
    def validate(self):
        """验证配置"""
        if self.ENV == "production" and self.DEBUG:
            raise ValueError("生产环境不能开启DEBUG模式")
        
        if not self.DB_PASSWORD and self.ENV == "production":
            raise ValueError("生产环境必须设置DB_PASSWORD")

# 使用
config = Config.from_env()
config.validate()
```

---

## 四、配置文件规范

### 4.1 配置文件结构

```yaml
# config.yaml 示例
app:
  name: ${APP_NAME:myapp}
  port: ${APP_PORT:8000}
  debug: ${APP_DEBUG:false}

database:
  host: ${DB_HOST:localhost}
  port: ${DB_PORT:5432}
  name: ${DB_NAME:mydb}
  user: ${DB_USER:user}
  password: ${DB_PASSWORD:}

redis:
  host: ${REDIS_HOST:localhost}
  port: ${REDIS_PORT:6379}

logging:
  level: ${LOG_LEVEL:INFO}
  format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
```

### 4.2 .env文件规范

```bash
# .env.example (提交到git)
APP_ENV=development
APP_DEBUG=true
APP_PORT=8000

DB_HOST=localhost
DB_PORT=5432
DB_NAME=mydb
DB_USER=user
DB_PASSWORD=your_password_here

# .env (不提交到git，添加到.gitignore)
APP_ENV=development
APP_DEBUG=true
DB_PASSWORD=actual_password
```

---

## 五、检查清单

```yaml
配置分离:
  □ 配置与代码分离？
  □ 敏感信息不在代码中？
  □ 环境变量使用正确？

命名规范:
  □ 环境变量命名规范？
  □ 配置项命名清晰？
  □ 有默认值？

安全检查:
  □ 敏感配置使用密钥管理？
  □ .env文件在.gitignore中？
  □ 生产配置安全？

文档完善:
  □ 配置项有文档说明？
  □ 有.env.example模板？
  □ 配置变更记录？
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           配置管理速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  核心原则:                                   │
│    □ 配置外置 (环境变量)                    │
│    □ 环境分离 (dev/staging/prod)           │
│    □ 默认安全                               │
│    □ 配置验证                               │
│                                             │
│  命名规范:                                   │
│    SERVICE_CATEGORY_NAME                    │
│    如: DB_HOST, APP_PORT                    │
│                                             │
│  安全要求:                                   │
│    □ 敏感信息用密钥管理                     │
│    □ .env不提交git                          │
│    □ 生产环境验证                           │
│                                             │
│  文档要求:                                   │
│    □ 有.env.example                         │
│    □ 配置项有说明                           │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```yaml
❌ 错误示例:
  # 硬编码配置
  DB_HOST = "localhost"
  DB_PASSWORD = "admin123"
  DEBUG = True

✅ 正确示例:
  # 使用环境变量
  import os
  DB_HOST = os.getenv("DB_HOST", "localhost")
  DB_PASSWORD = os.getenv("DB_PASSWORD")
  DEBUG = os.getenv("APP_DEBUG", "false").lower() == "true"
  
  # 验证
  if not DB_PASSWORD:
      raise ValueError("DB_PASSWORD环境变量未设置")
```

---

**记住**：配置外置，安全第一，文档完善！
