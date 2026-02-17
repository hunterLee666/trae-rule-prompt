---
name: Model Router Check
description: 模型路由检查触发器 - 选择模型时建议执行
version: "1.0"
priority: 95
alwaysApply: true
trigger: "during_model_selection"
---

# 模型路由检查触发器

> **⚠️ 建议：选择模型时应根据任务特征选择最优模型**

---

## 一、触发条件

```yaml
触发场景:
  - 需要选择LLM模型
  - 需要分配任务到不同模型
  - 需要优化模型使用成本
  - 需要平衡性能与成本

不触发场景:
  - 用户明确指定模型
  - 用户明确跳过
```

---

## 二、模型分类

```yaml
quick:
  用途: 简单修复、拼写错误
  特点: 快速、低成本
  示例:
    - 修复typo
    - 简单格式调整
    - 快速问答
  成本: 低
  速度: 快

unspecified-low:
  用途: 中等复杂度任务
  特点: 平衡性能与成本
  示例:
    - 添加函数
    - 简单重构
    - 代码注释
  成本: 中
  速度: 中

unspecified-high:
  用途: 复杂任务
  特点: 高性能、深度推理
  示例:
    - 复杂逻辑实现
    - 架构调整
    - Bug分析
  成本: 高
  速度: 中

ultrabrain:
  用途: 架构设计、复杂逻辑
  特点: 最强推理能力
  示例:
    - 系统设计
    - 技术选型
    - 复杂问题分析
  成本: 最高
  速度: 慢

visual-engineering:
  用途: 前端UI/UX
  特点: 视觉理解能力
  示例:
    - UI组件设计
    - 样式调整
    - 布局优化
  成本: 中高
  速度: 中

artistry:
  用途: 创造性任务
  特点: 创意生成能力
  示例:
    - 文案创作
    - 设计建议
    - 创意方案
  成本: 中
  速度: 中

writing:
  用途: 文档编写
  特点: 语言表达能力
  示例:
    - README编写
    - API文档
    - 技术文章
  成本: 中
  速度: 中
```

---

## 三、路由决策

### 3.1 任务复杂度评估

```yaml
简单任务:
  特征:
    - 单一操作
    - 明确指令
    - 无需推理
  推荐: quick

中等任务:
  特征:
    - 多步骤操作
    - 需要理解上下文
    - 简单推理
  推荐: unspecified-low

复杂任务:
  特征:
    - 多文件修改
    - 需要深度推理
    - 架构考虑
  推荐: unspecified-high

超复杂任务:
  特征:
    - 系统级设计
    - 多系统集成
    - 战略决策
  推荐: ultrabrain
```

### 3.2 任务类型匹配

```yaml
代码修改:
  typo修复 → quick
  函数添加 → unspecified-low
  重构 → unspecified-high
  架构调整 → ultrabrain

文档生成:
  注释添加 → unspecified-low
  README → writing
  API文档 → writing
  技术文章 → writing

UI/UX:
  样式调整 → visual-engineering
  组件设计 → visual-engineering
  布局优化 → visual-engineering

创意任务:
  文案创作 → artistry
  设计建议 → artistry
  创意方案 → artistry
```

---

## 四、路由实现

```python
from enum import Enum
from typing import Optional

class ModelType(Enum):
    """模型类型"""
    QUICK = "quick"
    UNSPECIFIED_LOW = "unspecified-low"
    UNSPECIFIED_HIGH = "unspecified-high"
    ULTRABRAIN = "ultrabrain"
    VISUAL_ENGINEERING = "visual-engineering"
    ARTISTRY = "artistry"
    WRITING = "writing"

def estimate_complexity(task: str) -> str:
    """评估任务复杂度"""
    simple_keywords = ["fix typo", "format", "rename"]
    medium_keywords = ["add function", "refactor", "implement"]
    complex_keywords = ["architecture", "design system", "integrate"]
    
    task_lower = task.lower()
    
    if any(kw in task_lower for kw in simple_keywords):
        return "simple"
    elif any(kw in task_lower for kw in complex_keywords):
        return "complex"
    elif any(kw in task_lower for kw in medium_keywords):
        return "medium"
    else:
        return "medium"

def detect_task_type(task: str) -> str:
    """检测任务类型"""
    if any(kw in task.lower() for kw in ["ui", "css", "style", "layout"]):
        return "visual"
    elif any(kw in task.lower() for kw in ["document", "readme", "doc"]):
        return "writing"
    elif any(kw in task.lower() for kw in ["creative", "design", "idea"]):
        return "artistry"
    else:
        return "code"

def route_model(task: str, context: dict = None) -> ModelType:
    """路由到合适的模型"""
    complexity = estimate_complexity(task)
    task_type = detect_task_type(task)
    
    # 任务类型优先
    if task_type == "visual":
        return ModelType.VISUAL_ENGINEERING
    elif task_type == "writing":
        return ModelType.WRITING
    elif task_type == "artistry":
        return ModelType.ARTISTRY
    
    # 复杂度路由
    if complexity == "simple":
        return ModelType.QUICK
    elif complexity == "medium":
        return ModelType.UNSPECIFIED_LOW
    elif complexity == "complex":
        return ModelType.UNSPECIFIED_HIGH
    else:
        return ModelType.UNSPECIFIED_LOW
```

---

## 五、检查清单

```yaml
任务评估:
  □ 任务复杂度评估正确？
  □ 任务类型识别准确？
  □ 上下文考虑充分？

模型选择:
  □ 模型能力匹配任务？
  □ 成本效益最优？
  □ 响应时间可接受？

执行验证:
  □ 模型输出质量达标？
  □ 是否需要升级模型？
  □ 是否可以降级模型？
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           模型路由速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  复杂度路由:                                 │
│    简单 → quick                             │
│    中等 → unspecified-low                   │
│    复杂 → unspecified-high                  │
│    超复杂 → ultrabrain                      │
│                                             │
│  类型路由:                                   │
│    UI/UX → visual-engineering               │
│    文档 → writing                           │
│    创意 → artistry                          │
│    代码 → 按复杂度路由                      │
│                                             │
│  检查要点:                                   │
│    □ 复杂度评估                             │
│    □ 类型识别                               │
│    □ 成本效益                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```yaml
❌ 错误示例:
  # 所有任务都用最强模型
  def process(task: str):
      return ultrabrain.process(task)

✅ 正确示例:
  def process(task: str):
      """根据任务特征选择模型"""
      model = route_model(task)
      return get_model(model).process(task)
```

---

**记住**：合适的模型，恰当的时机！
