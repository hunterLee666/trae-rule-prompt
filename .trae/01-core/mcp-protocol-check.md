---
name: MCP Protocol Check
description: MCP协议检查触发器 - 工具集成时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_tool_integration"
---

# MCP协议检查触发器

> **⚠️ 建议：集成工具时应遵循MCP协议规范**

---

## 一、触发条件

```yaml
触发场景:
  - 开发新工具
  - 集成外部工具
  - 设计工具接口
  - 创建工具服务器

不触发场景:
  - 使用已有工具
  - 用户明确跳过
```

---

## 二、协议概述

```yaml
MCP (Model Context Protocol):
  定义: 标准化的工具集成协议
  目标: 统一工具调用接口
  优势: 可扩展、可组合、可维护

核心概念:
  Tool:
    - 单一功能单元
    - 标准输入输出
    - 独立可测试
  
  Server:
    - 工具集合
    - 资源管理
    - 能力声明
  
  Client:
    - 工具发现
    - 工具调用
    - 结果处理
```

---

## 三、工具定义规范

### 3.1 工具结构

```yaml
工具结构:
  name: 工具名称 (必填)
    - 唯一标识
    - 小写字母和下划线
    - 描述性命名
  
  description: 工具描述 (必填)
    - 功能说明
    - 使用场景
    - 注意事项
  
  inputSchema: 输入参数模式 (必填)
    - JSON Schema格式
    - 参数类型定义
    - 参数验证规则
  
  outputSchema: 输出结果模式 (可选)
    - 返回值类型
    - 结果格式定义
```

### 3.2 工具示例

```json
{
  "name": "read_file",
  "description": "读取文件内容，支持指定行范围",
  "inputSchema": {
    "type": "object",
    "properties": {
      "file_path": {
        "type": "string",
        "description": "文件的绝对路径"
      },
      "limit": {
        "type": "integer",
        "description": "读取的最大行数",
        "minimum": 1,
        "maximum": 1000
      },
      "offset": {
        "type": "integer",
        "description": "起始行偏移量",
        "default": 0
      }
    },
    "required": ["file_path", "limit"]
  }
}
```

---

## 四、工具调用规范

### 4.1 调用流程

```yaml
标准调用流程:
  1. 参数验证:
     - 检查必填参数
     - 验证参数类型
     - 验证参数范围
  
  2. 权限检查:
     - 检查访问权限
     - 检查资源限制
  
  3. 执行操作:
     - 执行核心逻辑
     - 处理异常情况
  
  4. 返回结果:
     - 格式化输出
     - 包含状态信息
```

### 4.2 返回格式

```yaml
成功响应:
  status: "success"
  data: <结果数据>
  metadata:
    execution_time: <执行时间>
    resource_usage: <资源使用>

错误响应:
  status: "error"
  error:
    code: <错误代码>
    message: <错误信息>
    details: <错误详情>
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import Dict, Any, Optional, List
from enum import Enum
import json

class ToolStatus(Enum):
    """工具状态"""
    SUCCESS = "success"
    ERROR = "error"

@dataclass
class ToolResult:
    """工具结果"""
    status: ToolStatus
    data: Any = None
    error: Optional[Dict] = None
    metadata: Optional[Dict] = None

class MCPTool:
    """MCP工具基类"""
    
    name: str = ""
    description: str = ""
    input_schema: Dict = {}
    
    def __init__(self):
        self._validate_definition()
    
    def _validate_definition(self):
        """验证工具定义"""
        if not self.name:
            raise ValueError("工具名称不能为空")
        if not self.description:
            raise ValueError("工具描述不能为空")
        if not self.input_schema:
            raise ValueError("输入模式不能为空")
    
    def validate_input(self, params: Dict) -> bool:
        """验证输入参数"""
        required = self.input_schema.get("required", [])
        for field in required:
            if field not in params:
                raise ValueError(f"缺少必填参数: {field}")
        return True
    
    def execute(self, **params) -> ToolResult:
        """执行工具"""
        try:
            self.validate_input(params)
            result = self._run(**params)
            return ToolResult(
                status=ToolStatus.SUCCESS,
                data=result
            )
        except Exception as e:
            return ToolResult(
                status=ToolStatus.ERROR,
                error={
                    "code": "EXECUTION_ERROR",
                    "message": str(e)
                }
            )
    
    def _run(self, **params) -> Any:
        """实际执行逻辑 - 子类实现"""
        raise NotImplementedError

class ReadFileTool(MCPTool):
    """读取文件工具"""
    
    name = "read_file"
    description = "读取文件内容"
    input_schema = {
        "type": "object",
        "properties": {
            "file_path": {"type": "string"},
            "limit": {"type": "integer", "minimum": 1}
        },
        "required": ["file_path", "limit"]
    }
    
    def _run(self, file_path: str, limit: int, offset: int = 0) -> str:
        """读取文件"""
        with open(file_path, 'r') as f:
            lines = f.readlines()
            return ''.join(lines[offset:offset + limit])

class MCPServer:
    """MCP服务器"""
    
    def __init__(self):
        self._tools: Dict[str, MCPTool] = {}
    
    def register(self, tool: MCPTool):
        """注册工具"""
        self._tools[tool.name] = tool
    
    def list_tools(self) -> List[Dict]:
        """列出所有工具"""
        return [
            {
                "name": t.name,
                "description": t.description,
                "inputSchema": t.input_schema
            }
            for t in self._tools.values()
        ]
    
    def call_tool(self, name: str, params: Dict) -> ToolResult:
        """调用工具"""
        tool = self._tools.get(name)
        if not tool:
            return ToolResult(
                status=ToolStatus.ERROR,
                error={"code": "TOOL_NOT_FOUND", "message": f"工具不存在: {name}"}
            )
        return tool.execute(**params)
```

---

## 六、检查清单

```yaml
工具定义:
  □ 名称规范？
  □ 描述清晰？
  □ 输入模式完整？
  □ 输出格式标准？

工具实现:
  □ 参数验证完善？
  □ 错误处理完整？
  □ 返回格式标准？
  □ 文档完整？

工具集成:
  □ 注册机制正确？
  □ 发现机制有效？
  □ 调用机制可靠？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           MCP协议速查表                      │
├─────────────────────────────────────────────┤
│                                             │
│  工具定义:                                   │
│    □ name (唯一标识)                        │
│    □ description (功能说明)                 │
│    □ inputSchema (参数模式)                 │
│                                             │
│  调用流程:                                   │
│    1. 参数验证                              │
│    2. 权限检查                              │
│    3. 执行操作                              │
│    4. 返回结果                              │
│                                             │
│  返回格式:                                   │
│    成功: {status: "success", data: ...}     │
│    失败: {status: "error", error: ...}      │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：标准接口，无限扩展！
