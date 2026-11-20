# ooooOasis — Market Data Services

Two public data services for North American equities, available as **REST APIs** and as **MCP servers** (so AI agents and tools can query them directly):

| Service | What it covers | Base URL |
|---|---|---|
| **Factor Data** | Point-in-time factor panel for 583 names across US and Canadian listings: 180 registered factors, 83 with computed history (price-derived, fundamental, quality, value), SEC filing facts and index, option-chain snapshots | `https://api.oooooasis.com/fct` |
| **TSX Data** | Canadian market data: 3,219 securities across TSX / TSXV / CSE / CBOE Canada, daily bars, filings index, trading halts, corporate actions, short-sale reports | `https://api.oooooasis.com/tsx` |

## Documentation

Full documentation for each service — endpoint and tool references, limits, and data notes — lives in its own repository:

| Service | Documentation |
|---|---|
| **Factor Data** | [oooooasis-fds/factor-data](https://github.com/oooooasis-fds/factor-data) |
| **TSX Data** | [oooooasis-fds/tsx-data](https://github.com/oooooasis-fds/tsx-data) |

## Getting an API key

Both services are key-gated. The free tier is self-serve:

1. Register your email at [api.oooooasis.com/register](https://api.oooooasis.com/register) to receive a free API key.
2. Free tier: **100 calls/day**, no card required. Free keys expire 90 days after issue.
3. Lost your key? Use the separate reissue page, [api.oooooasis.com/reissue](https://api.oooooasis.com/reissue) — verify your email and the old key is revoked and a new one issued.

Send your key on every call as a header:

```
Authorization: Bearer <your key>
```

Free-tier scope: Factor free keys can call the current-values endpoint (`/asof`, S&P 500 Top-100 tickers × 37 recognized factors); TSX free keys can call the security master and daily bars. Everything else belongs to the paid tiers, which are **not on sale yet** — see the [pricing page](https://api.oooooasis.com/pricing) to join the waitlist by email.

## REST API — quick start

Factor service:

```bash
# Current factor values for one name (free tier)
curl -H "Authorization: Bearer $KEY" \
  "https://api.oooooasis.com/fct/asof?ticker=AAPL"

# Latest cross-section of one factor across the universe (paid tier)
curl -H "Authorization: Bearer $KEY" \
  "https://api.oooooasis.com/fct/cross_section?date=2026-11-19&factor=rsi14"
```

TSX service:

```bash
# Security master (free tier)
curl -H "Authorization: Bearer $KEY" "https://api.oooooasis.com/tsx/securities?limit=100"

# Daily bars for one trading day (free tier)
curl -H "Authorization: Bearer $KEY" "https://api.oooooasis.com/tsx/bars?date=2026-11-16&exchange=TSX&limit=100"
```

Health checks are public (no key): `GET /fct/health`, `GET /tsx/health`.

### Main endpoints

**Factor** (`/fct`): `/asof`, `/cross_section`, `/history`, `/registry`, `/bars`, `/calendar`, `/calendar/crosscheck`, `/regime`, `/markets`, `/universe`, `/iv`, `/iv/term`, `/iv/census`, `/iv/overlap`, `/sec/facts`, `/sec/filings`, `/px`, `/chain`, `/meta`, `/health`

**TSX** (`/tsx`): `/securities`, `/securities/{security_id}`, `/securities/{security_id}/bars`, `/bars`, `/filings`, `/corporate_actions`, `/halts`, `/short_sales`, `/short_sale_trading`, `/index_membership`, `/listing_snapshots`, `/meta`, `/health`

Paging (`limit` 1–1000, `offset` up to 10,000) applies to the TSX API. Factor endpoints cap responses per endpoint (10,000 rows for cross-sections and history) — narrow by factor, ticker, or date range. Errors are JSON: `{"error": {"code": ..., "message": ...}}`.

## MCP servers

Both services also speak [Model Context Protocol](https://modelcontextprotocol.io) over HTTP — the same datasets, exposed as tools your agent can call:

| Server | URL |
|---|---|
| Factor MCP | `https://mcp.oooooasis.com/fct` |
| TSX MCP | `https://mcp.oooooasis.com/tsx` |

Point any MCP client at the URL and authenticate with the same API key (`Authorization: Bearer`). Callable today: `factor_asof`, `factor_meta`, `tsx_list_securities`, `tsx_get_security`, `tsx_get_bars`, `tsx_get_bars_by_date`, `tsx_get_meta`. The remaining tools (cross-sections, history, registry, SEC facts and filings, option chains; TSX filings, halts, corporate actions, short sales) arrive with the paid tiers.

## Data notes

- Factor values are point-in-time: each value carries the date it describes and when it became available, so backtests don't leak future information.
- TSX short-sale reports are semi-monthly with roughly a two-week source lag (latest report: 2026-10-31); index membership is updated weekly. CBOE Canada names (106 securities) are in the security master but have no bars yet.
- Coverage counts (ticker × factor pairs) are metered over a rolling 30-day window per key (free tier: 300 pairs per 30 days); daily call quotas apply per tier (free: 100/day; within that, TSX securities 50/day and bars 80/day).

## Contact

- Factor service: fct-data@oooooasis.com
- TSX service: tsx-data@oooooasis.com

---

*ooooOasis is the data & services arm of a broader research company. This account publishes the public documentation and examples for the two services above.*
