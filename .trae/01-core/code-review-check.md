---
name: Code Review Check
description: 代码审查检查触发器 - 代码变更时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_code_change"
---

# 代码审查检查触发器

> **⚠️ 建议：代码变更时应执行代码审查**

---

## 一、触发条件

```yaml
触发场景:
  - 代码修改后
  - PR创建前
  - 合并请求前
  - 用户请求审查

不触发场景:
  - 用户明确跳过
  - 仅文档修改
```

---

## 二、审查维度

```yaml
正确性:
  - 逻辑正确
  - 边界处理
  - 异常处理
  - 并发安全

可读性:
  - 命名规范
  - 代码结构
  - 注释充分
  - 格式统一

可维护性:
  - 模块化
  - 低耦合
  - 高内聚
  - 易扩展

性能:
  - 算法效率
  - 资源使用
  - 内存管理
  - I/O优化

安全性:
  - 输入验证
  - 权限控制
  - 敏感数据处理
  - 漏洞防护
```

---

## 三、审查流程

```yaml
标准流程:
  1. 准备阶段:
     - 理解变更内容
     - 了解变更背景
     - 收集相关上下文
  
  2. 审查阶段:
     - 逐文件审查
     - 按维度检查
     - 记录问题
  
  3. 反馈阶段:
     - 分类问题
     - 提供修复建议
     - 确认修复方案
  
  4. 验证阶段:
     - 验证修复结果
     - 确认问题解决
     - 完成审查
```

---

## 四、问题分类

### 4.1 严重程度

```yaml
Critical (必须修复):
  - 安全漏洞
  - 数据丢失风险
  - 系统崩溃风险
  - 核心功能损坏

Major (应该修复):
  - 性能问题
  - 代码异味
  - 潜在Bug
  - 设计缺陷

Minor (建议修复):
  - 代码风格
  - 命名问题
  - 注释缺失
  - 格式问题

Suggestion (可选优化):
  - 优化建议
  - 最佳实践
  - 文档改进
```

### 4.2 问题类型

