# PaperMind

> Obsidian × Claude Code · 丢论文进去，LLM 自动入库、交叉引用、追踪进度、发现空白。

[![License: GPL v3](https://img.shields.io/badge/license-GPL%20v3-blue)](https://www.gnu.org/licenses/gpl-3.0.en.html)
[![Obsidian](https://img.shields.io/badge/Obsidian-%E2%9C%93-7C3AED)](https://obsidian.md/)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-%E2%9C%93-d97706)](https://docs.anthropic.com/en/docs/claude-code/overview)

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

## 命令速览

| | 命令 | 说明 |
|------|------|------|
| 🔍 | `/pub-research <问题>` | 7 数据库并行检索，输出交叉验证报告 |
| 💥 | `/blast <DOI> [--depth N]` | 沿参考文献链逐层爆发，按影响力筛选 |
| 📥 | `/ingest` | 录入论文（自动查重、方法学评估、术语提取） |
| 🕳️ | `/gap [--scope 方向]` | 五维空白扫描（人群/方法/对照/结局/时间） |
| 🔎 | `/query <问题>` | 检索 wiki + 作者分析 |
| 🩺 | `/lint` | 矛盾/孤页/stub/过时 全面检查 |
| 📄 | `/export <页面> [--bibtex\|ris]` | 导出参考文献 |
| 📝 | `/logit` | 生成每日工作日志 |

## Skill（说出触发词即激活）

| 触发词 | 效果 |
|--------|------|
| 「**审视这篇**」 | 入库时自动 8 维度方法学质量评估 |
| 「**PICO 分析**」 | 临床问题拆为 P-I-C-O 精准检索式 |
| 「**写作建议**」 | 每个发现提示论文嵌入位置，自动写段落草稿 |

## 阅读进度

在 Claude Code 中说「**已速览 / 已读 / 已分析**」+ 论文名，Obsidian BASE 自动同步五种阅读状态。

## 数据源

PubMed · Semantic Scholar · Europe PMC · arXiv / medRxiv / bioRxiv · 百度学术 · 知网 / 万方 / 维普

> 中文数据库仅能获取题录。

## 零插件

不依赖任何 Obsidian 第三方插件。所有工作流由 Claude Code 的 `.claude/commands/`、`.claude/skills/`、`.claude/agents/` 驱动。

## 自定义

- 换领域 → 编辑 `CLAUDE.md` 标签体系
- 改行为 → 编辑 `.claude/commands/` 对应文件
- 加功能 → 在 `.claude/` 中参考已有格式添加

## License

Free to use, modify, and share; derivative works must be released under the [same license](https://www.gnu.org/licenses/gpl-3.0.en.html).
