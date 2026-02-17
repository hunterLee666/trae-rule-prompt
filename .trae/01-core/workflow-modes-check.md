---
name: Workflow Modes Check
description: Plan/Build模式检查触发器 - 任务执行前必须执行
version: "1.0"
priority: 100
alwaysApply: true
trigger: "before_task_execution"
---

# Plan/Build模式检查触发器

> **⚠️ 强制规则：执行任务前必须确定工作模式**

---

## 一、触发条件

```yaml
触发场景:
  - 接收用户请求
  - 开始执行任务
  - 切换任务类型

不触发场景:
  - 用户明确跳过
```

---

## 二、模式识别

### 2.1 Plan模式触发

```yaml
关键词触发:
  - "分析..."
  - "设计..."
  - "计划..."
  - "如何..."
  - "评估..."
  - "审查..."
  - "比较..."
  - "为什么..."
  - "趋势..."
  - "预测..."

场景触发:
  - 需要理解现有代码
  - 需要制定实施方案
  - 需要评估多个选项
  - 需要分析问题根因
  - 复杂问题分析
```

### 2.2 Build模式触发

```yaml
关键词触发:
  - "实现..."
  - "修复..."
  - "添加..."
  - "创建..."
  - "删除..."
  - "修改..."
  - "重构..."
  - "更新..."

场景触发:
  - 需要修改代码
  - 需要执行命令
  - 需要创建文件
  - 需要运行测试
```

---

## 三、模式约束

### 3.1 Plan模式约束

```yaml
允许操作:
  ✅ read - 读取文件
  ✅ glob - 查找文件
  ✅ grep - 搜索内容
  ✅ lsp_* - LSP分析
  ✅ websearch - 网络搜索
  ✅ webfetch - 获取网页
  ✅ search_codebase - 代码库搜索

禁止操作:
  ❌ write - 写入文件
  ❌ edit - 编辑文件
  ❌ delete - 删除文件
  ❌ bash (修改性) - 修改性命令

输出要求:
  - 分析报告
  - 设计方案
  - 实施计划
  - 风险评估
```

### 3.2 Build模式约束

```yaml
允许操作:
  ✅ read - 读取文件
  ✅ write - 写入文件
  ✅ edit - 编辑文件
  ✅ delete - 删除文件
  ✅ bash - 执行命令
  ✅ 所有Plan模式操作

执行要求:
  - 先读取后修改
  - 修改后验证
  - 错误时回滚
  - 完成后报告

输出要求:
  - 代码变更
  - 测试结果
  - 验证报告
```

---

## 四、模式切换

```yaml
切换规则:
  Plan → Build:
    触发: 用户确认方案后
    要求: 展示完整计划
    动作: 开始执行修改
  
  Build → Plan:
    触发: 遇到不确定问题
    要求: 暂停修改
    动作: 分析问题，制定新方案

禁止切换:
  - Plan模式直接执行修改
  - Build模式跳过验证
```

---

## 五、实现示例

```python
from enum import Enum
from typing import List, Optional

class WorkflowMode(Enum):
    """工作流模式"""
    PLAN = "plan"
    BUILD = "build"

class WorkflowModeDetector:
    """工作流模式检测器"""
    
    PLAN_KEYWORDS = [
        "分析", "设计", "计划", "如何", "评估", 
        "审查", "比较", "为什么", "趋势", "预测",
        "analyze", "design", "plan", "how", "evaluate",
        "review", "compare", "why", "trend", "predict"
    ]
    
    BUILD_KEYWORDS = [
        "实现", "修复", "添加", "创建", "删除",
        "修改", "重构", "更新",
        "implement", "fix", "add", "create", "delete",
        "modify", "refactor", "update"
    ]
    
    def detect_mode(self, user_request: str) -> WorkflowMode:
        """检测工作流模式"""
        request_lower = user_request.lower()
        
        plan_score = sum(1 for kw in self.PLAN_KEYWORDS 
                        if kw in request_lower)
        build_score = sum(1 for kw in self.BUILD_KEYWORDS 
                         if kw in request_lower)
        
        if plan_score > build_score:
            return WorkflowMode.PLAN
        elif build_score > 0:
            return WorkflowMode.BUILD
        else:
            return WorkflowMode.PLAN
    
    def is_operation_allowed(self, mode: WorkflowMode, 
                            operation: str) -> bool:
        """检查操作是否允许"""
        read_only_ops = [
            "read", "glob", "grep", "search_codebase",
            "websearch", "webfetch"
        ]
        write_ops = ["write", "edit", "delete"]
        
        if operation in read_only_ops:
            return True
        
        if operation in write_ops:
            return mode == WorkflowMode.BUILD
        
        return True

class WorkflowModeEnforcer:
    """工作流模式强制执行器"""
    
    def __init__(self):
        self.current_mode: Optional[WorkflowMode] = None
        self.plan_output: List[str] = []
    
    def set_mode(self, mode: WorkflowMode):
        """设置当前模式"""
        self.current_mode = mode
    
    def check_operation(self, operation: str) -> bool:
        """检查操作是否允许"""
        if not self.current_mode:
            raise RuntimeError("未设置工作流模式")
        
        detector = WorkflowModeDetector()
        return detector.is_operation_allowed(self.current_mode, operation)
    
    def execute_with_check(self, operation: str, 
                          executor: callable) -> any:
        """带检查的执行"""
        if not self.check_operation(operation):
            raise PermissionError(
                f"当前模式 {self.current_mode.value} 不允许执行 {operation}"
            )
        return executor()
```

---

## 六、检查清单

```yaml
模式识别:
  □ 正确识别Plan/Build模式？
  □ 关键词匹配准确？
  □ 场景判断正确？

模式约束:
  □ Plan模式无写操作？
  □ Build模式先读后写？
  □ 操作权限正确？

模式切换:
  □ 切换时机正确？
  □ 切换流程完整？
  □ 用户确认到位？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           Plan/Build模式速查表              │
├─────────────────────────────────────────────┤
│                                             │
│  Plan模式 (只读):                           │
│    触发: 分析/设计/计划/如何                │
│    允许: read/glob/grep/search              │
│    禁止: write/edit/delete                  │
│    输出: 分析报告/设计方案                  │
│                                             │
│  Build模式 (读写):                          │
│    触发: 实现/修复/添加/创建                │
│    允许: 所有操作                           │
│    要求: 先读后写，修改后验证               │
│    输出: 代码变更/测试结果                  │
│                                             │
│  模式切换:                                   │
│    Plan → Build: 用户确认后                 │
│    Build → Plan: 遇到不确定问题             │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：先规划后执行，安全可控！
