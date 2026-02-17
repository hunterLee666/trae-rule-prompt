---
name: Testing Strategy Rules
description: 测试策略规则 - 测试类型、覆盖率、最佳实践
version: "8.4"
priority: 90
alwaysApply: true
---

# 测试策略规则

> **"测试先行，质量保障。"**

## 一、测试类型

### 1.1 测试金字塔

```yaml
端到端测试 (E2E):
  比例: 10%
  范围: 完整用户流程
  速度: 慢 (分钟级)
  成本: 高
  目的: 验证系统整体功能

集成测试:
  比例: 20%
  范围: 模块间交互
  速度: 中 (秒级)
  成本: 中
  目的: 验证模块集成

单元测试:
  比例: 70%
  范围: 单个函数/类
  速度: 快 (毫秒级)
  成本: 低
  目的: 验证代码逻辑
```

### 1.2 测试分类

```yaml
功能测试:
  单元测试: 测试单个组件
  集成测试: 测试组件集成
  端到端测试: 测试完整流程
  回归测试: 验证修复效果

非功能测试:
  性能测试: 响应时间、吞吐量
  负载测试: 并发处理能力
  安全测试: 漏洞扫描、渗透测试
  兼容性测试: 多环境兼容

专项测试:
  边界测试: 边界条件处理
  异常测试: 异常场景处理
  压力测试: 极限条件表现
  恢复测试: 故障恢复能力
```

---

## 二、测试覆盖率

### 2.1 覆盖率类型

```yaml
代码覆盖率:
  行覆盖率: 执行的代码行比例
  分支覆盖率: 执行的分支比例
  函数覆盖率: 调用的函数比例
  语句覆盖率: 执行的语句比例

业务覆盖率:
  功能覆盖率: 测试的功能比例
  场景覆盖率: 测试的场景比例
  接口覆盖率: 测试的API比例
  异常覆盖率: 测试的异常比例
```

### 2.2 覆盖率要求

```yaml
最低要求:
  新代码覆盖率: ≥80%
  整体覆盖率: ≥70%
  核心模块覆盖率: ≥90%
  工具模块覆盖率: ≥60%

分级要求:
  Level 1 (基础):
    行覆盖率: ≥60%
    分支覆盖率: ≥50%
    
  Level 2 (标准):
    行覆盖率: ≥80%
    分支覆盖率: ≥70%
    
  Level 3 (严格):
    行覆盖率: ≥90%
    分支覆盖率: ≥85%
    
  Level 4 (完整):
    行覆盖率: ≥95%
    分支覆盖率: ≥90%
```

---

## 三、测试命名

### 3.1 命名规范

```yaml
测试文件命名:
  Python: test_<module>.py
  JavaScript: <module>.test.js
  Go: <module>_test.go
  Rust: tests/<module>.rs

测试函数命名:
  格式: test_<功能>_<场景>_<预期结果>
  示例:
    test_login_with_valid_credentials_should_succeed
    test_login_with_invalid_password_should_fail
    test_login_with_empty_username_should_throw_error

测试类命名:
  格式: Test<功能>
  示例:
    TestUserLogin
    TestPaymentProcessor
    TestOrderService
```

### 3.2 描述性命名

```yaml
Given-When-Then格式:
  Given: 初始条件
  When: 执行操作
  Then: 预期结果
  
示例:
  test_given_valid_user_when_login_then_return_token
  test_given_expired_token_when_refresh_then_throw_error
  test_given_insufficient_balance_when_pay_then_decline
```

---

## 四、单元测试

### 4.1 单元测试原则

```yaml
FIRST原则:
  Fast: 测试要快
  Independent: 测试独立
  Repeatable: 测试可重复
  Self-validating: 自动验证
  Timely: 及时编写

AAA模式:
  Arrange: 准备测试数据
  Act: 执行被测代码
  Assert: 验证结果
  
示例:
  def test_add_numbers():
      # Arrange
      calculator = Calculator()
      
      # Act
      result = calculator.add(2, 3)
      
      # Assert
      assert result == 5
```

### 4.2 单元测试模板

```yaml
Python模板:
  import pytest
  from module import ClassToTest
  
  class TestClassToTest:
      @pytest.fixture
      def setup(self):
          self.instance = ClassToTest()
      
      def test_method_scenario_expected(self, setup):
          # Arrange
          input_data = "test"
          
          # Act
          result = setup.method(input_data)
          
          # Assert
          assert result == "expected"

JavaScript模板:
  describe('ClassToTest', () => {
    let instance;
    
    beforeEach(() => {
      instance = new ClassToTest();
    });
    
    it('should return expected when method called with valid input', () => {
      // Arrange
      const input = 'test';
      
      // Act
      const result = instance.method(input);
      
      // Assert
      expect(result).toBe('expected');
    });
  });
```

---

## 五、集成测试

### 5.1 集成测试策略

