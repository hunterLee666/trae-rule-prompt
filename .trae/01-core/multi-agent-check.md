---
name: Multi-Agent Check
description: 多Agent协作检查触发器 - 多Agent协作时强制执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_multi_agent_collaboration"
---

# 多Agent协作检查触发器

> **⚠️ 强制规则：多Agent协作时必须选择正确的协作模式**

---

## 一、触发条件

```yaml
触发场景:
  - 任务需要多个Agent协作完成
  - 用户请求涉及多角色配合
  - 复杂任务需要分解给多个Agent
  - 需要不同专业能力的任务

不触发场景:
  - 单一Agent可完成的任务
  - 简单的顺序执行任务
  - 用户明确指定单一Agent
```

---

## 二、协作模式选择

### 2.1 模式分类

```yaml
Master-Slave模式:
  结构: 1个主Agent + N个从Agent
  特点: 集中控制，任务分发
  适用: 明确的层级任务
  示例: 代码审查（主Agent分配审查任务）
  优点: 控制简单，职责清晰
  缺点: 单点故障，扩展性差

Peer-to-Peer模式:
  结构: N个对等Agent
  特点: 平等协作，相互通信
  适用: 无明确主次的任务
  示例: 头脑风暴、创意协作
  优点: 灵活性高，无单点故障
  缺点: 协调复杂，可能冲突

Pipeline模式:
  结构: Agent1 → Agent2 → Agent3 → ...
  特点: 流水线处理，顺序执行
  适用: 有明确流程的任务
  示例: 代码编写→审查→优化
  优点: 流程清晰，易于监控
  缺点: 延迟累积，瓶颈问题

Blackboard模式:
  结构: 共享黑板 + 多个专家Agent
  特点: 知识共享，异步协作
  适用: 需要知识整合的任务
  示例: 复杂问题诊断
  优点: 知识整合，异步协作
  缺点: 黑板管理复杂

Contract Net模式:
  结构: 任务发布 + 竞标执行
  特点: 动态分配，能力匹配
  适用: 任务类型多样的场景
  示例: 多技能任务分配
  优点: 动态匹配，资源优化
  缺点: 竞标开销

Coalition模式:
  结构: 动态联盟，临时组队
  特点: 灵活组合，按需协作
  适用: 复杂多变的环境
  示例: 应急响应系统
  优点: 高度灵活，适应性强
  缺点: 组织开销大
```

### 2.2 模式选择指南

```yaml
决策因素:
  任务复杂度:
    简单: Master-Slave
    中等: Pipeline
    复杂: Blackboard / Coalition
  
  Agent数量:
    2-3个: Pipeline / Master-Slave
    4-6个: Blackboard / Contract Net
    7+个: Coalition
  
  任务类型:
    并行处理: Master-Slave
    顺序处理: Pipeline
    知识整合: Blackboard
    动态分配: Contract Net
    应急响应: Coalition
  
  时间约束:
    紧急: Pipeline
    宽松: Blackboard / Coalition
  
  通信需求:
    低: Master-Slave
    中: Pipeline
    高: Peer-to-Peer / Blackboard
```

---

## 三、模式选择算法

```python
def select_collaboration_pattern(
    task_type: str,
    agent_count: int,
    task_complexity: str,
    time_constraint: str,
    communication_need: str
) -> str:
    """选择协作模式"""
    
    # Master-Slave: 适合简单、可分解的任务
    if task_type == "parallel_processing" and task_complexity == "low":
        return "master_slave"
    
    # Pipeline: 适合流水线处理
    if task_type == "sequential_processing" and time_constraint == "tight":
        return "pipeline"
    
    # Peer-to-Peer: 适合去中心化、平等协作
    if task_type == "knowledge_sharing" and agent_count > 3:
        return "peer_to_peer"
    
    # Blackboard: 适合复杂问题求解
    if task_complexity == "high" and task_type == "problem_solving":
        return "blackboard"
    
    # Contract Net: 适合任务分配
    if task_type == "task_allocation" and agent_count > 5:
        return "contract_net"
    
    # Coalition: 适合资源共享
    if task_type == "resource_sharing":
        return "coalition"
    
    # 默认
    return "master_slave"
```

---

## 四、协作检查清单

```yaml
模式选择:
  □ 任务复杂度评估正确？
  □ Agent数量统计准确？
  □ 任务类型识别正确？
  □ 时间约束考虑到位？
  □ 通信需求评估合理？

协作设计:
  □ Agent角色定义清晰？
  □ 通信协议明确？
  □ 冲突解决机制存在？
  □ 任务分配合理？
  □ 结果聚合方式明确？

执行监控:
  □ 进度可追踪？
  □ 异常可处理？
  □ 结果可验证？
```

---

## 五、快速检查卡

```
┌─────────────────────────────────────────────┐
│           多Agent协作速查表                  │
├─────────────────────────────────────────────┤
│                                             │
│  模式选择:                                   │
│    并行处理 → Master-Slave                  │
│    顺序处理 → Pipeline                      │
│    知识整合 → Blackboard                    │
│    动态分配 → Contract Net                  │
│    应急响应 → Coalition                     │
│                                             │
│  决策因素:                                   │
│    □ 任务复杂度 (简单/中等/复杂)            │
│    □ Agent数量 (2-3/4-6/7+)                │
│    □ 任务类型 (并行/顺序/知识整合)          │
│    □ 时间约束 (紧急/宽松)                   │
│                                             │
│  检查要点:                                   │
│    □ 角色定义清晰                           │
│    □ 通信协议明确                           │
│    □ 冲突解决机制                           │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 六、违规示例

```yaml
❌ 错误示例:
  任务: 复杂系统诊断
  行为: 随机选择Master-Slave模式
  问题: 无法整合多专家知识

✅ 正确示例:
  任务: 复杂系统诊断
  分析:
    - 复杂度: 高
    - Agent数: 5个专家
    - 类型: 问题求解
  选择: Blackboard模式
  理由: 需要多专家知识整合
```

---

**记住**：选对协作模式，多Agent才能发挥最大效能！
