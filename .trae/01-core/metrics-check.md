---
name: Metrics Check
description: 质量指标检查触发器 - 代码质量评估时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_quality_assessment"
---

# 质量指标检查触发器

> **⚠️ 建议：评估代码质量时应使用量化指标**

---

## 一、触发条件

```yaml
触发场景:
  - 代码审查时
  - 质量评估时
  - 发布前检查
  - 持续集成

不触发场景:
  - 用户明确跳过
```

---

## 二、代码质量指标

### 2.1 复杂度指标

```yaml
圈复杂度:
  定义: 独立路径数量
  计算: M = E - N + 2P
  目标: < 10
  警告: 10-20
  错误: > 20
  
认知复杂度:
  定义: 理解代码的难度
  特点: 考虑嵌套和中断
  目标: < 15
  警告: 15-25
  错误: > 25
```

### 2.2 可维护性指标

```yaml
代码行数:
  函数: < 50行
  类: < 500行
  文件: < 1000行

重复率:
  定义: 重复代码比例
  目标: < 3%
  警告: 3-5%
  错误: > 5%

耦合度:
  传入耦合: 依赖的外部模块数
  传出耦合: 被依赖的次数
  目标: 低耦合 (< 10)
```

### 2.3 可读性指标

```yaml
命名质量:
  命名长度: 3-30字符
  命名规范: 符合语言规范
  命名意义: 清晰表达意图

注释质量:
  注释率: > 10%
  文档覆盖: 公共API 100%
  注释有效性: 无冗余注释
```

---

## 三、测试质量指标

### 3.1 覆盖率指标

```yaml
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
```

### 3.2 测试效果指标

```yaml
测试通过率:
  目标: 100%
  计算: 通过测试数 / 总测试数

缺陷检出率:
  目标: > 90%
  计算: 测试发现的缺陷 / 总缺陷

回归测试通过率:
  目标: 100%
  计算: 通过的回归测试 / 总回归测试
```

---

## 四、过程质量指标

### 4.1 开发效率指标

```yaml
代码审查通过率:
  目标: > 80%
  计算: 一次通过的PR / 总PR数

一次提交成功率:
  目标: > 70%
  计算: 无修改合并的提交 / 总提交

平均修复时间:
  目标: < 1天
  计算: 缺陷从发现到修复的时间
```

### 4.2 缺陷指标