```yaml
测试范围:
  数据库集成: 测试数据访问层
  API集成: 测试外部API调用
  服务集成: 测试服务间通信
  消息队列集成: 测试消息处理

测试环境:
  使用测试数据库
  使用Mock外部服务
  使用测试配置
  隔离测试数据
```

### 5.2 集成测试模板

```yaml
数据库集成测试:
  import pytest
  from sqlalchemy import create_engine
  from myapp.models import Base, User
  
  @pytest.fixture
  def db_session():
      engine = create_engine('sqlite:///:memory:')
      Base.metadata.create_all(engine)
      session = Session(engine)
      yield session
      session.close()
  
  def test_create_user(db_session):
      user = User(name='test')
      db_session.add(user)
      db_session.commit()
      
      saved = db_session.query(User).first()
      assert saved.name == 'test'

API集成测试:
  import pytest
  from fastapi.testclient import TestClient
  from myapp.main import app
  
  @pytest.fixture
  def client():
      return TestClient(app)
  
  def test_get_users(client):
      response = client.get('/api/users')
      assert response.status_code == 200
      assert len(response.json()) > 0
```

---

## 六、端到端测试

### 6.1 E2E测试策略

```yaml
测试工具:
  Web: Cypress, Playwright, Selenium
  API: Postman, Newman
  Mobile: Appium, Detox

测试场景:
  用户注册流程
  用户登录流程
  核心业务流程
  支付流程
  数据导出流程

测试数据:
  使用测试账号
  使用测试数据集
  数据隔离
  测试后清理
```

### 6.2 E2E测试模板

```yaml
Cypress示例:
  describe('User Login Flow', () => {
    beforeEach(() => {
      cy.visit('/login');
    });
    
    it('should login successfully with valid credentials', () => {
      cy.get('[data-cy=username]').type('testuser');
      cy.get('[data-cy=password]').type('password123');
      cy.get('[data-cy=submit]').click();
      
      cy.url().should('include', '/dashboard');
      cy.get('[data-cy=welcome]').should('contain', 'Welcome');
    });
    
    it('should show error with invalid credentials', () => {
      cy.get('[data-cy=username]').type('wronguser');
      cy.get('[data-cy=password]').type('wrongpass');
      cy.get('[data-cy=submit]').click();
      
      cy.get('[data-cy=error]').should('be.visible');
    });
  });
```

---

## 七、Mock策略

### 7.1 Mock原则

```yaml
何时Mock:
  外部服务调用
  数据库操作 (单元测试)
  时间相关操作
  随机数生成
  文件系统操作

何时不Mock:
  简单数据转换
  纯函数计算
  集成测试场景
```

### 7.2 Mock工具

```yaml
Python:
  unittest.mock: 标准库
  pytest-mock: pytest插件
  responses: HTTP Mock
  freezegun: 时间Mock

JavaScript:
  jest.mock: Jest内置
  nock: HTTP Mock
  sinon: 通用Mock

Go:
  testify/mock: Mock框架
  gomock: 官方Mock
  httptest: HTTP Mock
```

### 7.3 Mock示例

```yaml
Python Mock:
  from unittest.mock import Mock, patch
  
  def test_external_api():
      with patch('requests.get') as mock_get:
          mock_get.return_value.json.return_value = {'key': 'value'}
          
          result = fetch_data()
          
          assert result == {'key': 'value'}
          mock_get.assert_called_once()

JavaScript Mock:
  jest.mock('axios');
  
  test('fetches data successfully', async () => {
    axios.get.mockResolvedValue({ data: { key: 'value' } });
    
    const result = await fetchData();
    
    expect(result).toEqual({ key: 'value' });
    expect(axios.get).toHaveBeenCalledTimes(1);
  });
```

---

## 八、测试报告模板

```markdown
## 🧪 测试报告

### 测试统计
| 类型 | 总数 | 通过 | 失败 | 覆盖率 |
|-----|------|------|------|-------|
| 单元测试 | X | X | X | X% |
| 集成测试 | X | X | X | X% |
| E2E测试 | X | X | X | X% |

### 失败详情
[失败测试的详细信息]

### 覆盖率分析
[覆盖率详情和改进建议]
```

---

## 九、测试最佳实践

### 9.1 测试原则

```yaml
测试独立性:
  - 每个测试独立运行
  - 不依赖执行顺序
  - 不共享可变状态

测试可读性:
  - 清晰的测试名称
  - 简单的测试逻辑
  - 有意义的断言消息

测试可维护性:
  - 避免重复代码
  - 使用测试工具函数
  - 保持测试简单
```

### 8.2 常见反模式

```yaml
反模式:
  测试私有方法:
    问题: 测试实现细节
    正确: 测试公开接口
    
  过度Mock:
    问题: 测试失去意义
    正确: 只Mock外部依赖
    
  断言不足:
    问题: 测试无验证
    正确: 每个测试有断言
    
  测试依赖:
    问题: 测试顺序敏感
    正确: 测试完全独立
```
