# Hermes Memory Engine — Workflows

## 1. L0 健康检查

### 触发条件
- 每周日 3:00（cron 定时）
- 用户询问记忆状态

### 步骤
```
1. 写入临时标记到 memory 工具
   → 内容: "[temp] health-check — 本次检查后删除"
   → 从 response 读取 usage 百分比
2. 删除临时标记
   → memory(action='remove', old_text='[temp] health-check')
3. 判断阈值
   - <50%  → 报告"健康"，结束
   - 50-70% → 报告当前状态和条目数量
   - 70-85% → 报告 + 生成压缩方案，等待用户确认
   - >85%   → 报告 + 建议立即压缩
4. 可选：在同一次运行中完成报告
```

## 2. 压缩与融合

### 触发条件
- L0 检查发现使用率 >70%
- 用户确认执行压缩
- 手动触发

### 步骤
```
1. 读取所有 memory 条目
2. 按优先级分类：
   - P0: 不动
   - P1: 检查能否合并同标签条目
   - P2: 评估是否已完成
3. 合并策略：
   - 同标签两条 → "[标签][P1] 主题A + 主题B — 细节"
   - 冗余修饰词删除: "比较" "可能" "大概" → 删
   - 技术细节必须保留: URL、路径、命令
4. 旧版本先写入归档文件
5. 更新 memory 工具
```

## 3. 归档

### 触发条件
- 压缩过程中产生的旧数据
- 完成的 P2 条目
- 过时的 P1 条目

### 步骤
```
1. 读取 INDEX.md，了解已有归档
2. 创建/追加当前日期的归档文件：
   ~/.hermes/memory_archive/YYYY-MM-DD.md
3. 每个归档条目带类别标记：
   [resolved] [config] [course] [personal]
4. 更新 INDEX.md 索引表
5. 从 memory 移除被归档的条目
```

## 4. 恢复

### 触发条件
- 用户提到已被归档的信息
- 需要回溯历史配置或决策

### 步骤
```
1. 索引定位
   → read_file(~/.hermes/memory_archive/INDEX.md)
   → 找到对应日期的归档文件
2. 读取归档文件
   → read_file(~/.hermes/memory_archive/YYYY-MM-DD.md)
3. 取回所需条目
   → memory(action='add', content='...')
```

## 5. Skill 回环

### 触发条件
- 压缩时发现重复模式
- 同标签条目数量增长
- 用户多次纠正同类问题

### 信号识别
| 信号 | 行动 |
|------|------|
| 同一问题被记录 2+ 次 | 写入 skill 候选 |
| 同标签条目从 1 变 3+ | 主题可能需要 skill 化 |
| 同一工具/路径被重复记录 | 生成配置类 skill 候选 |
| >2 个已归档条目指向同一类问题 | 评估是否生成诊断类 skill |

### 输出
写入 `~/.hermes/cron/evolution-candidates/{日期}_{skill名}.md`
格式与 `hermes-auto-evolution-cron` 兼容，复用其审批流程。
