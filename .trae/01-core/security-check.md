---
name: Security Check
description: 安全检查触发器 - 代码生成时强制执行
version: "1.0"
priority: 95
alwaysApply: true
trigger: "during_code_generation"
---

# 安全检查触发器

> **⚠️ 强制规则：生成代码时必须执行安全检查**

---

## 一、触发条件

```yaml
触发场景:
  - 生成新代码时
  - 修改代码时
  - 创建配置文件时
  - 编写数据库连接时
  - 处理用户输入时
  - 实现API接口时

必须检查:
  - 敏感信息是否硬编码
  - 用户输入是否验证
  - SQL是否参数化
  - 文件路径是否安全
```

---

## 二、敏感信息检测

### 2.1 禁止硬编码的敏感信息

```yaml
P0 - 严重敏感信息 (绝对禁止):
  - API密钥: API_KEY, API_SECRET
  - 数据库密码: DB_PASSWORD, DATABASE_URL
  - 认证令牌: AUTH_TOKEN, JWT_SECRET
  - 加密密钥: ENCRYPTION_KEY, SECRET_KEY
  - 私钥内容: RSA_PRIVATE_KEY, SSH_KEY

P1 - 高敏感信息 (禁止硬编码):
  - 用户密码
  - 访问令牌: ACCESS_TOKEN, REFRESH_TOKEN
  - OAuth密钥: OAUTH_CLIENT_SECRET
  - 云服务凭证: AWS_SECRET_KEY, GCP_API_KEY

P2 - 中敏感信息 (建议不硬编码):
  - 内部IP地址
  - 非标准端口号
  - 数据库用户名
  - 内部邮箱地址
```

### 2.2 检测模式

```yaml
硬编码检测:
  
  直接赋值:
    ❌ 错误: API_KEY = "sk-xxx"
    ✅ 正确: API_KEY = os.environ.get("API_KEY")
  
  字符串拼接:
    ❌ 错误: password = "admin" + "123"
    ✅ 正确: password = os.environ.get("DB_PASSWORD")
  
  配置字典:
    ❌ 错误: config = {"password": "xxx"}
    ✅ 正确: config = {"password": os.environ.get("DB_PASSWORD")}
  
  URL连接:
    ❌ 错误: mysql://user:password@localhost/db
    ✅ 正确: mysql://user:${DB_PASSWORD}@localhost/db
```

### 2.3 正确的替代方案

```yaml
环境变量:
  Python:
    import os
    API_KEY = os.environ.get("API_KEY")
    DB_PASSWORD = os.environ.get("DB_PASSWORD", "default_value")
  
  Node.js:
    const apiKey = process.env.API_KEY;
    const dbPassword = process.env.DB_PASSWORD;

配置文件:
  Python:
    from config import settings
    API_KEY = settings.API_KEY
  
  Node.js:
    const config = require('./config');
    const apiKey = config.apiKey;

密钥管理服务:
  - AWS Secrets Manager
  - Azure Key Vault
  - HashiCorp Vault
  - 本地.env文件 (不提交到git)
```

---

## 三、输入验证检查

### 3.1 必须验证的输入

```yaml
用户输入:
  - 表单数据
  - URL参数
  - HTTP头
  - Cookie值
  - 文件上传

外部数据:
  - API响应
  - 数据库查询结果
  - 文件内容
  - 环境变量
```

### 3.2 验证规则

```yaml
类型验证:
  ❌ 错误: user_id = request.args.get("id")
  ✅ 正确: user_id = int(request.args.get("id", 0))

范围验证:
  ❌ 错误: page = request.args.get("page")
  ✅ 正确: page = max(1, int(request.args.get("page", 1)))

长度验证:
  ❌ 错误: name = request.form.get("name")
  ✅ 正确: name = request.form.get("name", "")[:100]

格式验证:
  ❌ 错误: email = request.form.get("email")
  ✅ 正确: 
    import re
    email = request.form.get("email", "")
    if not re.match(r"^[^@]+@[^@]+\.[^@]+$", email):
        raise ValueError("Invalid email format")
```

