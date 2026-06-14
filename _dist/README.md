# Obsidian 医学科研 Wiki — LLM 驱动的文献管理框架

一套基于 Obsidian + Claude Code 的文献阅读与知识管理工具。丢论文进去，LLM 自动入库、交叉引用、追踪阅读进度、发现研究空白。

## 快速开始

```bash
# 1. 克隆到 Obsidian vaults 目录
cd ~/Documents/Obsidian
git clone https://github.com/你的用户名/med-research-wiki.git
# 2. 用 Obsidian 打开 "med-research-wiki" 文件夹
# 3. 用 Claude Code 打开同一文件夹
# 4. 丢一篇 PDF 到 raw/artical/，然后说：/ingest
```

## 前置条件

- [Obsidian](https://obsidian.md/)（启用核心插件：Bases）
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview)

## 目录结构

```
├── CLAUDE.md              # LLM 工作流说明书（添加/修改行为改这里）
├── index.md               # Wiki 页面目录索引
├── .claude/               # Claude Code 扩展
│   ├── commands/   (9个)   #   /ingest /blast /pub-research /gap /lint /query /export /logit /refac
│   ├── skills/     (3个)   #   方法论审视者 / PICO 解构者 / 论文写作顾问
│   └── agents/     (4个)   #   Blast引擎 / 爬虫 / Gap扫描器 / Lint检查器
├── raw/                    # 丢原始文件（PDF、数据表格）
│   ├── artical/            #   论文 PDF
│   ├── data/               #   Excel/CSV 原始数据
│   └── assets/             #   图片、图表
├── wiki/                   # LLM 维护的结构化知识
│   ├── entities/           #   作者、机构、期刊、软件、疾病
│   ├── concepts/           #   方法、框架、术语
│   ├── summaries/          #   每篇论文一页摘要
│   └── synthesis/          #   文献综述、对比分析、论文大纲
│       ├── 文献索引.base    #   Obsidian BASE 论文主索引
│       ├── 期刊数据库.base  #   期刊 CiteScore/h-index 缓存
│       └── 术语表.base      #   中英医学术语库
└── work/                   # 你的个人草稿区（LLM 不主动修改）
    ├── tables/             #   基线表、统计结果
    ├── manuscripts/        #   论文草稿
    ├── code/               #   分析脚本
    ├── notes/              #   个人笔记
    └── log/                #   每日工作日志
```

## 核心工作流

### 从零到一：第一次使用

```bash
# 1. 把第一篇论文 PDF 丢到 raw/artical/
# 2. 对 Claude Code 说：
/ingest

# 3. LLM 自动：
#    - 读 PDF（含 OCR 扫描版）
#    - 提取方法、人群、关键发现
#    - 评估方法学质量
#    - 创建作者/期刊实体页（调 OpenAlex API 补全）
#    - 提取中英术语
#    - 写摘要页 + 更新索引
#    - 标注矛盾

# 4. 后续检索：
/query T1 mapping 在心肌炎中的诊断价值
```

### 文献发现

| 命令 | 用途 | 示例 |
|------|------|------|
| `/pub-research <问题>` | 多数据库并行检索摸底 | `/pub-research CABG术后桥血管通畅率评估方法` |
| `/blast <DOI> [--depth 2]` | 从种子论文追引用链 | `/blast 10.1056/NEJMoa2302983 --depth 2` |
| `/gap [--scope 方向]` | 识别研究空白 | `/gap --scope "心肌水肿的CMR评估"` |

### 文献管理

| 命令 | 用途 | 示例 |
|------|------|------|
| `/ingest` | 录入论文（自动查重） | 指定 raw/artical/ 中的 PDF |
| `/lint` | Wiki 健康检查（矛盾/孤页/过期） | `/lint` |
| `/export <页面> --format bibtex` | 导出参考文献 | `/export "最近5篇" --format ris` |
| `/logit` | 生成每日工作日志 | `/logit` |

### 思维增强（Skills）

说出对应触发词即可激活：

| Skill | 触发词 | 效果 |
|-------|--------|------|
| 方法论审视者 | 「审视这篇」「方法学检查」 | 自动从 8 个维度评估论文质量 |
| PICO 解构者 | 「PICO 分析」「拆解问题」 | 将临床问题转为结构化检索策略 |
| 论文写作顾问 | 「写作建议」「怎么写到论文里」 | 每个发现都提示可嵌入哪个章节 |

## 数据源（W10/W11 共享）

Semantic Scholar / Europe PMC / PubMed / arXiv+medRxiv+bioRxiv / 百度学术 / 知网+万方+维普

> ⚠️ 中文数据库（知网/万方/维普）仅能获取题录，无法获取摘要和全文。

## 自定义

1. **换领域**：编辑 CLAUDE.md 的「领域标签体系」节，替换为你的学科术语
2. **改行为**：编辑 `.claude/commands/` 下的 markdown 文件，调整工作流步骤
3. **加新功能**：参考现有格式，在 `.claude/commands/`、`.claude/skills/`、`.claude/agents/` 中添加新文件

## 许可证

MIT
