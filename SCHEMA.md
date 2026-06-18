# Data schema

Each line of `data/YYYY-MM-DD.jsonl` is one JSON object. Fields:

| field | type | description |
|-------|------|-------------|
| `id` | string | stable hash of `source` + `url` + `headline`, used for de-duplication |
| `scraped_utc` | string (ISO 8601) | when the agent recorded this item |
| `published` | string (ISO 8601) or null | article/event publish time if known |
| `source` | string | outlet name, e.g. `"Reuters"` |
| `url` | string | canonical link to the item |
| `headline` | string | the headline |
| `summary` | string | 1–3 sentence factual summary (not verbatim copy) |
| `tickers` | string[] | stock symbols mentioned, e.g. `["AAPL","MSFT"]` |
| `companies` | string[] | company names mentioned |
| `sectors` | string[] | e.g. `["Technology","Energy"]` |
| `asset_classes` | string[] | `equities`, `bonds`, `commodities`, `fx`, `crypto`, `macro` |
| `region` | string | `US`, `EU`, `Asia`, `Global`, etc. |
| `event_type` | string | one of `earnings`, `guidance`, `m&a`, `macro`, `regulatory`, `analyst_rating`, `market_move`, `commodity`, `crypto`, `fx`, `ipo`, `dividend`, `other` |
| `key_facts` | string[] | atomic factual bullets, e.g. `["Q2 revenue $94.9B +5% YoY","raised FY guidance"]` |
| `numbers` | object | extracted numeric fields where present: `price`, `pct_change`, `eps`, `revenue`, `yield`, `target_price`, `rating`, etc. (null if absent) |
| `sentiment` | string | `bullish`, `bearish`, or `neutral` (market-directional reading of the item) |

## Special: daily market snapshot

Each run also emits one record with `event_type: "market_move"` and `source: "market-snapshot"` whose `numbers` object captures index/level data:

```json
{
  "id": "snapshot-2026-06-18",
  "source": "market-snapshot",
  "event_type": "market_move",
  "numbers": {
    "sp500": null, "sp500_pct": null,
    "nasdaq": null, "nasdaq_pct": null,
    "dow": null, "dow_pct": null,
    "russell2000": null,
    "vix": null,
    "ust10y": null, "ust2y": null,
    "wti_oil": null, "gold": null,
    "dxy": null, "eurusd": null, "usdjpy": null,
    "btc": null, "eth": null
  }
}
```

Use `null` for any field that can't be sourced confidently — never guess a number.
