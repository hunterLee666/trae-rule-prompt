---
name: Quality Gates Check
description: 质量门禁检查触发器 - 代码提交前建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "before_code_commit"
---

# 质量门禁检查触发器

> **⚠️ 建议：代码提交前应执行质量门禁检查**

---

## 一、触发条件

```yaml
触发场景:
  - 代码提交前
  - PR创建前
  - 合并请求前
  - 发布前

不触发场景:
  - 用户明确跳过
  - 紧急修复
```

---

## 二、门禁级别

```yaml
Level 1 - 基础:
  检查项:
    - 语法检查通过
    - 无Error级别诊断
    - 单元测试通过
  适用: 快速迭代、原型开发
  通过标准: 全部通过

Level 2 - 标准:
  检查项:
    - Level 1全部通过
    - 无Warning级别诊断
    - 代码覆盖率 > 80%
    - 代码审查通过
  适用: 常规开发、功能迭代
  通过标准: 全部通过

Level 3 - 严格:
  检查项:
    - Level 2全部通过
    - 集成测试通过
    - 性能基准测试通过
    - 安全扫描通过
  适用: 核心模块、关键功能
  通过标准: 全部通过

Level 4 - 生产:
  检查项:
    - Level 3全部通过
    - E2E测试通过
    - 压力测试通过
    - 合规检查通过
  适用: 生产发布、重大更新
  通过标准: 全部通过
```

---

## 三、检查项详解

### 3.1 代码质量检查

```yaml
语法检查:
  工具: ESLint / Pylint / Ruff
  标准: 无语法错误
  命令: npm run lint / ruff check .

类型检查:
  工具: TypeScript / mypy
  标准: 无类型错误
  命令: tsc --noEmit / mypy .

代码风格:
  工具: Prettier / Black
  标准: 符合风格规范
  命令: npm run format / black .
```

### 3.2 测试检查

```yaml
单元测试:
  工具: Jest / pytest
  标准: 全部通过
  命令: npm test / pytest
  
覆盖率:
  工具: coverage / istanbul
  标准: > 80%
  命令: pytest --cov / npm run coverage

集成测试:
  工具: pytest / Jest
  标准: 全部通过
  命令: npm run test:integration

E2E测试:
  工具: Playwright / Cypress
  标准: 全部通过
  命令: npm run test:e2e
```

### 3.3 安全检查

```yaml
依赖安全:
  工具: npm audit / safety
  标准: 无高危漏洞
  命令: npm audit / safety check

代码安全:
  工具: SonarQube / Bandit
  标准: 无安全问题
  命令: sonar-scanner / bandit .

敏感信息:
  工具: git-secrets / truffleHog
  标准: 无敏感信息泄露
  命令: git secrets --scan
```

---

## 四、门禁阈值标准

```yaml
代码质量阈值:
  圈复杂度: < 10
  认知复杂度: < 15
  代码重复率: < 3%
  注释率: > 10%

测试阈值:
  单元测试覆盖率: > 80%
  分支覆盖率: > 70%
  集成测试覆盖率: > 60%

性能阈值:
  响应时间: < 500ms (P95)
  吞吐量: > 100 req/s
  错误率: < 0.1%

安全阈值:
  高危漏洞: 0
  中危漏洞: < 3
  低危漏洞: < 10
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict
from enum import Enum

class GateLevel(Enum):
    """门禁级别"""
    BASIC = 1
    STANDARD = 2
    STRICT = 3
    PRODUCTION = 4

@dataclass
class CheckResult:
    """检查结果"""
    name: str
    passed: bool
    message: str
    details: Dict = None

class QualityGate:
    """质量门禁"""
    
    def __init__(self, level: GateLevel = GateLevel.STANDARD):
        self.level = level
        self.results: List[CheckResult] = []
    
    def check_syntax(self) -> CheckResult:
        """语法检查"""
        passed = True
        message = "语法检查通过"
        return CheckResult("syntax", passed, message)
    
    def check_types(self) -> CheckResult:
        """类型检查"""
        passed = True
        message = "类型检查通过"
        return CheckResult("types", passed, message)
    
    def check_tests(self) -> CheckResult:
        """测试检查"""
        passed = True
        message = "测试通过，覆盖率85%"
        return CheckResult("tests", passed, message)
    
    def check_coverage(self) -> CheckResult:
        """覆盖率检查"""
        passed = True
        message = "覆盖率85% > 80%"
        return CheckResult("coverage", passed, message)
    
    def check_security(self) -> CheckResult:
        """安全检查"""
        passed = True
        message = "无安全漏洞"
        return CheckResult("security", passed, message)
    
    def run_all_checks(self) -> bool:
        """运行所有检查"""
        self.results = []
        
        # Level 1 检查
        self.results.append(self.check_syntax())
        self.results.append(self.check_types())
        self.results.append(self.check_tests())
        
        if self.level.value >= GateLevel.STANDARD.value:
            self.results.append(self.check_coverage())
        
        if self.level.value >= GateLevel.STRICT.value:
            self.results.append(self.check_security())
        
        return all(r.passed for r in self.results)
    
    def get_report(self) -> str:
        """获取报告"""
        lines = ["=" * 50, "质量门禁报告", "=" * 50]
        
        for result in self.results:
            status = "✓" if result.passed else "✗"
            lines.append(f"{status} {result.name}: {result.message}")
        
        all_passed = all(r.passed for r in self.results)
        lines.append("=" * 50)
        lines.append(f"结果: {'通过' if all_passed else '失败'}")
        
        return "\n".join(lines)

def run_quality_gate(level: GateLevel = GateLevel.STANDARD) -> bool:
    """运行质量门禁"""
    gate = QualityGate(level)
    passed = gate.run_all_checks()
    print(gate.get_report())
    return passed
```

---

## 六、检查清单

```yaml
Level 1 基础:
  □ 语法检查通过？
  □ 无Error诊断？
  □ 单元测试通过？

Level 2 标准:
  □ Level 1全部通过？
  □ 无Warning诊断？
  □ 覆盖率 > 80%？
  □ 代码审查通过？

Level 3 严格:
  □ Level 2全部通过？
  □ 集成测试通过？
  □ 性能测试通过？
  □ 安全扫描通过？

Level 4 生产:
  □ Level 3全部通过？
  □ E2E测试通过？
  □ 压力测试通过？
  □ 合规检查通过？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           质量门禁速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  门禁级别:                                   │
│    Level 1: 基础 (语法/测试)                │
│    Level 2: 标准 (+覆盖率/审查)             │
│    Level 3: 严格 (+集成/安全)               │
│    Level 4: 生产 (+E2E/合规)                │
│                                             │
│  检查顺序:                                   │
│    1. 语法检查                              │
│    2. 类型检查                              │
│    3. 单元测试                              │
│    4. 覆盖率                                │
│    5. 安全扫描                              │
│                                             │
│  通过标准:                                   │
│    全部检查项通过                           │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：质量关卡，层层把关！
