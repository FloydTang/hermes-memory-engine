---
name: hermes-memory-engine
description: AI九两自主记忆管理系统 — L0自检/L1活跃/L2归档三层架构，自带自动压缩、冷热分离、skill回环
triggers:
  - 每次 session 启动（作为背景知识注入）
  - 周度 cron 调用 memory-health-compaction
  - 用户询问记忆架构或健康状态
tags:
  - memory-management
  - self-evolution
  - archive
  - core-infrastructure
metadata:
  author: AI九两 · 半斤九两科技
  version: 1.0.0
  created: "2026-05-19"
---

# Hermes Memory Engine

## 核心哲学

三条原则决定一切操作：

1. **记忆不是存什么，而是忘什么** — 判断力比容量更重要
2. **冷热分离** — 日常用的才放活跃区，冷数据归档但仍可回溯
3. **记忆长skill** — 重复模式不占记忆，沉淀为skill

## 三层架构

```
┌─────────────────────────────────────────────────────┐
│  L0 — 自检层（毫秒级，零LLM成本）                   │
│  每次 session 注入 + 周度 cron                      │
│  · 检查使用率：<50%健康 / 70%警告 / 85%紧急         │
│  · 触发压缩 / 归档 / 告警                           │
├─────────────────────────────────────────────────────┤
│  L1 — 活跃记忆（Hermes memory 工具，2,200 chars）    │
│  · 标签分类 + P0/P1/P2 优先级                       │
│  · P0: 永不清除 [行为][核心][身份]                   │
│  · P1: 可归档 [平台][群规][infra][架构]              │
│  · P2: 用完清 [备忘][待办]                          │
├─────────────────────────────────────────────────────┤
│  L2 — 冷存储（文件系统归档）                         │
│  · ~/.hermes/memory_archive/YYYY-MM-DD.md           │
│  · INDEX.md 索引全部归档                            │
│  · 需要时 read_file 回溯恢复                        │
│  · skill候选 → evolution-candidates/ 目录           │
└─────────────────────────────────────────────────────┘
```

## 记忆条目格式

```
[标签][优先级] 内容 — 关键细节
```

标签一览：
| 标签 | 优先级 | 说明 |
|------|--------|------|
| `[行为]` | P0 | 用户对我行为方式的期望和纠正 |
| `[核心]` | P0 | 产品线、路径、绝对不能忘的判断 |
| `[身份]` | P0 | 用户个人资料（已迁至 user profile） |
| `[平台]` | P1 | 外部平台信息、工具配置 |
| `[群规]` | P1 | 群组规则 |
| `[infra]` | P1 | 基础设施、文件路径、归档指针 |
| `[架构]` | P1 | 系统架构决策 |
| `[备忘]` | P2 | 临时待办、完成即清 |
| `[待办]` | P2 | 待跟进事项 |

## 核心 Workflows

### 1. L0 健康检查

```
1. 写入临时标记 `[temp] health-check`
2. 从 response 读 `usage` 百分比
3. 删除临时标记
4. 判断：
   <50%  → 健康，跳过
   50-70% → 报告状态
   70-85% → 生成压缩方案，等待用户确认
   >85%   → 立即执行压缩
```

### 2. 压缩与融合

```
1. 按优先级分类所有条目
2. P0 条目不动
3. 同标签 P1 条目尝试合并
4. 删除冗余修饰词
5. 旧版本写入归档文件
6. 更新 memory 工具
```

### 3. 归档

```
1. 读取 INDEX.md 了解已有归档
2. 追加写入当前日期的归档文件
3. 更新 INDEX.md 索引表
4. 被归档的条目从 memory 移除
```

### 4. 恢复

```
1. 用户问"那个XXX信息找不到了"
2. 查 INDEX.md 找到对应归档文件
3. read_file 取回
4. memory(action='add') 恢复
```

### 5. Skill 回环

压缩时自动扫描以下模式：
- 同标签条目数量增长 → 可能是 skill 候选
- 同一工具/配置被重复记录 → 生成配置类 skill
- 用户多次纠正同类问题 → 生成行为规则类 skill
- 高频故障模式 → 生成诊断类 skill

候选写入 `~/.hermes/cron/evolution-candidates/`，与 `hermes-auto-evolution-cron` 共用审批流程。

## 文件位置

| 路径 | 说明 |
|------|------|
| `memory` tool | L1 活跃记忆 |
| `~/.hermes/memory_archive/` | L2 冷存储目录 |
| `~/.hermes/memory_archive/INDEX.md` | 归档索引 |
| `~/.hermes/memory_archive/YYYY-MM-DD.md` | 单次归档文件 |
| `~/.hermes/cron/evolution-candidates/` | skill 候选目录 |
| `~/.hermes/skills/memory-health-compaction/` | 健康管理 skill |
| `~/.hermes/skills/hermes-memory-engine/` | 本 skill（引擎定义） |

## Cron 任务

| 任务 | 频率 | 动作 |
|------|------|------|
| `weekly-memory-compaction` | 每周日 3:00 | L0 自检 → 报告 → 待确认后压缩 |

首次运行仅报告模式，确认后切换为可执行模式。

## 安全边界

1. P0 条目永不删除、永不归档
2. 每次压缩前先读归档，避免重复归档
3. 先报告再执行（首次运行）
4. 旧版本永远保留在归档中
