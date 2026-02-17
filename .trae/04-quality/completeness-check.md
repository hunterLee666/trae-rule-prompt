---
name: Completeness Check
description: 完整性检查规则 - 确保代码功能完整、边界条件覆盖、文档齐全
version: "1.0"
priority: 85
alwaysApply: true
tags: [quality, completeness, testing]
---

# 完整性检查规则

## 一、完整性定义

### 1.1 核心维度

```yaml
完整性维度:
  
  功能完整性:
    权重: 40%
    内容:
      - 核心功能实现
      - 边界条件处理
      - 异常情况处理
      - 错误恢复机制
      
  测试完整性:
    权重: 25%
    内容:
      - 单元测试覆盖
      - 集成测试覆盖
      - 边界测试覆盖
      - 异常测试覆盖
      
  文档完整性:
    权重: 20%
    内容:
      - 代码注释
      - API文档
      - README文档
      - 使用示例
      
  配置完整性:
    权重: 15%
    内容:
      - 配置文件模板
      - 环境变量说明
      - 依赖声明
      - 部署配置
```

### 1.2 完整性等级

```yaml
等级定义:
  
  Level 5 - 完整:
    功能: 100%实现，边界处理完善
    测试: 覆盖率>90%
    文档: 全面的文档和示例
    配置: 完整的配置模板
    
  Level 4 - 良好:
    功能: 核心功能完整，主要边界处理
    测试: 覆盖率>80%
    文档: 主要功能有文档
    配置: 基本配置模板
    
  Level 3 - 合格:
    功能: 核心功能实现，基本边界处理
    测试: 覆盖率>60%
    文档: 基本文档
    配置: 配置说明
    
  Level 2 - 基础:
    功能: 核心功能基本实现
    测试: 有基本测试
    文档: 有README
    配置: 有配置说明
    
  Level 1 - 不完整:
    功能: 功能不完整
    测试: 无测试或测试不足
    文档: 无文档或文档缺失
    配置: 无配置说明
```

---

## 二、功能完整性检查

### 2.1 功能实现清单

```yaml
核心功能检查:
  
  数据处理:
    - [ ] 数据输入验证
    - [ ] 数据格式转换
    - [ ] 数据存储/持久化
    - [ ] 数据检索/查询
    - [ ] 数据更新/修改
    - [ ] 数据删除
    
  业务逻辑:
    - [ ] 主要业务流程
    - [ ] 业务规则验证
    - [ ] 状态管理
    - [ ] 事务处理
    
  接口实现:
    - [ ] 输入接口
    - [ ] 输出接口
    - [ ] 错误响应
    - [ ] 成功响应
```

### 2.2 边界条件检查

```yaml
边界条件清单:
  
  数值边界:
    - [ ] 最小值处理
    - [ ] 最大值处理
    - [ ] 零值处理
    - [ ] 负值处理
    - [ ] 空值/None处理
    
  字符串边界:
    - [ ] 空字符串处理
    - [ ] 超长字符串处理
    - [ ] 特殊字符处理
    - [ ] Unicode字符处理
    
  集合边界:
    - [ ] 空集合处理
    - [ ] 单元素集合处理
    - [ ] 大集合处理
    - [ ] 嵌套集合处理
    
  时间边界:
    - [ ] 过去时间处理
    - [ ] 未来时间处理
    - [ ] 时区处理
    - [ ] 闰年/闰秒处理
```

### 2.3 异常处理检查

```yaml
异常处理清单:
  
  输入异常:
    - [ ] 类型错误处理
    - [ ] 格式错误处理
    - [ ] 范围错误处理
    - [ ] 缺失参数处理
    
  资源异常:
    - [ ] 文件不存在处理
    - [ ] 网络超时处理
    - [ ] 数据库连接失败处理
    - [ ] 内存不足处理
    
  业务异常:
    - [ ] 业务规则违反处理
    - [ ] 权限不足处理
    - [ ] 状态冲突处理
    - [ ] 并发冲突处理
    
  系统异常:
    - [ ] 系统错误处理
    - [ ] 服务不可用处理
    - [ ] 资源耗尽处理
    - [ ] 未知错误处理
```

---

## 三、测试完整性检查

### 3.1 测试覆盖要求

```yaml
覆盖率标准:
  
  单元测试:
    语句覆盖率:
      Level 5: >90%
      Level 4: >80%
      Level 3: >60%
      
    分支覆盖率:
      Level 5: >85%
      Level 4: >70%
      Level 3: >50%
      
    函数覆盖率:
      Level 5: 100%
      Level 4: >90%
      Level 3: >80%
```

### 3.2 测试类型清单

