# stock-news-corpus

An automatically accumulated corpus of market- and stock-related news data, scraped every weekday morning by a scheduled Claude (Sonnet) cloud agent. Intended as training data for a stock model.

## How it works

- **Schedule:** every weekday at **7:00am America/New_York** (cron `0 11 * * 1-5` UTC during EDT).
- **Agent:** a cheap Claude Sonnet cloud routine sweeps major financial news outlets via web search, extracts structured records, and commits them here. No notifications are sent — data just accumulates silently.
- **Append-only:** each run writes one file `data/YYYY-MM-DD.jsonl` (one JSON record per line) and appends a summary row to `data/INDEX.csv`. Re-runs on the same day de-duplicate against the existing file.

## Sources swept

Reuters, Bloomberg, CNBC, Wall Street Journal, Financial Times, MarketWatch, Yahoo Finance, Barron's, AP, Nasdaq, Investing.com, Benzinga, Seeking Alpha, Business Insider, and others — plus a daily market snapshot (major indices, Treasury yields, VIX, oil, gold, major FX, BTC/ETH).

## Data format

One JSON object per line in `data/YYYY-MM-DD.jsonl`. See [SCHEMA.md](SCHEMA.md) for the full field reference. Records capture **facts, headlines, numbers, and short factual summaries with source attribution** — not verbatim article text.

## Using the corpus

```python
import json, glob
records = []
for path in sorted(glob.glob("data/*.jsonl")):
    with open(path) as f:
        records += [json.loads(line) for line in f if line.strip()]
print(len(records), "records")
```

## Stopping / pausing

Tell Claude Code to stop the routine, or disable it directly at https://claude.ai/code/routines.
