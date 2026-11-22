# ooooOasis — Market Data Services

Two public data services for North American equities, available as **REST APIs** and as **MCP servers** (so AI agents and tools can query them directly):

| Service | What it covers | Base URL |
|---|---|---|
| **Factor Data** | Point-in-time factor panel for 583 US- and Canadian-listed names: 180 registered factors (83 with computed history), SEC filing facts and index, option-chain snapshots | `https://api.oooooasis.com/fct` |
| **TSX Data** | Canadian market data: 3,219 TSX / TSXV / CSE / CBOE Canada securities, daily bars, filings index, trading halts, corporate actions, short-sale reports | `https://api.oooooasis.com/tsx` |

## Documentation

Full documentation for each service — endpoint and tool references, limits, and data notes — lives in its own repository:

| Service | Documentation |
|---|---|
| **Factor Data** | [oooooasis-fds/factor-data](https://github.com/oooooasis-fds/factor-data) |
| **TSX Data** | [oooooasis-fds/tsx-data](https://github.com/oooooasis-fds/tsx-data) |

## Getting an API key

Both services are key-gated. The free tier is self-serve:

1. Register your email on the service's key page to receive a free API key.
2. Free tier: **100 calls/day**, no card required. Free keys expire **90 days** after issue.
3. Lost your key? Re-issue from the separate reissue page — verify your email and the old key is revoked and a new one issued.

Send your key on every call as a header:

```
Authorization: Bearer <your key>
```

**Free-tier scope:** Factor Data free keys can call the current-value endpoints only, scoped to S&P 500 Top-100 tickers × 37 recognized factors, one ticker × factor per call. TSX Data free keys can call the `securities` and `bars` families only (securities 50/day, bars 80/day, inside the 100/day total). Everything else is a paid tier — paid tiers are not on sale yet; join the waitlist on the [pricing page](https://api.oooooasis.com/pricing).

## REST API — quick start

Factor service (free tier — current value of one factor for one ticker):

```bash
curl -H "Authorization: Bearer <your key>" \
  "https://api.oooooasis.com/fct/asof?ticker=AAPL&factor=rsi14&as_of=2026-11-19"
```

Paid endpoints (not on sale yet — shown for reference):

```bash
# Cross-section of one factor across the universe
curl -H "Authorization: Bearer <your key>" \
  "https://api.oooooasis.com/fct/cross_section?date=2026-11-19&factor=rsi14"

# One name, one factor, full history
curl -H "Authorization: Bearer <your key>" \
  "https://api.oooooasis.com/fct/history?ticker=AAPL&factor=rsi14"
```

TSX service:

```bash
# Security master
curl -H "Authorization: Bearer <your key>" \
  "https://api.oooooasis.com/tsx/securities?exchange=TSX&limit=100"

# Daily bars
curl -H "Authorization: Bearer <your key>" \
  "https://api.oooooasis.com/tsx/bars?date=2026-11-16&exchange=TSX&limit=100"
```

Health checks are public (no key): `GET /fct/health`, `GET /tsx/health`.

### Main endpoints

**Factor** (`/fct`): `/asof` (free), `/cross_section`, `/history`, `/registry`, `/sec/facts`, `/sec/filings`, `/px`, `/bars`, `/calendar`, `/calendar/crosscheck`, `/regime`, `/markets`, `/universe`, `/iv`, `/iv/term`, `/iv/census`, `/iv/overlap`, `/chain`, `/meta`, `/health`

**TSX** (`/tsx`): `/securities`, `/bars`, `/filings`, `/halts`, `/corporate_actions`, `/short_sales`, `/meta`, `/health`

TSX paging: `limit` 1–1000, `offset` up to 10,000. Factor endpoints take no `limit`/`offset`; their caps are per-endpoint row ceilings (10,000 rows for `/cross_section`, `/history`, `/chain`; 1,000 for `/sec/*`; 366-day ranges for `/px`, `/bars`, `/regime`, `/markets`). Errors are JSON: `{"error": {"code": ..., "message": ...}}`.

## MCP servers

Both services also speak [Model Context Protocol](https://modelcontextprotocol.io) over HTTP — the same datasets, exposed as tools your agent can call:

| Server | URL |
|---|---|
| Factor MCP | `https://mcp.oooooasis.com/fct` |
| TSX MCP | `https://mcp.oooooasis.com/tsx` |

Point any MCP client at the URL and authenticate with the same API key (`Authorization: Bearer <your key>`). While paid tiers are not on sale, only these tools are listed and callable — the rest appear when paid access opens:

- Factor: `factor_asof` (free), `factor_meta` (not metered)
- TSX: `tsx_list_securities`, `tsx_get_security`, `tsx_get_bars`, `tsx_get_bars_by_date` (free), `tsx_get_meta` (not metered)

## Listings

- **Factor Data** — [MCP Yellow Page](https://mcpyp.com/mcp/com.oooooasis/factor-data)
- **TSX Data** — [MCP Yellow Page](https://mcpyp.com/mcp/com.oooooasis/tsx-data)

## Data notes

- Factor values are point-in-time: each value carries the date it describes and when it became available, so backtests don't leak future information.
- TSX short-sale reports are semi-monthly with roughly a two-week source lag (latest report 2026-10-31); index membership is weekly. Check `/tsx/meta` for live dates rather than assuming.
- CBOE Canada listings (106 securities) are served in the security master but have **no bars** — the API does not invent them.
- Coverage counts (ticker × factor pairs) are metered over a rolling 30-day window per key (free cap: 300 pairs); daily call quotas apply per tier (free: 100/day).

## Contact

- Factor service: fct-data@oooooasis.com
- TSX service: tsx-data@oooooasis.com

---

*ooooOasis is the data & services arm of a broader research company. This account publishes the public documentation and examples for the two services above.*
