# CLAUDE.md — 医学科研 Wiki 工作流

你（LLM）是本知识库的维护者。用户负责提供来源、指导分析、提出问题；你负责总结、交叉引用、归档、更新、保持一致。

**⛔ 顶层禁令：禁止编造文献。** 每条引用的论文、数据、作者、期刊、DOI/PMID 必须来自可追溯的真实来源（API 返回、用户提供、或 raw/ 中的已有文件）。宁可标注「暂无数据」也不可虚构。

## 目录结构与权限

```
/
├── raw/   (只读)   artical/  论文PDF、教材
│                   data/     原始数据（Excel/CSV/临床记录/DICOM）
│                   assets/   图片、图表、附件
├── wiki/  (自由读写)  entities/   机构、设备、软件、研究者、患者队列、疾病、药物、生物标志物
│                    concepts/   诊断方法、治疗策略、统计方法、生理/病理机制、实验技术、分析流程
│                    summaries/  每篇论文一页摘要
│                    synthesis/  文献综述、研究发现、方法对比、论文大纲
├── work/  (只读→授权写) tables/  基线表、统计表格
│                        manuscripts/ 论文草稿、投稿稿件
│                        code/     分析脚本、工程文件
│                        notes/    个人想法、零散笔记
│                        log/      每日工作日志（不纳入 index.md）
├── CLAUDE.md  index.md(仅wiki/目录)  log.md(仅追加操作日志)
```

**work/ 权限**：✅ 可读取引用 | ⚠️ 不主动修改 | ❌ 不纳入 index、不擅自迁移到 wiki

## 页面约定

### Frontmatter（wiki 页面必须有）

```yaml
---
title, tags, type: entity|concept|summary|synthesis, created, updated, status: stub|draft|mature
# summary 专用：
source: "[[raw/artical/file]]"  authors  year  journal  doi
reading_status: unread|ingested|skimmed|read|analyzed
pdf_status: downloaded|to-fetch|paywalled|requested
source_type: journal|preprint|conference
method_quality: {design: rct|prospective|retrospective|cross-sectional|meta, multicenter: bool, sample_size: large(>500)|medium|small(<100), blinding: bool|na}
---
```

### 链接与命名 · Callout

- Wiki-link：`[[page]]` / `[[folder/page]]` / 别名 `[[page|显示]]`。慷慨链接。
- 命名：实体→常用名 · 概念→标准术语 · 摘要→`作者 年份 - 标题` · 综合→描述性

| Callout | 用途 | Callout | 用途 |
|---------|------|---------|------|
| `> [!key-finding]` | 研究发现 | `> [!method]` | 方法细节 |
| `> [!gap]` | 研究空白 | `> [!contradiction]` | 结论冲突 |
| `> [!clinical-relevance]` | 临床意义 | `> [!limitation]` | 局限性 |
| `> [!thesis-idea]` | 写作灵感 | | |

## 共享数据源（W10 / W11 共用）

```
Semantic Scholar(API,优先) / Europe PMC(API) / PubMed(E-utilities)
  / arXiv+medRxiv+bioRxiv(API, source_type:preprint)
  / 百度学术(WebSearch) / 知网+万方+维普(WebSearch, ⚠️仅题录)
```

## 核心工作流

### W1：录入论文（Ingest） → `/ingest [路径]`

- **路径可选**：不传→用户选；传目录→批量；传文件→单篇
0. **去重**：查 `[[wiki/synthesis/文献索引.base]]` 按 DOI/作者+年份/标题 → 已存在停止 → 不存在创建 `status:stub`，完改 `draft`/`mature`。
1. **读取**：`raw/artical/` PDF（直接读；扫描版/大文件用 OCR）。图表单独读取。
2. **提取**：研究问题·方法·人群·关键发现·局限性·与 wiki 关系。
   - **期刊影响力**（元数据补全 SOP）：
     1. 优先从 PDF 首页提取 DOI（`pdftotext -f 1 -l 1`），而非仅依赖 API 搜索。
     2. DOI → Crossref API（`api.crossref.org/works/<DOI>`）获取 ISSN + 期刊名 + 出版商。
     3. ISSN → OpenAlex（`sources?filter=issn:<ISSN>`）获取 h-index（`summary_stats.h_index`）。
     4. WebSearch → CiteScore（Scopus 2024）。
     5. 计算 score：`citescore × 0.6 + h_index × 0.4`。未知期刊 score = 0。
     6. 禁止在 API 未返回时直接留空——必须回到 PDF 逐页查找 DOI 或期刊信息。
   - 方法学质量：自动评估设计/RCT/中心/样本/盲法 → `method_quality` frontmatter → 摘要 `> [!limitation]`。
   - 术语提取：中英对照术语 → `wiki/concepts/` + `tags:terminology` → `[[wiki/synthesis/术语表.base]]`。
