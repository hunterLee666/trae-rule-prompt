---
name: Error Handling Check
description: 错误处理检查触发器 - 代码生成时强制执行
version: "1.0"
priority: 90
alwaysApply: true
trigger: "during_code_generation"
---

# 错误处理检查触发器

> **⚠️ 强制规则：生成代码时必须包含完善的错误处理**

---

## 一、触发条件

```yaml
触发场景:
  - 生成业务逻辑代码
  - 生成API接口代码
  - 生成数据处理代码
  - 生成文件操作代码
  - 生成网络请求代码

不触发场景:
  - 简单配置代码
  - 纯数据定义
  - 用户明确跳过
```

---

## 二、错误分类体系

### 2.1 错误级别

```yaml
Critical (致命):
  定义: 系统无法继续运行
  示例:
    - 数据库连接失败
    - 核心服务不可用
    - 内存耗尽
    - 配置文件缺失
  处理: 立即终止，通知管理员
  响应时间: 立即
  日志级别: CRITICAL

Error (错误):
  定义: 严重问题，需要立即处理
  示例:
    - 文件不存在
    - 权限不足
    - 配置错误
    - 验证失败
  处理: 终止当前操作，报告用户
  响应时间: 1分钟内
  日志级别: ERROR

Warning (警告):
  定义: 潜在问题，建议处理
  示例:
    - 弃用的API
    - 性能警告
    - 兼容性问题
    - 资源接近上限
  处理: 记录日志，继续执行
  响应时间: 1小时内
  日志级别: WARNING

Info (信息):
  定义: 正常操作信息
  示例:
    - 操作完成
    - 状态变更
    - 进度更新
  处理: 记录日志
  响应时间: 无需响应
  日志级别: INFO
```

### 2.2 错误类型

```yaml
业务错误:
  类型: BusinessError
  特点: 可预期的业务异常
  示例:
    - 用户余额不足
    - 商品库存不足
    - 订单状态不允许操作
  处理: 返回友好提示，引导用户

验证错误:
  类型: ValidationError
  特点: 输入数据不符合要求
  示例:
    - 必填字段缺失
    - 格式不正确
    - 值超出范围
  处理: 返回具体错误信息

权限错误:
  类型: PermissionError
  特点: 用户无权执行操作
  示例:
    - 未登录
    - 角色权限不足
    - 资源访问受限
  处理: 引导登录或提示权限

系统错误:
  类型: SystemError
  特点: 系统内部错误
  示例:
    - 数据库错误
    - 网络超时
    - 服务不可用
  处理: 记录日志，返回通用错误
```

---

## 三、错误处理模式

### 3.1 Python错误处理

```python
from typing import Optional, Result
from dataclasses import dataclass
from enum import Enum
import logging

logger = logging.getLogger(__name__)

class ErrorCode(Enum):
    BUSINESS_ERROR = "BUSINESS_ERROR"
    VALIDATION_ERROR = "VALIDATION_ERROR"
    PERMISSION_ERROR = "PERMISSION_ERROR"
    SYSTEM_ERROR = "SYSTEM_ERROR"

@dataclass
class AppError:
    code: ErrorCode
    message: str
    details: Optional[dict] = None

def process_order(order_id: int) -> Result[dict, AppError]:
    """处理订单 - 带完整错误处理"""
    try:
        # 1. 验证输入
        if not order_id or order_id <= 0:
            logger.warning(f"无效的订单ID: {order_id}")
            return Result.error(AppError(
                code=ErrorCode.VALIDATION_ERROR,
                message="订单ID无效",
                details={"order_id": order_id}
            ))
        
        # 2. 获取订单
        order = get_order(order_id)
        if not order:
            logger.info(f"订单不存在: {order_id}")
            return Result.error(AppError(
                code=ErrorCode.BUSINESS_ERROR,
                message="订单不存在"
            ))
        
        # 3. 检查权限
        if not has_permission(current_user, order):
            logger.warning(f"权限不足: user={current_user.id}, order={order_id}")
            return Result.error(AppError(
                code=ErrorCode.PERMISSION_ERROR,
                message="无权访问此订单"
            ))
        
        # 4. 处理订单
        result = do_process(order)
        logger.info(f"订单处理成功: {order_id}")
        return Result.ok(result)
        
    except DatabaseError as e:
        logger.error(f"数据库错误: {e}", exc_info=True)
        return Result.error(AppError(
            code=ErrorCode.SYSTEM_ERROR,
            message="系统繁忙，请稍后重试"
        ))
    except Exception as e:
        logger.critical(f"未知错误: {e}", exc_info=True)
        return Result.error(AppError(
            code=ErrorCode.SYSTEM_ERROR,
            message="系统错误，请联系管理员"
        ))
```

