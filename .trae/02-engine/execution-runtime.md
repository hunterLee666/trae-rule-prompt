---
name: Execution Runtime
description: 执行运行时 - 统一的代码执行环境
version: "7.0"
priority: 90
alwaysApply: true
---

# 执行运行时

> **"安全执行，可靠结果。"**

## 一、执行环境

```yaml
执行能力:
  文件操作:
    - read: 读取文件
    - write: 写入文件
    - edit: 编辑文件
    - delete: 删除文件
    - glob: 查找文件
    - grep: 搜索内容
    
  代码分析:
    - lsp_goto_definition: 跳转定义
    - lsp_find_references: 查找引用
    - lsp_symbols: 符号提取
    - lsp_diagnostics: 诊断检查
    - ast_grep_search: AST搜索
    
  系统命令:
    - bash: 执行命令
    - interactive_bash: 交互式命令
    
  网络操作:
    - webfetch: 获取网页
    - websearch: 网络搜索
```

## 二、执行原则

```yaml
安全原则:
  1. 最小权限: 只执行必要操作
  2. 验证优先: 修改后立即验证
  3. 可回滚: 保持操作可撤销
  4. 审计追踪: 记录所有操作

效率原则:
  1. 并行优先: 独立操作并行执行
  2. 批量处理: 合并相似操作
  3. 缓存复用: 避免重复读取
  4. 延迟加载: 按需获取数据
```

## 三、文件操作

### 3.1 读取文件

```yaml
read操作:
  用途: 读取文件内容
  参数:
    file_path: 文件路径(绝对路径)
    limit: 读取行数(可选)
    offset: 起始行(可选)
    
  最佳实践:
    - 使用绝对路径
    - 大文件分批读取
    - 优先读取关键部分
```

### 3.2 写入文件

```yaml
write操作:
  用途: 写入文件内容
  参数:
    file_path: 文件路径(绝对路径)
    content: 写入内容
    
  安全检查:
    - 路径有效性验证
    - 文件存在性检查
    - 内容合法性验证
```

### 3.3 编辑文件

```yaml
edit操作:
  用途: 编辑文件内容
  参数:
    file_path: 文件路径
    old_str: 要替换的内容
    new_str: 新内容
    
  最佳实践:
    - old_str要足够精确
    - 包含上下文确保唯一
    - 编辑后立即验证
```

## 四、代码分析

### 4.1 LSP操作

```yaml
lsp_goto_definition:
  用途: 跳转到定义
  场景: 理解代码结构
  
lsp_find_references:
  用途: 查找所有引用
  场景: 重构前影响分析
  
lsp_symbols:
  用途: 提取文件符号
  场景: 快速理解文件结构
  
lsp_diagnostics:
  用途: 获取诊断信息
  场景: 代码质量检查
```

### 4.2 AST搜索

```yaml
ast_grep_search:
  用途: 基于AST的代码搜索
  优势: 语义级别匹配
  场景: 代码模式匹配、重构
```

## 五、系统命令

### 5.1 Bash执行

```yaml
bash操作:
  用途: 执行系统命令
  参数:
    command: 命令字符串
    cwd: 工作目录(可选)
    blocking: 是否阻塞(可选)
    
  安全检查:
    - 命令白名单验证
    - 危险命令拦截
    - 敏感操作确认
```

### 5.2 命令分类

```yaml
安全命令 (自动执行):
  - ls, cat, head, tail
  - grep, find, wc
  - git status, git log
  - npm list, pip list

需确认命令:
  - rm, mv
  - git push, git reset
  - npm install, pip install

禁止命令:
  - rm -rf /
  - sudo
  - chmod 777
```

## 六、网络操作

### 6.1 网页获取

```yaml
webfetch操作:
  用途: 获取网页内容
  参数:
    url: 网页URL
    
  最佳实践:
    - 验证URL合法性
    - 处理超时情况
    - 解析HTML为Markdown
```

### 6.2 网络搜索

```yaml
websearch操作:
  用途: 搜索网络信息
  参数:
    query: 搜索关键词
    num: 结果数量(可选)
    
  最佳实践:
    - 精确搜索关键词
    - 限制结果数量
    - 验证信息可靠性
```

## 七、执行流程

```yaml
标准执行流程:
  1. 接收任务
     - 解析任务需求
     - 确定执行策略
     
  2. 准备执行
     - 收集必要信息
     - 验证执行条件
     
  3. 执行操作
     - 按计划执行
     - 监控执行状态
     
  4. 验证结果
     - 检查执行结果
     - 运行验证测试
     
  5. 报告结果
     - 汇总执行情况
     - 提供后续建议
```
