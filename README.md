# bloxscout-data

Open historical dataset of Roblox game metrics (concurrent players, visits,
favorites, votes), captured every ~30 minutes by the
[bloxscout](https://github.com/IvanKuria/bloxscout) ingestion pipeline and
published here as static JSON so any client — the `bloxscout` MCP server/CLI,
a website, your own scripts — can read it over plain HTTPS with no API key.

> Consume via `https://raw.githubusercontent.com/IvanKuria/bloxscout-data/main/v1/...`

## Layout (`/v1/`)

| Path | Resolution | Retention | Contents |
|---|---|---|---|
| `v1/meta.json` | — | latest | `{ schemaVersion, generatedAt, gamesTracked, latestRunId }` — freshness probe |
| `v1/registry.json` | — | latest | Tracked universe IDs with name/genre/discovery info |
| `v1/views/trending.json` | per run | latest | Top games ranked by composite 24h/7d CCU growth |
| `v1/views/up-and-coming.json` | per run | latest | Small-baseline games with high growth |
| `v1/views/breakouts.json` | per run | latest | Games whose current CCU is anomalous vs their own history (z-score) |
| `v1/views/genres.json` | per run | latest | Genre-aggregated CCU + momentum |
| `v1/raw/YYYY-MM-DD/<runId>.json.gz` | ~30 min | 48 h | One file per ingestion run, all tracked games |
| `v1/hourly/YYYY-MM-DD.json.gz` | 1 h | 30 d | Hourly avg/peak rollups, one file per UTC day |
| `v1/daily/YYYY-MM-DD.json.gz` | 1 d | forever | Daily rollups |
| `v1/history/<universeId % 256>.json.gz` | mixed | rebuilt daily | Per-game serving shards: hourly (7d) + daily (full) series |

All timestamps are true capture times (UTC); never assume grid alignment —
the scheduler jitters. Schema changes land under a new `/v2/` prefix; files
under `/v1/` stay backward-compatible.

## Notes

- History of this repo is periodically squashed to keep it clonable; do not
  build anything on top of its git history — only on file contents.
- Data is collected politely from Roblox's public, unauthenticated APIs
  (batched requests, backoff, descriptive User-Agent). See the
  [bloxscout docs](https://github.com/IvanKuria/bloxscout) for details.

## License

Repository contents (schemas, rollups, this README) are MIT-licensed.
The underlying game metrics describe experiences created by Roblox and its
community of developers; Roblox is a trademark of Roblox Corporation. This
project is unofficial and not affiliated with or endorsed by Roblox.
