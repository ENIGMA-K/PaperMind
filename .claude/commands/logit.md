# /logit — 每日工作日志

生成当天的工作总结。

## 参数

- 无参数。输入 `/logit` 即可。

## 执行流程

严格按 CLAUDE.md W9 执行：

1. 回顾本轮对话所有操作，按类别归并（thesis / query / work-assist / ingest-paper / lint / blast / pub-research / gap-analysis / export）
2. 在 `work/log/` 创建 `YYYY-MM-DD.md`：
   - 标题 `# YYYY-MM-DD 工作日志`
   - 「今日完成工作总览」表格（时段/产出/类型）
   - 分节：课题核心进展、概念咨询要点、文件变更清单
   - 「下一步」待办列表（`- [ ]`）
3. 不更新 `index.md`。`log.md` 追加 `work-assist` 条目。

## 风格

写给自己的总结——有进展、有决策理由、有遗留事项。不用 YAML frontmatter。
