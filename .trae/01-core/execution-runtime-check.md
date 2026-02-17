---
name: Execution Runtime Check
description: 执行运行时检查触发器 - 执行操作时建议执行
version: "1.0"
priority: 95
alwaysApply: true
trigger: "during_execution"
---

# 执行运行时检查触发器

> **⚠️ 建议：执行操作时应遵循安全原则**

---

## 一、触发条件

```yaml
触发场景:
  - 执行文件操作
  - 执行系统命令
  - 执行网络请求
  - 执行代码分析

不触发场景:
  - 用户明确跳过
```

---

## 二、执行能力

```yaml
文件操作:
  - read: 读取文件
  - write: 写入文件
  - edit: 编辑文件
  - delete: 删除文件
  - glob: 查找文件
  - grep: 搜索内容
  
代码分析:
  - lsp_goto_definition: 跳转定义
  - lsp_find_references: 查找引用
  - lsp_symbols: 符号提取
  - lsp_diagnostics: 诊断检查
  - search_codebase: 语义搜索
  
系统命令:
  - bash: 执行命令
  - interactive_bash: 交互式命令
  
网络操作:
  - webfetch: 获取网页
  - websearch: 网络搜索
```

---

## 三、执行原则

### 3.1 安全原则

```yaml
安全原则:
  1. 最小权限: 只执行必要操作
  2. 验证优先: 修改后立即验证
  3. 可回滚: 保持操作可撤销
  4. 审计追踪: 记录所有操作

权限检查:
  文件操作:
    - 检查文件路径合法性
    - 检查文件权限
    - 检查磁盘空间
  
  命令执行:
    - 检查命令白名单
    - 检查参数安全性
    - 检查执行环境
```

### 3.2 效率原则

```yaml
效率原则:
  1. 并行优先: 独立操作并行执行
  2. 批量处理: 合并相似操作
  3. 缓存复用: 避免重复读取
  4. 延迟加载: 按需获取数据

优化策略:
  文件读取:
    - 大文件分批读取
    - 优先读取关键部分
    - 缓存已读取内容
  
  搜索操作:
    - 先精确后模糊
    - 限制搜索范围
    - 使用索引加速
```

---

## 四、文件操作规范

### 4.1 读取文件

```yaml
read操作:
  用途: 读取文件内容
  参数:
    file_path: 文件路径(绝对路径)
    limit: 读取行数(可选)
    offset: 起始行(可选)
    
  最佳实践:
    - 使用绝对路径
    - 大文件分批读取
    - 优先读取关键部分

  错误处理:
    文件不存在: 提示用户确认路径
    权限不足: 提示权限问题
    文件过大: 建议分批读取
```

### 4.2 写入文件

```yaml
write操作:
  用途: 写入文件内容
  参数:
    file_path: 文件路径(绝对路径)
    content: 文件内容
    
  最佳实践:
    - 先读取后写入
    - 备份重要文件
    - 验证写入结果

  错误处理:
    权限不足: 提示权限问题
    磁盘空间不足: 提示清理空间
    路径不存在: 提示创建目录
```

### 4.3 编辑文件

```yaml
edit操作:
  用途: 编辑文件内容
  参数:
    file_path: 文件路径
    old_str: 要替换的内容
    new_str: 替换后的内容
    
  最佳实践:
    - 精确匹配替换内容
    - 包含上下文确保唯一
    - 替换后验证结果

  错误处理:
    内容不存在: 提示检查原文
    多处匹配: 提示精确定位
```

---

## 五、命令执行规范

### 5.1 命令白名单

```yaml
安全命令:
  文件操作:
    - ls, cat, head, tail
    - mkdir, cp, mv, rm (需确认)
    - find, grep, sed, awk
  
  开发工具:
    - git, npm, pip, cargo
    - python, node, go, rustc
    - make, cmake, gradle

  系统信息:
    - pwd, whoami, date
    - ps, top, htop
    - df, du, free

危险命令 (需确认):
  - rm -rf
  - sudo
  - chmod 777
  - > /dev/sda
```

### 5.2 执行流程

```yaml
标准流程:
  1. 验证命令:
     - 检查命令格式
     - 检查参数安全
     - 检查执行权限
  
  2. 执行命令:
     - 设置超时时间
     - 捕获输出和错误
     - 处理执行结果
  
  3. 处理结果:
     - 解析命令输出
     - 处理错误信息
     - 返回执行结果
```

---

## 六、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Any, Optional, Callable
from enum import Enum
import os

class OperationType(Enum):
    """操作类型"""
    READ = "read"
    WRITE = "write"
    EDIT = "edit"
    DELETE = "delete"
    EXECUTE = "execute"

@dataclass
class ExecutionResult:
    """执行结果"""
    success: bool
    output: Any
    error: Optional[str] = None
    duration: float = 0.0

