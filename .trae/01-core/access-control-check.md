---
name: Access Control Check
description: 访问控制检查触发器 - 权限管理代码生成时强制执行
version: "1.0"
priority: 100
alwaysApply: true
trigger: "during_permission_code_generation"
---

# 访问控制检查触发器

> **⚠️ 强制规则：权限管理代码必须遵循最小权限原则**

---

## 一、触发条件

```yaml
触发场景:
  - 生成权限管理代码
  - 生成认证授权代码
  - 生成角色管理代码
  - 生成API访问控制代码
  - 生成资源访问代码

不触发场景:
  - 简单工具函数
  - 纯计算逻辑
  - 用户明确跳过
```

---

## 二、权限模型

### 2.1 RBAC (基于角色)

```yaml
角色定义:
  admin:
    权限: 完全访问
    适用: 系统管理员
  
  developer:
    权限: 开发权限
    适用: 开发人员
  
  viewer:
    权限: 只读权限
    适用: 普通用户

权限类型:
  read: 读取权限
  write: 写入权限
  execute: 执行权限
  admin: 管理权限
```

### 2.2 ABAC (基于属性)

```yaml
属性类型:
  用户属性:
    - 角色
    - 部门
    - 级别
  
  资源属性:
    - 类型
    - 敏感度
    - 所有者
  
  环境属性:
    - 时间
    - 位置
    - 设备

规则示例:
  - IF user.role == "admin" THEN allow
  - IF resource.sensitivity == "high" AND user.level < 3 THEN deny
```

---

## 三、权限级别

```yaml
Level 1 - 只读:
  允许:
    - 读取文件
    - 搜索内容
    - 查看日志
  禁止:
    - 修改文件
    - 执行命令
    - 访问敏感数据

Level 2 - 标准:
  允许:
    - Level 1全部权限
    - 修改非敏感文件
    - 执行安全命令
  禁止:
    - 修改系统配置
    - 访问核心数据

Level 3 - 高级:
  允许:
    - Level 2全部权限
    - 修改配置文件
    - 执行管理命令
  禁止:
    - 修改核心系统
    - 访问密钥

Level 4 - 管理:
  允许:
    - Level 3全部权限
    - 完全系统访问
    - 密钥管理
  禁止:
    - 无
```

---

## 四、实现示例

```python
from enum import Enum
from functools import wraps
from typing import List, Optional

class Permission(Enum):
    """权限枚举"""
    READ = "read"
    WRITE = "write"
    EXECUTE = "execute"
    ADMIN = "admin"

class Role:
    """角色定义"""
    def __init__(self, name: str, permissions: List[Permission]):
        self.name = name
        self.permissions = permissions

ROLES = {
    "viewer": Role("viewer", [Permission.READ]),
    "developer": Role("developer", [Permission.READ, Permission.WRITE, Permission.EXECUTE]),
    "admin": Role("admin", list(Permission)),
}

def check_permission(user_role: str, required_permission: Permission) -> bool:
    """检查权限"""
    role = ROLES.get(user_role)
    if not role:
        return False
    return required_permission in role.permissions

def require_permission(permission: Permission):
    """权限装饰器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            user_role = kwargs.get("user_role") or args[0] if args else None
            if not check_permission(user_role, permission):
                raise PermissionError(f"需要 {permission.value} 权限")
            return func(*args, **kwargs)
        return wrapper
    return decorator

@require_permission(Permission.WRITE)
def modify_file(path: str, content: str, user_role: str):
    """修改文件 - 需要写权限"""
    with open(path, 'w') as f:
        f.write(content)
```

---

## 五、检查清单

```yaml
权限设计:
  □ 遵循最小权限原则？
  □ 权限分级合理？
  □ 角色定义清晰？
  □ 权限继承正确？

权限检查:
  □ 每个操作都有权限检查？
  □ 权限检查位置正确？
  □ 默认拒绝策略？
  □ 失败处理完善？

安全措施:
  □ 权限变更记录？
  □ 异常访问告警？
  □ 定期权限审计？
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           访问控制速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  核心原则:                                   │
│    □ 最小权限原则                           │
│    □ 默认拒绝                               │
│    □ 显式授权                               │
│                                             │
│  权限级别:                                   │
│    Level 1: 只读                            │
│    Level 2: 标准                            │
│    Level 3: 高级                            │
│    Level 4: 管理                            │
│                                             │
│  检查要点:                                   │
│    □ 每操作都检查权限                       │
│    □ 权限检查在操作前                       │
│    □ 失败有明确提示                         │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```python
❌ 错误示例:
  def delete_user(user_id: int):
      # 无权限检查
      db.delete(user_id)

✅ 正确示例:
  @require_permission(Permission.ADMIN)
  def delete_user(user_id: int, user_role: str):
      """删除用户 - 需要管理权限"""
      if user_id == current_user.id:
          raise ValueError("不能删除自己")
      db.delete(user_id)
      log_security_event("user_delete", str(user_id), "success", user_id=current_user.id)
```

---

**记住**：最小权限，按需授权！
