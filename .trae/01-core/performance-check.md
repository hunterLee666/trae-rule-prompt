---
name: Performance Check
description: 性能测试检查触发器 - 功能实现后强制执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "after_feature_implementation"
---

# 性能测试检查触发器

> **⚠️ 强制规则：功能实现后必须考虑性能测试**

---

## 一、触发条件

```yaml
触发场景:
  - 核心功能实现完成
  - API接口实现完成
  - 数据处理逻辑实现
  - 用户明确要求性能优化
  - 涉及大量数据处理的功能

不触发场景:
  - 简单配置修改
  - 文档更新
  - UI样式调整
  - 用户明确跳过
```

---

## 二、性能测试类型

### 2.1 负载测试 (Load Testing)

```yaml
定义: 在预期负载下测试系统性能
目标: 验证系统在正常负载下的表现
指标:
  - 响应时间: P50, P95, P99
  - 吞吐量: QPS/TPS
  - 资源使用率: CPU, 内存, 网络
  - 错误率: < 1%

执行方式:
  工具: k6, Locust, JMeter, Gatling
  持续时间: 10-30分钟
  并发用户: 预期峰值的80%-100%

示例配置 (k6):
  import http from 'k6/http';
  import { check } from 'k6';
  
  export const options = {
    stages: [
      { duration: '2m', target: 100 },  // 升温
      { duration: '5m', target: 100 },  // 稳定
      { duration: '2m', target: 0 },    // 降温
    ],
    thresholds: {
      http_req_duration: ['p(95)<500'], // 95%请求<500ms
      http_req_failed: ['rate<0.01'],   // 错误率<1%
    },
  };
  
  export default function () {
    const res = http.get('https://api.example.com/users');
    check(res, { 'status is 200': (r) => r.status === 200 });
  }
```

### 2.2 压力测试 (Stress Testing)

```yaml
定义: 在超负载条件下测试系统极限
目标: 发现系统的瓶颈和崩溃点
指标:
  - 最大并发数
  - 崩溃阈值
  - 恢复时间
  - 降级行为

执行方式:
  工具: k6, Locust, JMeter
  持续时间: 5-15分钟
  并发用户: 预期峰值的150%-200%

示例配置 (k6):
  export const options = {
    stages: [
      { duration: '2m', target: 100 },
      { duration: '5m', target: 500 },  // 超负载
      { duration: '2m', target: 1000 }, // 极限测试
      { duration: '2m', target: 0 },
    ],
  };
```

### 2.3 基准测试 (Benchmark Testing)

```yaml
定义: 测量特定操作的基准性能
目标: 建立性能基线，检测性能退化
指标:
  - 单次操作耗时
  - 内存分配
  - CPU周期
  - I/O操作

Python示例:
  import time
  import timeit
  
  def benchmark_function(func, iterations=1000):
      """基准测试函数"""
      start = time.perf_counter()
      for _ in range(iterations):
          func()
      end = time.perf_counter()
      
      total_time = end - start
      avg_time = total_time / iterations
      
      return {
          'total_time': total_time,
          'avg_time': avg_time,
          'ops_per_second': iterations / total_time
      }
  
  # 使用示例
  result = benchmark_function(lambda: process_data(sample_data))
  print(f"平均耗时: {result['avg_time']*1000:.2f}ms")
  print(f"每秒操作: {result['ops_per_second']:.0f}")
```

---

## 三、性能指标标准

### 3.1 响应时间标准

```yaml
响应时间等级:
  极快: < 100ms
  快速: 100ms - 300ms
  正常: 300ms - 1s
  较慢: 1s - 3s
  慢: > 3s

API响应时间要求:
  P50 (中位数): < 200ms
  P95 (95分位): < 500ms
  P99 (99分位): < 1s
  最大响应时间: < 3s
```

### 3.2 吞吐量标准

```yaml
吞吐量参考:
  简单查询API: > 1000 QPS
  复杂查询API: > 100 QPS
  写入操作API: > 500 TPS
  批量处理: > 10000 条/秒
```

### 3.3 资源使用标准

```yaml
资源使用率:
  CPU: < 70% (正常负载)
  内存: < 80%
  磁盘I/O: < 70%
  网络: < 50%

连接池:
  数据库连接: < 80% 使用率
  HTTP连接: < 80% 使用率
```

---

## 四、性能优化检查清单

```yaml
代码层面:
  □ 避免N+1查询
  □ 使用批量操作
  □ 添加适当索引
  □ 使用缓存
  □ 异步处理耗时操作
  □ 避免大对象频繁创建

数据库层面:
  □ 查询是否有索引
  □ 是否使用分页
  □ 是否避免全表扫描
  □ 连接池配置合理

缓存层面:
  □ 热点数据是否缓存
  □ 缓存过期策略合理
  □ 缓存穿透防护
  □ 缓存雪崩防护
```

---

## 五、性能测试输出模板

```markdown
## 📊 性能测试报告

### 测试配置
- **测试类型**: 负载测试
- **工具**: k6
- **持续时间**: 10分钟
- **并发用户**: 100

### 测试结果

| 指标 | 目标 | 实际 | 状态 |
|-----|------|------|------|
| P50响应时间 | < 200ms | 150ms | ✅ |
| P95响应时间 | < 500ms | 420ms | ✅ |
| P99响应时间 | < 1s | 850ms | ✅ |
| 错误率 | < 1% | 0.5% | ✅ |
| 吞吐量 | > 100 QPS | 120 QPS | ✅ |

### 资源使用

| 资源 | 峰值使用率 | 状态 |
|-----|-----------|------|
| CPU | 65% | ✅ |
| 内存 | 72% | ✅ |
| 数据库连接 | 60% | ✅ |

### 性能瓶颈
- [ ] 待优化项1: ...
- [ ] 待优化项2: ...

### 优化建议
1. 添加Redis缓存热点数据
2. 优化慢查询SQL
3. 增加数据库连接池大小
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           性能测试速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  测试类型:                                   │
│    □ 负载测试 (正常负载)                    │
│    □ 压力测试 (极限测试)                    │
│    □ 基准测试 (单操作性能)                  │
│                                             │
│  响应时间:                                   │
│    □ P50 < 200ms？                          │
│    □ P95 < 500ms？                          │
│    □ P99 < 1s？                             │
│                                             │
│  吞吐量:                                     │
│    □ 满足业务需求？                          │
│    □ 错误率 < 1%？                          │
│                                             │
│  资源使用:                                   │
│    □ CPU < 70%？                            │
│    □ 内存 < 80%？                           │
│                                             │
│  口诀: 响应快，吞吐高，资源省               │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```yaml
❌ 错误示例:
  功能实现后:
  - 不进行性能测试
  - 只测试功能正确性
  - 不关注响应时间

✅ 正确示例:
  功能实现后:
  - 编写性能测试脚本
  - 执行负载测试
  - 记录性能指标
  - 输出性能报告
  - 标注优化建议
```

---

**记住**：功能正确只是基础，性能达标才是生产！
