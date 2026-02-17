---
name: Code Quality Rules
description: 代码质量规则 - 代码风格、最佳实践、审查清单、测试模式
version: "10.0"
priority: 85
alwaysApply: true
---

# 代码质量规则

> **"代码是写给人看的，顺便能在机器上运行。"**

## 一、代码风格指南

### 1.1 Python代码风格

```yaml
命名规范:
  变量: snake_case
    示例: user_name, total_count
    
  函数: snake_case
    示例: calculate_total, get_user_info
    
  类: PascalCase
    示例: UserManager, DataProcessor
    
  常量: UPPER_SNAKE_CASE
    示例: MAX_RETRY, DEFAULT_TIMEOUT
    
  私有属性: _leading_underscore
    示例: _internal_state, _cache
    
  保护属性: _leading_underscore
    示例: _protected_method

缩进和空格:
  缩进: 4空格
  行宽: 88字符(Black默认)
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

### 1.2 类型注解规范

```yaml
基本类型:
  def greet(name: str) -> str:
      return f"Hello, {name}"

可选类型:
  from typing import Optional
  
  def find_user(user_id: int) -> Optional[User]:
      ...

集合类型:
  from typing import List, Dict, Set, Tuple
  
  def process_items(items: List[str]) -> Dict[str, int]:
      ...

函数类型:
  from typing import Callable
  
  def apply_func(data: List[int], func: Callable[[int], int]) -> List[int]:
      ...

泛型类型:
  from typing import TypeVar, Generic
  
  T = TypeVar('T')
  
  class Container(Generic[T]):
      def __init__(self, value: T):
          self.value = value

Union类型:
  from typing import Union
  
  def process(value: Union[str, int]) -> str:
      ...
```

### 1.3 文档字符串规范

```yaml
Google风格:
  def calculate_average(numbers: List[float]) -> float:
      """计算数字列表的平均值。
      
      Args:
          numbers: 数字列表，不能为空。
          
      Returns:
          平均值，浮点数。
          
      Raises:
          ValueError: 如果列表为空。
          
      Examples:
          >>> calculate_average([1, 2, 3])
          2.0
      """
      if not numbers:
          raise ValueError("列表不能为空")
      return sum(numbers) / len(numbers)

NumPy风格:
  def calculate_average(numbers):
      """
      计算数字列表的平均值。
      
      Parameters
      ----------
      numbers : List[float]
          数字列表，不能为空。
          
      Returns
      -------
      float
          平均值。
          
      Raises
      ------
      ValueError
          如果列表为空。
      """
      ...

类文档:
  class UserManager:
      """用户管理器，负责用户的增删改查操作。
      
      Attributes:
          users: 用户字典，键为用户ID，值为用户对象。
          cache: 用户缓存，用于加速查询。
          
      Example:
          >>> manager = UserManager()
          >>> manager.add_user(User(id=1, name="Alice"))
          >>> user = manager.get_user(1)
      """
```

---

## 二、代码最佳实践

### 2.1 函数设计

```yaml
单一职责:
  原则: 一个函数只做一件事
  示例:
    不推荐:
      def process_user(user):
          validate(user)
          save(user)
          notify(user)
    
    推荐:
      def validate_user(user): ...
      def save_user(user): ...
      def notify_user(user): ...

参数设计:
  原则: 参数数量不超过5个
  解决: 使用配置对象或**kwargs
  
  不推荐:
      def create_user(name, email, age, address, phone, company):
          ...
  
  推荐:
      @dataclass
      class UserConfig:
          name: str
          email: str
          age: int = 0
          address: str = ""
          phone: str = ""
          company: str = ""
      
      def create_user(config: UserConfig):
          ...

返回值设计:
  原则: 返回值类型一致
  示例:
    不推荐:
      def find_user(id):
          if not id:
              return None
          return User(name="test")
    
    推荐:
      def find_user(id: int) -> Optional[User]:
          if not id:
              return None
          return User(name="test")
```

### 2.2 类设计

```yaml
类大小:
  原则: 类方法数量不超过10个
  解决: 拆分为多个小类

继承设计:
  原则: 优先组合而非继承
  示例:
    不推荐:
      class UserAdmin(User):
          def admin_action(self): ...
    
    推荐:
      class User:
          def __init__(self, role: Role):
              self.role = role
      
      class AdminRole:
          def admin_action(self): ...

属性封装:
  原则: 使用@property控制访问
  示例:
      class Circle:
          def __init__(self, radius: float):
              self._radius = radius
          
          @property
          def radius(self) -> float:
              return self._radius
          
          @radius.setter
          def radius(self, value: float):
              if value < 0:
                  raise ValueError("半径不能为负")
              self._radius = value
```

### 2.3 异常处理

```yaml
异常类型:
  使用具体异常: 避免使用Exception
  示例:
    不推荐:
      try:
          ...
      except Exception:
          pass
    
    推荐:
      try:
          ...
      except (ValueError, KeyError) as e:
          logger.error(f"处理失败: {e}")
          raise