class ExecutionRuntime:
    """执行运行时"""
    
    SAFE_COMMANDS = [
        "ls", "cat", "head", "tail", "pwd",
        "git", "npm", "pip", "python", "node",
        "find", "grep"
    ]
    
    DANGEROUS_COMMANDS = [
        "rm -rf", "sudo", "chmod 777",
        "> /dev/", "mkfs"
    ]
    
    def __init__(self):
        self.operation_log: List[Dict] = []
    
    def execute_file_operation(self, op_type: OperationType,
                               file_path: str, **kwargs) -> ExecutionResult:
        """执行文件操作"""
        if not self._validate_path(file_path):
            return ExecutionResult(
                success=False,
                output=None,
                error=f"路径无效: {file_path}"
            )
        
        self._log_operation(op_type, file_path, kwargs)
        
        try:
            if op_type == OperationType.READ:
                return self._read_file(file_path, **kwargs)
            elif op_type == OperationType.WRITE:
                return self._write_file(file_path, **kwargs)
            elif op_type == OperationType.EDIT:
                return self._edit_file(file_path, **kwargs)
            elif op_type == OperationType.DELETE:
                return self._delete_file(file_path)
            else:
                return ExecutionResult(
                    success=False,
                    output=None,
                    error=f"未知操作类型: {op_type}"
                )
        except Exception as e:
            return ExecutionResult(
                success=False,
                output=None,
                error=str(e)
            )
    
    def execute_command(self, command: str, 
                       timeout: int = 30) -> ExecutionResult:
        """执行命令"""
        if not self._validate_command(command):
            return ExecutionResult(
                success=False,
                output=None,
                error=f"命令不安全: {command}"
            )
        
        self._log_operation(OperationType.EXECUTE, command, {})
        
        try:
            import subprocess
            import time
            
            start = time.time()
            result = subprocess.run(
                command,
                shell=True,
                capture_output=True,
                text=True,
                timeout=timeout
            )
            duration = time.time() - start
            
            return ExecutionResult(
                success=result.returncode == 0,
                output=result.stdout,
                error=result.stderr if result.returncode != 0 else None,
                duration=duration
            )
        except subprocess.TimeoutExpired:
            return ExecutionResult(
                success=False,
                output=None,
                error=f"命令执行超时 ({timeout}s)"
            )
    
    def _validate_path(self, path: str) -> bool:
        """验证路径"""
        if not os.path.isabs(path):
            return False
        if '..' in path:
            return False
        return True
    
    def _validate_command(self, command: str) -> bool:
        """验证命令"""
        for dangerous in self.DANGEROUS_COMMANDS:
            if dangerous in command:
                return False
        return True
    
    def _log_operation(self, op_type: OperationType, 
                      target: str, params: Dict):
        """记录操作"""
        import datetime
        self.operation_log.append({
            "timestamp": datetime.datetime.utcnow().isoformat(),
            "type": op_type.value,
            "target": target,
            "params": params
        })
    
    def _read_file(self, path: str, **kwargs) -> ExecutionResult:
        """读取文件"""
        limit = kwargs.get('limit', 2000)
        offset = kwargs.get('offset', 0)
        
        with open(path, 'r') as f:
            lines = f.readlines()
            content = ''.join(lines[offset:offset + limit])
        
        return ExecutionResult(success=True, output=content)
    
    def _write_file(self, path: str, **kwargs) -> ExecutionResult:
        """写入文件"""
        content = kwargs.get('content', '')
        
        with open(path, 'w') as f:
            f.write(content)
        
        return ExecutionResult(success=True, output="写入成功")
    
    def _edit_file(self, path: str, **kwargs) -> ExecutionResult:
        """编辑文件"""
        old_str = kwargs.get('old_str')
        new_str = kwargs.get('new_str')
        
        with open(path, 'r') as f:
            content = f.read()
        
        if old_str not in content:
            return ExecutionResult(
                success=False,
                output=None,
                error="未找到要替换的内容"
            )
        
        new_content = content.replace(old_str, new_str, 1)
        
        with open(path, 'w') as f:
            f.write(new_content)
        
        return ExecutionResult(success=True, output="编辑成功")
    
    def _delete_file(self, path: str) -> ExecutionResult:
        """删除文件"""
        os.remove(path)
        return ExecutionResult(success=True, output="删除成功")
```

---

## 七、检查清单

```yaml
安全检查:
  □ 路径验证通过？
  □ 命令验证通过？
  □ 权限检查通过？
  □ 操作已记录？

效率检查:
  □ 并行执行优化？
  □ 缓存使用合理？
  □ 批量处理应用？
  □ 超时设置合理？

错误处理:
  □ 错误捕获完善？
  □ 错误信息清晰？
  □ 回滚机制存在？
```

---

## 八、快速检查卡

```
┌─────────────────────────────────────────────┐
│           执行运行时速查表                   │
├─────────────────────────────────────────────┤
│                                             │
│  安全原则:                                   │
│    □ 最小权限                               │
│    □ 验证优先                               │
│    □ 可回滚                                 │
│    □ 审计追踪                               │
│                                             │
│  效率原则:                                   │
│    □ 并行优先                               │
│    □ 批量处理                               │
│    □ 缓存复用                               │
│    □ 延迟加载                               │
│                                             │
│  危险命令:                                   │
│    rm -rf / sudo / chmod 777               │
│    需要用户确认后执行                       │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：安全执行，可靠结果！
