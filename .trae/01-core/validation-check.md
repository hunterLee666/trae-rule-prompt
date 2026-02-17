---
name: Validation Check
description: 验证规则检查触发器 - 输入输出验证时建议执行
version: "1.0"
priority: 95
alwaysApply: true
trigger: "during_input_output_processing"
---

# 验证规则检查触发器

> **⚠️ 建议：处理输入输出时应执行验证**

---

## 一、触发条件

```yaml
触发场景:
  - 接收用户输入
  - 调用工具前
  - 生成输出前
  - 返回结果前

不触发场景:
  - 用户明确跳过
```

---

## 二、验证类型

### 2.1 输入验证

```yaml
类型验证:
  参数类型检查:
    - 字符串、数字、布尔值
    - 列表、字典、对象
  数据格式检查:
    - 日期格式
    - 邮箱格式
    - URL格式
  值范围检查:
    - 最小/最大值
    - 枚举值
    - 正则匹配

逻辑验证:
  业务规则检查:
    - 必填字段
    - 条件依赖
    - 唯一性约束
  约束条件检查:
    - 长度限制
    - 大小限制
    - 数量限制
  依赖关系检查:
    - 前置条件
    - 后置条件
    - 关联关系

安全验证:
  权限检查:
    - 用户权限
    - 资源权限
    - 操作权限
  敏感数据检测:
    - 密钥检测
    - 个人信息检测
    - 敏感词检测
  注入攻击防护:
    - SQL注入
    - 命令注入
    - XSS攻击
```

### 2.2 输出验证

```yaml
格式验证:
  输出格式检查:
    - JSON格式
    - Markdown格式
    - 代码格式
  结构完整性检查:
    - 必需字段
    - 字段类型
    - 嵌套结构

内容验证:
  内容正确性检查:
    - 逻辑正确
    - 语法正确
    - 语义正确
  内容完整性检查:
    - 无截断
    - 无遗漏
    - 无冗余

质量验证:
  代码质量检查:
    - 无语法错误
    - 无类型错误
    - 符合规范
  文档质量检查:
    - 结构清晰
    - 内容完整
    - 格式正确
```

---

## 三、验证规则

### 3.1 文件操作验证

```yaml
路径验证:
  - 路径格式正确
  - 路径在允许范围内
  - 路径不存在危险字符
  - 路径不存在目录遍历

权限验证:
  - 读取权限检查
  - 写入权限检查
  - 执行权限检查

内容验证:
  - 文件大小限制
  - 文件类型检查
  - 编码格式检查
```

### 3.2 命令执行验证

```yaml
命令验证:
  - 命令在白名单内
  - 参数格式正确
  - 无危险操作

资源验证:
  - 资源充足
  - 无资源冲突
  - 符合限制条件

环境验证:
  - 环境变量正确
  - 依赖已安装
  - 配置已加载
```

### 3.3 代码生成验证

```yaml
语法验证:
  - 语法正确
  - 无编译错误
  - 无解析错误

逻辑验证:
  - 逻辑正确
  - 无运行时错误
  - 无死循环

风格验证:
  - 符合编码规范
  - 无代码异味
  - 命名规范
```

---

## 四、验证流程

