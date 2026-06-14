# Obsidian 医学科研 Wiki — LLM 驱动的文献管理框架

丢论文进去，LLM 自动入库、交叉引用、追踪阅读进度、发现研究空白。

## 安装

### 1. 确认前置工具

本框架依赖两个工具，请先安装：

| 工具 | 安装方式 | 验证 |
|------|---------|------|
| [Obsidian](https://obsidian.md/) | 官网下载 macOS/Windows/Linux 客户端 | 打开任意 vault，设置 → 核心插件 → 确保 **Bases** 已启用 |
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) | 终端运行 `npm install -g @anthropic-ai/claude-code` | 终端运行 `claude --version` |

### 2. 获取框架

```bash
cd ~/Documents/Obsidian                           # 或其他存放 vault 的目录
git clone https://github.com/ENIGMA-K/med-research-wiki.git
```

### 3. 在 Obsidian 中打开

1. 启动 Obsidian
2. 点击「打开其他 Vault」→「打开本地文件夹」
3. 选择刚 clone 的 `med-research-wiki` 文件夹
4. 进入设置 → 核心插件 → 确认 **Bases** 已启用
5. 在右侧边栏打开 `wiki/synthesis/文献索引.base`，确认能正常显示（此时为空表）

### 4. 在 Claude Code 中打开

```bash
cd ~/Documents/Obsidian/med-research-wiki
claude
```

Claude Code 会自动加载 `CLAUDE.md` 和 `.claude/` 下的所有配置。输入 `/ingest` 测试，看到命令被识别即表示安装成功。

### 5. 开始使用

```bash
# 1. 把第一篇论文 PDF 拖进 raw/artical/
# 2. 对 Claude Code 说：
/ingest
```

## 前置工具

只需要两个：

| 工具 | 用途 | 安装 |
|------|------|------|
| Obsidian | 浏览 wiki 知识库、查看 BASE 数据库 | [obsidian.md](https://obsidian.md/) |
| Claude Code | LLM 引擎，执行所有工作流 | `npm install -g @anthropic-ai/claude-code` |

不需要安装任何 Obsidian 第三方插件。本框架只用 Obsidian 核心插件 Bases，其他功能由 Claude Code 的 `.claude/` 配置驱动。

## 目录结构

```
├── CLAUDE.md              # LLM 工作流说明书（13 个工作流，修改行为改这里）
├── README.md              # 本文档
├── .claude/               # Claude Code 扩展配置
│   ├── commands/   (9个)   #   /ingest /blast /pub-research /gap /lint /query /export /logit /refac
│   ├── skills/     (3个)   #   方法论审视者 / PICO 解构者 / 论文写作顾问
│   └── agents/     (4个)   #   Blast引擎 / 学术爬虫 / Gap扫描器 / Lint检查器
├── raw/                    # 丢原始文件
│   ├── artical/            #   论文 PDF
│   ├── data/               #   原始数据（Excel/CSV）
│   └── assets/             #   图片、图表
├── wiki/                   # LLM 自动维护的结构化知识
│   ├── entities/           #   作者、机构、期刊、疾病
│   ├── concepts/           #   方法、术语、框架
│   ├── summaries/          #   每篇论文一页摘要
│   └── synthesis/          #   文献综述、空白分析、论文大纲
│       ├── 文献索引.base    #   论文主索引（Obsidian BASE）
│       ├── 期刊数据库.base  #   期刊 CiteScore/h-index 缓存
│       └── 术语表.base      #   中英医学术语库
├── work/                   # 个人草稿区（LLM 不主动修改）
│   ├── tables/             #   基线表、统计结果
│   ├── manuscripts/        #   论文草稿
│   ├── code/               #   分析脚本
│   ├── notes/              #   个人笔记
│   └── log/                #   每日工作日志
└── index.md                # Wiki 页面目录索引
```

## 所有命令

| 命令 | 用途 |
|------|------|
| `/ingest` | 录入论文到 wiki |
| `/blast <DOI> [--depth N] [--threshold T]` | 从种子论文追引用链 |
| `/pub-research <问题>` | 多数据库并行检索摸底 |
| `/gap [--scope <方向\|论文>]` | 识别研究空白 |
| `/query <问题>` | 检索 wiki 已有知识 |
| `/lint` | Wiki 健康检查 |
| `/export <页面> [--format bibtex\|ris]` | 导出参考文献 |
| `/logit` | 生成每日工作日志 |
| `/refac` | 精简 CLAUDE.md |

## 数据源

Semantic Scholar / Europe PMC / PubMed / arXiv, medRxiv, bioRxiv / 百度学术 / 知网, 万方, 维普

> ⚠️ 知网/万方/维普仅能获取题录，无法获取摘要和全文。

## 自定义

1. **换领域**：编辑 `CLAUDE.md` 的「领域标签体系」节
2. **改行为**：编辑 `.claude/commands/` 下的对应文件
3. **加功能**：参考已有格式在 `.claude/` 中添加新文件

## 许可证

MIT
