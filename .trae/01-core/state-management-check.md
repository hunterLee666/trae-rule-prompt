---
name: State Management Check
description: 状态管理检查触发器 - 系统设计时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_stateful_system_design"
---

# 状态管理检查触发器

> **⚠️ 建议：设计有状态系统时应考虑状态管理**

---

## 一、触发条件

```yaml
触发场景:
  - 设计对话系统
  - 设计工作流系统
  - 设计任务管理系统
  - 设计多步骤操作

不触发场景:
  - 无状态服务
  - 用户明确跳过
```

---

## 二、状态类型

```yaml
会话状态:
  内容:
    - 对话历史
    - 当前任务
    - 用户偏好
  生命周期: 会话期间
  存储: 内存/Redis

任务状态:
  内容:
    - 任务进度
    - 中间结果
    - 待办事项
  生命周期: 任务期间
  存储: 数据库/文件

系统状态:
  内容:
    - 配置信息
    - 资源状态
    - 错误信息
  生命周期: 系统运行期间
  存储: 配置文件/数据库
```

---

## 三、状态追踪

### 3.1 对话追踪

```yaml
追踪内容:
  话题追踪:
    - 当前话题
    - 话题历史
    - 话题切换
  
  意图追踪:
    - 用户意图
    - 意图变化
    - 意图完成度
  
  情感追踪:
    - 用户情绪
    - 情绪变化
    - 情绪趋势
```

### 3.2 任务追踪

```yaml
追踪内容:
  进度追踪:
    - 当前步骤
    - 完成步骤
    - 剩余步骤
  
  资源追踪:
    - 已用资源
    - 可用资源
    - 资源预测
  
  结果追踪:
    - 中间结果
    - 最终结果
    - 结果验证
```

---

## 四、状态持久化

```yaml
持久化策略:
  内存缓存:
    用途: 高频访问状态
    特点: 快速、易丢失
    示例: 会话状态
  
  文件存储:
    用途: 中等频率状态
    特点: 持久、中等速度
    示例: 任务状态
  
  数据库存储:
    用途: 低频访问状态
    特点: 持久、可查询
    示例: 历史记录
  
  分布式缓存:
    用途: 分布式状态
    特点: 共享、高可用
    示例: Redis
```

---

## 五、实现示例

```python
from dataclasses import dataclass, field
from typing import Dict, List, Any, Optional
from enum import Enum
import json

class StateType(Enum):
    """状态类型"""
    SESSION = "session"
    TASK = "task"
    SYSTEM = "system"

@dataclass
class State:
    """状态基类"""
    id: str
    type: StateType
    data: Dict[str, Any] = field(default_factory=dict)
    created_at: str = ""
    updated_at: str = ""

class StateManager:
    """状态管理器"""
    
    def __init__(self):
        self._states: Dict[str, State] = {}
        self._history: List[Dict] = []
    
    def create_state(self, state_id: str, state_type: StateType, 
                     initial_data: Dict = None) -> State:
        """创建状态"""
        import datetime
        now = datetime.datetime.utcnow().isoformat()
        
        state = State(
            id=state_id,
            type=state_type,
            data=initial_data or {},
            created_at=now,
            updated_at=now
        )
        self._states[state_id] = state
        return state
    
    def get_state(self, state_id: str) -> Optional[State]:
        """获取状态"""
        return self._states.get(state_id)
    
    def update_state(self, state_id: str, updates: Dict) -> Optional[State]:
        """更新状态"""
        state = self._states.get(state_id)
        if not state:
            return None
        
        import datetime
        state.data.update(updates)
        state.updated_at = datetime.datetime.utcnow().isoformat()
        
        self._record_history(state_id, "update", updates)
        return state
    
    def delete_state(self, state_id: str) -> bool:
        """删除状态"""
        if state_id in self._states:
            del self._states[state_id]
            self._record_history(state_id, "delete", {})
            return True
        return False
    
    def _record_history(self, state_id: str, action: str, data: Dict):
        """记录历史"""
        import datetime
        self._history.append({
            "timestamp": datetime.datetime.utcnow().isoformat(),
            "state_id": state_id,
            "action": action,
            "data": data
        })
    
    def save_to_file(self, filepath: str):
        """保存到文件"""
        data = {
            "states": {k: v.__dict__ for k, v in self._states.items()},
            "history": self._history
        }
        with open(filepath, 'w') as f:
            json.dump(data, f, default=str)
    
    def load_from_file(self, filepath: str):
        """从文件加载"""
        with open(filepath, 'r') as f:
            data = json.load(f)
        
        for state_id, state_data in data.get("states", {}).items():
            self._states[state_id] = State(**state_data)
        self._history = data.get("history", [])
```

---

## 六、检查清单

```yaml
状态设计:
  □ 状态类型定义清晰？
  □ 状态生命周期明确？
  □ 状态转换规则完整？

状态追踪:
  □ 状态变化可追踪？
  □ 历史记录完整？
  □ 回滚机制存在？

状态持久化:
  □ 持久化策略合理？
  □ 数据一致性保证？
  □ 恢复机制完善？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           状态管理速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  状态类型:                                   │
│    □ 会话状态 (对话历史)                    │
│    □ 任务状态 (进度/结果)                   │
│    □ 系统状态 (配置/资源)                   │
│                                             │
│  追踪维度:                                   │
│    □ 话题追踪                               │
│    □ 意图追踪                               │
│    □ 进度追踪                               │
│                                             │
│  持久化策略:                                 │
│    高频 → 内存缓存                          │
│    中频 → 文件存储                          │
│    低频 → 数据库存储                        │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：状态连续，体验连贯！