异常信息:
  原则: 提供有意义的错误信息
  示例:
    不推荐:
      raise ValueError("错误")
    
    推荐:
      raise ValueError(f"用户ID {user_id} 不存在")

异常链:
  原则: 保留原始异常信息
  示例:
      try:
          result = risky_operation()
      except IOError as e:
          raise DataProcessingError("数据处理失败") from e
```

---

## 三、代码审查清单

### 3.1 功能正确性

```yaml
逻辑检查:
  - [ ] 逻辑是否正确
  - [ ] 边界条件是否处理
  - [ ] 空值/None是否处理
  - [ ] 类型是否正确

功能完整性:
  - [ ] 是否满足所有需求
  - [ ] 是否有遗漏的功能
  - [ ] 是否有冗余的功能
```

### 3.2 代码质量

```yaml
可读性:
  - [ ] 命名是否清晰
  - [ ] 注释是否充分
  - [ ] 代码结构是否清晰
  - [ ] 是否有重复代码

可维护性:
  - [ ] 函数是否过长
  - [ ] 类是否过大
  - [ ] 依赖是否清晰
  - [ ] 是否易于修改

性能:
  - [ ] 是否有不必要的循环
  - [ ] 是否有内存泄漏风险
  - [ ] 是否有性能瓶颈
```

### 3.3 安全性

```yaml
输入验证:
  - [ ] 是否验证用户输入
  - [ ] 是否防止SQL注入
  - [ ] 是否防止XSS攻击

敏感信息:
  - [ ] 是否暴露敏感信息
  - [ ] 是否安全存储密码
  - [ ] 是否安全处理日志

权限控制:
  - [ ] 是否有适当的权限检查
  - [ ] 是否防止越权访问
```

### 3.4 测试覆盖

```yaml
单元测试:
  - [ ] 是否有单元测试
  - [ ] 测试覆盖率是否足够
  - [ ] 边界情况是否测试

集成测试:
  - [ ] 是否有集成测试
  - [ ] 接口是否测试
  - [ ] 异常流程是否测试
```

---

## 四、代码度量标准

### 4.1 复杂度度量

```yaml
圈复杂度:
  定义: 独立路径数量
  标准:
    1-10: 简单，可接受
    11-20: 中等，需要关注
    21+: 复杂，需要重构

认知复杂度:
  定义: 理解代码的难度
  标准:
    0-5: 简单
    6-10: 中等
    11+: 复杂

嵌套深度:
  定义: 代码块嵌套层数
  标准:
    1-2: 良好
    3: 可接受
    4+: 需要重构
```

### 4.2 质量度量

```yaml
代码重复率:
  标准: < 5%
  工具: pylint, sonarqube

测试覆盖率:
  标准: > 80%
  工具: pytest-cov, coverage

文档覆盖率:
  标准: > 90%
  工具: interrogate

类型覆盖率:
  标准: > 80%
  工具: mypy --strict
```

---

## 五、代码重构指南

### 5.1 重构时机

```yaml
代码异味:
  重复代码: 提取公共方法
  过长函数: 拆分为小函数
  过大类: 拆分为小类
  过长参数列表: 使用配置对象
  发散式变化: 提取类
  霰弹式修改: 移动方法
  依恋情结: 移动方法
  数据泥团: 提取类
```

### 5.2 重构技术

```yaml
提取方法:
  场景: 重复代码或过长函数
  步骤:
    1. 识别需要提取的代码块
    2. 创建新方法
    3. 将代码移入新方法
    4. 替换原代码为方法调用

提取类:
  场景: 过大类或职责不清
  步骤:
    1. 识别相关的方法和属性
    2. 创建新类
    3. 移动方法和属性
    4. 更新引用

内联方法:
  场景: 方法体简单或无意义
  步骤:
    1. 找到方法的所有调用
    2. 将方法体替换调用
    3. 删除方法

重命名:
  场景: 名称不清晰
  步骤:
    1. 检查所有使用处
    2. 更新名称
    3. 更新所有引用
```

---

## 六、测试模式指南

### 6.1 测试类型

```yaml
单元测试:
  目标: 测试单个函数/方法
  特点: 快速、隔离、可重复
  示例:
    def test_calculate_total():
        result = calculate_total([1, 2, 3])
        assert result == 6

集成测试:
  目标: 测试模块间交互
  特点: 多组件协作
  示例:
    def test_user_service_with_db():
        service = UserService(db)
        user = service.create_user("test")
        assert user.name == "test"

端到端测试:
  目标: 测试完整流程
  特点: 模拟真实使用
  示例:
    def test_login_flow():
        response = client.post("/login", data={...})
        assert response.status_code == 200

边界测试:
  目标: 测试边界条件
  特点: 极端值、空值
  示例:
    @pytest.mark.parametrize("value,expected", [
        (0, 0),
        (-1, None),
        (MAX_INT, MAX_INT),
        (None, None),
    ])
    def test_boundary(value, expected):
        assert process(value) == expected
