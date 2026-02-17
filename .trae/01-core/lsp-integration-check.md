---
name: LSP Integration Check
description: LSP集成检查触发器 - 代码理解时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_code_understanding"
---

# LSP集成检查触发器

> **⚠️ 建议：理解代码时应使用LSP能力**

---

## 一、触发条件

```yaml
触发场景:
  - 需要理解代码结构
  - 需要追踪依赖关系
  - 需要进行重构
  - 需要检查代码质量

不触发场景:
  - 简单文本搜索
  - 用户明确跳过
```

---

## 二、LSP能力

```yaml
核心能力:
  lsp_goto_definition:
    用途: 跳转到定义
    场景: 理解函数/类的来源
    返回: 定义位置和内容
    
  lsp_find_references:
    用途: 查找所有引用
    场景: 重构前影响分析
    返回: 所有引用位置
    
  lsp_symbols:
    用途: 提取文件符号
    场景: 快速了解文件结构
    返回: 符号列表和位置
    
  lsp_diagnostics:
    用途: 获取诊断信息
    场景: 代码质量检查
    返回: 错误和警告列表
    
  lsp_rename:
    用途: 安全重命名
    场景: 批量重命名变量/函数
    返回: 所有修改位置
```

---

## 三、使用场景

### 3.1 代码探索

```yaml
探索流程:
  1. lsp_symbols(file):
     目的: 了解文件的整体结构
     输出: 主要函数、类、变量列表
  
  2. lsp_goto_definition(symbol):
     目的: 深入理解具体实现
     输出: 定义位置和内容
  
  3. lsp_find_references(symbol):
     目的: 了解使用情况
     输出: 所有引用位置

最佳实践:
  - 先用symbols获取全局视图
  - 再用goto深入细节
  - 最后用references评估影响
```

### 3.2 安全重构

```yaml
重构流程:
  1. lsp_find_references(symbol):
     目的: 找到所有引用位置
     输出: 影响范围评估
  
  2. lsp_rename(symbol, new_name):
     目的: 安全重命名
     输出: 自动更新所有引用
  
  3. lsp_diagnostics(file):
     目的: 验证重构结果
     输出: 检查是否有遗漏

最佳实践:
  - 重构前必须检查引用
  - 使用rename而非手动修改
  - 重构后必须验证
```

### 3.3 质量检查

```yaml
检查流程:
  1. lsp_diagnostics(file):
     目的: 获取所有诊断信息
     输出: 错误和警告列表
  
  2. 分析诊断结果:
     - Error: 必须修复
     - Warning: 建议修复
     - Info: 可选优化
  
  3. 修复问题:
     - 按优先级处理
     - 验证修复结果

最佳实践:
  - 修改后立即检查诊断
  - 优先处理Error级别
  - 保持无Error状态
```

---

## 四、LSP vs 传统方法

