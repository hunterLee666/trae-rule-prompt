---
name: Resilience Check
description: 系统韧性检查触发器 - 生成服务代码时强制执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_service_code_generation"
---

# 系统韧性检查触发器

> **⚠️ 强制规则：生成服务代码时必须考虑韧性设计**

---

## 一、触发条件

```yaml
触发场景:
  - 生成API调用代码
  - 生成数据库操作代码
  - 生成外部服务调用代码
  - 生成微服务代码
  - 生成网络请求代码

不触发场景:
  - 简单工具函数
  - 纯计算逻辑
  - 本地数据处理
```

---

## 二、韧性设计要素

### 2.1 熔断机制

```yaml
熔断器状态:
  Closed (关闭):
    条件: 正常状态
    行为: 所有请求正常通过
    转换: 错误率超过阈值 → Open
  
  Open (打开):
    条件: 熔断状态
    行为: 所有请求快速失败
    转换: 超时后 → Half-Open
  
  Half-Open (半开):
    条件: 探测状态
    行为: 允许部分请求通过
    转换: 
      成功 → Closed
      失败 → Open

熔断配置:
  错误率阈值: 50%
  最小请求数: 10
  熔断时长: 30s
  半开请求数: 3
```

### 2.2 重试策略

```yaml
重试配置:
  最大重试次数: 3
  重试间隔: 指数退避
  初始间隔: 100ms
  最大间隔: 10s
  抖动因子: 0.5

重试条件:
  可重试错误:
    - 网络超时
    - 连接失败
    - 服务暂时不可用 (503)
    - 速率限制 (429)
  
  不可重试错误:
    - 认证失败 (401)
    - 权限不足 (403)
    - 资源不存在 (404)
    - 参数错误 (400)
```

### 2.3 降级策略

```yaml
降级方案:
  服务降级:
    条件: 下游服务不可用
    动作: 返回缓存数据或默认值
  
  数据库降级:
    条件: 数据库连接失败
    动作: 返回缓存数据或只读模式
  
  API降级:
    条件: 外部API超时
    动作: 返回默认值或错误提示

降级响应示例:
  {
    "success": false,
    "error": "SERVICE_DEGRADED",
    "message": "服务暂时不可用，请稍后重试",
    "fallback": true
  }
```

### 2.4 超时控制

```yaml
超时配置:
  连接超时: 5s
  读取超时: 30s
  写入超时: 30s
  总超时: 60s

超时处理:
  - 记录超时日志
  - 触发熔断检查
  - 返回超时错误
  - 可选：执行降级方案
```

---

## 三、韧性代码模板

### 3.1 Python示例

```python
import asyncio
from circuitbreaker import circuit
from tenacity import retry, stop_after_attempt, wait_exponential

class ResilientService:
    def __init__(self):
        self.cache = {}
    
    @circuit(failure_threshold=5, recovery_timeout=30)
    @retry(
        stop=stop_after_attempt(3),
        wait=wait_exponential(multiplier=1, min=1, max=10)
    )
    async def call_external_api(self, url: str, timeout: int = 30):
        """调用外部API，带熔断和重试"""
        try:
            async with asyncio.timeout(timeout):
                response = await self._make_request(url)
                return response
        except asyncio.TimeoutError:
            raise ServiceTimeoutError(f"API调用超时: {url}")
        except ConnectionError as e:
            raise ServiceUnavailableError(f"服务不可用: {e}")
    
    async def call_with_fallback(self, url: str):
        """带降级的调用"""
        try:
            return await self.call_external_api(url)
        except (ServiceTimeoutError, ServiceUnavailableError):
            return self._get_fallback_response(url)
    
    def _get_fallback_response(self, url: str):
        """获取降级响应"""
        if url in self.cache:
            return self.cache[url]
        return {"success": False, "error": "SERVICE_DEGRADED"}
```

### 3.2 JavaScript/TypeScript示例

```typescript
import CircuitBreaker from 'opossum';

class ResilientService {
  private cache: Map<string, any> = new Map();
  private breaker: CircuitBreaker;

  constructor() {
    this.breaker = new CircuitBreaker(this.callExternalApi.bind(this), {
      timeout: 30000,
      errorThresholdPercentage: 50,
      resetTimeout: 30000
    });
    
    this.breaker.fallback(() => ({ success: false, degraded: true }));
  }

  async callWithResilience(url: string): Promise<any> {
    return this.breaker.fire(url);
  }

  private async callExternalApi(url: string): Promise<any> {
    const response = await fetch(url, {
      signal: AbortSignal.timeout(30000)
    });
    
    if (!response.ok) {
      throw new Error(`API error: ${response.status}`);
    }
    
    return response.json();
  }
}
```

---

## 四、检查清单

```yaml
韧性检查项:

  熔断机制:
    □ 是否添加熔断器？
    □ 熔断阈值是否合理？
    □ 是否有熔断恢复机制？
  
  重试策略:
    □ 是否添加重试机制？
    □ 重试次数是否合理？
    □ 是否使用指数退避？
    □ 是否区分可重试错误？
  
  降级方案:
    □ 是否有降级方案？
    □ 降级响应是否友好？
    □ 是否有缓存机制？
  
  超时控制:
    □ 是否设置超时？
    □ 超时时间是否合理？
    □ 超时后是否有处理？
```

---

## 五、快速检查卡

```
┌─────────────────────────────────────────────┐
│           系统韧性速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  熔断机制:                                   │
│    □ 熔断器已添加？                          │
│    □ 阈值设置合理？                          │
│    □ 恢复机制存在？                          │
│                                             │
│  重试策略:                                   │
│    □ 重试次数 ≤ 3？                          │
│    □ 使用指数退避？                          │
│    □ 区分错误类型？                          │
│                                             │
│  降级方案:                                   │
│    □ 有降级响应？                            │
│    □ 有缓存机制？                            │
│                                             │
│  超时控制:                                   │
│    □ 设置超时时间？                          │
│    □ 超时处理完善？                          │
│                                             │
│  口诀: 熔断重试降级超时，四件套不能少        │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 六、违规示例

```yaml
❌ 错误示例:
  async def call_api(url):
      response = await fetch(url)
      return response.json()

✅ 正确示例:
  @circuit(failure_threshold=5, recovery_timeout=30)
  @retry(stop=stop_after_attempt(3), wait=wait_exponential())
  async def call_api(url):
      try:
          async with asyncio.timeout(30):
              response = await fetch(url)
              return response.json()
      except (TimeoutError, ConnectionError):
          return get_cached_response(url)
```

---

**记住**：没有韧性设计的服务，生产环境必出问题！
