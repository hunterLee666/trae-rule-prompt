---
name: Design Pattern Check
description: 设计模式检查触发器 - 复杂代码生成时建议执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "during_complex_code_generation"
---

# 设计模式检查触发器

> **⚠️ 建议：复杂代码生成时考虑应用设计模式**

---

## 一、触发条件

```yaml
触发场景:
  - 生成复杂业务逻辑代码
  - 生成需要扩展的代码
  - 生成多类型对象创建代码
  - 生成需要灵活切换算法的代码
  - 用户明确要求设计模式

不触发场景:
  - 简单工具函数
  - 单一功能代码
  - 用户明确跳过
```

---

## 二、常用设计模式

### 2.1 创建型模式

```yaml
工厂模式 (Factory):
  适用场景:
    - 需要根据条件创建不同对象
    - 创建逻辑复杂，需要封装
    - 需要统一创建接口
  示例:
    class AgentFactory:
        @staticmethod
        def create_agent(agent_type: str, config: dict):
            if agent_type == "react":
                return ReActAgent(config)
            elif agent_type == "reflection":
                return ReflectionAgent(config)
            raise ValueError(f"Unknown type: {agent_type}")

单例模式 (Singleton):
  适用场景:
    - 需要全局唯一的实例
    - 资源共享管理
    - 配置管理
  示例:
    class LLMClient:
        _instance = None
        def __new__(cls):
            if cls._instance is None:
                cls._instance = super().__new__(cls)
            return cls._instance

建造者模式 (Builder):
  适用场景:
    - 复杂对象的构建
    - 需要分步骤创建
    - 构建过程需要灵活配置
  示例:
    class AgentBuilder:
        def with_llm(self, llm): ...
        def with_tools(self, tools): ...
        def with_memory(self, memory): ...
        def build(self): return Agent(self._config)
```

### 2.2 结构型模式

```yaml
适配器模式 (Adapter):
  适用场景:
    - 接口不兼容的类需要协作
    - 需要统一不同类的接口
  示例:
    class LLMAdapter:
        def __init__(self, llm_client):
            self._client = llm_client
        def generate(self, prompt: str) -> str:
            # 统一不同LLM的调用接口
            return self._client.chat(prompt)

装饰器模式 (Decorator):
  适用场景:
    - 动态添加功能
    - 避免类爆炸
  示例:
    def retry_decorator(max_retries=3):
        def decorator(func):
            def wrapper(*args, **kwargs):
                for _ in range(max_retries):
                    try:
                        return func(*args, **kwargs)
                    except Exception:
                        continue
            return wrapper
        return decorator

代理模式 (Proxy):
  适用场景:
    - 控制对象访问
    - 添加额外处理
  示例:
    class CachingProxy:
        def __init__(self, service):
            self._service = service
            self._cache = {}
        def get_data(self, key):
            if key not in self._cache:
                self._cache[key] = self._service.get_data(key)
            return self._cache[key]
```

### 2.3 行为型模式

```yaml
策略模式 (Strategy):
  适用场景:
    - 需要灵活切换算法
    - 避免大量条件判断
  示例:
    class SortingStrategy:
        def sort(self, data): pass
    
    class QuickSort(SortingStrategy):
        def sort(self, data): return sorted(data)
    
    class MergeSort(SortingStrategy):
        def sort(self, data): return self._merge_sort(data)

观察者模式 (Observer):
  适用场景:
    - 一对多依赖关系
    - 事件通知机制
  示例:
    class EventEmitter:
        def __init__(self):
            self._listeners = []
        def subscribe(self, listener):
            self._listeners.append(listener)
        def emit(self, event):
            for listener in self._listeners:
                listener(event)

责任链模式 (Chain of Responsibility):
  适用场景:
    - 多个处理器处理请求
    - 处理顺序灵活
  示例:
    class Handler:
        def __init__(self, next_handler=None):
            self._next = next_handler
        def handle(self, request):
            if self._can_handle(request):
                return self._do_handle(request)
            if self._next:
                return self._next.handle(request)
```

---

## 三、模式选择指南

```yaml
创建对象:
  简单创建: 直接实例化
  条件创建: 工厂模式
  复杂构建: 建造者模式
  全局唯一: 单例模式

扩展功能:
  接口不兼容: 适配器模式
  动态添加功能: 装饰器模式
  控制访问: 代理模式

算法切换:
  多算法选择: 策略模式
  多步骤处理: 责任链模式

事件处理:
  一对多通知: 观察者模式
```

---

## 四、快速检查卡

```
┌─────────────────────────────────────────────┐
│           设计模式速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  创建对象:                                   │
│    条件创建 → 工厂模式                      │
│    复杂构建 → 建造者模式                    │
│    全局唯一 → 单例模式                      │
│                                             │
│  扩展功能:                                   │
│    接口不兼容 → 适配器模式                  │
│    动态添加 → 装饰器模式                    │
│    控制访问 → 代理模式                      │
│                                             │
│  算法切换:                                   │
│    多算法 → 策略模式                        │
│    多步骤 → 责任链模式                      │
│                                             │
│  事件处理:                                   │
│    一对多 → 观察者模式                      │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 五、违规示例

```yaml
❌ 错误示例:
  def create_agent(agent_type):
      if agent_type == "react":
          return ReActAgent()
      elif agent_type == "reflection":
          return ReflectionAgent()
      # 每次添加新类型都要修改这里

✅ 正确示例:
  class AgentFactory:
      _registry = {}
      
      @classmethod
      def register(cls, agent_type, agent_class):
          cls._registry[agent_type] = agent_class
      
      @classmethod
      def create(cls, agent_type, config):
          return cls._registry[agent_type](config)
```

---

**记住**：设计模式是经验的结晶，用对模式事半功倍！
