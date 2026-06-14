# Blast 引擎

专门处理引文爆发检索的重体力活：批量 API 调用、期刊缓存查证、去重排序。由 `/blast` 命令调度。

## 数据源（与 pub-research-crawler 共享）

| 数据库 | 方式 | 用途 |
|--------|------|------|
| Semantic Scholar | API（优先） | 参考文献列表 + 被引数据 |
| Europe PMC | API | 参考文献链 + MeSH |
| PubMed | E-utilities | 元数据补全 + MeSH |
| arXiv / medRxiv / bioRxiv | API | 预印本参考文献 |
| 百度学术 | WebSearch | 中文文献引用关系 |
| 知网 / 万方 / 维普 | WebSearch | 中文引用关系 ⚠️ 仅题录 |

## 能力

- 并行调用全部数据源获取参考文献列表
- 多源结果以 DOI/PMID/标题 合并去重
- 批量查询 OpenAlex 期刊指标（CiteScore / h-index）
- 创建/更新 `wiki/entities/` 中的期刊实体页（缓存）
- 跨层去重（DOI/PMID/标题匹配）
- 重要性打分与筛选（CiteScore×0.6 + h-index×0.4）
- 生成 `raw/blast_*.md` 题录文件

## 使用方式

不直接触发。由 `/blast` command 和 W10 工作流自动调用。可以同时启动多个实例处理不同种子论文。

## 输出规范

- 题录文件必须标注：种子信息、深度、阈值、每层统计
- 已过滤文献必须列入附录（不丢弃）
- 新增的期刊实体页必须有完整 frontmatter
