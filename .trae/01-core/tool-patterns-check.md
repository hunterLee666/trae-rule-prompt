---
name: Tool Patterns Check
description: 工具模式检查触发器 - 工具调用时建议执行
version: "1.0"
priority: 95
alwaysApply: true
trigger: "during_tool_call"
---

# 工具模式检查触发器

> **⚠️ 建议：调用工具时应遵循最佳实践**

---

## 一、触发条件

```yaml
触发场景:
  - 调用任何工具前
  - 规划多工具调用
  - 工具调用失败后

不触发场景:
  - 用户明确跳过
```

---

## 二、工具选择策略

### 2.1 选择原则

```yaml
选择原则:
  1. 精确匹配: 选择最适合的工具
  2. 最小化: 使用最少的工具调用
  3. 可靠性: 优先使用稳定工具
  4. 效率: 考虑执行时间和资源

工具分类:
  探索类:
    - glob: 文件查找
    - grep: 内容搜索
    - search_codebase: 语义搜索
    
  理解类:
    - read: 读取内容
    - lsp_symbols: 符号提取
    - lsp_goto_definition: 跳转定义
    
  修改类:
    - write: 写入文件
    - edit: 编辑文件
    - delete: 删除文件
    
  验证类:
    - lsp_diagnostics: 诊断检查
    - bash: 运行测试
```

### 2.2 工具优先级

```yaml
搜索优先级:
  1. search_codebase: 语义搜索，最智能
  2. grep: 精确模式匹配
  3. glob: 文件名模式匹配

读取优先级:
  1. read: 直接读取
  2. lsp_symbols: 结构化读取

修改优先级:
  1. edit: 精确修改
  2. write: 完整重写
```

---

## 三、并行vs串行

### 3.1 并行执行

```yaml
适用场景:
  - 操作之间无依赖
  - 读取多个独立文件
  - 搜索多个模式

并行示例:
  同时执行:
    - glob("*.py")
    - glob("*.js")
    - grep("TODO")
    
  优势:
    - 减少总执行时间
    - 提高效率

并行规则:
  - 最多同时5个工具调用
  - 确保操作间无依赖
  - 收集所有结果后处理
```

### 3.2 串行执行

```yaml
适用场景:
  - 操作之间有依赖
  - 后续操作依赖前序结果
  - 修改后需要验证

串行示例:
  顺序执行:
    1. read(file) → 获取内容
    2. edit(file) → 修改内容
    3. lsp_diagnostics(file) → 验证修改

串行规则:
  - 有依赖必须串行
  - 修改操作后验证
  - 错误时停止后续
```

---

## 四、错误处理

### 4.1 错误分类

```yaml
文件错误:
  文件不存在:
    处理: 检查路径，提示用户
  权限不足:
    处理: 提示权限问题
  路径无效:
    处理: 验证路径格式

执行错误:
  命令执行失败:
    处理: 分析错误，重试或跳过
  超时:
    处理: 增加超时时间或简化操作
  资源不足:
    处理: 释放资源，分批执行

验证错误:
  诊断检查失败:
    处理: 修复问题，重新验证
  测试失败:
    处理: 分析失败原因，修复代码
```

### 4.2 重试策略

```yaml
重试规则:
  可重试错误:
    - 网络超时
    - 临时资源不足
    - 服务暂时不可用
  
  不可重试错误:
    - 文件不存在
    - 权限不足
    - 语法错误

重试配置:
  最大重试次数: 3
  重试间隔: 1s, 2s, 4s (指数退避)
  超时时间: 30s
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Any, Optional
from enum import Enum

class ExecutionMode(Enum):
    """执行模式"""
    PARALLEL = "parallel"
    SEQUENTIAL = "sequential"

@dataclass
class ToolCall:
    """工具调用"""
    name: str
    params: Dict[str, Any]
    dependencies: List[str] = None

class ToolPatternOptimizer:
    """工具模式优化器"""
    
    def analyze_dependencies(self, calls: List[ToolCall]) -> Dict:
        """分析依赖关系"""
        graph = {}
        for call in calls:
            deps = call.dependencies or []
            graph[call.name] = deps
        return graph
    
    def determine_execution_mode(self, calls: List[ToolCall]) -> ExecutionMode:
        """确定执行模式"""
        graph = self.analyze_dependencies(calls)
        
        for call in calls:
            if call.dependencies:
                return ExecutionMode.SEQUENTIAL
        
        return ExecutionMode.PARALLEL
    
    def optimize_order(self, calls: List[ToolCall]) -> List[List[ToolCall]]:
        """优化执行顺序"""
        mode = self.determine_execution_mode(calls)
        
        if mode == ExecutionMode.PARALLEL:
            return [calls]
        
        result = []
        remaining = list(calls)
        completed = set()
        
        while remaining:
            batch = []
            for call in remaining[:]:
                deps = call.dependencies or []
                if all(d in completed for d in deps):
                    batch.append(call)
                    remaining.remove(call)
            
            if batch:
                result.append(batch)
                completed.update(c.name for c in batch)
            else:
                result.append([remaining.pop(0)])
        
        return result

class ToolCallValidator:
    """工具调用验证器"""
    
    MAX_PARALLEL_CALLS = 5
    
    def validate_parallel_calls(self, calls: List[ToolCall]) -> bool:
        """验证并行调用数量"""
        return len(calls) <= self.MAX_PARALLEL_CALLS
    
    def validate_no_write_conflict(self, calls: List[ToolCall]) -> bool:
        """验证无写冲突"""
        write_targets = set()
        for call in calls:
            if call.name in ["write", "edit", "delete"]:
                target = call.params.get("file_path")
                if target in write_targets:
                    return False
                write_targets.add(target)
        return True
    
    def validate(self, calls: List[ToolCall]) -> Dict:
        """完整验证"""
        return {
            "parallel_ok": self.validate_parallel_calls(calls),
            "no_conflict": self.validate_no_write_conflict(calls)
        }
```

---

## 六、检查清单

```yaml
工具选择:
  □ 选择最合适的工具？
  □ 调用次数最小化？
  □ 工具可靠性考虑？

执行模式:
  □ 并行/串行选择正确？
  □ 依赖关系处理正确？
  □ 并行数量限制遵守？

错误处理:
  □ 错误分类正确？
  □ 重试策略合理？
  □ 回退方案存在？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           工具模式速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  工具选择:                                   │
│    搜索 → search_codebase > grep > glob     │
│    读取 → read > lsp_symbols                │
│    修改 → edit > write                      │
│                                             │
│  执行模式:                                   │
│    并行: 无依赖操作，最多5个                 │
│    串行: 有依赖操作，顺序执行                │
│                                             │
│  错误处理:                                   │
│    可重试: 超时/资源不足                     │
│    不可重试: 文件不存在/权限不足             │
│    重试: 3次，指数退避                       │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：正确工具，正确方式！
