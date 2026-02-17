---
name: Code Generation Check
description: 代码生成检查触发器 - 生成代码时强制执行
version: "1.0"
priority: 95
alwaysApply: true
trigger: "during_code_generation"
---

# 代码生成检查触发器

> **⚠️ 强制规则：生成代码时必须遵循风格规范**

---

## 一、触发条件

```yaml
触发场景:
  - 生成新代码
  - 修改现有代码
  - 创建新文件
  - 重构代码

不触发场景:
  - 用户明确跳过
  - 仅文档修改
```

---

## 二、代码风格

### 2.1 通用风格

```yaml
缩进:
  Python: 4空格
  JavaScript: 2空格
  Go: Tab
  Rust: 4空格
  Java: 4空格

行宽:
  最大长度: 100字符
  推荐: 80字符

空行:
  类之间: 2空行
  方法之间: 1空行
  逻辑块之间: 1空行

括号:
  风格: K&R风格
  示例:
    if (condition) {
      // code
    }
```

### 2.2 语言特定风格

```yaml
Python:
  格式化: black, autopep8
  导入排序: isort
  类型注解: 必须添加
  文档字符串: Google风格

JavaScript:
  格式化: prettier
  分号: 必须添加
  引号: 单引号
  箭头函数: 优先使用

Go:
  格式化: gofmt
  导出: 大写开头
  错误处理: 显式处理
  接口: 小接口

Rust:
  格式化: rustfmt
  错误处理: Result<T, E>
  所有权: 遵循借用规则
  命名: snake_case
```

---

## 三、命名规范

### 3.1 通用命名

```yaml
命名风格:
  snake_case:
    用途: 变量、函数 (Python, Rust)
    示例: user_name, get_user_by_id
  
  camelCase:
    用途: 变量、函数 (JavaScript, Java)
    示例: userName, getUserById
  
  PascalCase:
    用途: 类、接口、类型
    示例: UserService, UserRepository
  
  UPPER_SNAKE_CASE:
    用途: 常量
    示例: MAX_RETRY_COUNT, API_BASE_URL
```

### 3.2 命名规则

```yaml
变量命名:
  - 有意义: user_count 而非 uc
  - 避免缩写: customer 而非 cust
  - 布尔值: is_active, has_permission
  - 集合: users, user_list

函数命名:
  - 动词开头: get_user, create_order
  - 返回布尔: is_valid, can_delete
  - 转换函数: to_string, parse_json
  - 事件处理: on_click, handle_error

类命名:
  - 名词: User, OrderService
  - 避免Manager后缀: 使用具体职责
  - 接口不加I前缀: Repository 而非 IRepository
```

---

## 四、注释标准

### 4.1 文档注释

```yaml
Python (Google风格):
  def get_user(user_id: int) -> User:
      """获取用户信息
      
      Args:
          user_id: 用户ID
      
      Returns:
          User: 用户对象
      
      Raises:
          UserNotFoundError: 用户不存在
      """

JavaScript (JSDoc):
  /**
   * 获取用户信息
   * @param {number} userId - 用户ID
   * @returns {User} 用户对象
   * @throws {UserNotFoundError} 用户不存在
   */
```

### 4.2 代码注释

