---
name: Memory System Check
description: 记忆系统检查触发器 - AI系统设计时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_ai_memory_design"
---

# 记忆系统检查触发器

> **⚠️ 建议：设计AI系统时应考虑记忆管理**

---

## 一、触发条件

```yaml
触发场景:
  - 设计AI Agent系统
  - 设计对话系统
  - 设计知识管理系统
  - 设计长期运行系统

不触发场景:
  - 无状态系统
  - 用户明确跳过
```

---

## 二、记忆类型

```yaml
工作记忆:
  特点: 实时、临时、有限容量
  内容: 当前任务信息
  生命周期: 任务期间
  容量: 有限 (7±2 信息块)
  示例: 当前对话上下文

短期记忆:
  特点: 近期、可检索、中等容量
  内容: 最近交互信息
  生命周期: 数小时到数天
  容量: 中等
  示例: 最近对话历史

长期记忆:
  特点: 持久、可检索、大容量
  内容: 知识、规则、经验
  生命周期: 永久
  容量: 理论无限
  示例: 用户偏好、知识库
```

---

## 三、工作记忆管理

```yaml
管理策略:
  信息选择:
    - 只保留关键信息
    - 过滤无关信息
    - 合并相似信息
  
  信息组织:
    - 分块处理
    - 建立关联
    - 结构化存储
  
  信息更新:
    - 实时更新
    - 优先级排序
    - 淘汰机制

容量限制:
  最大信息块: 7±2
  溢出策略: FIFO / LRU / 优先级
```

---

## 四、长期记忆管理

```yaml
存储结构:
  知识存储:
    - 事实知识
    - 规则知识
    - 过程知识
  
  经验存储:
    - 成功案例
    - 失败案例
    - 最佳实践
  
  用户存储:
    - 用户偏好
    - 用户历史
    - 用户画像

检索机制:
  关键词检索:
    - 精确匹配
    - 模糊匹配
  
  语义检索:
    - 向量相似度
    - 语义理解
  
  关联检索:
    - 知识图谱
    - 关联规则
```

---

## 五、实现示例

```python
from dataclasses import dataclass, field
from typing import Dict, List, Any, Optional
from collections import OrderedDict
import heapq

@dataclass
class MemoryItem:
    """记忆项"""
    id: str
    content: Any
    priority: int = 0
    access_count: int = 0
    created_at: str = ""
    last_accessed: str = ""

class WorkingMemory:
    """工作记忆"""
    
    def __init__(self, max_size: int = 7):
        self.max_size = max_size
        self._items: OrderedDict[str, MemoryItem] = OrderedDict()
    
    def add(self, item: MemoryItem):
        """添加记忆项"""
        if len(self._items) >= self.max_size:
            self._evict()
        
        self._items[item.id] = item
        self._items.move_to_end(item.id)
    
    def get(self, item_id: str) -> Optional[MemoryItem]:
        """获取记忆项"""
        item = self._items.get(item_id)
        if item:
            item.access_count += 1
            self._items.move_to_end(item_id)
        return item
    
    def _evict(self):
        """淘汰策略 - LRU"""
        if self._items:
            self._items.popitem(last=False)
    
    def get_all(self) -> List[MemoryItem]:
        """获取所有记忆项"""
        return list(self._items.values())

class LongTermMemory:
    """长期记忆"""
    
    def __init__(self):
        self._knowledge: Dict[str, MemoryItem] = {}
        self._experiences: List[MemoryItem] = []
        self._user_data: Dict[str, MemoryItem] = {}
    
    def store_knowledge(self, item: MemoryItem):
        """存储知识"""
        self._knowledge[item.id] = item
    
    def store_experience(self, item: MemoryItem):
        """存储经验"""
        self._experiences.append(item)
    
    def store_user_data(self, user_id: str, item: MemoryItem):
        """存储用户数据"""
        key = f"{user_id}:{item.id}"
        self._user_data[key] = item
    
    def retrieve_knowledge(self, query: str) -> List[MemoryItem]:
        """检索知识"""
        results = []
        for item in self._knowledge.values():
            if query.lower() in str(item.content).lower():
                results.append(item)
        return results
    
    def retrieve_experience(self, limit: int = 10) -> List[MemoryItem]:
        """检索经验"""
        return heapq.nlargest(
            limit, 
            self._experiences, 
            key=lambda x: x.access_count
        )

class MemorySystem:
    """记忆系统"""
    
    def __init__(self, working_size: int = 7):
        self.working = WorkingMemory(working_size)
        self.long_term = LongTermMemory()
    
    def remember(self, item: MemoryItem, is_long_term: bool = False):
        """记忆"""
        self.working.add(item)
        if is_long_term:
            self.long_term.store_knowledge(item)
    
    def recall(self, query: str) -> List[MemoryItem]:
        """回忆"""
        results = []
        
        for item in self.working.get_all():
            if query.lower() in str(item.content).lower():
                results.append(item)
        
        results.extend(self.long_term.retrieve_knowledge(query))
        
        return results
```

---

## 六、检查清单

```yaml
工作记忆:
  □ 容量限制合理？
  □ 淘汰策略正确？
  □ 更新机制完善？

长期记忆:
  □ 存储结构清晰？
  □ 检索机制有效？
  □ 数据持久化？

记忆整合:
  □ 工作记忆到长期记忆？
  □ 长期记忆到工作记忆？
  □ 遗忘机制合理？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           记忆系统速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  记忆类型:                                   │
│    工作记忆: 7±2块, 任务期间                │
│    短期记忆: 数小时到数天                   │
│    长期记忆: 永久存储                       │
│                                             │
│  工作记忆管理:                               │
│    □ 信息选择                               │
│    □ 分块处理                               │
│    □ 淘汰策略                               │
│                                             │
│  长期记忆管理:                               │
│    □ 知识存储                               │
│    □ 经验存储                               │
│    □ 检索机制                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：记忆是智能的基础！
