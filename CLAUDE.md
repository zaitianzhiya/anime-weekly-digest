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
