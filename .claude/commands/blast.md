# /blast — 引文爆发检索

以种子论文为起点，沿参考文献链向外辐射，逐层收集题录，按期刊影响力筛选。

## 参数

- `$ARGUMENTS`：`<DOI|PMID|文件名> [--depth N] [--threshold T]`
- 示例：
  - `/blast 10.1056/NEJMoa2302983` → depth=1, threshold=0.8
  - `/blast 12345678 --depth 2 --threshold 0.5` → 2 层，保留前 50%
  - `/blast "my-paper.pdf"` → 从 raw/artical/ 中的 PDF 出发

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--depth N` | 1 | 引文爆发层数 |
| `--threshold T` | 0.8 | 保留前 T×100% 最重要文献（按期刊 CiteScore/h-index 排序） |

## 数据源

参考文献获取与 /pub-research 共享全部数据源：

| 数据库 | 方式 | 获取内容 |
|--------|------|---------|
| Semantic Scholar | API（优先） | 参考文献列表、被引、摘要 |
| Europe PMC | API | 参考文献链、MeSH |
| PubMed | E-utilities API | 元数据、MeSH |
| arXiv / medRxiv / bioRxiv | API | 预印本参考文献 |
| 百度学术 | WebSearch | 中文文献引用关系 |
| 知网 / 万方 / 维普 | WebSearch | 中文文献引用 ⚠️ 仅题录 |

> 每层爆发时并行查询全部数据源，以 DOI/PMID/标题 合并去重。

## 执行流程

严格按 CLAUDE.md W10 执行：解析种子 → 并行多源爆发 → 去重 → 查期刊缓存/补全 → 重要性筛选 → 写入单个题录文件 → 统计汇报。

### 期刊缓存机制

- 优先查 `[[wiki/synthesis/期刊数据库.base]]`（`wiki/entities/` 中 `tags: journal` 的实体页）
- 命中：直接用 frontmatter 中的 `citescore` 和 `h_index`
- 未命中：调 OpenAlex → 创建 `wiki/entities/<期刊名>.md` 实体页 → 下次直接复用
- 避免重复 API 调用

### 重要性分数

`score = citescore × 0.6 + h_index × 0.4`

无数据文献赋予所有已知期刊文献的中位数分数。

### 输出文件

`raw/blast_<种子简称>_d<N>.md` — 仅一个文件，含保留文献 + 已过滤附录。
