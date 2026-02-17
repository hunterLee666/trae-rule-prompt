---
name: Testing Strategy Check
description: 测试策略检查触发器 - 编写测试时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_test_writing"
---

# 测试策略检查触发器

> **⚠️ 建议：编写测试时应遵循测试策略**

---

## 一、触发条件

```yaml
触发场景:
  - 编写单元测试
  - 编写集成测试
  - 编写E2E测试
  - 代码实现后

不触发场景:
  - 用户明确跳过
```

---

## 二、测试金字塔

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

---

## 三、测试分类

### 3.1 功能测试

```yaml
单元测试:
  目标: 测试单个组件
  原则:
    - 快速执行
    - 独立运行
    - 可重复
    - 自我验证
  示例:
    - 测试函数返回值
    - 测试边界条件
    - 测试异常处理

集成测试:
  目标: 测试组件集成
  原则:
    - 测试模块交互
    - 使用真实依赖
    - 验证数据流
  示例:
    - 测试API调用
    - 测试数据库操作
    - 测试服务集成

端到端测试:
  目标: 测试完整流程
  原则:
    - 模拟真实用户
    - 测试关键路径
    - 验证业务流程
  示例:
    - 用户注册流程
    - 订单处理流程
    - 支付流程
```

### 3.2 非功能测试

```yaml
性能测试:
  响应时间测试:
    目标: < 500ms (P95)
    工具: JMeter, Locust
  
  吞吐量测试:
    目标: > 100 req/s
    工具: wrk, ab
  
  负载测试:
    目标: 支持1000并发
    工具: k6, Gatling

安全测试:
  漏洞扫描:
    工具: OWASP ZAP, SonarQube
  
  渗透测试:
    工具: Burp Suite, Nmap
  
  依赖检查:
    工具: npm audit, safety
```

---

## 四、测试覆盖率

### 4.1 覆盖率类型

```yaml
代码覆盖率:
  行覆盖率:
    目标: > 80%
    警告: 60-80%
    错误: < 60%
  
  分支覆盖率:
    目标: > 70%
    警告: 50-70%
    错误: < 50%
  
  函数覆盖率:
    目标: > 90%
    警告: 70-90%
    错误: < 70%

业务覆盖率:
  功能覆盖率:
    目标: 100%核心功能
  
  场景覆盖率:
    目标: 覆盖主要场景
  
  异常覆盖率:
    目标: 覆盖已知异常
```

### 4.2 覆盖率检查

```yaml
检查规则:
  新代码:
    要求: 覆盖率 > 80%
    阻断: 低于阈值阻止合并
  
  修改代码:
    要求: 不能降低覆盖率
    阻断: 覆盖率下降阻止合并
  
  核心模块:
    要求: 覆盖率 > 90%
    阻断: 低于阈值阻止合并
```

---

## 五、测试最佳实践

### 5.1 AAA模式

```yaml
AAA模式:
  Arrange (准备):
    - 设置测试数据
    - 配置测试环境
    - 准备测试依赖
  
  Act (执行):
    - 执行被测代码
    - 调用目标方法
    - 触发测试事件
  
  Assert (断言):
    - 验证执行结果
    - 检查状态变化
    - 确认副作用

示例:
  def test_user_creation():
      # Arrange
      user_data = {"name": "test", "email": "test@example.com"}
      
      # Act
      user = create_user(user_data)
      
      # Assert
      assert user.name == "test"
      assert user.email == "test@example.com"
```

### 5.2 测试命名

```yaml
命名规范:
  格式: test_<method>_<scenario>_<expected>
  
  示例:
    - test_create_user_with_valid_data_returns_user
    - test_create_user_with_invalid_email_raises_error
    - test_login_with_wrong_password_fails
  
  描述性:
    - 清晰说明测试内容
    - 包含输入条件
    - 包含预期结果
```

---

## 六、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Optional, Callable
from enum import Enum

class TestType(Enum):
    """测试类型"""
    UNIT = "unit"
    INTEGRATION = "integration"
    E2E = "e2e"
    PERFORMANCE = "performance"
    SECURITY = "security"

@dataclass
class TestResult:
    """测试结果"""
    name: str
    passed: bool
    duration: float
    error: Optional[str] = None

@dataclass
class CoverageReport:
    """覆盖率报告"""
    line_coverage: float
    branch_coverage: float
    function_coverage: float
    uncovered_lines: List[int]

class TestPyramidValidator:
    """测试金字塔验证器"""
    
    def __init__(self):
        self.tests: Dict[TestType, List[str]] = {
            TestType.UNIT: [],
            TestType.INTEGRATION: [],
            TestType.E2E: [],
        }
    
    def add_test(self, test_type: TestType, test_name: str):
        """添加测试"""
        self.tests[test_type].append(test_name)
    
    def validate_pyramid(self) -> Dict:
        """验证测试金字塔"""
        total = sum(len(tests) for tests in self.tests.values())
        
        if total == 0:
            return {"valid": False, "reason": "没有测试"}
        
        unit_ratio = len(self.tests[TestType.UNIT]) / total
        integration_ratio = len(self.tests[TestType.INTEGRATION]) / total
        e2e_ratio = len(self.tests[TestType.E2E]) / total
        
        issues = []
        
        if unit_ratio < 0.5:
            issues.append(f"单元测试比例过低: {unit_ratio:.1%} < 50%")
        
        if e2e_ratio > 0.2:
            issues.append(f"E2E测试比例过高: {e2e_ratio:.1%} > 20%")
        
        return {
            "valid": len(issues) == 0,
            "unit_ratio": unit_ratio,
            "integration_ratio": integration_ratio,
            "e2e_ratio": e2e_ratio,
            "issues": issues
        }

class CoverageChecker:
    """覆盖率检查器"""
    
    THRESHOLDS = {
        "line": {"target": 80, "warning": 60},
        "branch": {"target": 70, "warning": 50},
        "function": {"target": 90, "warning": 70}
    }
    
    def check_coverage(self, report: CoverageReport) -> Dict:
        """检查覆盖率"""
        results = {}
        
        results["line"] = self._check_threshold(
            report.line_coverage, "line"
        )
        results["branch"] = self._check_threshold(
            report.branch_coverage, "branch"
        )
        results["function"] = self._check_threshold(
            report.function_coverage, "function"
        )
        
        results["passed"] = all(
            r["status"] != "error" for r in results.values()
        )
        
        return results
    
    def _check_threshold(self, value: float, 
                        coverage_type: str) -> Dict:
        """检查阈值"""
        threshold = self.THRESHOLDS[coverage_type]
        
        if value >= threshold["target"]:
            return {"status": "pass", "value": value}
        elif value >= threshold["warning"]:
            return {"status": "warning", "value": value}
        else:
            return {"status": "error", "value": value}

class TestGenerator:
    """测试生成器"""
    
    def generate_unit_test(self, func_name: str,
                          params: Dict,
                          expected: any) -> str:
        """生成单元测试"""
        return f'''
def test_{func_name}_returns_expected():
    """测试 {func_name} 返回预期结果"""
    # Arrange
    params = {params}
    
    # Act
    result = {func_name}(**params)
    
    # Assert
    assert result == {expected}
'''

    def generate_exception_test(self, func_name: str,
                               params: Dict,
                               exception: str) -> str:
        """生成异常测试"""
        return f'''
def test_{func_name}_raises_{exception.lower()}():
    """测试 {func_name} 抛出 {exception}"""
    # Arrange
    params = {params}
    
    # Act & Assert
    with pytest.raises({exception}):
        {func_name}(**params)
'''
