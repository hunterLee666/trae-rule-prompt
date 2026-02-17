---
name: Learning Check
description: 学习系统检查触发器 - 任务完成后建议执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "after_task_completion"
---

# 学习系统检查触发器

> **⚠️ 建议：任务完成后应进行学习总结**

---

## 一、触发条件

```yaml
触发场景:
  - 任务成功完成后
  - 任务失败后
  - 用户给出反馈后
  - 发现新模式后

不触发场景:
  - 简单查询任务
  - 用户明确跳过
```

---

## 二、学习类型

```yaml
监督学习:
  来源: 用户反馈
  内容:
    - 成功案例学习
    - 错误案例学习
    - 偏好学习
  应用: 优化响应策略

强化学习:
  来源: 奖励/惩罚信号
  内容:
    - 策略优化
    - 价值函数更新
    - 行为调整
  应用: 提高任务成功率

迁移学习:
  来源: 跨任务知识
  内容:
    - 知识复用
    - 领域适应
    - 模式迁移
  应用: 加速新任务学习
```

---

## 三、学习循环

```yaml
标准学习循环:
  1. 观察:
     - 收集交互数据
     - 记录用户反馈
     - 追踪任务结果
  
  2. 分析:
     - 识别模式和趋势
     - 发现问题和机会
     - 评估学习价值
  
  3. 提取:
     - 提炼知识和规则
     - 归纳最佳实践
     - 形成可复用模式
  
  4. 应用:
     - 将学习应用到实践
     - 优化响应策略
     - 改进执行效率
  
  5. 验证:
     - 验证学习效果
     - 评估改进程度
     - 确认知识有效性
  
  6. 整合:
     - 整合到长期记忆
     - 更新知识库
     - 完善规则系统
```

---

## 四、学习内容

### 4.1 用户偏好

```yaml
偏好类型:
  交互偏好:
    - 响应风格
    - 详细程度
    - 语言偏好
  
  任务偏好:
    - 工作流程
    - 工具选择
    - 验证方式
  
  输出偏好:
    - 格式偏好
    - 结构偏好
    - 重点偏好

学习方式:
  - 显式反馈: 用户直接表达
  - 隐式反馈: 从行为推断
  - 模式识别: 统计分析
```

### 4.2 成功模式

```yaml
模式类型:
  解决方案模式:
    - 问题类型
    - 解决方案
    - 适用条件
  
  工具使用模式:
    - 工具组合
    - 执行顺序
    - 参数配置
  
  工作流模式:
    - 任务分解
    - 执行策略
    - 验证方法

提取规则:
  - 成功次数 >= 3次
  - 用户满意度 >= 4/5
  - 可复用性高
```

### 4.3 错误模式

```yaml
错误类型:
  理解错误:
    - 需求理解偏差
    - 上下文缺失
    - 意图识别错误
  
  执行错误:
    - 工具使用错误
    - 参数配置错误
    - 顺序错误
  
  输出错误:
    - 格式错误
    - 内容错误
    - 质量不达标

学习规则:
  - 记录错误详情
  - 分析根本原因
  - 制定预防措施
  - 更新规则系统
```

---

## 五、实现示例

```python
from dataclasses import dataclass, field
from typing import Dict, List, Any, Optional
from enum import Enum
import json

class FeedbackType(Enum):
    """反馈类型"""
    POSITIVE = "positive"
    NEGATIVE = "negative"
    NEUTRAL = "neutral"

@dataclass
class LearningRecord:
    """学习记录"""
    task_id: str
    task_type: str
    success: bool
    feedback: Optional[FeedbackType] = None
    patterns: List[str] = field(default_factory=list)
    errors: List[str] = field(default_factory=list)
    improvements: List[str] = field(default_factory=list)

class LearningSystem:
    """学习系统"""
    
    def __init__(self):
        self.records: List[LearningRecord] = []
        self.patterns: Dict[str, int] = {}
        self.preferences: Dict[str, Any] = {}
        self.errors: Dict[str, int] = {}
    
    def record_task(self, record: LearningRecord):
        """记录任务"""
        self.records.append(record)
        self._extract_patterns(record)
        self._record_errors(record)
    
    def _extract_patterns(self, record: LearningRecord):
        """提取模式"""
        if record.success and record.feedback == FeedbackType.POSITIVE:
            for pattern in record.patterns:
                self.patterns[pattern] = self.patterns.get(pattern, 0) + 1
    
    def _record_errors(self, record: LearningRecord):
        """记录错误"""
        if not record.success:
            for error in record.errors:
                self.errors[error] = self.errors.get(error, 0) + 1
    
    def get_best_patterns(self, limit: int = 10) -> List[str]:
        """获取最佳模式"""
        sorted_patterns = sorted(
            self.patterns.items(),
            key=lambda x: x[1],
            reverse=True
        )
        return [p[0] for p in sorted_patterns[:limit]]
    
    def get_common_errors(self, limit: int = 10) -> List[str]:
        """获取常见错误"""
        sorted_errors = sorted(
            self.errors.items(),
            key=lambda x: x[1],
            reverse=True
        )
        return [e[0] for e in sorted_errors[:limit]]
    
    def update_preference(self, key: str, value: Any):
        """更新偏好"""
        self.preferences[key] = value
    
    def get_preference(self, key: str, default: Any = None) -> Any:
        """获取偏好"""
        return self.preferences.get(key, default)
    
    def generate_report(self) -> Dict:
        """生成学习报告"""
        return {
            "total_tasks": len(self.records),
            "success_rate": sum(1 for r in self.records if r.success) / len(self.records) if self.records else 0,
            "top_patterns": self.get_best_patterns(5),
            "common_errors": self.get_common_errors(5),
            "preferences": self.preferences
        }

class FeedbackProcessor:
    """反馈处理器"""
    
    def __init__(self, learning_system: LearningSystem):
        self.learning = learning_system
    
    def process_feedback(self, task_id: str, feedback: str):
        """处理反馈"""
        feedback_type = self._classify_feedback(feedback)
        
        for record in self.learning.records:
            if record.task_id == task_id:
                record.feedback = feedback_type
                break
    
    def _classify_feedback(self, feedback: str) -> FeedbackType:
        """分类反馈"""
        positive_words = ["好", "棒", "谢谢", "完美", "good", "great", "thanks"]
        negative_words = ["错", "不好", "问题", "wrong", "bad", "issue"]
        
        feedback_lower = feedback.lower()
        
        if any(w in feedback_lower for w in positive_words):
            return FeedbackType.POSITIVE
        elif any(w in feedback_lower for w in negative_words):
            return FeedbackType.NEGATIVE
        else:
            return FeedbackType.NEUTRAL
```

---

## 六、检查清单

```yaml
学习记录:
  □ 任务结果记录？
  □ 用户反馈记录？
  □ 模式提取完成？

知识更新:
  □ 成功模式更新？
  □ 错误模式更新？
  □ 偏好更新？

学习验证:
  □ 学习效果验证？
  □ 改进措施有效？
  □ 知识整合完成？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           学习系统速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  学习类型:                                   │
│    监督学习: 用户反馈                       │
│    强化学习: 奖惩信号                       │
│    迁移学习: 跨任务知识                     │
│                                             │
│  学习循环:                                   │
│    观察 → 分析 → 提取                       │
│    → 应用 → 验证 → 整合                     │
│                                             │
│  学习内容:                                   │
│    □ 用户偏好                               │
│    □ 成功模式                               │
│    □ 错误模式                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：从交互中学习，在实践中进化！
