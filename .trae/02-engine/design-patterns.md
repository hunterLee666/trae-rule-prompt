---
name: Design Patterns Rules
description: 设计模式应用规则 - 常用模式、应用场景、最佳实践
version: "9.1"
priority: 85
alwaysApply: true
---

# 设计模式应用规则

> **"模式是经验的结晶。"**

## 一、创建型模式

### 1.1 工厂模式

```yaml
适用场景:
  - 需要根据条件创建不同对象
  - 创建逻辑复杂，需要封装
  - 需要统一创建接口

实现示例:
  class AgentFactory:
      @staticmethod
      def create_agent(agent_type: str, config: dict):
          if agent_type == "react":
              return ReActAgent(config)
          elif agent_type == "reflection":
              return ReflectionAgent(config)
          elif agent_type == "plan_solve":
              return PlanAndSolveAgent(config)
          else:
              raise ValueError(f"Unknown agent type: {agent_type}")

AI Agent应用:
  - 创建不同类型的Agent
  - 创建不同的工具实例
  - 创建不同的LLM客户端
```

### 1.2 单例模式

```yaml
适用场景:
  - 需要全局唯一的实例
  - 资源共享管理
  - 配置管理

实现示例:
  class LLMClient:
      _instance = None
      
      def __new__(cls):
          if cls._instance is None:
              cls._instance = super().__new__(cls)
          return cls._instance

AI Agent应用:
  - LLM客户端管理
  - 配置管理器
  - 日志管理器
```

### 1.3 建造者模式

```yaml
适用场景:
  - 复杂对象的构建
  - 需要分步创建
  - 创建过程需要灵活配置

实现示例:
  class AgentBuilder:
      def __init__(self):
          self.name = "Agent"
          self.llm = None
          self.tools = []
          
      def with_name(self, name: str):
          self.name = name
          return self
          
      def with_llm(self, llm):
          self.llm = llm
          return self
          
      def with_tools(self, tools: list):
          self.tools = tools
          return self
          
      def build(self):
          return Agent(self.name, self.llm, self.tools)

AI Agent应用:
  - Agent配置构建
  - 复杂提示词构建
  - 工作流构建
```

---

## 二、结构型模式

### 2.1 适配器模式

```yaml
适用场景:
  - 接口不兼容需要转换
  - 统一不同实现的接口
  - 集成第三方库

实现示例:
  class LLMAdapter:
      def __init__(self, llm_client):
          self.client = llm_client
          
      def generate(self, prompt: str) -> str:
          # 统一不同LLM的调用接口
          return self.client.invoke(prompt)

AI Agent应用:
  - 统一不同LLM提供商接口
  - 统一不同工具接口
  - 集成外部服务
```

### 2.2 装饰器模式

```yaml
适用场景:
  - 动态添加功能
  - 功能组合
  - 避免类爆炸

实现示例:
  def retry_decorator(max_retries=3):
      def decorator(func):
          def wrapper(*args, **kwargs):
              for i in range(max_retries):
                  try:
                      return func(*args, **kwargs)
                  except Exception as e:
                      if i == max_retries - 1:
                          raise
          return wrapper
      return decorator

AI Agent应用:
  - LLM调用重试
  - 工具调用日志
  - 性能监控
```

### 2.3 组合模式

```yaml
适用场景:
  - 树形结构处理
  - 统一处理单个和组合对象
  - 工作流编排

实现示例:
  class Task:
      def execute(self):
          pass
          
  class SimpleTask(Task):
      def __init__(self, action):
          self.action = action
          
      def execute(self):
          return self.action()
          
  class CompositeTask(Task):
      def __init__(self):
          self.tasks = []
          
      def add(self, task: Task):
          self.tasks.append(task)
          
      def execute(self):
          results = []
          for task in self.tasks:
              results.append(task.execute())
          return results

AI Agent应用:
  - 任务编排
  - 工作流组合
  - 多Agent协作
```

---

## 三、行为型模式

### 3.1 策略模式

```yaml
适用场景:
  - 需要在运行时切换算法
  - 避免条件语句
  - 算法独立变化

实现示例:
  class ReasoningStrategy:
      def reason(self, context: dict) -> str:
          pass
          
  class ReActStrategy(ReasoningStrategy):
      def reason(self, context: dict) -> str:
          # ReAct推理逻辑
          pass
          
  class ReflectionStrategy(ReasoningStrategy):
      def reason(self, context: dict) -> str:
          # Reflection推理逻辑
          pass

AI Agent应用:
  - 推理策略选择
  - 工具选择策略
  - 响应生成策略
```

