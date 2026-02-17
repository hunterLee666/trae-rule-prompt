---
name: Documentation Check
description: 文档生成检查触发器 - 文档生成时建议执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "during_documentation_generation"
---

# 文档生成检查触发器

> **⚠️ 建议：生成文档时遵循文档规范**

---

## 一、触发条件

```yaml
触发场景:
  - 生成README文档
  - 生成API文档
  - 生成代码注释
  - 生成CHANGELOG
  - 用户明确要求文档

不触发场景:
  - 简单注释
  - 用户明确跳过
```

---

## 二、文档类型

### 2.1 项目文档

```yaml
README.md:
  用途: 项目入口文档
  必须: 是
  内容:
    - 项目名称和简介
    - 快速开始指南
    - 安装说明
    - 基本用法
    - 配置说明
    - 贡献指南链接
  模板:
    # 项目名称
    
    简短描述
    
    ## 安装
    ## 快速开始
    ## 配置
    ## 文档
    ## 贡献
    ## 许可证

CHANGELOG.md:
  用途: 版本变更记录
  必须: 是
  格式: 遵循 Keep a Changelog
  模板:
    # Changelog
    
    ## [Unreleased]
    
    ## [1.0.0] - 2024-01-01
    ### Added
    - 新功能
    
    ### Changed
    - 变更内容
    
    ### Fixed
    - 修复内容

CONTRIBUTING.md:
  用途: 贡献指南
  必须: 开源项目
  内容:
    - 开发环境设置
    - 代码规范
    - 提交规范
    - PR流程
```

### 2.2 API文档

```yaml
OpenAPI/Swagger:
  用途: REST API文档
  格式: YAML/JSON
  内容:
    - API端点
    - 请求参数
    - 响应格式
    - 示例代码
  示例:
    openapi: 3.0.0
    info:
      title: API名称
      version: 1.0.0
    paths:
      /users:
        get:
          summary: 获取用户列表
          responses:
            '200':
              description: 成功

GraphQL Schema:
  用途: GraphQL API文档
  格式: SDL
  内容:
    - 类型定义
    - 查询
    - 变更
    - 订阅
```

### 2.3 代码文档

```yaml
Python:
  格式: Google风格 / NumPy风格
  示例:
    def calculate_total(items: list[dict]) -> float:
        """计算订单总金额。
        
        Args:
            items: 订单项列表，每个项包含price和quantity
        
        Returns:
            订单总金额
        
        Raises:
            ValueError: 当items为空时
        
        Example:
            >>> calculate_total([{"price": 10, "quantity": 2}])
            20.0
        """

JavaScript/TypeScript:
  格式: JSDoc / TSDoc
  示例:
    /**
     * 计算订单总金额
     * @param items - 订单项列表
     * @returns 订单总金额
     * @throws {Error} 当items为空时
     * @example
     * calculateTotal([{price: 10, quantity: 2}]) // 20
     */

Go:
  格式: Go标准注释
  示例:
    // CalculateTotal 计算订单总金额。
    // items参数为订单项列表，返回订单总金额。
    func CalculateTotal(items []Item) float64
```

---

## 三、文档结构规范

```yaml
标题层级:
  - 一级标题: 项目名称
  - 二级标题: 主要章节
  - 三级标题: 子章节
  - 四级标题: 详细内容

代码块:
  - 指定语言
  - 添加注释
  - 提供示例

链接:
  - 使用相对路径引用项目内文件
  - 使用绝对路径引用外部资源

格式:
  - 使用列表组织内容
  - 使用表格展示数据
  - 使用引用块强调重要信息
```

---

## 四、文档检查清单

```yaml
README检查:
  □ 项目名称和简介清晰？
  □ 安装说明完整？
  □ 快速开始可执行？
  □ 配置说明详细？
  □ 许可证信息存在？

API文档检查:
  □ 端点描述清晰？
  □ 参数说明完整？
  □ 响应格式明确？
  □ 示例代码可运行？

代码注释检查:
  □ 函数用途说明？
  □ 参数类型和说明？
  □ 返回值说明？
  □ 异常说明？
  □ 使用示例？
```

---

## 五、快速检查卡

```
┌─────────────────────────────────────────────┐
│           文档生成速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  必备文档:                                   │
│    □ README.md                              │
│    □ CHANGELOG.md                           │
│    □ LICENSE                                │
│                                             │
│  README结构:                                 │
│    □ 项目简介                               │
│    □ 安装说明                               │
│    □ 快速开始                               │
│    □ 配置说明                               │
│                                             │
│  代码注释:                                   │
│    □ 函数用途                               │
│    □ 参数说明                               │
│    □ 返回值                                 │
│    □ 示例                                   │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 六、违规示例

```yaml
❌ 错误示例:
  # 缺少文档的函数
  def process(data):
      return data.transform()

✅ 正确示例:
  def process(data: dict) -> dict:
      """处理输入数据并返回转换结果。
      
      Args:
          data: 输入数据字典，包含原始数据
      
      Returns:
          转换后的数据字典
      
      Raises:
          ValueError: 当data为空或格式不正确时
      
      Example:
          >>> process({"value": 10})
          {"value": 20}
      """
      return data.transform()
```

---

**记住**：好文档让代码更易用！
