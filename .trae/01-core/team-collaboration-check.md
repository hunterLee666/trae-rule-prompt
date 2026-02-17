---
name: Team Collaboration Check
description: 团队协作检查触发器 - 团队协作场景时建议执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "during_team_collaboration"
---

# 团队协作检查触发器

> **⚠️ 建议：团队协作场景应遵循协作规范**

---

## 一、触发条件

```yaml
触发场景:
  - 创建PR/MR
  - 代码审查
  - 分支管理
  - 冲突解决
  - 版本发布

不触发场景:
  - 个人项目
  - 用户明确跳过
```

---

## 二、协作角色

```yaml
角色定义:
  Owner:
    权限: 完全控制
    职责: 项目决策、权限管理
    数量: 1-2人
    
  Maintainer:
    权限: 代码合并、分支管理
    职责: 代码审查、版本发布
    数量: 2-5人
    
  Developer:
    权限: 代码提交、PR创建
    职责: 功能开发、Bug修复
    数量: 不限
    
  Reviewer:
    权限: 代码审查、评论
    职责: 质量把关、最佳实践
    数量: 3-10人
```

---

## 三、协作流程

### 3.1 标准开发流程

```yaml
标准开发流程:
  1. 任务分配:
     - 创建Issue/Task
     - 分配责任人
     - 设置优先级
  
  2. 分支创建:
     - 从main创建feature分支
     - 分支命名规范: feature/xxx
     - 定期同步主分支
  
  3. 开发阶段:
     - 小步提交
     - 编写测试
     - 本地验证
  
  4. 代码审查:
     - 创建Pull Request
     - 指定审查者
     - 处理审查意见
  
  5. 合并部署:
     - 通过所有检查
     - 合并到主分支
     - 触发部署流程
```

### 3.2 代码审查流程

```yaml
审查流程:
  1. 创建PR:
     - 填写PR模板
     - 关联Issue
     - 指定审查者
  
  2. 自动检查:
     - CI/CD流水线
     - 代码风格检查
     - 测试覆盖率
  
  3. 人工审查:
     - 代码逻辑
     - 设计方案
     - 最佳实践
  
  4. 处理意见:
     - 修改代码
     - 回复评论
     - 请求重新审查
  
  5. 合并代码:
     - 获得批准
     - Squash合并
     - 删除分支
```

---

## 四、分支管理

### 4.1 分支策略

```yaml
Git Flow:
  main:
    用途: 生产环境代码
    保护: 必须通过PR合并
    命名: main
  
  develop:
    用途: 开发集成分支
    保护: 必须通过PR合并
    命名: develop
  
  feature:
    用途: 功能开发
    命名: feature/xxx
    来源: develop
  
  release:
    用途: 发布准备
    命名: release/v1.0.0
    来源: develop
  
  hotfix:
    用途: 紧急修复
    命名: hotfix/xxx
    来源: main

GitHub Flow:
  main:
    用途: 主分支
    保护: 必须通过PR合并
  
  feature:
    用途: 功能开发
    命名: feature/xxx
    来源: main
```

### 4.2 分支命名规范

```yaml
命名规范:
  功能分支:
    格式: feature/描述
    示例: feature/user-auth
  
  修复分支:
    格式: fix/issue-编号
    示例: fix/issue-123
  
  发布分支:
    格式: release/版本号
    示例: release/v1.0.0
  
  热修复分支:
    格式: hotfix/描述
    示例: hotfix/login-error
```

---

## 五、提交规范

### 5.1 提交信息格式

```yaml
格式:
  <type>(<scope>): <subject>
  
  <body>
  
  <footer>

类型 (type):
  feat: 新功能
  fix: Bug修复
  docs: 文档更新
  style: 代码格式
  refactor: 重构
  test: 测试
  chore: 构建/工具

示例:
  feat(auth): 添加用户登录功能
  
  - 实现JWT认证
  - 添加登录页面
  - 集成OAuth2.0
  
  Closes #123
```

### 5.2 提交最佳实践

```yaml
最佳实践:
  - 原子提交: 每个提交只做一件事
  - 频繁提交: 小步快跑
  - 有意义的信息: 清晰描述变更
  - 关联Issue: 方便追踪

禁止行为:
  - 提交无关变更
  - 提交不完整代码
  - 提交敏感信息
  - 提交大型二进制文件
```

---

## 六、实现示例