```yaml
LSP优势:
  语义理解:
    LSP: 理解代码语义，精确定位
    grep: 文本匹配，可能误报
  
  跨文件追踪:
    LSP: 自动追踪跨文件引用
    grep: 需要手动搜索多个文件
  
  实时诊断:
    LSP: 实时获取编译器诊断
    grep: 无法获取诊断信息
  
  安全重构:
    LSP: 自动更新所有引用
    手动: 容易遗漏

何时使用grep:
  - 搜索注释内容
  - 搜索字符串字面量
  - 搜索非代码文件
  - LSP不可用时
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Any, Optional
from enum import Enum

class DiagnosticSeverity(Enum):
    """诊断严重程度"""
    ERROR = 1
    WARNING = 2
    INFORMATION = 3
    HINT = 4

@dataclass
class Diagnostic:
    """诊断信息"""
    file: str
    line: int
    column: int
    severity: DiagnosticSeverity
    message: str
    source: Optional[str] = None

@dataclass
class Symbol:
    """符号信息"""
    name: str
    kind: str
    line: int
    column: int
    file: str

@dataclass
class Reference:
    """引用信息"""
    file: str
    line: int
    column: int
    text: str

class LSPIntegration:
    """LSP集成"""
    
    def get_symbols(self, file_path: str) -> List[Symbol]:
        """获取文件符号"""
        pass
    
    def goto_definition(self, file_path: str, 
                       line: int, column: int) -> Optional[Symbol]:
        """跳转到定义"""
        pass
    
    def find_references(self, file_path: str,
                       line: int, column: int) -> List[Reference]:
        """查找引用"""
        pass
    
    def get_diagnostics(self, file_path: str) -> List[Diagnostic]:
        """获取诊断"""
        pass
    
    def rename_symbol(self, file_path: str, line: int, 
                     column: int, new_name: str) -> List[str]:
        """重命名符号"""
        pass

class LSPCodeExplorer:
    """LSP代码探索器"""
    
    def __init__(self, lsp: LSPIntegration):
        self.lsp = lsp
    
    def explore_file(self, file_path: str) -> Dict:
        """探索文件"""
        symbols = self.lsp.get_symbols(file_path)
        diagnostics = self.lsp.get_diagnostics(file_path)
        
        return {
            "file": file_path,
            "symbols": [s.__dict__ for s in symbols],
            "diagnostics": [d.__dict__ for d in diagnostics],
            "symbol_count": len(symbols),
            "error_count": sum(1 for d in diagnostics 
                             if d.severity == DiagnosticSeverity.ERROR)
        }
    
    def understand_symbol(self, file_path: str, 
                         symbol_name: str) -> Dict:
        """理解符号"""
        symbols = self.lsp.get_symbols(file_path)
        target = None
        
        for s in symbols:
            if s.name == symbol_name:
                target = s
                break
        
        if not target:
            return {"error": f"Symbol {symbol_name} not found"}
        
        definition = self.lsp.goto_definition(
            file_path, target.line, target.column
        )
        references = self.lsp.find_references(
            file_path, target.line, target.column
        )
        
        return {
            "symbol": target.__dict__,
            "definition": definition.__dict__ if definition else None,
            "reference_count": len(references),
            "references": [r.__dict__ for r in references[:10]]
        }

class LSPRefactoringAssistant:
    """LSP重构助手"""
    
    def __init__(self, lsp: LSPIntegration):
        self.lsp = lsp
    
    def safe_rename(self, file_path: str, symbol_name: str,
                   new_name: str) -> Dict:
        """安全重命名"""
        symbols = self.lsp.get_symbols(file_path)
        target = None
        
        for s in symbols:
            if s.name == symbol_name:
                target = s
                break
        
        if not target:
            return {"error": f"Symbol {symbol_name} not found"}
        
        references = self.lsp.find_references(
            file_path, target.line, target.column
        )
        
        affected_files = self.lsp.rename_symbol(
            file_path, target.line, target.column, new_name
        )
        
        return {
            "old_name": symbol_name,
            "new_name": new_name,
            "reference_count": len(references),
            "affected_files": affected_files
        }
```

---

## 六、检查清单

```yaml
代码探索:
  □ 使用symbols获取结构？
  □ 使用goto理解定义？
  □ 使用references评估影响？

安全重构:
  □ 重构前检查引用？
  □ 使用rename而非手动？
  □ 重构后验证诊断？

质量检查:
  □ 修改后检查诊断？
  □ 优先处理Error？
  □ 保持无Error状态？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           LSP集成速查表                      │
├─────────────────────────────────────────────┤
│                                             │
│  核心能力:                                   │
│    symbols: 获取文件结构                    │
│    goto_definition: 跳转定义                │
│    find_references: 查找引用                │
│    diagnostics: 获取诊断                    │
│    rename: 安全重命名                       │
│                                             │
│  使用场景:                                   │
│    探索: symbols → goto → references        │
│    重构: references → rename → diagnostics  │
│    检查: diagnostics → 修复 → 验证          │
│                                             │
│  LSP vs grep:                               │
│    LSP: 语义理解、跨文件、实时诊断           │
│    grep: 文本搜索、注释、非代码文件          │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：语义理解，超越文本！
