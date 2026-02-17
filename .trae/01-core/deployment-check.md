---
name: Deployment Check
description: 部署策略检查触发器 - 部署代码生成时建议执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "during_deployment_code_generation"
---

# 部署策略检查触发器

> **⚠️ 建议：生成部署代码时考虑部署策略**

---

## 一、触发条件

```yaml
触发场景:
  - 生成CI/CD配置文件
  - 生成Docker/Kubernetes配置
  - 生成部署脚本
  - 生成环境配置
  - 用户明确要求部署相关

不触发场景:
  - 简单配置文件
  - 用户明确跳过
```

---

## 二、部署策略

### 2.1 策略分类

```yaml
蓝绿部署 (Blue-Green):
  原理: 两套环境，一键切换
  优点: 快速回滚，零停机
  缺点: 资源占用高
  适用: 关键服务、需要快速回滚
  示例:
    # Kubernetes蓝绿部署
    kubectl apply -f deployment-green.yaml
    kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'

金丝雀发布 (Canary):
  原理: 逐步放量，渐进发布
  优点: 风险可控，问题早发现
  缺点: 发布时间长
  适用: 大规模服务、需要验证
  示例:
    # 金丝雀发布 - 先10%流量
    kubectl patch service myapp -p '{"spec":{"canary":{"weight":10}}}'

滚动更新 (Rolling Update):
  原理: 逐个更新，逐步替换
  优点: 资源利用率高
  缺点: 回滚较慢
  适用: 无状态服务
  示例:
    # Kubernetes滚动更新
    kubectl set image deployment/myapp app=myapp:v2
    kubectl rollout status deployment/myapp

A/B测试 (A/B Testing):
  原理: 同时运行多版本
  优点: 数据驱动决策
  缺点: 实现复杂
  适用: 功能验证、用户测试
  示例:
    # 基于Header的路由
    if (req.headers["x-ab-test"] == "variant-b") {
      route to variant-b service
    }
```

### 2.2 策略选择指南

```yaml
决策因素:
  服务类型:
    有状态服务: 蓝绿部署
    无状态服务: 滚动更新
    关键服务: 蓝绿部署 / 金丝雀
    实验功能: A/B测试
  
  资源约束:
    资源充足: 蓝绿部署
    资源有限: 滚动更新
  
  风险容忍:
    低风险容忍: 金丝雀发布
    高风险容忍: 滚动更新
  
  回滚需求:
    需要秒级回滚: 蓝绿部署
    可接受分钟回滚: 滚动更新
```

---

## 三、部署原则

```yaml
核心原则:
  原子性: 部署要么成功，要么失败
  可重复: 相同输入产生相同结果
  可追溯: 记录所有部署历史
  可回滚: 支持快速回滚

最佳实践:
  - 使用版本控制管理部署配置
  - 实施自动化测试
  - 配置健康检查
  - 设置资源限制
  - 实施监控告警
```

---

## 四、部署检查清单

```yaml
部署前:
  □ 代码已通过所有测试？
  □ 配置已验证？
  □ 回滚计划已准备？
  □ 监控已配置？
  □ 告警已设置？

部署中:
  □ 使用正确的部署策略？
  □ 健康检查通过？
  □ 流量切换正确？
  □ 监控指标正常？

部署后:
  □ 功能验证通过？
  □ 性能指标正常？
  □ 错误率在阈值内？
  □ 部署记录已保存？
```

---

## 五、快速检查卡

```
┌─────────────────────────────────────────────┐
│           部署策略速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  策略选择:                                   │
│    关键服务 → 蓝绿部署                      │
│    大规模服务 → 金丝雀发布                  │
│    无状态服务 → 滚动更新                    │
│    功能验证 → A/B测试                       │
│                                             │
│  核心原则:                                   │
│    □ 原子性                                 │
│    □ 可重复                                 │
│    □ 可追溯                                 │
│    □ 可回滚                                 │
│                                             │
│  检查要点:                                   │
│    □ 测试通过                               │
│    □ 健康检查                               │
│    □ 监控告警                               │
│    □ 回滚计划                               │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 六、违规示例

```yaml
❌ 错误示例:
  # 直接替换，无回滚能力
  kubectl delete deployment myapp
  kubectl apply -f deployment-new.yaml

✅ 正确示例:
  # 滚动更新，支持回滚
  kubectl set image deployment/myapp app=myapp:v2
  kubectl rollout status deployment/myapp
  
  # 如果有问题，快速回滚
  kubectl rollout undo deployment/myapp
```

---

**记住**：选对部署策略，发布安全可控！