```

### 6.2 测试模式

```yaml
AAA模式:
  结构: Arrange → Act → Assert
  示例:
    def test_user_creation():
        # Arrange
        user_data = {"name": "test"}
        
        # Act
        user = create_user(user_data)
        
        # Assert
        assert user.name == "test"

Given-When-Then模式:
  结构: Given(给定) → When(当) → Then(那么)
  示例:
    def test_withdraw():
        # Given: 账户有100元
        account = Account(balance=100)
        
        # When: 取款50元
        account.withdraw(50)
        
        # Then: 余额为50元
        assert account.balance == 50

参数化测试:
  目的: 用同一测试逻辑测试多组数据
  示例:
    @pytest.mark.parametrize("input,expected", [
        ("hello", "HELLO"),
        ("World", "WORLD"),
        ("", ""),
    ])
    def test_uppercase(input, expected):
        assert uppercase(input) == expected

Fixture模式:
  目的: 复用测试准备代码
  示例:
    @pytest.fixture
    def user_service():
        db = TestDatabase()
        return UserService(db)
    
    def test_create_user(user_service):
        user = user_service.create("test")
        assert user is not None
```

### 6.3 Mock最佳实践

```yaml
Mock场景:
  - 外部API调用
  - 数据库操作
  - 文件系统操作
  - 时间相关操作

Mock工具选择:
  Python: unittest.mock, pytest-mock
  示例:
    from unittest.mock import Mock, patch
    
    def test_external_api():
        with patch('requests.get') as mock_get:
            mock_get.return_value.json.return_value = {"data": "test"}
            result = fetch_data()
            assert result == {"data": "test"}

Mock模式:
  返回值Mock:
    mock_func.return_value = "expected"
  
  副作用Mock:
    mock_func.side_effect = [1, 2, 3]
  
  异常Mock:
    mock_func.side_effect = ValueError("error")
  
  属性Mock:
    mock_obj.attribute = "value"

Mock验证:
  调用验证:
    mock_func.assert_called_once()
    mock_func.assert_called_with(arg1, arg2)
  
  调用次数:
    assert mock_func.call_count == 3
```

### 6.4 测试覆盖率

```yaml
覆盖率类型:
  行覆盖率: 执行的代码行比例
  分支覆盖率: 执行的分支比例
  函数覆盖率: 调用的函数比例
  
覆盖率目标:
  核心代码: > 90%
  业务代码: > 80%
  工具代码: > 70%

覆盖率工具:
  pytest-cov:
    pytest --cov=src --cov-report=html
    
  coverage:
    coverage run -m pytest
    coverage report

覆盖率报告分析:
  未覆盖代码:
    - 识别未测试的分支
    - 分析是否需要测试
    - 添加缺失的测试
```

---

## 七、代码审查详细清单

### 7.1 设计审查

```yaml
架构设计:
  - [ ] 模块划分是否合理
  - [ ] 依赖关系是否清晰
  - [ ] 是否符合SOLID原则
  - [ ] 是否有过度设计

接口设计:
  - [ ] 接口是否简洁
  - [ ] 参数是否合理
  - [ ] 返回值是否一致
  - [ ] 错误处理是否完善

数据设计:
  - [ ] 数据结构是否合理
  - [ ] 数据流是否清晰
  - [ ] 数据验证是否完整
  - [ ] 数据安全是否考虑
```

### 7.2 实现审查

```yaml
代码逻辑:
  - [ ] 逻辑是否正确
  - [ ] 是否有死代码
  - [ ] 是否有冗余逻辑
  - [ ] 是否有潜在Bug

错误处理:
  - [ ] 异常是否捕获
  - [ ] 错误信息是否清晰
  - [ ] 是否有恢复机制
  - [ ] 是否记录日志

资源管理:
  - [ ] 资源是否释放
  - [ ] 连接是否关闭
  - [ ] 内存是否泄漏
  - [ ] 文件是否关闭
```

### 7.3 性能审查

```yaml
算法效率:
  - [ ] 时间复杂度是否合理
  - [ ] 空间复杂度是否合理
  - [ ] 是否有不必要的计算
  - [ ] 是否有优化空间

I/O效率:
  - [ ] 是否有过多I/O操作
  - [ ] 是否有批量处理
  - [ ] 是否有缓存机制
  - [ ] 是否有异步处理

并发处理:
  - [ ] 是否线程安全
  - [ ] 是否有竞态条件
  - [ ] 是否有死锁风险
  - [ ] 是否有资源竞争
```

### 7.4 文档审查

```yaml
代码注释:
  - [ ] 复杂逻辑是否有注释
  - [ ] 注释是否准确
  - [ ] 注释是否更新
  - [ ] 是否有TODO/FIXME

API文档:
  - [ ] 是否有文档字符串
  - [ ] 参数是否描述
  - [ ] 返回值是否描述
  - [ ] 异常是否描述

使用示例:
  - [ ] 是否有使用示例
  - [ ] 示例是否可运行
  - [ ] 示例是否覆盖典型场景
```
