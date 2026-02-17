---
name: Code Generation Rules
description: 代码生成规则 - 风格指南、命名规范、注释标准
version: "8.3"
priority: 90
alwaysApply: true
---

# 代码生成规则

> **"代码如诗，规范为先。"**

## 一、代码风格

### 1.1 通用风格

```yaml
缩进:
  Python: 4空格
  JavaScript: 2空格
  Go: Tab
  Rust: 4空格
  Java: 4空格

行宽:
  最大长度: 100字符
  推荐: 80字符

空行:
  类之间: 2空行
  方法之间: 1空行
  逻辑块之间: 1空行

括号:
  风格: K&R风格
  示例:
    if (condition) {
      // code
    }
```

### 1.2 语言特定风格

```yaml
Python:
  格式化: black, autopep8
  导入排序: isort
  类型注解: 必须添加
  文档字符串: Google风格

JavaScript:
  格式化: prettier
  分号: 必须添加
  引号: 单引号
  箭头函数: 优先使用

Go:
  格式化: gofmt
  导出: 大写开头
  错误处理: 显式处理
  接口: 小接口

Rust:
  格式化: rustfmt
  错误处理: Result<T, E>
  所有权: 遵循借用规则
  命名: snake_case
```

---

## 二、命名规范

### 2.1 通用命名

```yaml
命名风格:
  snake_case:
    用途: 变量、函数
    示例: user_name, get_user_by_id
    
  camelCase:
    用途: JavaScript变量、方法
    示例: userName, getUserById
    
  PascalCase:
    用途: 类、类型、接口
    示例: UserService, UserDTO
    
  UPPER_SNAKE_CASE:
    用途: 常量
    示例: MAX_RETRY_COUNT, API_BASE_URL
```

### 2.2 特定命名

```yaml
类命名:
  服务类: XxxService
  控制器: XxxController
  仓库: XxxRepository
  工厂: XxxFactory
  构建器: XxxBuilder
  工具类: XxxUtils

方法命名:
  查询: get_xxx, find_xxx, query_xxx
  创建: create_xxx, add_xxx, new_xxx
  更新: update_xxx, modify_xxx, set_xxx
  删除: delete_xxx, remove_xxx
  检查: is_xxx, has_xxx, can_xxx, check_xxx
  转换: to_xxx, convert_xxx, parse_xxx

变量命名:
  集合: xxx_list, xxx_set, xxx_map
  布尔: is_xxx, has_xxx, can_xxx
  计数: xxx_count, num_xxx
  索引: xxx_index, xxx_idx
```

### 2.3 禁止命名

```yaml
禁止使用:
  - 单字母变量 (循环除外)
  - 无意义名称: data, temp, var, value
  - 缩写: usr, pwd, cnt
  - 拼音命名
  - 数字开头
  - 保留字
```

---

## 三、注释规范

### 3.1 注释类型

```yaml
文档注释:
  用途: 模块、类、方法说明
  格式: 
    Python: """docstring"""
    JavaScript: /** JSDoc */
    Go: // Godoc
    Rust: /// Doc comment

行内注释:
  用途: 复杂逻辑说明
  格式: // comment
  位置: 代码上方或行尾

块注释:
  用途: 算法说明、复杂逻辑
  格式: /* comment */
  使用: 尽量少用

TODO注释:
  用途: 待办事项
  格式: // TODO(author): description
  示例: // TODO(john): 需要优化性能
```

### 3.2 注释内容

```yaml
必须注释:
  - 模块功能说明
  - 公开API接口
  - 复杂算法逻辑
  - 业务规则说明
  - 非显而易见的代码

禁止注释:
  - 显而易见的代码
  - 代码的翻译
  - 过时的注释
  - 误导性注释

注释原则:
  - 解释为什么，不是做什么
  - 保持注释与代码同步
  - 代码即文档
```

### 3.3 文档模板

```yaml
Python:
  """
  函数功能描述。
  
  Args:
      param1: 参数1说明
      param2: 参数2说明
      
  Returns:
      返回值说明
      
  Raises:
      Exception: 异常说明
      
  Example:
      >>> func_name(arg1, arg2)
      expected_result
  """

JavaScript:
  /**
   * 函数功能描述
   * @param {Type} param1 - 参数1说明
   * @param {Type} param2 - 参数2说明
   * @returns {Type} 返回值说明
   * @throws {Error} 异常说明
   * @example
   * funcName(arg1, arg2)
   */
```

---

## 四、代码组织

### 4.1 文件结构

```yaml
Python文件结构:
  1. 模块文档字符串
  2. 导入语句
  3. 模块常量
  4. 模块变量
  5. 类定义
  6. 函数定义
  7. 主程序入口

JavaScript文件结构:
  1. 文件头注释
  2. 导入语句
  3. 常量定义
  4. 类型定义
  5. 组件/类定义
  6. 辅助函数
  7. 导出语句
```

### 4.2 导入顺序

```yaml
Python导入顺序:
  1. 标准库
  2. 第三方库
  3. 本地模块
  示例:
    import os
    import sys
    
    import numpy as np
    import pandas as pd
    
    from myproject import utils
    from myproject.core import Base

JavaScript导入顺序:
  1. React核心
  2. 第三方库
  3. 内部组件
  4. 样式文件
  示例:
    import React, { useState } from 'react';
    import { BrowserRouter } from 'react-router-dom';
    import { Button } from 'antd';
    
    import Header from './Header';
    import Footer from './Footer';
    
    import './styles.css';
```

### 4.3 代码分组

```yaml
类内分组:
  1. 静态属性
  2. 实例属性
  3. 初始化方法
  4. 公有方法
  5. 保护方法
  6. 私有方法
  7. 静态方法

模块分组:
  1. 常量
  2. 类型定义
  3. 工具函数
  4. 核心类
  5. 辅助类
```

---

## 五、最佳实践

### 5.1 函数设计

```yaml
函数原则:
  单一职责: 一个函数只做一件事
  参数数量: 不超过5个
  函数长度: 不超过50行
  嵌套深度: 不超过3层

函数模板:
  def function_name(param1: Type, param2: Type) -> ReturnType:
      """函数功能描述"""
      # 1. 参数验证
      # 2. 核心逻辑
      # 3. 结果处理
      # 4. 返回结果
      return result
```

### 5.2 类设计

```yaml
类原则:
  单一职责: 一个类只有一个变化原因
  开闭原则: 对扩展开放，对修改关闭
  接口隔离: 不应强迫依赖不使用的方法
  依赖倒置: 依赖抽象，不依赖具体

类模板:
  class ClassName:
      """类功能描述"""
      
      # 类常量
      CONSTANT = "value"
      
      def __init__(self, param: Type):
          """初始化"""
          self._param = param
      
      @property
      def param(self) -> Type:
          """属性访问器"""
          return self._param
      
      def public_method(self) -> ReturnType:
          """公有方法"""
          return self._private_method()
      
      def _private_method(self) -> ReturnType:
          """私有方法"""
          pass
```

### 5.3 错误处理

```yaml
错误处理原则:
  - 尽早验证输入
  - 使用具体异常类型
  - 提供有意义的错误信息
  - 记录错误上下文

错误处理模板:
  try:
      result = risky_operation()
  except SpecificError as e:
      logger.error(f"Operation failed: {e}", extra=context)
      raise CustomError(f"Failed to process: {e}") from e
  finally:
      cleanup()
```
