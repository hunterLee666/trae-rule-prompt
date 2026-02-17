---
name: Model Router
description: 智能模型路由 - 根据任务特征选择最优模型
version: "7.0"
priority: 95
alwaysApply: true
---

# 模型路由器

> **"合适的模型，恰当的时机。"**

## 一、模型分类

```yaml
模型类别:
  quick:
    用途: 简单修复、拼写错误
    特点: 快速、低成本
    示例: 修复typo、简单格式调整
    
  unspecified-low:
    用途: 中等复杂度任务
    特点: 平衡性能与成本
    示例: 添加函数、简单重构
    
  unspecified-high:
    用途: 复杂任务
    特点: 高性能、深度推理
    示例: 复杂逻辑实现、架构调整
    
  ultrabrain:
    用途: 架构设计、复杂逻辑
    特点: 最强推理能力
    示例: 系统设计、技术选型
    
  visual-engineering:
    用途: 前端UI/UX
    特点: 视觉理解能力
    示例: UI组件、样式调整
    
  artistry:
    用途: 创造性任务
    特点: 创意生成能力
    示例: 文案创作、设计建议
    
  writing:
    用途: 文档编写
    特点: 语言表达能力
    示例: README、API文档
```

## 二、路由决策树

```yaml
决策流程:
  Step 1: 检查任务类型
    if UI/UX相关:
      → visual-engineering
      
    if 文档相关:
      → writing
      
    if 创意相关:
      → artistry
      
  Step 2: 评估复杂度
    if complexity < 0.3:
      → quick
      
    if complexity > 0.8:
      → ultrabrain
      
    if complexity > 0.6:
      → unspecified-high
      
    else:
      → unspecified-low
      
  Step 3: 检查特殊条件
    if 架构设计:
      → ultrabrain
      
    if 成本敏感:
      → 降级选择
      
    if 延迟敏感:
      → quick 或 unspecified-low
```

## 三、复杂度评估

### 3.1 评估维度

```yaml
维度权重:
  代码量: 20%
  逻辑复杂度: 25%
  依赖关系: 20%
  影响范围: 15%
  创新需求: 20%

评分标准:
  0.0-0.3: 简单
  0.3-0.6: 中等
  0.6-0.8: 复杂
  0.8-1.0: 极复杂
```

### 3.2 复杂度指标

```yaml
代码量指标:
  < 50行: 0.2
  50-200行: 0.4
  200-500行: 0.6
  500-1000行: 0.8
  > 1000行: 1.0

逻辑复杂度指标:
  简单CRUD: 0.2
  业务逻辑: 0.5
  算法实现: 0.7
  架构设计: 0.9

依赖关系指标:
  无依赖: 0.1
  单模块: 0.3
  多模块: 0.6
  跨系统: 0.9

影响范围指标:
  单文件: 0.2
  单模块: 0.4
  多模块: 0.7
  全系统: 1.0
```

## 四、任务类型识别

```yaml
任务类型关键词:
  UI/UX:
    - "界面"
    - "样式"
    - "CSS"
    - "组件"
    - "布局"
    - "动画"
    
  文档:
    - "文档"
    - "README"
    - "说明"
    - "注释"
    - "API文档"
    
  创意:
    - "设计"
    - "创意"
    - "文案"
    - "命名"
    
  架构:
    - "架构"
    - "设计模式"
    - "系统设计"
    - "技术选型"
    
  简单修复:
    - "typo"
    - "拼写"
    - "格式"
    - "缩进"
```

## 五、成本优化

```yaml
成本策略:
  默认: 性能优先
  成本敏感: 成本优先
  延迟敏感: 速度优先

降级规则:
  ultrabrain → unspecified-high
  unspecified-high → unspecified-low
  unspecified-low → quick

升级规则:
  quick → unspecified-low (任务超出能力)
  unspecified-low → unspecified-high (复杂度增加)
```

## 六、路由示例

```yaml
示例1: 修复typo
  复杂度: 0.1
  类型: 简单修复
  路由: quick

示例2: 添加登录功能
  复杂度: 0.5
  类型: 业务逻辑
  路由: unspecified-low

示例3: 重构核心模块
  复杂度: 0.8
  类型: 架构调整
  路由: ultrabrain

示例4: 设计登录页面
  复杂度: 0.4
  类型: UI/UX
  路由: visual-engineering

示例5: 编写API文档
  复杂度: 0.3
  类型: 文档
  路由: writing
```
