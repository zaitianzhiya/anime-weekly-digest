# CLAUDE.md — Anime Weekly Digest

全球动漫产业每周发展摘要 — 覆盖日本动画番剧、全球动画电影、国产动画(国漫)、流媒体平台的完整产业生态。

## 架构

```
30 源 → RealSearchCollector → 去重/合并 → 5-dim Scoring → DeepAnalyzer (LLM) → Markdown 周报 → GitHub Actions commit
     (Tier 1 DDG + Tier 2 skeleton, 7 生态)
```

## 模块

| Module | Path | Purpose |
|--------|------|---------|
| Collectors | `src/collectors/` | `base.py` (EventRecord/SourceCitation), `real_search.py` (分层 DDG) |
| Filters | `src/filters/` | `dedup.py`, `quality.py`, `scorer.py` (交叉生态验证) |
| AI | `src/ai/` | `llm_client.py` (multi-provider), `deep_analyzer.py`, `feedback_loader.py` |
| Render | `src/render/` | `markdown_weekly.py` |
| Config | `config/` | `sources.yml` (30 源 × 7 生态), `keywords.yml`, `quality.yml` |
| Prompts | `prompts/` | `weekly-deep.md` (AI 分析指南), `taxonomy.md`, `feedback-rules.md` |

## 快速上手

```bash
pip install -r requirements.txt
python run.py --mode weekly
```

## 部署

- **Workflow**: cron `57 10 * * 1` (Mon 18:57 CST)
- **Watchdog**: 3× Monday check
- **Secrets**: `GH_TOKEN`, `GEMINI_API_KEY`

## 关键实现要求

- **双语标题**: 所有表格的事件列必须使用中英文双语格式。EN标题为主文本，CN翻译放在 `<br/><small>` 标签中。
- **实现路径**: `markdown_weekly.py` 中的 `_event_title()` 方法 + `main.py` 中的 `_generate_cn_titles()` LLM批量翻译全部事件
- **Fallback**: 所有项目必须有足够大的 `_PREPROCESS` 字典（30+对），保证无LLM时的基本可读性
- **LLM速率保护**: LLM翻译使用 `BATCH_SIZE=15` + `time.sleep(2)` + 3次重试，避免Gemini 429错误
- **审核**: 首次部署后检查 `grep '<br/><small>' output/` 确认双语渲染
