---
name: Collaboration Check
description: 团队协作检查触发器 - 团队协作场景时建议执行
version: "1.0"
priority: 85
alwaysApply: true
trigger: "during_team_collaboration"
---

# 团队协作检查触发器

> **⚠️ 建议：团队协作场景时遵循协作规范**

---

## 一、触发条件

```yaml
触发场景:
  - 生成代码审查配置
  - 生成PR/CR模板
  - 生成协作流程文档
  - 生成Git工作流配置
  - 用户明确要求团队协作

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
1. 任务分配:
   - 创建Issue/Task
   - 分配责任人
   - 设置优先级
   - 添加标签

2. 分支创建:
   - 从main创建feature分支
   - 命名规范: feature/xxx, fix/xxx
   - 关联Issue

3. 代码开发:
   - 遵循代码规范
   - 编写单元测试
   - 本地测试通过

4. 提交代码:
   - 编写清晰的commit message
   - 关联Issue编号
   - 推送到远程分支

5. 创建PR:
   - 填写PR模板
   - 添加审查者
   - 等待审查

6. 代码审查:
   - 审查代码质量
   - 提出修改建议
   - 批准或拒绝

7. 合并代码:
   - 通过所有检查
   - 获得足够批准
   - 合并到主分支
```

### 3.2 分支策略

```yaml
Git Flow:
  main: 生产分支，只接受合并
  develop: 开发分支，日常开发
  feature/*: 功能分支
  release/*: 发布分支
  hotfix/*: 紧急修复分支

GitHub Flow:
  main: 主分支，始终可部署
  feature/*: 功能分支，完成后合并

Trunk Based:
  main: 主分支，直接提交
  release/*: 发布分支（可选）
```

### 3.3 Commit规范

```yaml
格式:
  <type>(<scope>): <subject>
  
  <body>
  
  <footer>

类型:
  feat: 新功能
  fix: 修复Bug
  docs: 文档变更
  style: 代码格式
  refactor: 重构
  test: 测试相关
  chore: 构建/工具

示例:
  feat(auth): 添加OAuth2登录支持
  
  - 实现OAuth2授权流程
  - 添加Google登录
  - 添加GitHub登录
  
  Closes #123
```

---

## 四、代码审查规范

```yaml
审查要点:
  代码质量:
    - 代码风格一致
    - 命名清晰
    - 逻辑清晰
    - 无重复代码
  
  功能正确:
    - 实现符合需求
    - 边界情况处理
    - 错误处理完善
  
  测试覆盖:
    - 单元测试存在
    - 测试覆盖充分
    - 测试用例有效
  
  安全性:
    - 无安全漏洞
    - 敏感信息保护
    - 输入验证

审查流程:
  1. 检查PR描述是否完整
  2. 检查CI是否通过
  3. 审查代码变更
  4. 提出审查意见
  5. 批准或请求修改
```

---

## 五、协作检查清单

```yaml
任务分配:
  □ Issue已创建？
  □ 责任人已分配？
  □ 优先级已设置？

代码开发:
  □ 分支命名规范？
  □ 代码风格一致？
  □ 测试已编写？

提交代码:
  □ Commit message规范？
  □ 关联Issue？
  □ 无敏感信息？

代码审查:
  □ PR描述完整？
  □ CI通过？
  □ 审查意见已处理？
  □ 获得批准？
```

---

## 六、快速检查卡

```
┌─────────────────────────────────────────────┐
│           团队协作速查表                     │
├─────────────────────────────────────────────┤
│                                             │
│  分支命名:                                   │
│    feature/xxx - 新功能                     │
│    fix/xxx - Bug修复                        │
│    hotfix/xxx - 紧急修复                    │
│                                             │
│  Commit格式:                                 │
│    type(scope): subject                     │
│    feat(auth): 添加登录功能                 │
│                                             │
│  PR检查:                                     │
│    □ 描述完整                               │
│    □ CI通过                                 │
│    □ 测试覆盖                               │
│    □ 审查批准                               │
│                                             │
│  审查要点:                                   │
│    □ 代码质量                               │
│    □ 功能正确                               │
│    □ 安全性                                 │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 七、违规示例

```yaml
❌ 错误示例:
  # 不规范的commit
  git commit -m "fix bug"
  
  # 直接推送到main
  git push origin main

✅ 正确示例:
  # 规范的commit
  git commit -m "fix(auth): 修复登录超时问题
  
  - 增加token刷新逻辑
  - 添加超时重试机制
  
  Closes #456"
  
  # 创建PR流程
  git checkout -b fix/auth-timeout
  git push origin fix/auth-timeout
  # 然后创建PR
```

---

**记住**：协作有序，效率倍增！