### 3.2 JavaScript/TypeScript错误处理

```typescript
enum ErrorCode {
  BUSINESS_ERROR = 'BUSINESS_ERROR',
  VALIDATION_ERROR = 'VALIDATION_ERROR',
  PERMISSION_ERROR = 'PERMISSION_ERROR',
  SYSTEM_ERROR = 'SYSTEM_ERROR',
}

interface AppError {
  code: ErrorCode;
  message: string;
  details?: Record<string, unknown>;
}

class ErrorHandler {
  private logger = new Logger();

  async processOrder(orderId: number): Promise<Result<Order, AppError>> {
    try {
      // 1. 验证输入
      if (!orderId || orderId <= 0) {
        this.logger.warn(`无效的订单ID: ${orderId}`);
        return Result.error({
          code: ErrorCode.VALIDATION_ERROR,
          message: '订单ID无效',
          details: { orderId },
        });
      }

      // 2. 获取订单
      const order = await this.getOrder(orderId);
      if (!order) {
        this.logger.info(`订单不存在: ${orderId}`);
        return Result.error({
          code: ErrorCode.BUSINESS_ERROR,
          message: '订单不存在',
        });
      }

      // 3. 处理订单
      const result = await this.doProcess(order);
      this.logger.info(`订单处理成功: ${orderId}`);
      return Result.ok(result);

    } catch (error) {
      return this.handleError(error);
    }
  }

  private handleError(error: unknown): Result<never, AppError> {
    if (error instanceof DatabaseError) {
      this.logger.error('数据库错误', error);
      return Result.error({
        code: ErrorCode.SYSTEM_ERROR,
        message: '系统繁忙，请稍后重试',
      });
    }

    this.logger.critical('未知错误', error);
    return Result.error({
      code: ErrorCode.SYSTEM_ERROR,
      message: '系统错误，请联系管理员',
    });
  }
}
```

---

## 四、错误处理检查清单

```yaml
输入验证:
  □ 参数类型验证
  □ 参数范围验证
  □ 必填参数检查
  □ 格式验证

错误分类:
  □ 区分错误类型
  □ 使用适当的错误级别
  □ 错误信息清晰
  □ 包含错误详情

日志记录:
  □ 关键操作有日志
  □ 错误有堆栈信息
  □ 敏感信息已脱敏
  □ 日志级别正确

错误恢复:
  □ 有恢复策略
  □ 有降级方案
  □ 用户提示友好
  □ 不暴露系统信息
```

---

## 五、快速检查卡

```
┌─────────────────────────────────────────────┐
│           错误处理速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  错误分类:                                   │
│    □ Critical: 立即终止，通知管理员         │
│    □ Error: 终止操作，报告用户              │
│    □ Warning: 记录日志，继续执行            │
│    □ Info: 记录日志                         │
│                                             │
│  错误类型:                                   │
│    □ BusinessError: 业务异常                │
│    □ ValidationError: 验证失败              │
│    □ PermissionError: 权限不足              │
│    □ SystemError: 系统错误                  │
│                                             │
│  处理要求:                                   │
│    □ 输入验证完善                           │
│    □ 错误分类正确                           │
│    □ 日志记录完整                           │
│    □ 用户提示友好                           │
│                                             │
│  口诀: 分类分级，日志友好，不暴露细节       │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 六、违规示例

```yaml
❌ 错误示例:
  def process_order(order_id):
      order = get_order(order_id)
      return process(order)

✅ 正确示例:
  def process_order(order_id: int) -> Result[dict, AppError]:
      try:
          if not order_id:
              logger.warning("订单ID为空")
              return Result.error(AppError(
                  code=ErrorCode.VALIDATION_ERROR,
                  message="订单ID不能为空"
              ))
          
          order = get_order(order_id)
          if not order:
              logger.info(f"订单不存在: {order_id}")
              return Result.error(AppError(
                  code=ErrorCode.BUSINESS_ERROR,
                  message="订单不存在"
              ))
          
          result = process(order)
          logger.info(f"订单处理成功: {order_id}")
          return Result.ok(result)
          
      except Exception as e:
          logger.error(f"处理订单失败: {e}", exc_info=True)
          return Result.error(AppError(
              code=ErrorCode.SYSTEM_ERROR,
              message="系统繁忙，请稍后重试"
          ))
```

---

**记住**：没有错误处理的代码，生产环境就是定时炸弹！