```yaml
测试类型:
  
  正常流程测试:
    - [ ] 主流程测试
    - [ ] 分支流程测试
    - [ ] 循环流程测试
    
  边界测试:
    - [ ] 边界值测试
    - [ ] 等价类测试
    - [ ] 边界组合测试
    
  异常测试:
    - [ ] 输入异常测试
    - [ ] 资源异常测试
    - [ ] 业务异常测试
    
  性能测试:
    - [ ] 负载测试
    - [ ] 压力测试
    - [ ] 并发测试
    
  安全测试:
    - [ ] 输入验证测试
    - [ ] 权限测试
    - [ ] 注入测试
```

### 3.3 测试用例模板

```python
import pytest
from typing import Any, List, Tuple

class TestFunctionName:
    """测试类命名规范: Test + 被测函数/类名"""
    
    @pytest.mark.parametrize("input,expected", [
        ("normal_input", "expected_output"),
        ("edge_case_1", "expected_output_1"),
        ("edge_case_2", "expected_output_2"),
    ])
    def test_normal_cases(self, input: Any, expected: Any):
        """测试正常情况"""
        result = function_under_test(input)
        assert result == expected
    
    @pytest.mark.parametrize("input", [
        None,
        "",
        [],
        -1,
        float('inf'),
    ])
    def test_boundary_cases(self, input: Any):
        """测试边界条件"""
        result = function_under_test(input)
        assert result is not None
    
    @pytest.mark.parametrize("input,exception", [
        (None, ValueError),
        ("invalid", TypeError),
    ])
    def test_exception_cases(self, input: Any, exception: type):
        """测试异常情况"""
        with pytest.raises(exception):
            function_under_test(input)
```

---

## 四、文档完整性检查

### 4.1 代码注释规范

```yaml
注释要求:
  
  模块级注释:
    必需:
      - 模块功能描述
      - 作者信息
      - 版本信息
    可选:
      - 使用示例
      - 注意事项
      
  函数级注释:
    必需:
      - 函数功能描述
      - 参数说明 (Args)
      - 返回值说明 (Returns)
    可选:
      - 异常说明 (Raises)
      - 使用示例 (Example)
      
  类级注释:
    必需:
      - 类功能描述
      - 属性说明 (Attributes)
    可选:
      - 使用示例
      - 继承关系
```

### 4.2 Docstring模板

```python
def function_name(param1: Type1, param2: Type2) -> ReturnType:
    """
    函数功能简要描述
    
    详细描述（可选），说明函数的详细功能、
    使用场景、注意事项等。
    
    Args:
        param1: 参数1的说明
        param2: 参数2的说明
        
    Returns:
        返回值的说明
        
    Raises:
        ExceptionType: 异常情况的说明
        
    Example:
        >>> result = function_name("value1", "value2")
        >>> print(result)
        expected_output
    """
    pass

class ClassName:
    """
    类功能简要描述
    
    详细描述（可选）。
    
    Attributes:
        attr1: 属性1的说明
        attr2: 属性2的说明
        
    Example:
        >>> obj = ClassName()
        >>> obj.method()
    """
    
    def __init__(self, param: Type):
        """初始化方法"""
        pass
```

### 4.3 README模板

```markdown
# 项目名称

简要描述项目功能

## 功能特性

- 功能1
- 功能2
- 功能3

## 安装

pip install package_name

## 快速开始

```python
from package import main_function

result = main_function()
```

## 配置

| 环境变量 | 说明 | 默认值 |
|---------|------|-------|
| VAR1 | 说明1 | default1 |
| VAR2 | 说明2 | default2 |

## API文档

### function_name(param1, param2)

功能描述

**参数**:
- param1 (Type): 说明
- param2 (Type): 说明

**返回**: ReturnType - 说明

## 测试

pytest tests/

## 许可证

MIT License
```

---

## 五、配置完整性检查

### 5.1 配置文件清单

```yaml
必需配置文件:
  
  Python项目:
    - [ ] requirements.txt / pyproject.toml
    - [ ] setup.py / setup.cfg
    - [ ] .env.example
    - [ ] .gitignore
    - [ ] README.md
    
  Node.js项目:
    - [ ] package.json
    - [ ] package-lock.json / yarn.lock
    - [ ] .env.example
    - [ ] .gitignore
    - [ ] README.md
    
  通用配置:
    - [ ] .editorconfig
    - [ ] .prettierrc (如适用)
    - [ ] .eslintrc (如适用)
    - [ ] docker-compose.yml (如适用)
    - [ ] Dockerfile (如适用)
```

### 5.2 环境变量模板

```yaml
.env.example模板:
  
  应用配置:
    APP_NAME=myapp
    APP_ENV=development
    APP_DEBUG=true
    APP_PORT=8000
    
  数据库配置:
    DB_HOST=localhost
    DB_PORT=5432
    DB_NAME=myapp
    DB_USER=
    DB_PASSWORD=
    
  缓存配置:
    REDIS_HOST=localhost
    REDIS_PORT=6379
    
  安全配置:
    SECRET_KEY=
    API_KEY=
    
  日志配置:
    LOG_LEVEL=INFO
    LOG_FILE=logs/app.log
```

