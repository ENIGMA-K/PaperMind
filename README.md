# PaperMind

> Obsidian × Claude Code · 丢论文进去，LLM 自动入库、交叉引用、追踪进度、发现空白。

[![Version](https://img.shields.io/badge/version-v1.0-333333)](https://github.com/ENIGMA-K/PaperMind/releases)
[![License: GPL v3](https://img.shields.io/badge/license-GPL%20v3-blue)](https://www.gnu.org/licenses/gpl-3.0.en.html)
[![Obsidian](https://img.shields.io/badge/Obsidian-%E2%9C%93-7C3AED)](https://obsidian.md/)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-%E2%9C%93-d97706)](https://docs.anthropic.com/en/docs/claude-code/overview)

## 为什么需要 PaperMind

PDF 堆满文件夹，读过和没读过的混成一团；想写综述，翻三个月前的论文死活找不到；临床冒出问题，PubMed 检索式改了又改，最后还是漏掉关键文献；追参考文献链时，一篇引一篇，半天过去了还在第一层。

**PaperMind 不是又一个文献管理软件。** 它是一套架在 Obsidian 和 Claude Code 之上的 AI 文献工作流引擎——你把 PDF 丢进去，LLM 自动拆解研究问题、评估方法学质量、提取术语、建立交叉引用。你说「已读」，它记进度；你问「这个领域还有什么空白」，它扫描全库给你标出来。

### 核心能力

- **没有种子论文？** `/pub-research` 一次性搜遍 PubMed、Semantic Scholar、Europe PMC、arXiv、知网、万方、维普——7 数据库并行检索，自动去重、按影响力排序、交叉验证结论。中英文文献通吃。
- **有种子论文？** `/blast` 沿参考文献链逐层爆发，按 CiteScore 和 h-index 自动筛出高价值文献——不会让你在垃圾引用里刨金。
- **读论文时**，LLM 自动做八维度方法学评估：研究设计、多中心与否、样本量、盲法、终点指标、统计方法、缺失数据处理、纳入标准——不用自己对着 Methods 一节一节抠。
- **写论文时**，说「写作建议」，AI 扫描你的知识库，告诉你哪篇文献可以支撑哪个论点，甚至写出段落草稿——每个论点旁标出 `[!thesis-idea]`，缺支撑、有空白一目了然。
- **日常答疑**，`/query` 直接检索你读过的一切。不是搜文件名，而是问一个懂你的 AI：「T1 mapping 在心肌炎里的诊断价值如何？」它能综合你录入的所有论文给你答案。
- **定期体检**，`/lint` 扫一遍全库：哪里有矛盾结论？哪些页面成了孤岛？哪些 stub 躺了 30 天还没填？期刊数据有没有过期？

**零插件，纯 Markdown。** 没有 Obsidian 第三方插件依赖，所有工作流靠 `.claude/commands/`、`.claude/skills/`、`.claude/agents/` 驱动。知识全部以纯文本 Markdown 存储——你的数据永远不会被锁死在某个软件里。

**换个领域？** 改两行 CLAUDE.md 的标签体系就行，心内科变神经科只是换几个标签的事。

## 安装

```bash
# 1. 确保已安装 Obsidian + Claude Code
npm install -g @anthropic-ai/claude-code

# 2. 克隆到 Obsidian vaults 目录
git clone https://github.com/ENIGMA-K/PaperMind.git
cd PaperMind

# 3. Obsidian → 打开本文件夹 → 设置 → 核心插件 → 启用 Bases
# 4. Claude Code → 打开同一目录 → 输入 /ingest 测试
```

## 快速开始

```bash
# 把 PDF 拖进 raw/artical/，然后说：
/ingest       # 读 PDF → 提取关键信息 → 写摘要 → 建实体页 → 更新索引

# 没有种子论文时：
/pub-research CABG术后桥血管通畅率的影像学评估    # 多库检索摸底
/blast 10.1056/NEJMoa2302983 --depth 2           # 从种子追引用链
/gap --scope "心肌水肿的CMR评估"                   # 识别研究空白

# 日常维护：
/query T1 mapping 在心肌炎中的诊断价值             # 检索已有知识
/lint                                           # 健康检查
/export [[某篇综述]] --format bibtex              # 导出参考文献
/logit                                          # 每日工作日志
```

## 命令与技能

**🔍 `/pub-research <问题>`** — 7 数据库并行检索，中英文文献通吃，输出交叉验证报告  
**💥 `/blast <DOI> [--depth N]`** — 沿参考文献链逐层爆发，按 CiteScore + h-index 自动筛选高价值文献  
**📥 `/ingest`** — 录入论文：自动查重 → 方法学评估 → 术语提取 → 写摘要 → 更新索引  
**🕳️ `/gap [--scope 方向]`** — 五维空白扫描：人群 / 方法 / 对照 / 结局 / 时间  
**🔎 `/query <问题>`** — 检索已有知识库，综合所有录入论文作答；支持作者合作网络分析  
**🩺 `/lint`** — 健康检查：矛盾结论、孤页、过期 stub、期刊数据刷新  
**📄 `/export <页面> [--bibtex|ris]`** — 导出参考文献  
**📝 `/logit`** — 生成每日工作日志  

说「**审视这篇**」→ 自动 8 维度方法学质量评估  
说「**PICO 分析**」→ 临床问题拆为 P-I-C-O，生成精准检索式  
说「**写作建议**」→ 扫描知识库，定位文献支撑论点，自动写段落草稿  
说「**已速览 / 已读 / 已分析**」→ 自动同步阅读进度

## 自定义

- 换领域 → 编辑 `CLAUDE.md` 标签体系
- 改行为 → 编辑 `.claude/commands/` 对应文件
- 加功能 → 在 `.claude/` 中参考已有格式添加

## License

Free to use, modify, and share; derivative works must be released under the [GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html).
