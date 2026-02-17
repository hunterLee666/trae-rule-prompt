---
name: Skill System Check
description: 技能系统检查触发器 - 能力模块设计时建议执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_skill_design"
---

# 技能系统检查触发器

> **⚠️ 建议：设计能力模块时应遵循技能系统规范**

---

## 一、触发条件

```yaml
触发场景:
  - 设计新技能
  - 组合多个能力
  - 创建能力模块
  - 扩展系统能力

不触发场景:
  - 使用已有技能
  - 用户明确跳过
```

---

## 二、技能定义

```yaml
技能结构:
  name: 技能名称
    - 唯一标识
    - 描述性命名
  
  description: 技能描述
    - 功能说明
    - 使用场景
  
  triggers: 触发条件
    - 关键词触发
    - 场景触发
    - 条件触发
  
  capabilities: 能力列表
    - 核心能力
    - 辅助能力
  
  dependencies: 依赖项
    - 工具依赖
    - 技能依赖
    - 资源依赖
```

---

## 三、技能类型

```yaml
分析型:
  特点: 信息处理、问题诊断
  示例:
    - 代码分析
    - 问题诊断
    - 性能分析
  输出: 分析报告、诊断结果

生成型:
  特点: 内容创建、代码生成
  示例:
    - 代码生成
    - 文档生成
    - 测试生成
  输出: 生成内容、代码文件

转换型:
  特点: 格式转换、内容重构
  示例:
    - 代码重构
    - 格式转换
    - 语言翻译
  输出: 转换结果

执行型:
  特点: 操作执行、任务完成
  示例:
    - 文件操作
    - 命令执行
    - 流程自动化
  输出: 执行结果
```

---

## 四、技能发现机制

```yaml
自动发现:
  流程:
    1. 扫描技能目录
    2. 解析技能定义
    3. 验证技能完整性
    4. 注册到技能库
  
  目录结构:
    skills/
      ├── code-analysis/
      │   ├── skill.yaml
      │   └── implementation.py
      └── code-generation/
          ├── skill.yaml
          └── implementation.py

手动注册:
  流程:
    1. 创建技能实例
    2. 调用注册接口
    3. 验证注册成功
```

---

## 五、实现示例

```python
from dataclasses import dataclass, field
from typing import Dict, List, Any, Optional, Callable
from enum import Enum
import os
import yaml

class SkillType(Enum):
    """技能类型"""
    ANALYSIS = "analysis"
    GENERATION = "generation"
    TRANSFORMATION = "transformation"
    EXECUTION = "execution"

@dataclass
class SkillTrigger:
    """技能触发器"""
    keywords: List[str] = field(default_factory=list)
    patterns: List[str] = field(default_factory=list)
    conditions: List[str] = field(default_factory=list)

@dataclass
class Skill:
    """技能定义"""
    name: str
    description: str
    type: SkillType
    triggers: SkillTrigger
    capabilities: List[str]
    dependencies: List[str] = field(default_factory=list)
    implementation: Optional[Callable] = None
    
    def can_trigger(self, context: Dict) -> bool:
        """检查是否可以触发"""
        text = context.get("text", "").lower()
        
        for keyword in self.triggers.keywords:
            if keyword.lower() in text:
                return True
        
        return False
    
    def execute(self, context: Dict) -> Any:
        """执行技能"""
        if self.implementation:
            return self.implementation(context)
        raise NotImplementedError(f"技能 {self.name} 未实现")

class SkillRegistry:
    """技能注册表"""
    
    def __init__(self):
        self._skills: Dict[str, Skill] = {}
    
    def register(self, skill: Skill):
        """注册技能"""
        self._skills[skill.name] = skill
    
    def unregister(self, name: str):
        """注销技能"""
        if name in self._skills:
            del self._skills[name]
    
    def get(self, name: str) -> Optional[Skill]:
        """获取技能"""
        return self._skills.get(name)
    
    def find_by_trigger(self, context: Dict) -> List[Skill]:
        """根据触发条件查找技能"""
        return [
            skill for skill in self._skills.values()
            if skill.can_trigger(context)
        ]
    
    def list_all(self) -> List[str]:
        """列出所有技能"""
        return list(self._skills.keys())

class SkillLoader:
    """技能加载器"""
    
    def __init__(self, registry: SkillRegistry):
        self.registry = registry
    
    def load_from_directory(self, directory: str):
        """从目录加载技能"""
        for item in os.listdir(directory):
            skill_dir = os.path.join(directory, item)
            if os.path.isdir(skill_dir):
                self._load_skill(skill_dir)
    
    def _load_skill(self, skill_dir: str):
        """加载单个技能"""
        config_path = os.path.join(skill_dir, "skill.yaml")
        if not os.path.exists(config_path):
            return
        
        with open(config_path, 'r') as f:
            config = yaml.safe_load(f)
        
        skill = Skill(
            name=config.get("name"),
            description=config.get("description"),
            type=SkillType(config.get("type", "analysis")),
            triggers=SkillTrigger(
                keywords=config.get("triggers", {}).get("keywords", []),
                patterns=config.get("triggers", {}).get("patterns", []),
                conditions=config.get("triggers", {}).get("conditions", [])
            ),
            capabilities=config.get("capabilities", []),
            dependencies=config.get("dependencies", [])
        )
        
        self.registry.register(skill)

# 示例技能
code_analysis_skill = Skill(
    name="code_analysis",
    description="分析代码质量和结构",
    type=SkillType.ANALYSIS,
    triggers=SkillTrigger(
        keywords=["分析", "analyze", "检查", "check"]
    ),
    capabilities=["代码质量分析", "结构分析", "问题检测"],
    dependencies=["read_file", "grep"]
)
```

---

## 六、检查清单

```yaml
技能定义:
  □ 名称规范？
  □ 描述清晰？
  □ 类型正确？
  □ 触发条件明确？

技能实现:
  □ 能力列表完整？
  □ 依赖项明确？
  □ 实现正确？
  □ 测试覆盖？

技能注册:
  □ 注册成功？
  □ 发现机制有效？
  □ 触发机制正确？
```

---

## 七、快速检查卡

```
┌─────────────────────────────────────────────┐
│           技能系统速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  技能类型:                                   │
│    分析型: 信息处理、问题诊断               │
│    生成型: 内容创建、代码生成               │
│    转换型: 格式转换、内容重构               │
│    执行型: 操作执行、任务完成               │
│                                             │
│  技能结构:                                   │
│    □ name (名称)                            │
│    □ description (描述)                     │
│    □ triggers (触发条件)                    │
│    □ capabilities (能力)                    │
│    □ dependencies (依赖)                    │
│                                             │
│  发现机制:                                   │
│    □ 自动扫描                               │
│    □ 手动注册                               │
│    □ 触发匹配                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：能力模块化，组合无限可能！