### 5.3 依赖声明检查

```yaml
依赖检查清单:
  
  requirements.txt:
    - [ ] 所有依赖已声明
    - [ ] 版本号已固定或范围明确
    - [ ] 开发依赖与生产依赖分离
    
  pyproject.toml:
    - [ ] 项目元数据完整
    - [ ] 依赖分组清晰
    - [ ] 版本约束合理
    
  版本约束规范:
    生产依赖: package>=1.0.0,<2.0.0
    开发依赖: package==1.0.0
    可选依赖: package>=1.0.0; extra == "feature"
```

---

## 六、完整性评分

### 6.1 评分计算

```python
def calculate_completeness_score(checks: dict) -> dict:
    """
    计算完整性得分
    
    checks = {
        'functionality': {
            'core_features': 0.9,      # 核心功能实现率
            'boundary_handling': 0.8,   # 边界处理率
            'exception_handling': 0.85  # 异常处理率
        },
        'testing': {
            'unit_tests': 0.85,         # 单元测试覆盖率
            'integration_tests': 0.7,   # 集成测试覆盖率
            'edge_tests': 0.6           # 边界测试覆盖率
        },
        'documentation': {
            'code_comments': 0.9,       # 代码注释率
            'api_docs': 0.8,            # API文档完整度
            'readme': 1.0               # README完整度
        },
        'configuration': {
            'config_templates': 0.9,    # 配置模板完整度
            'env_vars': 0.8,            # 环境变量说明
            'dependencies': 1.0         # 依赖声明完整度
        }
    }
    """
    weights = {
        'functionality': 0.40,
        'testing': 0.25,
        'documentation': 0.20,
        'configuration': 0.15
    }
    
    dimension_scores = {}
    for dimension, items in checks.items():
        dimension_scores[dimension] = sum(items.values()) / len(items)
    
    total_score = sum(
        dimension_scores[k] * weights[k]
        for k in weights
    ) * 100
    
    return {
        'total_score': round(total_score, 2),
        'dimension_scores': dimension_scores,
        'level': get_completeness_level(total_score)
    }

def get_completeness_level(score: float) -> str:
    if score >= 90:
        return "Level 5 - 完整"
    elif score >= 80:
        return "Level 4 - 良好"
    elif score >= 70:
        return "Level 3 - 合格"
    elif score >= 60:
        return "Level 2 - 基础"
    else:
        return "Level 1 - 不完整"
```

### 6.2 完整性报告模板

```yaml
完整性报告:
  
  项目: {project_name}
  日期: {date}
  
  总体评分: {total_score}/100
  等级: {level}
  
  维度得分:
    功能完整性: {functionality_score}/100
    测试完整性: {testing_score}/100
    文档完整性: {documentation_score}/100
    配置完整性: {configuration_score}/100
    
  改进建议:
    - {suggestion_1}
    - {suggestion_2}
    - {suggestion_3}
```

---

## 七、检查清单模板

### 7.1 代码提交前检查

```yaml
提交前检查清单:
  
  功能检查:
    - [ ] 核心功能已实现
    - [ ] 边界条件已处理
    - [ ] 异常处理已完善
    
  测试检查:
    - [ ] 单元测试已编写
    - [ ] 测试已通过
    - [ ] 覆盖率达标
    
  文档检查:
    - [ ] 代码有注释
    - [ ] 函数有docstring
    - [ ] README已更新
    
  配置检查:
    - [ ] 无硬编码配置
    - [ ] 环境变量已配置
    - [ ] 依赖已声明
    
  安全检查:
    - [ ] 无敏感信息硬编码
    - [ ] 输入已验证
    - [ ] 权限已检查
```

### 7.2 发布前检查

```yaml
发布前检查清单:
  
  功能完整性:
    - [ ] 所有功能已实现
    - [ ] 所有边界已处理
    - [ ] 所有异常已处理
    
  测试完整性:
    - [ ] 单元测试覆盖率>80%
    - [ ] 集成测试已通过
    - [ ] 性能测试已通过
    - [ ] 安全测试已通过
    
  文档完整性:
    - [ ] API文档完整
    - [ ] 用户手册完整
    - [ ] 部署文档完整
    - [ ] 变更日志已更新
    
  配置完整性:
    - [ ] 生产配置已准备
    - [ ] 环境变量已配置
    - [ ] 监控告警已配置
    - [ ] 备份恢复已配置
```

---

## 八、版本历史

```yaml
版本记录:
  - v1.0 (2026-02-16): 初始版本
    - 定义完整性维度
    - 建立检查清单
    - 提供评分标准
```
