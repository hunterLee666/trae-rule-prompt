---
name: Code Style Check
description: 代码风格检查触发器 - 代码生成时强制执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "during_code_generation"
---

# 代码风格检查触发器

> **⚠️ 强制规则：生成代码时必须遵循风格规范**

---

## 一、触发条件

```yaml
触发场景:
  - 生成新代码时
  - 修改现有代码时
  - 创建新文件时
  - 重构代码时

不触发场景:
  - 读取代码
  - 分析代码
  - 用户明确指定风格
```

---

## 二、命名规范

### 2.1 Python命名规范

```yaml
变量命名:
  规范: snake_case
  示例:
    ✅ user_name, total_count, api_key
    ❌ userName, TotalCount, APIKey

函数命名:
  规范: snake_case
  示例:
    ✅ get_user_info(), calculate_total()
    ❌ getUserInfo(), CalculateTotal()

类命名:
  规范: PascalCase
  示例:
    ✅ UserManager, DataProcessor
    ❌ userManager, data_processor

常量命名:
  规范: UPPER_SNAKE_CASE
  示例:
    ✅ MAX_RETRY, DEFAULT_TIMEOUT
    ❌ maxRetry, default_timeout

私有属性:
  规范: _leading_underscore
  示例:
    ✅ _internal_state, _cache
    ❌ internalState, cache_
```

### 2.2 JavaScript/TypeScript命名规范

```yaml
变量命名:
  规范: camelCase
  示例:
    ✅ userName, totalCount, apiKey
    ❌ user_name, TotalCount

函数命名:
  规范: camelCase
  示例:
    ✅ getUserInfo(), calculateTotal()
    ❌ get_user_info(), CalculateTotal()

类命名:
  规范: PascalCase
  示例:
    ✅ UserManager, DataProcessor
    ❌ userManager, data_processor

常量命名:
  规范: UPPER_SNAKE_CASE 或 camelCase
  示例:
    ✅ MAX_RETRY, maxRetry
    ❌ max_retry

接口命名:
  规范: PascalCase (I前缀可选)
  示例:
    ✅ IUserService, UserService
    ❌ iUserService, user_service
```

---

## 三、代码格式规范

### 3.1 Python格式规范

```yaml
缩进:
  规范: 4空格
  示例:
    ✅ def func():
            pass
    ❌ def func():
          pass  # 2空格

行宽:
  规范: 88字符 (Black默认)
  说明: 超过行宽应换行

空行:
  类之间: 2行
  方法之间: 1行
  函数内逻辑块: 1行

导入顺序:
  1. 标准库
  2. 第三方库
  3. 本地模块
  示例:
    import os
    import sys
    from typing import Optional, List
    
    import numpy as np
    import pandas as pd
    
    from my_module import MyClass
```

### 3.2 JavaScript/TypeScript格式规范

```yaml
缩进:
  规范: 2空格
  示例:
    ✅ function func() {
          return;
        }
    ❌ function func() {
            return;  // 4空格
        }

分号:
  规范: 使用分号 (推荐)
  示例:
    ✅ const x = 1;
    ❌ const x = 1

引号:
  规范: 单引号 (推荐)
  示例:
    ✅ const name = 'test'
    ❌ const name = "test"

大括号:
  规范: 同行开括号
  示例:
    ✅ if (condition) {
          // ...
        }
    ❌ if (condition)
        {
          // ...
        }
```

---

## 四、类型注解规范

### 4.1 Python类型注解

```yaml
函数类型注解:
  ✅ 正确示例:
    def get_user(user_id: int) -> Optional[User]:
        """获取用户信息"""
        pass
    
    def process_data(
        data: List[Dict[str, Any]],
        config: Optional[Config] = None
    ) -> Result:
        pass

  ❌ 错误示例:
    def get_user(user_id):
        pass

变量类型注解:
  ✅ 正确示例:
    users: List[User] = []
    config: Dict[str, Any] = {}
    count: int = 0

  ❌ 错误示例:
    users = []
    config = {}
```

### 4.2 TypeScript类型注解

```yaml
接口定义:
  ✅ 正确示例:
    interface User {
      id: number;
      name: string;
      email?: string;
    }

函数类型:
  ✅ 正确示例:
    function getUser(userId: number): User | null {
      // ...
    }
    
    const processData = (
      data: User[],
      config?: Config
    ): Result => {
      // ...
    }
```

---

## 五、注释规范

### 5.1 文档字符串

```yaml
Python文档字符串:
  ✅ 正确示例:
    def calculate_total(items: List[Item]) -> float:
        """计算商品总价
        
        Args:
            items: 商品列表
            
        Returns:
            商品总价
            
        Raises:
            ValueError: 当商品列表为空时
        """
        pass

  ❌ 错误示例:
    def calculate_total(items):
        # 计算总价
        pass
```

### 5.2 行内注释

```yaml
注释规范:
  ✅ 正确示例:
    # 检查用户权限
    if user.has_permission('admin'):
        result = process_admin_action()
    
    # TODO: 添加缓存机制
    data = fetch_from_database()

  ❌ 错误示例:
    if user.has_permission('admin'):  # check permission
        result = process_admin_action()
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           代码风格速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  命名规范:                                   │
│    Python: snake_case (变量/函数)           │
│            PascalCase (类)                  │
│    JS/TS: camelCase (变量/函数)             │
│           PascalCase (类)                   │
│                                             │
│  格式规范:                                   │
│    Python: 4空格缩进, 88字符行宽            │
│    JS/TS: 2空格缩进, 单引号, 分号           │
│                                             │
│  类型注解:                                   │
│    □ 函数参数有类型？                       │
│    □ 返回值有类型？                         │
│    □ 复杂变量有类型？                       │
│                                             │
│  注释规范:                                   │
│    □ 函数有文档字符串？                     │
│    □ 复杂逻辑有注释？                       │
│    □ TODO/FIXME已标注？                     │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```yaml
❌ 错误示例:
  def getUserInfo(userId):
      # get user
      result=db.query(userId)
      return result

✅ 正确示例:
  def get_user_info(user_id: int) -> Optional[Dict[str, Any]]:
      """获取用户信息
      
      Args:
          user_id: 用户ID
          
      Returns:
          用户信息字典，未找到返回None
      """
      result = db.query(user_id)
      return result
```

---

**记住**：代码风格一致，团队协作更顺畅！