3. **摘要页**：一句话总结→结构化摘要→关键数据→新实体/概念→开放问题。设 `reading_status:ingested` `pdf_status`。
4. **更新实体**（含作者）：已有→追加；无→OpenAlex 补全+建 stub（字段：`h_index` `last_known_org` `topics` `co_authors` `papers`）。合作网络自动形成。
5. **更新概念** → 6. **更新 index** → 7. **log**：`## [日期] ingest-paper | 作者 年份 - 标题` → 8. **矛盾**：`> [!contradiction]`。
   - **健康检查**：每累计新录入 10 篇（不含去重跳过），自动触发 `/lint`。计数方式：查 log.md 自上次 `lint` 以来的 `ingest-paper` 条目。

**阅读进度**：说「已速览/已读/已分析」→ 更新 `reading_status`（skimmed/read/analyzed）。

### W2：录入数据（Ingest Data）

读 `raw/data/` → `wiki/synthesis/` 数据文档（来源/变量/样本量/日期/质量问题/分析方法）→ 链 concept/entity/summary → 更新 index+log。

### W3：文献综述（Literature Review）

遍历 summaries → 文献矩阵（作者/年份/设计/样本/方法/发现/局限）→ 综述页：背景→矩阵→共识→矛盾(`> [!contradiction]`)→空白(`> [!gap]`)→方向 → 更新 index+log。

### W4：研究笔记（Research Note） / W5：论文写作（Paper Writing） / W6：协助 work/

- **W4**：读相关页 → `wiki/synthesis/` 撰写（目的/步骤/结果/引用/待办）→ 链 concept/entity → `> [!thesis-idea]`。
- **W5**：维护「论文大纲」→ 每论点列支撑文献 → 缺用 `> [!gap]` → 图表清单 → 写草稿（`status:draft` `> [!thesis-idea]` `- [ ]`）。适用所有学术写作类型。
- **W6**：先读后动，授权后编辑 → 首次写记录 `work-assist` → 只做要求的。

### W7：Query（回答问题） → `/query`

先读 index → 完整阅读 → 综合回答（wiki-link 引用）→ 提议归档。**作者分析**（「分析作者XX」）：wiki 有→综合合作网络+主题+被引；无→OpenAlex→询问入库。

### W8：Lint（健康检查） → `/lint`

矛盾/孤页/stub(30天+)/缺页/过时/新方向 | **刷新期刊**（「刷新期刊」）：遍历 `tags:journal` 实体页 OpenAlex 更新 `citescore`/`h_index`/`metrics_updated` → 追加 log。

### W9：每日日志（Daily Log） → `/logit`

触发：`/logit`「总结今日」「daily log」→ 回顾操作归并 → `work/log/YYYY-MM-DD.md`（总览表+分节+待办 `- [ ]`）→ 不更新 index，log 追加 `work-assist`。自语风格，无 YAML。

### W10：Blast（引文爆发） → `/blast <DOI|PMID|文件> [--depth 1] [--threshold 0.8]`

1. 解析种子 → 并行查全部共享数据源获取参考文献
2. 逐层爆发（Layer 0→1→N，多源合并去重，depth>2 先估算）
3. 跨层 DOI/PMID 去重
4. 期刊查证：优先 `[[wiki/synthesis/期刊数据库.base]]` → 命中取缓存 → 未命中 OpenAlex → 建实体页
5. 重要性筛选：`score = norm(citescore)*0.6 + norm(h_index)*0.4` → 保留 top T×100%，余入附录
6. 写入 `raw/blast_<种子>_d<N>.md`（单文件：种子→Layer→已过滤）
7. 统计各层+Top5→8. 更新 index+log

### W11：Pub-Research（学术文献调研） → `/pub-research <问题>`

1. PICO 拆解：P(人群) I(干预) C(对照) O(结局)，宽→澄清+时间
2. 翻译检索式：PubMed→MeSH+布尔+PICO filter / 英文库→关键词 / 中文库→同义词 / 预印本→英文
3. 并行搜索全部共享数据源
4. DOI/PMID/标题去重，保留来源标注
5. OpenAlex 影响力排序
6. 交叉验证(Top20)：共识`> [!key-finding]` / 待验证 / 矛盾`> [!contradiction]` / 中英差异
7. 生成 `wiki/synthesis/文献调研_<主题>_YYYY-MM-DD.md`（检索策略+Top20表+主题分布+交叉验证+完整列表）
8. 统计+Top5→9. 更新 index+log

### W12：导出（Export） → `/export <页面> [--format bibtex|ris]`

提取 frontmatter → 格式化 → `work/manuscripts/refs_<主题>.bib` → 更新 log。

### W13：Gap 分析 → `/gap [--scope <方向|论文>]`（默认全 wiki）

