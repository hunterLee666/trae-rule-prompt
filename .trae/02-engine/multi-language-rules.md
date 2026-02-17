---
name: Multi-Language Project Rules
description: 多语言项目规则 - 跨语言项目处理指南
version: "8.0"
priority: 85
alwaysApply: true
---

# 多语言项目规则

> **"多语言共存，规则统一。"**

## 一、语言检测

### 1.1 自动检测

```yaml
检测方法:
  文件扩展名:
    .py → Python
    .js/.ts → JavaScript/TypeScript
    .java → Java
    .go → Go
    .rs → Rust
    .cpp/.h → C/C++
    .rb → Ruby
    .php → PHP
    .swift → Swift
    .kt → Kotlin
    
  配置文件:
    package.json → Node.js项目
    requirements.txt → Python项目
    pom.xml → Java Maven项目
    Cargo.toml → Rust项目
    go.mod → Go项目
    
  项目结构:
    src/main/java → Java标准结构
    app/ → Rails/Django结构
    lib/ → 多种语言库
```

### 1.2 主语言判定

```yaml
判定规则:
  1. 统计各语言文件数
  2. 计算各语言代码行数
  3. 检查主要配置文件
  4. 综合判定主语言

权重计算:
  文件数权重: 30%
  代码行数权重: 40%
  配置文件权重: 20%
  入口文件权重: 10%
```

---

## 二、语言特定规则

### 2.1 Python

```yaml
代码风格:
  格式化: black, isort
  检查: pylint, flake8, mypy
  导入排序: isort
  
项目结构:
  src/
    __init__.py
    module/
      __init__.py
      core.py
  tests/
    test_module.py
  pyproject.toml
  requirements.txt

依赖管理:
  包管理: pip, poetry, pdm
  虚拟环境: venv, conda
  锁文件: requirements.lock, poetry.lock
```

### 2.2 JavaScript/TypeScript

```yaml
代码风格:
  格式化: prettier
  检查: eslint
  类型检查: tsc
  
项目结构:
  src/
    index.ts
    components/
    utils/
  tests/
    index.test.ts
  package.json
  tsconfig.json

依赖管理:
  包管理: npm, yarn, pnpm
  锁文件: package-lock.json, yarn.lock
```

### 2.3 Java

```yaml
代码风格:
  格式化: google-java-format
  检查: checkstyle, spotbugs
  
项目结构:
  src/
    main/
      java/
        com/example/
    test/
      java/
        com/example/
  pom.xml 或 build.gradle

依赖管理:
  构建工具: maven, gradle
  依赖范围: compile, test, provided
```

### 2.4 Go

```yaml
代码风格:
  格式化: gofmt
  检查: golint, staticcheck
  
项目结构:
  cmd/
    app/
  pkg/
  internal/
  go.mod
  go.sum

依赖管理:
  模块管理: go mod
  代理: GOPROXY
```

### 2.5 Rust

```yaml
代码风格:
  格式化: rustfmt
  检查: clippy
  
项目结构:
  src/
    main.rs
    lib.rs
  Cargo.toml
  Cargo.lock

依赖管理:
  包管理: cargo
  特性: features
```

---

## 三、跨语言交互

### 3.1 API接口

```yaml
REST API:
  格式: OpenAPI/Swagger
  版本: URL路径或Header
  文档: 自动生成

GraphQL:
  Schema定义: .graphql文件
  代码生成: 各语言客户端

gRPC:
  Proto定义: .proto文件
  代码生成: protoc编译器
```

### 3.2 数据交换

```yaml
JSON:
  通用格式
  各语言原生支持
  
Protocol Buffers:
  高效二进制格式
  跨语言序列化

MessagePack:
  紧凑二进制格式
  比JSON更小
```

### 3.3 进程间通信

```yaml
HTTP/gRPC:
  服务间通信
  语言无关

消息队列:
  RabbitMQ, Kafka, Redis
  异步通信

共享内存:
  性能敏感场景
  需要序列化
```

---

## 四、构建与部署

### 4.1 多语言构建

```yaml
构建顺序:
  1. 检测所有语言
  2. 确定依赖关系
  3. 按依赖顺序构建
  4. 生成构建产物

并行构建:
  无依赖模块并行
  有依赖模块串行
  资源限制控制
```

### 4.2 容器化

```yaml
Dockerfile策略:
  多阶段构建:
    - 各语言独立构建阶段
    - 最终镜像只包含运行时
    
  示例结构:
    FROM node:18 AS builder
    # Node.js构建
    
    FROM python:3.11 AS python-builder
    # Python构建
    
    FROM alpine:latest
    # 合并产物
```

### 4.3 CI/CD配置

```yaml
GitHub Actions:
  matrix策略:
    strategy:
      matrix:
        language: [python, node, go]
        
  条件执行:
    paths-filter:
      - 'src/**/*.py'
      - 'src/**/*.ts'
```

---

## 五、测试策略

### 5.1 单元测试

```yaml
各语言测试框架:
  Python: pytest, unittest
  JavaScript: jest, mocha
  Java: JUnit, TestNG
  Go: testing包
  Rust: cargo test

覆盖率要求:
  新代码: ≥80%
  总覆盖率: ≥60%
```

### 5.2 集成测试

```yaml
跨语言测试:
  API契约测试
  端到端测试
  性能测试

测试环境:
  Docker Compose
  Testcontainers
  Mock服务
```

---

## 六、代码审查

### 6.1 审查规则

```yaml
通用规则:
  - 代码风格一致性
  - 无安全漏洞
  - 有足够的测试
  - 文档完整

语言特定规则:
  Python:
    - 类型注解完整
    - 文档字符串规范
    
  JavaScript:
    - TypeScript类型完整
    - 无any类型滥用
    
  Java:
    - 异常处理规范
    - 资源正确关闭
```

### 6.2 审查工具

```yaml
自动化工具:
  SonarQube: 多语言静态分析
  CodeClimate: 代码质量检查
  Snyk: 安全漏洞扫描

集成方式:
  PR检查
  Pre-commit钩子
  CI流水线
```

---

## 七、文档规范

### 7.1 README结构

```markdown
# 项目名称

## 技术栈
- 语言: Python 3.11, TypeScript 5.0
- 框架: FastAPI, React

## 项目结构
## 快速开始
## 开发指南
## API文档
## 部署说明
```

### 7.2 API文档

```yaml
OpenAPI规范:
  版本: 3.0+
  格式: YAML/JSON
  生成: 自动从代码生成

文档位置:
  /docs/api/
  /swagger/
  在线文档服务
```

---

## 八、迁移指南

### 8.1 语言迁移

```yaml
迁移步骤:
  1. 评估现有代码
  2. 确定迁移范围
  3. 创建新语言实现
  4. 并行运行验证
  5. 逐步切换
  6. 清理旧代码

注意事项:
  保持API兼容
  数据格式一致
  测试覆盖完整
```

### 8.2 版本兼容

```yaml
语义版本:
  MAJOR.MINOR.PATCH
  
API版本:
  URL版本: /api/v1/
  Header版本: Accept-Version

废弃策略:
  警告期: 3个月
  文档标注
  迁移指南
```
