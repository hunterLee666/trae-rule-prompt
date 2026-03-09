# TOOLS

> 环境配置与工具使用

---

## 可用工具

### 内置工具

| 工具 | 用途 | 示例 |
|------|------|------|
| Read | 读取文件 | `Read file_path` |
| Write | 写入文件 | `Write file_path content` |
| SearchReplace | 编辑文件 | `SearchReplace file_path old_str new_str` |
| Grep | 搜索代码 | `Grep pattern path` |
| RunCommand | 执行命令 | `RunCommand command` |
| WebSearch | 网络搜索 | `WebSearch query` |
| WebFetch | 抓取网页 | `WebFetch url` |

### MCP 扩展

| MCP | 用途 |
|-----|------|
| WebSearch | 实时信息 |
| WebFetch | 网页内容 |
| Filesystem | 文件操作 |
| GitHub | 代码搜索 |

---

## 工具优先级

```
1. MCP（扩展能力）
   └── 外部服务集成

2. Skill（技能增强）
   └── 浏览器、Git 等专业能力

3. 内置工具（基础能力）
   └── 搜索、读取、编辑、执行

4. 推理（最后手段）
   └── 当工具无法解决时
```

### Skill 知识库

| Skill | 用途 | 触发场景 |
|-------|------|----------|
| **playwright** | 浏览器自动化 | 端到端测试、网页截图 |
| **git-master** | Git 操作 | 提交、rebase、合并 |
| **dev-browser** | 浏览器交互 | 填表、点击、截图 |

---

## 搜索策略

### 何时搜索

- 不确定的信息
- 最新技术动态
- 外部库文档

### 停止条件

- 足够上下文可以继续
- 相同信息重复出现
- 2次搜索无新数据

---

## 并行探索（模拟多 Agent）

**替代方案**: 不委托，自己并行探索

```
复杂任务时:
┌─────────────────────────────────────┐
│  探索任务 A │  探索任务 B │ 探索任务 C│
│  (并行执行)  │  (并行执行) │ (并行执行)│
└─────────────────────────────────────┘
    ↓
  整合结果
```

**并行探索示例**:
```
任务：理解一个复杂项目

同时执行:
├── Read: src/main.py
├── Grep: import
├── Grep: class/function
├── Read: package.json
└── Read: README.md

→ 整合所有信息，形成全局理解
```

---

## 执行纪律

1. **搜索优先** - 不确定先验证
2. **最小改动** - Edit 优于 Write
3. **验证必做** - LSP/编译/测试

---

> 工具是手段，交付是目的
