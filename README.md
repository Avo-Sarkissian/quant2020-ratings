# quant2020 ratings feed

Daily stock ratings from a personal, paper-traded research system. Published as static
files; no API, no key, no guarantees.

**This is research output of a paper-traded system. It is not investment advice, not a
recommendation, and carries no warranty of any kind.**

## Files

| Path | Content |
| --- | --- |
| `data/latest.json` | today's ratings, one row per ticker |
| `data/latest.csv` | the same rows as CSV |
| `data/history/<date>.json` | one archived day, identical shape, never rewritten |
| `data/index.json` | `{"as_of": "...", "dates": [...]}` — the available history |

## Schema

Top level: `schema_version`, `as_of` (the trading date the ratings describe),
`generated_at` (UTC), `source`, `cadence`, `panel_size`, `counts`, `disclaimer`,
`ratings`.

Each entry in `ratings`:

| Field | Meaning |
| --- | --- |
| `ticker` | US listing symbol |
| `sector` | GICS-style sector, or `null` |
| `composite_z` | cross-sectional composite z-score, **higher is better** |
| `composite_rank` | 1-based, 1 = best. Ties break by ticker ascending. |
| `composite_pct` | 100.0 at rank 1, 0.0 at the last name. **Use this to compare across days** — the panel size changes daily. |
| `ai_score` | 0–100 AI rating, or `null` if the name is not currently AI-rated |
| `ai_rated_at` | when that AI rating was produced (it is cached and re-rated on change) |
| `value_quality_score`, `value_rank` | value/quality composite, where computed |
| `growth_score`, `growth_rank` | growth composite, where computed |
| `exclusion_reason` | why a name carries no scores (`new_issue`, `insufficient_history`) |

`null` means "not rated", never zero.

## Cadence and staleness

Updated on US weekday evenings, roughly 18:35 America/New_York. **Check `as_of` before
using the data** — if the producing machine is down, `latest.json` keeps serving the last
good day rather than emptying.

Coverage: the composite covers the full eligible US universe (~2,500 names). The AI score
covers a far smaller curated set (~50).