```yaml
注释原则:
  - 解释为什么，而非是什么
  - 避免冗余注释
  - 保持注释与代码同步
  - 使用TODO标记待办事项

禁止注释:
  - 注释掉的代码
  - 无意义的注释
  - 过时的注释
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Optional
from enum import Enum

class Language(Enum):
    """编程语言"""
    PYTHON = "python"
    JAVASCRIPT = "javascript"
    GO = "go"
    RUST = "rust"
    JAVA = "java"

@dataclass
class StyleConfig:
    """风格配置"""
    indent_size: int
    indent_type: str
    max_line_length: int
    quote_type: str

class CodeStyleChecker:
    """代码风格检查器"""
    
    STYLE_CONFIGS = {
        Language.PYTHON: StyleConfig(4, "space", 100, "double"),
        Language.JAVASCRIPT: StyleConfig(2, "space", 100, "single"),
        Language.GO: StyleConfig(1, "tab", 100, "double"),
        Language.RUST: StyleConfig(4, "space", 100, "double"),
        Language.JAVA: StyleConfig(4, "space", 100, "double"),
    }
    
    def check_indent(self, code: str, language: Language) -> List[str]:
        """检查缩进"""
        issues = []
        config = self.STYLE_CONFIGS[language]
        lines = code.split('\n')
        
        for i, line in enumerate(lines, 1):
            if not line:
                continue
            
            leading = len(line) - len(line.lstrip())
            
            if config.indent_type == "space":
                if '\t' in line[:leading]:
                    issues.append(f"行 {i}: 应使用空格缩进")
                if leading % config.indent_size != 0:
                    issues.append(f"行 {i}: 缩进应为 {config.indent_size} 的倍数")
            else:
                if ' ' in line[:leading]:
                    issues.append(f"行 {i}: 应使用Tab缩进")
        
        return issues
    
    def check_line_length(self, code: str, language: Language) -> List[str]:
        """检查行长度"""
        issues = []
        config = self.STYLE_CONFIGS[language]
        lines = code.split('\n')
        
        for i, line in enumerate(lines, 1):
            if len(line) > config.max_line_length:
                issues.append(
                    f"行 {i}: 行长度 {len(line)} 超过最大值 {config.max_line_length}"
                )
        
        return issues

class NamingChecker:
    """命名检查器"""
    
    def check_function_name(self, name: str, language: Language) -> List[str]:
        """检查函数命名"""
        issues = []
        
        if language == Language.PYTHON:
            if not name.islower() and '_' not in name:
                if name != name.lower():
                    issues.append(f"函数名 '{name}' 应使用 snake_case")
        
        elif language in [Language.JAVASCRIPT, Language.JAVA]:
            if name[0].isupper():
                issues.append(f"函数名 '{name}' 应使用 camelCase")
        
        return issues
    
    def check_class_name(self, name: str) -> List[str]:
        """检查类命名"""
        issues = []
        
        if name[0].islower():
            issues.append(f"类名 '{name}' 应使用 PascalCase")
        
        if 'Manager' in name:
            issues.append(f"类名 '{name}' 避免使用 Manager 后缀")
        
        return issues

class CodeGenerator:
    """代码生成器"""
    
    def __init__(self, language: Language):
        self.language = language
        self.style_checker = CodeStyleChecker()
        self.naming_checker = NamingChecker()
    
    def generate_with_check(self, template: str, 
                           context: Dict) -> str:
        """生成代码并检查"""
        code = self._render_template(template, context)
        
        style_issues = self.style_checker.check_indent(code, self.language)
        style_issues.extend(self.style_checker.check_line_length(code, self.language))
        
        if style_issues:
            code = self._auto_fix_style(code, style_issues)
        
        return code
    
    def _render_template(self, template: str, context: Dict) -> str:
        """渲染模板"""
        pass
    
    def _auto_fix_style(self, code: str, issues: List[str]) -> str:
        """自动修复风格"""
        pass
```

---

## 六、检查清单

```yaml
代码风格:
  □ 缩进正确？
  □ 行长度合规？
  □ 空行规范？
  □ 括号风格一致？

命名规范:
  □ 变量命名有意义？
  □ 函数命名动词开头？
  □ 类命名PascalCase？
  □ 常量UPPER_SNAKE_CASE？

注释标准:
  □ 文档注释完整？
  □ 注释有意义？
  □ 无注释掉的代码？
  □ 注释与代码同步？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           代码生成速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  缩进规范:                                   │
│    Python: 4空格                            │
│    JS/TS: 2空格                             │
│    Go: Tab                                 │
│    Rust: 4空格                              │
│                                             │
│  命名规范:                                   │
│    变量: snake_case / camelCase            │
│    函数: 动词开头                           │
│    类: PascalCase                          │
│    常量: UPPER_SNAKE_CASE                  │
│                                             │
│  注释规范:                                   │
│    文档: 解释参数和返回值                   │
│    代码: 解释为什么                         │
│    禁止: 注释掉的代码                       │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：代码如诗，规范为先！