```python
from dataclasses import dataclass
from typing import List, Dict, Optional
from enum import Enum

class Role(Enum):
    """协作角色"""
    OWNER = "owner"
    MAINTAINER = "maintainer"
    DEVELOPER = "developer"
    REVIEWER = "reviewer"

class BranchType(Enum):
    """分支类型"""
    MAIN = "main"
    DEVELOP = "develop"
    FEATURE = "feature"
    RELEASE = "release"
    HOTFIX = "hotfix"

@dataclass
class CommitInfo:
    """提交信息"""
    type: str
    scope: str
    subject: str
    body: str
    footer: str
    
    def format(self) -> str:
        """格式化提交信息"""
        result = f"{self.type}({self.scope}): {self.subject}"
        if self.body:
            result += f"\n\n{self.body}"
        if self.footer:
            result += f"\n\n{self.footer}"
        return result

@dataclass
class PullRequest:
    """Pull Request"""
    title: str
    description: str
    source_branch: str
    target_branch: str
    reviewers: List[str]
    labels: List[str]

class BranchManager:
    """分支管理器"""
    
    NAMING_RULES = {
        BranchType.FEATURE: "feature/",
        BranchType.RELEASE: "release/",
        BranchType.HOTFIX: "hotfix/",
        BranchType.FIX: "fix/"
    }
    
    def validate_branch_name(self, name: str) -> bool:
        """验证分支名称"""
        if name in ["main", "develop", "master"]:
            return True
        
        for prefix in self.NAMING_RULES.values():
            if name.startswith(prefix):
                return True
        
        return False
    
    def get_branch_type(self, name: str) -> Optional[BranchType]:
        """获取分支类型"""
        if name in ["main", "master"]:
            return BranchType.MAIN
        if name == "develop":
            return BranchType.DEVELOP
        if name.startswith("feature/"):
            return BranchType.FEATURE
        if name.startswith("release/"):
            return BranchType.RELEASE
        if name.startswith("hotfix/"):
            return BranchType.HOTFIX
        return None

class CommitValidator:
    """提交验证器"""
    
    VALID_TYPES = [
        "feat", "fix", "docs", "style",
        "refactor", "test", "chore"
    ]
    
    def validate_commit_message(self, message: str) -> List[str]:
        """验证提交信息"""
        issues = []
        
        if not message:
            issues.append("提交信息不能为空")
            return issues
        
        first_line = message.split('\n')[0]
        
        if ':' not in first_line:
            issues.append("提交信息格式错误，应为: type(scope): subject")
            return issues
        
        type_part = first_line.split(':')[0]
        
        if '(' in type_part:
            commit_type = type_part.split('(')[0]
        else:
            commit_type = type_part
        
        if commit_type not in self.VALID_TYPES:
            issues.append(f"无效的提交类型: {commit_type}")
        
        return issues
    
    def parse_commit(self, message: str) -> Optional[CommitInfo]:
        """解析提交信息"""
        if ':' not in message:
            return None
        
        lines = message.split('\n')
        first_line = lines[0]
        
        type_scope, subject = first_line.split(':', 1)
        
        if '(' in type_scope:
            commit_type = type_scope.split('(')[0]
            scope = type_scope.split('(')[1].rstrip(')')
        else:
            commit_type = type_scope
            scope = ""
        
        body = ""
        footer = ""
        
        if len(lines) > 1:
            rest = '\n'.join(lines[1:]).strip()
            if '\n\n' in rest:
                body, footer = rest.split('\n\n', 1)
            else:
                body = rest
        
        return CommitInfo(
            type=commit_type.strip(),
            scope=scope.strip(),
            subject=subject.strip(),
            body=body.strip(),
            footer=footer.strip()
        )

class CodeReviewAssistant:
    """代码审查助手"""
    
    def create_pr_checklist(self, pr: PullRequest) -> List[str]:
        """创建PR检查清单"""
        checklist = [
            "□ 代码编译通过",
            "□ 单元测试通过",
            "□ 代码覆盖率达标",
            "□ 无安全漏洞",
            "□ 代码风格符合规范",
            "□ 文档已更新",
            "□ 变更日志已更新"
        ]
        return checklist
    
    def suggest_reviewers(self, pr: PullRequest, 
                         team: Dict[str, List[str]]) -> List[str]:
        """建议审查者"""
        suggestions = []
        
        for role, members in team.items():
            if role in ["maintainer", "reviewer"]:
                suggestions.extend(members)
        
        return list(set(suggestions) - set(pr.reviewers))
```

---

## 七、检查清单

```yaml
分支管理:
  □ 分支命名规范？
  □ 分支来源正确？
  □ 定期同步主分支？

提交规范:
  □ 提交信息格式正确？
  □ 提交类型有效？
  □ 关联Issue？

代码审查:
  □ PR模板完整？
  □ CI检查通过？
  □ 获得足够批准？
  □ 处理所有评论？
```

---

## 八、快速检查卡

```
┌─────────────────────────────────────────────┐
│           团队协作速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  协作角色:                                   │
│    Owner: 完全控制                          │
│    Maintainer: 代码合并                     │
│    Developer: 代码提交                      │
│    Reviewer: 代码审查                       │
│                                             │
│  分支命名:                                   │
│    feature/xxx: 功能开发                    │
│    fix/issue-xxx: Bug修复                   │
│    release/v1.0.0: 发布准备                 │
│    hotfix/xxx: 紧急修复                     │
│                                             │
│  提交格式:                                   │
│    type(scope): subject                     │
│    feat/fix/docs/style/refactor/test/chore  │
│                                             │
└─────────────────────────────────────────────┘
```

---

**记住**：协作有序，效率倍增！
