---
name: Meta-Cognitive Check
description: 元认知监控检查触发器 - 任务执行时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_task_execution"
---

# 元认知监控检查触发器

> **⚠️ 建议：执行任务时应进行自我监控**

---

## 一、触发条件

```yaml
触发场景:
  - 执行复杂任务
  - 长时间运行任务
  - 多步骤任务
  - 高风险操作

不触发场景:
  - 简单单一操作
  - 用户明确跳过
```

---

## 二、元认知能力

```yaml
元认知定义:
  - 对自己认知过程的认知
  - 对思维过程的监控
  - 对学习过程的调节

核心能力:
  监控:
    - 过程监控
    - 状态监控
    - 结果监控
  
  评估:
    - 能力评估
    - 表现评估
    - 进度评估
  
  调节:
    - 策略调节
    - 资源调节
    - 行为调节
```

---

## 三、监控维度

### 3.1 过程监控

```yaml
监控内容:
  - 当前执行步骤
  - 执行进度
  - 资源消耗
  - 时间消耗

监控指标:
  步骤完成率: 已完成步骤 / 总步骤
  时间消耗率: 已用时间 / 预计时间
  资源使用率: 已用资源 / 可用资源
  错误率: 错误次数 / 总操作次数
```

### 3.2 状态监控

```yaml
监控内容:
  - 理解程度
  - 置信度
  - 困惑点
  - 阻塞点

状态评估:
  理解程度: 1-5分
  置信度: 1-5分
  困惑程度: 1-5分
  阻塞程度: 1-5分
```

### 3.3 结果监控

```yaml
监控内容:
  - 输出质量
  - 目标达成度
  - 副作用评估
  - 用户满意度

评估标准:
  目标达成: 完全达成 / 部分达成 / 未达成
  质量评级: 优秀 / 良好 / 合格 / 不合格
  副作用: 无 / 轻微 / 中等 / 严重
```

---

## 四、自我调节

```yaml
策略调节:
  触发条件:
    - 进度落后
    - 错误率过高
    - 理解程度低
  
  调节动作:
    - 切换执行策略
    - 寻求帮助
    - 分解任务

资源调节:
  触发条件:
    - 资源不足
    - 时间不足
    - 能力不足
  
  调节动作:
    - 申请更多资源
    - 调整优先级
    - 寻求协作

行为调节:
  触发条件:
    - 方向偏离
    - 效率低下
    - 质量不达标
  
  调节动作:
    - 纠正方向
    - 优化方法
    - 提升标准
```

---

## 五、实现示例

```python
from dataclasses import dataclass
from typing import Optional, List
from enum import Enum

class TaskStatus(Enum):
    """任务状态"""
    NOT_STARTED = "not_started"
    IN_PROGRESS = "in_progress"
    BLOCKED = "blocked"
    COMPLETED = "completed"
    FAILED = "failed"

@dataclass
class MetaCognitiveState:
    """元认知状态"""
    understanding_level: int  # 1-5
    confidence_level: int     # 1-5
    confusion_level: int      # 1-5
    blocking_level: int       # 1-5

class MetaCognitiveMonitor:
    """元认知监控器"""
    
    def __init__(self):
        self.current_step = 0
        self.total_steps = 0
        self.errors = []
        self.state = MetaCognitiveState(3, 3, 1, 1)
    
    def update_progress(self, step: int, total: int):
        """更新进度"""
        self.current_step = step
        self.total_steps = total
        self._check_progress()
    
    def record_error(self, error: str):
        """记录错误"""
        self.errors.append(error)
        self._check_error_rate()
    
    def update_state(self, understanding: int, confidence: int, 
                     confusion: int, blocking: int):
        """更新状态"""
        self.state = MetaCognitiveState(
            understanding, confidence, confusion, blocking
        )
        self._check_state()
    
    def _check_progress(self):
        """检查进度"""
        if self.total_steps > 0:
            progress = self.current_step / self.total_steps
            if progress < 0.5 and len(self.errors) > 3:
                self._adjust_strategy("进度落后，错误过多")
    
    def _check_error_rate(self):
        """检查错误率"""
        if len(self.errors) > 5:
            self._adjust_strategy("错误率过高")
    
    def _check_state(self):
        """检查状态"""
        if self.state.blocking_level >= 4:
            self._seek_help("遇到严重阻塞")
        elif self.state.confusion_level >= 4:
            self._seek_help("理解困难")
    
    def _adjust_strategy(self, reason: str):
        """调整策略"""
        print(f"[元认知] 策略调整: {reason}")
    
    def _seek_help(self, reason: str):
        """寻求帮助"""
        print(f"[元认知] 寻求帮助: {reason}")
    
    def get_report(self) -> dict:
        """获取监控报告"""
        return {
            "progress": f"{self.current_step}/{self.total_steps}",
            "errors": len(self.errors),
            "state": self.state.__dict__
        }
```

---

## 六、检查清单

```yaml
过程监控:
  □ 进度追踪正常？
  □ 错误记录完整？
  □ 资源使用合理？

状态监控:
  □ 理解程度评估？
  □ 置信度评估？
  □ 困惑点识别？
  □ 阻塞点识别？

自我调节:
  □ 策略调整及时？
  □ 资源调配合理？
  □ 行为纠正有效？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           元认知监控速查表                   │
├─────────────────────────────────────────────┤
│                                             │
│  监控维度:                                   │
│    □ 过程监控 (进度/错误)                   │
│    □ 状态监控 (理解/置信)                   │
│    □ 结果监控 (质量/目标)                   │
│                                             │
│  调节机制:                                   │
│    □ 策略调节                               │
│    □ 资源调节                               │
│    □ 行为调节                               │
│                                             │
│  触发条件:                                   │
│    进度落后 → 调整策略                      │
│    错误过多 → 寻求帮助                      │
│    阻塞严重 → 重新规划                      │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：知其然，更知其所以然！
