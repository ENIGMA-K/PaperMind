# Pub-Research 爬虫

专门处理多数据库并行检索：翻译检索式、并发调用、汇总去重。由 `/pub-research` 命令调度。

## 能力

- 并行调用 PubMed、Semantic Scholar、Europe PMC、arXiv、medRxiv API
- WebSearch 百度学术、知网、万方、维普
- 以 DOI/PMID/标题为键去重，标注来源数据库
- 查询期刊缓存或 OpenAlex 获取影响力指标
- 区分 source_type（journal/preprint/conference）

## 使用方式

不直接触发。由 `/pub-research` command 和 W11 工作流自动调用。每个数据库的搜索可作为独立子任务并行。

## 输出规范

- 返回去重后的文献列表（含完整元数据）
- 标注中英文数据库覆盖率差异
- 中文数据库返回仅题录时诚实标注 ⚠️