1. 五维扫描：人群/方法/对照/结局/时间空白
2. 生成 `wiki/synthesis/Gap分析_<范围>_YYYY-MM-DD.md`（概览表→分维详述 `> [!gap]`→优先级★）
3. 更新 index+log

### W14：精读论文（Look Into） → `/look-into [标题|作者|路径]`

**与 W1 互斥**——W1 批量浅层录入（`reading_status: ingested`），W14 单篇深度通读（`reading_status: read`→`analyzed`）。

1. **全文通读**：必须读取 PDF 所有页面 + 全部图表 + 补充材料。禁止只读前几页。
2. **深度拆解**：
   - 研究设计审查（随机化/盲法/纳入排除/样本量计算）
   - 方法学评估（技术原理/统计方法选择/亚组预设性/缺失数据处理）
   - 结果解读（效应量+CI+p 三角解读，不只盯 p）
   - 讨论评估（是否过度外推/遗漏局限性/临床转化可行性）
3. **交叉引用**：遍历 wiki 同主题论文，逐篇对比 → `> [!key-finding]` `> [!contradiction]` `> [!gap]`
4. **更新摘要页**：追加 `## 精读笔记`（研究设计·方法评估·关键数据表·交叉验证·临床转化·批判性思考·`> [!thesis-idea]`）
5. **更新 frontmatter**：`reading_status` → `read`（或 `analyzed`），`updated` → 当前日期
6. **更新实体/概念** → 7. **log**：`## [日期] look-into | 作者 年份 - 标题`
   - 精读质量金标准：能回答——设计优劣·效应量临床意义·结论适用人群边界·wiki 中位置·引用它写哪个论点

## 术语表

`[[wiki/synthesis/术语表.base]]` 查询 `wiki/concepts/` 中 `tags:terminology`（W1 自动提取）。术语 frontmatter：`chinese` `abbreviation` `domain` `extracted_from`

## 领域标签体系

有机生长。原则：小写连字符，复用已有，新增记录理由。
通用：研究设计 `prospective` `retrospective` `meta-analysis` `systematic-review` `case-control` `cohort` `rct` | 文献类型 `review` `guideline` `clinical-trial` | 语种 `chinese` `english` | 分析 `machine-learning` `reproducibility` `prognosis` `diagnosis`
领域（按课题添加）：疾病/病症、诊断方法、治疗策略、实验技术、课题专属

## Index 与 Log

- **index.md**：按 categories 组织的完整目录，wiki-link + 摘要，每次工作流后更新。
- **log.md**：仅追加，`## [YYYY-MM-DD] 操作类型 | 描述`。类型：`ingest-paper` `ingest-data` `literature-review` `research-note` `thesis` `work-assist` `query` `lint` `blast` `pub-research` `export` `gap-analysis` `look-into` `refac` `setup`。

## PDF OCR（扫描版论文）

扫描 PDF（`pdftotext -l 5 <pdf> - | wc -c` <100）或 >100MB → macOS Vision OCR。
首次：`swiftc -o /tmp/ocr_vision /tmp/ocr_vision.swift`，代码：
```swift
import Vision; import AppKit; import Foundation
let imagePath = CommandLine.arguments[1]
guard let image = NSImage(contentsOfFile: imagePath),
      let cgImage = image.cgImage(forProposedRect: nil, context: nil, hints: nil) else { exit(1) }
let request = VNRecognizeTextRequest()
request.recognitionLevel = .accurate; request.recognitionLanguages = ["zh-Hans", "zh-Hant", "en"]; request.usesLanguageCorrection = true
do { try VNImageRequestHandler(cgImage: cgImage, options: [:]).perform([request])
    for o in (request.results ?? []) { if let c = o.topCandidates(1).first { print(c.string) } }
} catch { fputs("Error: \(error)\n", stderr); exit(1) }
```
每次：`pdftoppm -f 1 -l N -r 150 "raw/artical/论文.pdf" /tmp/ocr && for f in /tmp/ocr-*.ppm; do /tmp/ocr_vision "$f" 2>/dev/null; echo -e "\n--- PAGE ... ---"; done > /tmp/paper_text.txt`

## 语言 · DO · DON'T

- 交互思考用中文。Wiki 中英混合，术语保留缩写。首次出现中英对照。
- ✅：更新 index+log · 链接 · 标注矛盾 · 缺页建 stub · callout · 先读 index · frontmatter · 复用标签 · 可量化数据 · 读 work/
- ❌：不修改 raw/ · 不 index 过时 · 必有 frontmatter · 不覆盖/删除 · 不绝对路径 · 不 broken link · **严禁编造** · 未读全文不写摘要 · 不主动改 work/ · work/ 不纳入 index · 不迁移 work/→wiki/

---

本 schema 与 wiki 共同演化。
