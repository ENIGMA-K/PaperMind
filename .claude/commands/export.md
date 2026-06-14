# /export — 导出参考文献

将文献集导出为 BibTeX 或 RIS 格式。

## 参数

- `$ARGUMENTS`：`<页面|检索结果> [--format bibtex|ris]`
- 示例：
  - `/export [[wiki/synthesis/文献调研_PD1_2026-06-14]]` → 导出该调研报告中所有文献为 BibTeX
  - `/export "最近录入的 5 篇" --format ris` → RIS 格式
  - `/export [[wiki/summaries/Zhang 2023 - PD-1 NSCLC]]` → 导出单篇

## 执行流程

严格按 CLAUDE.md W12 执行：确定来源 → 收集 frontmatter 元数据 → 格式化 → 写入 `work/manuscripts/refs_<主题>.bib`。

### 输出格式

**BibTeX**（默认）：
```bibtex
@article{Author2024,
  author = {LastName, FirstName},
  title = {Full Title},
  journal = {Journal Name},
  year = {2024},
  volume = {10},
  pages = {100-110},
  doi = {10.xxxx/xxxxx}
}
```

**RIS**：
```
TY  - JOUR
AU  - LastName, FirstName
TI  - Full Title
JO  - Journal Name
PY  - 2024
DO  - 10.xxxx/xxxxx
ER  -
```