```yaml
标准流程:
  1. 接收输入:
     - 解析输入内容
     - 提取验证项
     
  2. 执行验证:
     - 按规则逐一验证
     - 收集验证结果
     
  3. 处理结果:
     通过: 继续执行
     失败: 返回错误信息
     
  4. 记录日志:
     - 记录验证结果
     - 记录错误详情
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import Dict, List, Any, Optional, Callable
from enum import Enum

class ValidationLevel(Enum):
    """验证级别"""
    ERROR = "error"
    WARNING = "warning"
    INFO = "info"

@dataclass
class ValidationResult:
    """验证结果"""
    valid: bool
    level: ValidationLevel
    message: str
    field: Optional[str] = None
    details: Optional[Dict] = None

class Validator:
    """验证器基类"""
    
    def __init__(self):
        self.rules: List[Callable] = []
    
    def add_rule(self, rule: Callable):
        """添加验证规则"""
        self.rules.append(rule)
    
    def validate(self, value: Any) -> List[ValidationResult]:
        """执行验证"""
        results = []
        for rule in self.rules:
            result = rule(value)
            if result:
                results.append(result)
        return results

class PathValidator(Validator):
    """路径验证器"""
    
    def __init__(self, allowed_dirs: List[str] = None):
        super().__init__()
        self.allowed_dirs = allowed_dirs or []
        
        self.add_rule(self._check_format)
        self.add_rule(self._check_traversal)
        self.add_rule(self._check_allowed_dirs)
    
    def _check_format(self, path: str) -> Optional[ValidationResult]:
        """检查路径格式"""
        import os
        try:
            os.path.normpath(path)
            return None
        except Exception as e:
            return ValidationResult(
                valid=False,
                level=ValidationLevel.ERROR,
                message=f"路径格式无效: {e}",
                field="path"
            )
    
    def _check_traversal(self, path: str) -> Optional[ValidationResult]:
        """检查目录遍历"""
        if ".." in path:
            return ValidationResult(
                valid=False,
                level=ValidationLevel.ERROR,
                message="路径包含目录遍历字符",
                field="path"
            )
        return None
    
    def _check_allowed_dirs(self, path: str) -> Optional[ValidationResult]:
        """检查允许目录"""
        if not self.allowed_dirs:
            return None
        
        import os
        abs_path = os.path.abspath(path)
        for allowed in self.allowed_dirs:
            if abs_path.startswith(os.path.abspath(allowed)):
                return None
        
        return ValidationResult(
            valid=False,
            level=ValidationLevel.ERROR,
            message="路径不在允许范围内",
            field="path"
        )

class InputValidator:
    """输入验证器"""
    
    def __init__(self):
        self.path_validator = PathValidator()
    
    def validate_file_path(self, path: str) -> List[ValidationResult]:
        """验证文件路径"""
        return self.path_validator.validate(path)
    
    def validate_command(self, command: str) -> List[ValidationResult]:
        """验证命令"""
        results = []
        
        dangerous_patterns = ["rm -rf", "sudo", "chmod 777", "> /dev/"]
        for pattern in dangerous_patterns:
            if pattern in command:
                results.append(ValidationResult(
                    valid=False,
                    level=ValidationLevel.ERROR,
                    message=f"命令包含危险操作: {pattern}",
                    field="command"
                ))
        
        return results
    
    def validate_code(self, code: str) -> List[ValidationResult]:
        """验证代码"""
        results = []
        
        sensitive_patterns = [
            "password", "api_key", "secret", "token"
        ]
        for pattern in sensitive_patterns:
            if pattern in code.lower():
                results.append(ValidationResult(
                    valid=False,
                    level=ValidationLevel.WARNING,
                    message=f"代码可能包含敏感信息: {pattern}",
                    field="code"
                ))
        
        return results
```

---

## 六、检查清单

```yaml
输入验证:
  □ 类型验证通过？
  □ 格式验证通过？
  □ 安全验证通过？
  □ 业务规则验证通过？

输出验证:
  □ 格式验证通过？
  □ 内容验证通过？
  □ 质量验证通过？

验证流程:
  □ 验证规则完整？
  □ 验证顺序正确？
  □ 错误处理完善？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           验证规则速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  输入验证:                                   │
│    □ 类型验证                               │
│    □ 格式验证                               │
│    □ 安全验证                               │
│    □ 业务规则验证                           │
│                                             │
│  输出验证:                                   │
│    □ 格式验证                               │
│    □ 内容验证                               │
│    □ 质量验证                               │
│                                             │
│  常见验证:                                   │
│    路径: 格式/遍历/权限                     │
│    命令: 白名单/危险操作                    │
│    代码: 语法/敏感信息                      │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：验证先行，质量保障！
