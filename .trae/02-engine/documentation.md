---
name: Documentation Rules
description: 文档生成规则 - 文档类型、结构规范、风格指南
version: "8.5"
priority: 85
alwaysApply: true
---

# 文档生成规则

> **"文档如镜，代码如真。"**

## 一、文档类型

### 1.1 项目文档

```yaml
README.md:
  用途: 项目入口文档
  内容: 项目介绍、快速开始、使用说明
  必须: 是

CHANGELOG.md:
  用途: 版本变更记录
  内容: 版本号、变更日期、变更内容
  必须: 是

CONTRIBUTING.md:
  用途: 贡献指南
  内容: 开发环境、代码规范、提交流程
  必须: 开源项目

LICENSE:
  用途: 许可证声明
  内容: 许可证类型、版权声明
  必须: 是
```

### 1.2 API文档

```yaml
OpenAPI/Swagger:
  用途: REST API文档
  内容: 端点、参数、响应、示例
  格式: YAML/JSON

GraphQL Schema:
  用途: GraphQL API文档
  内容: 类型、查询、变更、订阅
  格式: SDL

gRPC Proto:
  用途: gRPC API文档
  内容: 服务定义、消息类型
  格式: .proto
```

### 1.3 代码文档

```yaml
内联文档:
  模块文档: 文件头注释
  类文档: 类说明注释
  方法文档: 方法说明注释
  参数文档: 参数说明

外部文档:
  架构文档: 系统架构说明
  设计文档: 详细设计说明
  部署文档: 部署配置说明
  运维文档: 运维操作说明
```

---

## 二、文档结构

### 2.1 README结构

```yaml
标准结构:
  1. 项目标题和徽章
  2. 项目简介
  3. 功能特性
  4. 快速开始
     - 环境要求
     - 安装步骤
     - 基本使用
  5. 详细用法
  6. 配置说明
  7. API文档
  8. 示例代码
  9. 常见问题
  10. 贡献指南
  11. 许可证
  12. 致谢
```

### 2.2 API文档结构

```yaml
端点文档结构:
  1. 端点名称
  2. 端点描述
  3. 请求方法
  4. 请求路径
  5. 请求参数
     - 路径参数
     - 查询参数
     - 请求体
  6. 请求示例
  7. 响应格式
  8. 响应示例
  9. 错误码说明
  10. 注意事项
```

### 2.3 变更日志结构

```yaml
CHANGELOG格式:
  ## [版本号] - YYYY-MM-DD
  
  ### 新增 (Added)
  - 新增功能描述
  
  ### 变更 (Changed)
  - 变更内容描述
  
  ### 修复 (Fixed)
  - 修复问题描述
  
  ### 移除 (Removed)
  - 移除功能描述
  
  ### 弃用 (Deprecated)
  - 弃用功能描述

版本号规则:
  主版本: 不兼容的API变更
  次版本: 向后兼容的功能新增
  修订版: 向后兼容的问题修复
```

---

## 三、文档风格

### 3.1 Markdown规范

```yaml
标题:
  一级标题: 项目名称，仅一个
  二级标题: 主要章节
  三级标题: 子章节
  四级标题: 细节说明

列表:
  无序列表: 使用 - 或 *
  有序列表: 使用 1. 2. 3.
  嵌套列表: 缩进2空格

代码块:
  行内代码: 使用反引号
  代码块: 使用三个反引号
  语言标识: 指定语言高亮

链接:
  行内链接: [文本](URL)
  引用链接: [文本][引用名]
  图片: ![替代文本](URL)
```

### 3.2 写作风格

```yaml
语言风格:
  简洁明了: 避免冗长表述
  准确具体: 使用具体数字和示例
  一致性: 术语和格式保持一致

时态使用:
  功能描述: 使用现在时
  变更记录: 使用过去时
  计划功能: 使用将来时

人称使用:
  用户文档: 使用"你"
  技术文档: 使用被动语态
  注释: 使用第三人称
```

