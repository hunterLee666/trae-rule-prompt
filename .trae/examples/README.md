# TRAE v7.0 架构示例

本文档展示TRAE v7.0架构的核心概念和使用方式。

## 模型路由示例

```python
# 示例：如何使用模型路由器

from typing import Dict

class ModelRouter:
    """模型路由器示例"""
    
    def assess_complexity(
        self,
        task_description: str,
        files_involved: int = 1
    ) -> float:
        """
        评估任务复杂度
        
        Returns:
            复杂度分数 (0.0 - 1.0)
        """
        complexity = 0.0
        
        # 基于文件数量
        if files_involved == 1:
            complexity += 0.1
        elif files_involved <= 3:
            complexity += 0.2
        else:
            complexity += 0.4
        
        return min(1.0, complexity)
    
    def route_task(
        self,
        complexity: float,
        cost_sensitive: bool = False
    ) -> Dict:
        """
        为任务选择最佳模型类别
        
        Returns:
            路由决策
        """
        if complexity < 0.3 and cost_sensitive:
            return {
                "category": "quick",
                "reasoning": "简单任务，成本敏感",
                "provider": "groq"
            }
        elif complexity > 0.8:
            return {
                "category": "ultrabrain",
                "reasoning": "复杂任务，需要深度推理",
                "provider": "anthropic/claude-opus"
            }
        else:
            return {
                "category": "unspecified-low",
                "reasoning": "中等任务",
                "provider": "openai/gpt-4o"
            }

# 使用示例
router = ModelRouter()
complexity = router.assess_complexity("实现用户登录功能", files_involved=3)
decision = router.route_task(complexity)

print(f"复杂度: {complexity:.2f}")
print(f"推荐类别: {decision['category']}")
print(f"原因: {decision['reasoning']}")
```

## 工作流模式示例

```python
# 示例：Plan/Build模式切换

from enum import Enum

class WorkflowMode(Enum):
    PLAN = "plan"      # 规划模式：只读分析
    BUILD = "build"    # 构建模式：执行修改

class WorkflowManager:
    """工作流管理器示例"""
    
    PLAN_TRIGGERS = [
        "plan", "analyze", "design",
        "规划", "分析", "设计"
    ]
    
    BUILD_TRIGGERS = [
        "implement", "fix", "add", "create",
        "实现", "修复", "添加", "创建"
    ]
    
    def detect_mode(self, user_input: str) -> WorkflowMode:
        """检测工作流模式"""
        input_lower = user_input.lower()
        
        # 检查Plan模式触发词
        for trigger in self.PLAN_TRIGGERS:
            if trigger in input_lower:
                return WorkflowMode.PLAN
        
        # 检查Build模式触发词
        for trigger in self.BUILD_TRIGGERS:
            if trigger in input_lower:
                return WorkflowMode.BUILD
        
        # 默认为Plan模式（安全第一）
        return WorkflowMode.PLAN

# 使用示例
manager = WorkflowManager()

# 场景1：分析需求
mode1 = manager.detect_mode("分析项目结构")
print(f"输入'分析项目结构' → 模式: {mode1.value}")  # plan

# 场景2：执行开发
mode2 = manager.detect_mode("实现用户认证")
print(f"输入'实现用户认证' → 模式: {mode2.value}")  # build
```

## 配置加载示例

```python
# 示例：从YAML加载配置

import yaml
from pathlib import Path

def load_model_routing():
    """加载模型路由配置"""
    config_path = Path("config/system.yaml")
    
    with open(config_path, 'r', encoding='utf-8') as f:
        data = yaml.safe_load(f)
    
    routing = data.get("model_routing", {})
    
    return {
        "default_category": routing.get("default_category"),
        "categories": routing.get("categories", {})
    }

# 配置结构示例
config_example = """
model_routing:
  default_category: "unspecified-low"
  
  categories:
    quick:
      description: "简单修复、拼写错误"
      complexity_range: [0.0, 0.3]
      providers: ["groq", "local"]
    
    ultrabrain:
      description: "架构设计、复杂逻辑"
      complexity_range: [0.8, 1.0]
      providers: ["anthropic/claude-opus"]
"""
```

## 质量门禁示例

```python
# 示例：质量门禁检查

class QualityGate:
    """质量门禁示例"""
    
    LEVELS = {
        1: {
            "name": "基础",
            "requirements": ["no_syntax_errors", "unit_tests_pass"]
        },
        2: {
            "name": "标准",
            "requirements": [
                "no_warnings",
                "coverage_above_80",
                "code_review_pass"
            ]
        },
        3: {
            "name": "严格",
            "requirements": [
                "integration_tests_pass",
                "performance_benchmark_pass",
                "security_scan_pass"
            ]
        }
    }
    
    def check(self, level: int, results: dict) -> dict:
        """
        执行质量门禁检查
        
        Args:
            level: 门禁级别 (1-3)
            results: 检查结果
        
        Returns:
            检查报告
        """
        gate = self.LEVELS.get(level)
        if not gate:
            return {"error": "Invalid level"}
        
        passed = True
        failed_requirements = []
        
        for req in gate["requirements"]:
            if not results.get(req, False):
                passed = False
                failed_requirements.append(req)
        
        return {
            "passed": passed,
            "level": gate["name"],
            "failed": failed_requirements
        }

# 使用示例
gate = QualityGate()

results = {
    "no_syntax_errors": True,
    "unit_tests_pass": True,
    "no_warnings": False,
    "coverage_above_80": True
}

report = gate.check(level=2, results=results)
print(f"门禁通过: {report['passed']}")
print(f"失败项: {report['failed']}")
```

## 隐私保护示例

```python
# 示例：敏感数据脱敏

import re

class PrivacyFilter:
    """隐私过滤器示例"""
    
    SENSITIVE_PATTERNS = [
        (r'api[_-]?key\s*=\s*["\']?[^"\'\s]+', 'API_KEY=***'),
        (r'password\s*=\s*["\']?[^"\'\s]+', 'password=[REDACTED]'),
        (r'token\s*=\s*["\']?[^"\'\s]+', 'token=<HIDDEN>'),
    ]
    
    def redact(self, content: str) -> str:
        """脱敏敏感数据"""
        result = content
        for pattern, replacement in self.SENSITIVE_PATTERNS:
            result = re.sub(pattern, replacement, result, flags=re.IGNORECASE)
        return result

# 使用示例
filter = PrivacyFilter()

content = """
API_KEY=sk-1234567890
password=mysecret123
token=abc123xyz
"""

redacted = filter.redact(content)
print(redacted)
# 输出:
# API_KEY=***
# password=[REDACTED]
# token=<HIDDEN>
```

---

**说明**：以上代码仅为示例，展示TRAE v7.0架构的核心概念。实际运行时，TRAE IDE会直接读取`.md`和`.yaml`配置文件作为系统提示。
