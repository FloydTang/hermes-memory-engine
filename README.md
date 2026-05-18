# Hermes Memory Engine

AI 原生记忆管理系统。三层架构：L0 自检 → L1 活跃记忆 → L2 冷存储。

由 **半斤九两科技 · AI九两** 设计并维护。

## 核心理念

- **记忆不是存什么，而是忘什么** — 判断力比容量更重要
- **冷热分离** — 日常用的才放活跃区，冷数据归档但仍可回溯
- **记忆长skill** — 重复模式不占记忆，沉淀为可复用技能

## 架构速览

```
L0（自检层）
  每次 session 启动 + 周度 cron
  检查使用率 → 触发压缩/归档/告警
  
L1（活跃记忆）
  系统记忆工具，2,200 chars 上限
  标签 + 优先级（P0 永不清除 / P1 可归档 / P2 用完清）

L2（冷存储）
  文件系统归档 ~/.hermes/memory_archive/
  INDEX.md 索引全部冷数据
  需要时回溯恢复
```

## 适合谁用

- 需要自主记忆管理的 AI Agent
- 记忆容量受限但需要长期进化的系统
- 希望记忆能自然沉淀为技能的长期运行 agent

## 仓库结构

```
hermes-memory-engine/
├── README.md          ← 本文件
├── ARCHITECTURE.md    ← 三层架构详解
├── WORKFLOW.md        ← 日常工作流：检查/压缩/归档/恢复
├── FORMAT.md          ← 记忆条目格式规范
├── SKILL.md           ← 可导入 AI 系统的 skill 定义
└── LICENSE            ← 开源协议
```

## 快速开始

1. 阅读 `ARCHITECTURE.md` 理解三层设计
2. 按 `FORMAT.md` 规范初始化记忆条目
3. 配置 `WORKFLOW.md` 中的 cron 任务
4. 导入 `SKILL.md` 到你的 AI 系统

---

© 2026 半斤九两科技 · AI九两
