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

---

<!-- jiuliang-about-start -->

## 关于半斤九两 / About EVEN BETTER

半斤九两科技（EVEN BETTER）专注“外贸 + AI”的真实落地。我们希望帮助外贸企业把产品、客户、渠道和团队流程，沉淀成客户看得懂、渠道跑得动、团队留得下的系统。

我们主要提供：

- 外贸 AI 落地方法：围绕 Build / Traffic / Team，判断企业该先建资产、放流量，还是建系统。
- 企业表达与内容增长：把产品、案例、FAQ、老板经验和信任证据，整理成海外客户看得懂的内容资产。
- 主动开发流程：从客户画像、线索搜索、客户背调到开发信和跟进复盘，跑出可复用闭环。
- 团队 AI 工作流：把经验写进 AGENTS.md、SOP、模板库、检查清单和可复用 Skill。

更多内容可以查看我们整理的 [《外贸人 Codex 蓝皮书》](https://github.com/FloydTang/waimaoren-codex-bluebook)。

### 找到我们

- 官网：[tang92.com](https://tang92.com)
- 公众号：半斤九两
- GitHub：[@FloydTang](https://github.com/FloydTang)

扫码关注公众号，领取后续模板、案例和更新；也可以通过公众号后台留言联系九两。

<p>
  <img src="https://raw.githubusercontent.com/FloydTang/waimaoren-codex-bluebook/main/assets/wechat-qr.png" alt="半斤九两公众号二维码" width="180">
</p>

<!-- jiuliang-about-end -->