### 3.2 观察者模式

```yaml
适用场景:
  - 事件通知
  - 状态变化监听
  - 解耦发布者和订阅者

实现示例:
  class AgentObserver:
      def on_action(self, action: str):
          pass
          
      def on_result(self, result: str):
          pass
          
  class ObservableAgent:
      def __init__(self):
          self.observers = []
          
      def add_observer(self, observer: AgentObserver):
          self.observers.append(observer)
          
      def notify_action(self, action: str):
          for observer in self.observers:
              observer.on_action(action)

AI Agent应用:
  - Agent行为监控
  - 日志记录
  - 性能追踪
```

### 3.3 责任链模式

```yaml
适用场景:
  - 多步骤处理
  - 请求过滤
  - 处理器动态组合

实现示例:
  class Handler:
      def __init__(self):
          self.next_handler = None
          
      def set_next(self, handler):
          self.next_handler = handler
          return handler
          
      def handle(self, request):
          if self.next_handler:
              return self.next_handler.handle(request)
          return None
          
  class InputValidator(Handler):
      def handle(self, request):
          if not self.validate(request):
              return "Invalid input"
          return super().handle(request)

AI Agent应用:
  - 输入验证链
  - 工具执行链
  - 错误处理链
```

### 3.4 状态模式

```yaml
适用场景:
  - 对象行为随状态变化
  - 避免大量条件语句
  - 状态转换复杂

实现示例:
  class AgentState:
      def handle(self, agent, input_text):
          pass
          
  class IdleState(AgentState):
      def handle(self, agent, input_text):
          agent.state = ThinkingState()
          return agent.think(input_text)
          
  class ThinkingState(AgentState):
      def handle(self, agent, input_text):
          agent.state = ActingState()
          return agent.act(input_text)

AI Agent应用:
  - Agent状态管理
  - 会话状态管理
  - 工作流状态管理
```

---

## 四、AI Agent专用模式

### 4.1 ReAct模式

```yaml
定义: 推理(Reasoning) + 行动(Acting)交替执行

结构:
  Thought: 分析问题，规划下一步
  Action: 执行工具调用
  Observation: 观察执行结果
  循环直到得出答案

适用场景:
  - 需要多步推理的问题
  - 需要外部工具支持的任务
  - 不确定性较高的问题

实现要点:
  - 清晰的提示词模板
  - 严格的输出格式解析
  - 合理的最大步数限制
```

### 4.2 Reflection模式

```yaml
定义: 执行 + 反思 + 改进的迭代循环

结构:
  Execute: 执行初始方案
  Reflect: 反思执行结果
  Refine: 改进方案
  循环直到满意

适用场景:
  - 代码生成任务
  - 创意写作任务
  - 需要迭代优化的任务

实现要点:
  - 明确的反思标准
  - 具体的改进方向
  - 合理的迭代次数
```

### 4.3 Plan-and-Solve模式

```yaml
定义: 先规划后执行的两阶段模式

结构:
  Plan: 制定完整执行计划
  Solve: 按计划逐步执行

适用场景:
  - 复杂多步骤任务
  - 需要全局规划的任务
  - 步骤间有依赖的任务

实现要点:
  - 完整的计划生成
  - 计划的可执行性验证
  - 执行过程的跟踪
```

### 4.4 多Agent协作模式

```yaml
定义: 多个Agent分工协作完成任务

结构:
  Master-Slave: 主Agent分配任务给从Agent
  Peer-to-Peer: 对等Agent相互协作
  Pipeline: Agent按流水线处理
  Blackboard: 共享黑板模式

适用场景:
  - 大型复杂任务
  - 需要多种专业能力的任务
  - 可并行处理的任务

实现要点:
  - 清晰的角色分工
  - 高效的通信机制
  - 冲突解决策略
```

---

## 五、模式选择指南

### 5.1 按问题类型选择

| 问题类型 | 推荐模式 |
|---------|---------|
| 需要多步推理 | ReAct |
| 需要迭代优化 | Reflection |
| 需要全局规划 | Plan-and-Solve |
| 需要多专业协作 | 多Agent协作 |
| 需要动态策略 | 策略模式 |
| 需要状态管理 | 状态模式 |

### 5.2 按复杂度选择

| 复杂度 | 推荐模式组合 |
|-------|-------------|
| 简单 | 工厂 + 适配器 |
| 中等 | 建造者 + 策略 + 装饰器 |
| 复杂 | 组合 + 责任链 + 观察者 |
| 超复杂 | 多Agent协作 + 状态 + 策略 |