### 3.3 格式规范

```yaml
段落:
  长度: 每段不超过5行
  空行: 段落间空一行
  首行: 不缩进

代码:
  示例: 提供可运行示例
  注释: 解释关键逻辑
  长度: 不超过30行

表格:
  表头: 必须有表头
  对齐: 使用对齐符号
  简洁: 避免过宽表格
```

---

## 四、文档模板

### 4.1 README模板

```markdown
# 项目名称

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)]()

简短的项目描述。

## 功能特性

- 特性1
- 特性2
- 特性3

## 快速开始

### 环境要求

- Python 3.8+
- Node.js 16+

### 安装

```bash
pip install package-name
```

### 基本使用

```python
from package import main

main.run()
```

## 文档

详细文档请查看 [文档站点](链接)

## 贡献

欢迎贡献！请查看 [贡献指南](CONTRIBUTING.md)

## 许可证

[MIT License](LICENSE)
```

### 4.2 API文档模板

```markdown
## 端点名称

端点描述。

### 请求

`METHOD /api/v1/resource`

#### 参数

| 名称 | 类型 | 位置 | 必需 | 描述 |
|-----|------|------|------|------|
| id | string | path | 是 | 资源ID |
| name | string | query | 否 | 过滤名称 |

#### 请求示例

```bash
curl -X GET "https://api.example.com/api/v1/resource/123"
```

### 响应

#### 成功响应 (200)

```json
{
  "id": "123",
  "name": "示例",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### 错误响应

| 状态码 | 描述 |
|-------|------|
| 400 | 请求参数错误 |
| 401 | 未授权 |
| 404 | 资源不存在 |
| 500 | 服务器错误 |
```

### 4.3 函数文档模板

```python
def function_name(param1: Type, param2: Type) -> ReturnType:
    """
    函数功能简述。
    
    详细描述函数的功能和行为。
    
    Args:
        param1: 参数1说明
        param2: 参数2说明
        
    Returns:
        返回值说明
        
    Raises:
        ValueError: 参数无效时抛出
        RuntimeError: 运行时错误
        
    Example:
        >>> result = function_name("value1", "value2")
        >>> print(result)
        expected_output
    """
    pass
```

---

## 五、文档更新

### 5.1 更新时机

```yaml
必须更新:
  - 新增功能
  - API变更
  - 配置变更
  - 依赖变更
  - 修复重要Bug

建议更新:
  - 性能优化
  - 代码重构
  - 文档改进
```

### 5.2 版本控制

```yaml
文档版本:
  与代码版本同步
  使用Git管理
  记录变更历史

多版本管理:
  维护多个版本文档
  标注版本适用范围
  提供版本切换功能
```

### 5.3 审查机制

```yaml
文档审查:
  技术准确性: 代码示例可运行
  内容完整性: 覆盖所有功能
  语言流畅性: 表达清晰准确
  格式规范性: 符合格式要求

审查流程:
  1. 作者自查
  2. 同行评审
  3. 技术审核
  4. 发布上线
```

---

## 六、文档工具

### 6.1 文档生成工具

```yaml
Python:
  Sphinx: 官方文档工具
  MkDocs: Markdown文档
  pdoc: 自动API文档

JavaScript:
  JSDoc: JavaScript文档
  TypeDoc: TypeScript文档
  Docusaurus: 文档站点

Go:
  Godoc: 官方文档工具
  Swagger: API文档

通用:
  Swagger/OpenAPI: REST API文档
  GraphQL Docs: GraphQL文档
  PlantUML: 图表文档
```

### 6.2 文档托管

```yaml
静态托管:
  GitHub Pages: 免费托管
  GitBook: 文档平台
  Read the Docs: 文档托管

自建平台:
  Confluence: 企业Wiki
  Notion: 协作文档
  Docusaurus: 静态站点
```
