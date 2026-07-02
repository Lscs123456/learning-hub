# Learning System 协作说明

这个文件用于定义 `learning-hub`、`llm-notes` 和外部训练资料之间的联动方式。后续日常学习时，可以把它当成固定工作流：`learning-hub` 管计划和状态，`llm-notes` 管知识沉淀，训练资料管输入来源。

## 1. 仓库分工

| 仓库/资料 | 职责 | 产出 |
|---|---|---|
| `learning-hub` | 学习任务、进度状态、复习节奏、总览看板 | 任务列表、状态、今日建议、复习提醒 |
| `llm-notes` | LLM 知识库、概念解释、面试题、实验复盘 | 结构化 Markdown 笔记、知识地图、专题索引 |
| 训练资料 | 课程、论文、博客、视频、代码、PDF、面试题 | 待消化材料、摘录、问题、练习任务 |

核心原则：一个学习任务可以关联多个资料，一个资料可以产出多个笔记条目，一个笔记条目可以反向更新任务掌握状态。

## 2. 每日学习工作流

每天学习时，建议按下面顺序推进：

1. 从 `learning-hub` 选出今日任务。
2. 输入训练资料给我，比如课程链接、PDF、文章、代码片段、视频笔记或你自己的疑问。
3. 我把资料拆成可执行学习单元：
   - 今日要理解的概念
   - 今日要写入 `llm-notes` 的笔记
   - 今日要练习的问题
   - 今日需要回顾的旧知识
4. 学习后，我帮你把内容沉淀到 `llm-notes`：
   - 补充或新建专题笔记
   - 提炼关键概念
   - 增加面试问答
   - 增加代码/公式/图示说明
   - 标注来源和复习点
5. 最后同步 `learning-hub` 状态：
   - 更新 `mastery`
   - 更新 `last_review`
   - 记录今日产出
   - 生成下一次复习建议

## 3. 掌握状态定义

`llm-notes/INDEX.md` 中已经使用了下面的 mastery 等级，`learning-hub` 也建议沿用同一套状态：

| mastery | 判定标准 |
|---|---|
| `未接触` | 还没有系统学习，只知道名字或完全不了解 |
| `初识` | 能说出基本定义，知道它解决什么问题 |
| `能讲` | 能用自己的话讲清楚原理、流程、优缺点和常见问题 |
| `能用` | 能在代码、实验、项目或面试题中正确使用 |
| `融汇` | 能横向比较、迁移应用、解释边界条件和工程取舍 |

推荐更新规则：

- 读完资料并写出摘要：`未接触` -> `初识`
- 能脱稿解释并回答基础问题：`初识` -> `能讲`
- 做过代码/实验/题目：`能讲` -> `能用`
- 能和相邻主题建立联系：`能用` -> `融汇`

## 4. 任务数据结构

`learning-hub` 中每个任务建议至少包含这些字段：

```yaml
id: llm-architecture-attention
title: Attention 机制
domain: LLM
track: 02.大语言模型架构
status: in_progress
mastery: 初识
priority: high
source_refs:
  - type: note
    repo: llm-notes
    path: 02.大语言模型架构/1.attention/1.attention.md
  - type: material
    title: Transformer paper
    url: https://arxiv.org/abs/1706.03762
outputs:
  - type: note
    path: 02.大语言模型架构/1.attention/1.attention.md
  - type: qa
    path: 02.大语言模型架构/1.attention/1.attention.md#面试问题
last_review: 2026-06-27
next_review: 2026-06-30
```

## 5. 笔记头部元数据

为了让 `learning-hub` 自动读取 `llm-notes` 的状态，后续新增或重构笔记时，建议在 Markdown 顶部加入 YAML front matter：

```yaml
---
id: llm-architecture-attention
title: Attention 机制
track: 02.大语言模型架构
mastery: 初识
status: in_progress
last_review: 2026-06-27
next_review: 2026-06-30
source:
  - Transformer paper
---
```

这样 `learning-hub` 可以扫描 `llm-notes/**/*.md`，自动生成总览、复习提醒和完成度统计。

## 6. 训练资料接入流程

当你给我一份新资料时，我会按下面格式处理：

| 阶段 | 我会做什么 |
|---|---|
| 资料识别 | 判断资料属于哪个 track，比如基础、架构、训练、推理、RAG、评估、应用 |
| 知识拆解 | 提取核心概念、先修知识、易错点、实践点 |
| 任务映射 | 判断应该创建新任务，还是关联到已有任务 |
| 笔记沉淀 | 写入或更新 `llm-notes` 中对应 Markdown |
| 状态回写 | 给出 `learning-hub` 的状态更新建议 |

## 7. 日常输入模板

你每天可以直接这样发给我：

```text
今天学习资料：
- 资料/链接/文件：
- 我的目标：
- 已经看懂的部分：
- 卡住的问题：
- 希望产出：
```

我会返回：

```text
今日学习任务：
- 必学概念
- 建议阅读顺序
- 需要写入的笔记位置
- 练习/自测题
- learning-hub 状态更新建议
```

如果你让我直接修改仓库，我会继续把笔记和状态文件落到本地。

## 8. 推荐的联动实现

短期实现：

- 在 `learning-hub` 中维护任务状态。
- 在 `llm-notes` 中维护 Markdown 知识内容。
- 用 `id`、`path`、`mastery`、`last_review`、`next_review` 串联两边。

中期实现：

- 给 `llm-notes` 的重点笔记批量补充 front matter。
- 在 `learning-hub` 增加扫描脚本，读取 `llm-notes` 元数据生成看板。
- 在看板中显示每个 track 的完成度、待复习项和今日推荐。

长期实现：

- 支持资料导入队列。
- 支持从 PDF/文章/课程笔记自动生成学习任务。
- 支持按遗忘曲线生成复习计划。
- 支持把面试题、自测题和项目实践结果一起纳入 mastery 评估。