---

## 四、SQL注入防护

### 4.1 参数化查询

```yaml
❌ 错误示例 (SQL注入风险):
  user_id = request.args.get("id")
  query = f"SELECT * FROM users WHERE id = {user_id}"
  
✅ 正确示例 (参数化):
  user_id = request.args.get("id")
  query = "SELECT * FROM users WHERE id = ?"
  cursor.execute(query, (user_id,))

Python示例:
  # 使用ORM
  user = User.query.filter_by(id=user_id).first()
  
  # 使用参数化
  cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

Node.js示例:
  // 使用参数化
  const query = 'SELECT * FROM users WHERE id = $1';
  const result = await db.query(query, [userId]);
```

---

## 五、文件操作安全

### 5.1 路径安全

```yaml
❌ 错误示例 (路径遍历风险):
  filename = request.args.get("file")
  with open(f"/data/{filename}") as f:
      content = f.read()

✅ 正确示例:
  import os
  filename = request.args.get("file", "")
  # 防止路径遍历
  safe_filename = os.path.basename(filename)
  # 限制目录
  base_dir = "/data"
  file_path = os.path.join(base_dir, safe_filename)
  # 验证路径在允许范围内
  if not os.path.abspath(file_path).startswith(os.path.abspath(base_dir)):
      raise ValueError("Invalid file path")
```

### 5.2 文件上传安全

```yaml
检查项:
  □ 文件类型验证 (MIME类型 + 扩展名)
  □ 文件大小限制
  □ 文件名清理
  □ 存储路径安全
  □ 病毒扫描 (如适用)
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           安全检查速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  敏感信息:                                   │
│    □ 无硬编码密钥/密码？                     │
│    □ 使用环境变量？                          │
│    □ 配置文件不提交？                        │
│                                             │
│  输入验证:                                   │
│    □ 类型验证？                              │
│    □ 范围验证？                              │
│    □ 长度验证？                              │
│    □ 格式验证？                              │
│                                             │
│  SQL安全:                                   │
│    □ 使用参数化查询？                        │
│    □ 不拼接SQL字符串？                       │
│                                             │
│  文件安全:                                   │
│    □ 路径遍历防护？                          │
│    □ 文件类型验证？                          │
│                                             │
│  口诀: 敏感不硬码，输入必验证，SQL要参数化   │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例与修正

```yaml
❌ 违规代码:
  DB_PASSWORD = "admin123"
  user_id = request.args.get("id")
  query = f"SELECT * FROM users WHERE id = {user_id}"
  filename = request.args.get("file")
  with open(f"/data/{filename}") as f:
      data = f.read()

✅ 修正代码:
  DB_PASSWORD = os.environ.get("DB_PASSWORD")
  user_id = int(request.args.get("id", 0))
  query = "SELECT * FROM users WHERE id = ?"
  cursor.execute(query, (user_id,))
  filename = os.path.basename(request.args.get("file", ""))
  file_path = os.path.join("/data", filename)
  if os.path.abspath(file_path).startswith("/data"):
      with open(file_path) as f:
          data = f.read()
```

---

## 八、安全提示输出

```markdown
## 🔒 安全检查提示

在生成代码时，已自动应用以下安全措施：

✅ 敏感信息: 使用环境变量替代硬编码
✅ 输入验证: 添加类型和范围验证
✅ SQL安全: 使用参数化查询
✅ 文件安全: 添加路径遍历防护

⚠️ 注意事项:
- 请确保设置环境变量: DB_PASSWORD, API_KEY
- 请将 .env 文件添加到 .gitignore
- 生产环境请使用密钥管理服务
```

---

**记住**：安全是代码质量的第一道防线，任何代码生成都必须通过安全检查！