```yaml
安全问题:
  SQL注入:
    检查: 字符串拼接SQL
    修复: 使用参数化查询
  
  XSS攻击:
    检查: 未转义的用户输入
    修复: 使用转义函数
  
  敏感信息泄露:
    检查: 硬编码密钥/密码
    修复: 使用环境变量

性能问题:
  N+1查询:
    检查: 循环中执行查询
    修复: 批量查询或JOIN
  
  内存泄漏:
    检查: 未释放的资源
    修复: 使用上下文管理器
  
  无限循环:
    检查: 无退出条件的循环
    修复: 添加退出条件

代码质量问题:
  重复代码:
    检查: 相似的代码块
    修复: 提取公共方法
  
  过长函数:
    检查: 超过50行的函数
    修复: 拆分为小函数
  
  过深嵌套:
    检查: 嵌套超过3层
    修复: 提前返回或提取方法
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Any, Optional
from enum import Enum

class Severity(Enum):
    """严重程度"""
    CRITICAL = "critical"
    MAJOR = "major"
    MINOR = "minor"
    SUGGESTION = "suggestion"

class IssueType(Enum):
    """问题类型"""
    SECURITY = "security"
    PERFORMANCE = "performance"
    QUALITY = "quality"
    STYLE = "style"

@dataclass
class ReviewIssue:
    """审查问题"""
    file: str
    line: int
    severity: Severity
    type: IssueType
    message: str
    suggestion: str
    code_snippet: Optional[str] = None

@dataclass
class ReviewResult:
    """审查结果"""
    file: str
    issues: List[ReviewIssue]
    passed: bool
    summary: Dict[str, int]

class CodeReviewer:
    """代码审查器"""
    
    def __init__(self):
        self.rules: List[callable] = []
    
    def add_rule(self, rule: callable):
        """添加审查规则"""
        self.rules.append(rule)
    
    def review_file(self, file_path: str, 
                   content: str) -> ReviewResult:
        """审查文件"""
        issues = []
        
        for rule in self.rules:
            rule_issues = rule(file_path, content)
            issues.extend(rule_issues)
        
        summary = self._summarize(issues)
        passed = all(i.severity != Severity.CRITICAL for i in issues)
        
        return ReviewResult(
            file=file_path,
            issues=issues,
            passed=passed,
            summary=summary
        )
    
    def _summarize(self, issues: List[ReviewIssue]) -> Dict[str, int]:
        """汇总问题"""
        summary = {
            "critical": 0,
            "major": 0,
            "minor": 0,
            "suggestion": 0
        }
        
        for issue in issues:
            summary[issue.severity.value] += 1
        
        return summary

class SecurityRules:
    """安全规则"""
    
    @staticmethod
    def check_sql_injection(file_path: str, 
                           content: str) -> List[ReviewIssue]:
        """检查SQL注入"""
        issues = []
        lines = content.split('\n')
        
        for i, line in enumerate(lines, 1):
            if 'execute' in line and '+' in line and 'SELECT' in line.upper():
                issues.append(ReviewIssue(
                    file=file_path,
                    line=i,
                    severity=Severity.CRITICAL,
                    type=IssueType.SECURITY,
                    message="潜在的SQL注入风险",
                    suggestion="使用参数化查询代替字符串拼接",
                    code_snippet=line.strip()
                ))
        
        return issues
    
    @staticmethod
    def check_hardcoded_secrets(file_path: str,
                               content: str) -> List[ReviewIssue]:
        """检查硬编码密钥"""
        issues = []
        lines = content.split('\n')
        
        secret_patterns = ['password', 'api_key', 'secret', 'token']
        
        for i, line in enumerate(lines, 1):
            for pattern in secret_patterns:
                if pattern in line.lower() and '=' in line and '"' in line:
                    issues.append(ReviewIssue(
                        file=file_path,
                        line=i,
                        severity=Severity.CRITICAL,
                        type=IssueType.SECURITY,
                        message=f"发现硬编码的敏感信息: {pattern}",
                        suggestion="使用环境变量存储敏感信息",
                        code_snippet=line.strip()
                    ))
        
        return issues

class QualityRules:
    """质量规则"""
    
    @staticmethod
    def check_long_function(file_path: str,
                           content: str) -> List[ReviewIssue]:
        """检查过长函数"""
        issues = []
        lines = content.split('\n')
        
        function_start = None
        function_name = None
        indent_level = 0
        
        for i, line in enumerate(lines, 1):
            if 'def ' in line:
                if function_start and i - function_start > 50:
                    issues.append(ReviewIssue(
                        file=file_path,
                        line=function_start,
                        severity=Severity.MAJOR,
                        type=IssueType.QUALITY,
                        message=f"函数 {function_name} 过长 ({i - function_start} 行)",
                        suggestion="将函数拆分为更小的函数"
                    ))
                
                function_start = i
                function_name = line.split('def ')[1].split('(')[0]
        
        return issues
    
    @staticmethod
    def check_deep_nesting(file_path: str,
                          content: str) -> List[ReviewIssue]:
        """检查过深嵌套"""
        issues = []
        lines = content.split('\n')
        
        for i, line in enumerate(lines, 1):
            indent = len(line) - len(line.lstrip())
            nesting_level = indent // 4
            
            if nesting_level > 3:
                issues.append(ReviewIssue(
                    file=file_path,
                    line=i,
                    severity=Severity.MINOR,
                    type=IssueType.QUALITY,
                    message=f"嵌套层级过深 ({nesting_level} 层)",
                    suggestion="使用提前返回或提取方法减少嵌套"
                ))
        
        return issues

class CodeReviewOrchestrator:
    """代码审查编排器"""
    
    def __init__(self):
        self.reviewer = CodeReviewer()
        self.reviewer.add_rule(SecurityRules.check_sql_injection)
        self.reviewer.add_rule(SecurityRules.check_hardcoded_secrets)
        self.reviewer.add_rule(QualityRules.check_long_function)
        self.reviewer.add_rule(QualityRules.check_deep_nesting)
    
    def review_changes(self, changes: Dict[str, str]) -> Dict:
        """审查变更"""
        results = {}
        
        for file_path, content in changes.items():
            results[file_path] = self.reviewer.review_file(file_path, content)
        
        return {
            "files": {k: v.__dict__ for k, v in results.items()},
            "passed": all(v.passed for v in results.values()),
            "total_issues": sum(len(v.issues) for v in results.values())
        }
```

---

## 六、检查清单

```yaml
安全检查:
  □ 无SQL注入风险？
  □ 无XSS风险？
  □ 无硬编码密钥？
  □ 输入验证完善？

性能检查:
  □ 无N+1查询？
  □ 无内存泄漏？
  □ 无无限循环？
  □ 资源使用合理？

质量检查:
  □ 无重复代码？
  □ 函数长度合理？
  □ 嵌套层级合理？
  □ 命名规范？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           代码审查速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  审查维度:                                   │
│    □ 正确性                                 │
│    □ 可读性                                 │
│    □ 可维护性                               │
│    □ 性能                                   │
│    □ 安全性                                 │
│                                             │
│  严重程度:                                   │
│    Critical: 必须修复                       │
│    Major: 应该修复                          │
│    Minor: 建议修复                          │
│    Suggestion: 可选优化                     │
│                                             │
│  常见问题:                                   │
│    安全: SQL注入/硬编码密钥                 │
│    性能: N+1查询/内存泄漏                   │
│    质量: 重复代码/过长函数                  │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：审查是质量的守护者！