```yaml
缺陷密度:
  目标: < 1个/千行
  计算: 缺陷数 / 代码行数 * 1000

缺陷重开率:
  目标: < 5%
  计算: 重开的缺陷 / 已关闭缺陷

缺陷修复率:
  目标: > 95%
  计算: 已修复缺陷 / 总缺陷
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Optional
from enum import Enum

class MetricLevel(Enum):
    """指标级别"""
    PASS = "pass"
    WARNING = "warning"
    ERROR = "error"

@dataclass
class MetricResult:
    """指标结果"""
    name: str
    value: float
    level: MetricLevel
    target: float
    message: str

class ComplexityMetrics:
    """复杂度指标计算器"""
    
    THRESHOLDS = {
        "cyclomatic": {"target": 10, "warning": 20},
        "cognitive": {"target": 15, "warning": 25}
    }
    
    def calculate_cyclomatic_complexity(self, code: str) -> int:
        """计算圈复杂度"""
        complexity = 1
        
        decision_points = [
            'if ', 'elif ', 'else:', 'for ', 'while ',
            'and ', 'or ', 'try:', 'except:', 'with '
        ]
        
        for point in decision_points:
            complexity += code.count(point)
        
        return complexity
    
    def calculate_cognitive_complexity(self, code: str) -> int:
        """计算认知复杂度"""
        complexity = 0
        nesting_level = 0
        lines = code.split('\n')
        
        for line in lines:
            stripped = line.strip()
            
            if any(stripped.startswith(kw) for kw in ['if ', 'for ', 'while ']):
                complexity += nesting_level + 1
                nesting_level += 1
            elif stripped in ['else:', 'elif ', 'except:']:
                complexity += nesting_level
            elif stripped in ['break', 'continue']:
                complexity += 1
            
            if stripped and not stripped.startswith('#'):
                if line.index(stripped[0]) < len(line) - len(stripped):
                    nesting_level = max(0, nesting_level - 1)
        
        return complexity
    
    def evaluate(self, code: str) -> List[MetricResult]:
        """评估复杂度"""
        results = []
        
        cc = self.calculate_cyclomatic_complexity(code)
        results.append(MetricResult(
            name="圈复杂度",
            value=cc,
            level=self._get_level(cc, "cyclomatic"),
            target=self.THRESHOLDS["cyclomatic"]["target"],
            message=f"圈复杂度: {cc}"
        ))
        
        cog = self.calculate_cognitive_complexity(code)
        results.append(MetricResult(
            name="认知复杂度",
            value=cog,
            level=self._get_level(cog, "cognitive"),
            target=self.THRESHOLDS["cognitive"]["target"],
            message=f"认知复杂度: {cog}"
        ))
        
        return results
    
    def _get_level(self, value: int, metric_type: str) -> MetricLevel:
        """获取级别"""
        threshold = self.THRESHOLDS[metric_type]
        if value <= threshold["target"]:
            return MetricLevel.PASS
        elif value <= threshold["warning"]:
            return MetricLevel.WARNING
        else:
            return MetricLevel.ERROR

class MaintainabilityMetrics:
    """可维护性指标计算器"""
    
    THRESHOLDS = {
        "function_lines": {"target": 50, "warning": 100},
        "class_lines": {"target": 500, "warning": 1000},
        "file_lines": {"target": 1000, "warning": 2000},
        "duplication": {"target": 3, "warning": 5}
    }
    
    def calculate_function_lines(self, code: str) -> Dict[str, int]:
        """计算函数行数"""
        import re
        functions = {}
        
        pattern = r'def\s+(\w+)\s*\([^)]*\):'
        lines = code.split('\n')
        
        current_func = None
        start_line = 0
        
        for i, line in enumerate(lines):
            match = re.search(pattern, line)
            if match:
                if current_func:
                    functions[current_func] = i - start_line
                current_func = match.group(1)
                start_line = i
        
        if current_func:
            functions[current_func] = len(lines) - start_line
        
        return functions
    
    def calculate_duplication(self, code: str) -> float:
        """计算重复率"""
        lines = code.split('\n')
        unique_lines = set(lines)
        
        if len(lines) == 0:
            return 0
        
        return (1 - len(unique_lines) / len(lines)) * 100

class QualityScoreCalculator:
    """质量分数计算器"""
    
    WEIGHTS = {
        "complexity": 0.3,
        "maintainability": 0.3,
        "coverage": 0.2,
        "duplication": 0.2
    }
    
    def calculate_score(self, metrics: Dict[str, float]) -> float:
        """计算质量分数"""
        score = 0
        
        complexity_score = max(0, 100 - metrics.get("complexity", 0) * 5)
        maintainability_score = metrics.get("maintainability", 50)
        coverage_score = metrics.get("coverage", 0)
        duplication_score = max(0, 100 - metrics.get("duplication", 0) * 10)
        
        score = (
            complexity_score * self.WEIGHTS["complexity"] +
            maintainability_score * self.WEIGHTS["maintainability"] +
            coverage_score * self.WEIGHTS["coverage"] +
            duplication_score * self.WEIGHTS["duplication"]
        )
        
        return round(score, 2)
    
    def get_grade(self, score: float) -> str:
        """获取等级"""
        if score >= 90:
            return "A"
        elif score >= 80:
            return "B"
        elif score >= 70:
            return "C"
        elif score >= 60:
            return "D"
        else:
            return "F"
```

---

## 六、检查清单

```yaml
复杂度指标:
  □ 圈复杂度 < 10？
  □ 认知复杂度 < 15？
  □ 函数行数 < 50？

可维护性指标:
  □ 重复率 < 3%？
  □ 耦合度合理？
  □ 注释率 > 10%？

测试指标:
  □ 行覆盖率 > 80%？
  □ 分支覆盖率 > 70%？
  □ 测试通过率 100%？

过程指标:
  □ 审查通过率 > 80%？
  □ 缺陷密度 < 1/千行？
  □ 平均修复时间 < 1天？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           质量指标速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  复杂度指标:                                 │
│    圈复杂度: < 10 (目标) < 20 (警告)        │
│    认知复杂度: < 15 (目标) < 25 (警告)      │
│                                             │
│  可维护性指标:                               │
│    函数行数: < 50行                         │
│    重复率: < 3%                             │
│    注释率: > 10%                            │
│                                             │
│  测试指标:                                   │
│    行覆盖率: > 80%                          │
│    分支覆盖率: > 70%                        │
│    测试通过率: 100%                         │
│                                             │
│  质量等级:                                   │
│    A: >= 90  B: >= 80                      │
│    C: >= 70  D: >= 60  F: < 60             │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：可度量，方可改进！
