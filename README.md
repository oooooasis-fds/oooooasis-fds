# oooooasis — Market Data Services

Two public data services for North American equities, available as **REST APIs** and as **MCP servers** (so AI agents and tools can query them directly):

| Service | What it covers | Base URL |
|---|---|---|
| **Factor Data** | Point-in-time factor panel for ~580 US-listed names: 100+ factors (price-derived, fundamental, quality, value), SEC filing facts and index, option-chain snapshots | `https://api.oooooasis.com/fct` |
| **TSX Data** | Canadian market data: 3,200+ TSX/TSXV/CSE securities, daily bars, filings index, trading halts, corporate actions, short-sale reports | `https://api.oooooasis.com/tsx` |

## Getting an API key

Both services are key-gated. The free tier is self-serve:

1. Register your email on the service's key page to receive a free API key.
2. Free tier: **100 calls/day**, no card required.
3. Lost your key? Re-issue from the same page — verify your email and the old key is revoked and a new one issued.

Send your key on every call as a header:

```
X-API-Key: <your key>
```

## REST API — quick start

Factor service:

```bash
# Latest cross-section of one factor across the universe
curl -H "X-API-Key: $KEY" \
  "https://api.oooooasis.com/fct/factor/cross_section?date=2026-11-19&factor=rsi14"

# One name, one factor, full history
curl -H "X-API-Key: $KEY" \
  "https://api.oooooasis.com/fct/factor/history?ticker=AAPL&factor=rsi14"
```

TSX service:

```bash
# Security master
curl -H "X-API-Key: $KEY" "https://api.oooooasis.com/tsx/securities?limit=100"

# Daily bars
curl -H "X-API-Key: $KEY" "https://api.oooooasis.com/tsx/bars?symbol=TD.TO&limit=100"
```

Health checks are public (no key): `GET /fct/factor/health`, `GET /tsx/health`.

### Main endpoints

**Factor** (`/fct`): `/factor/cross_section`, `/factor/history`, `/factor/registry`, `/factor/asof`, `/sec/facts`, `/sec/filings`, `/px`, `/chain`

**TSX** (`/tsx`): `/securities`, `/bars`, `/filings`, `/halts`, `/corporate_actions`, `/short_sales`

Paging: `limit` 1–1000, `offset` up to 10,000. Single responses are capped at 10,000 rows — narrow by factor, ticker, or date range. Errors are JSON: `{"error": {"code": ..., "message": ...}}`.

## MCP servers

Both services also speak [Model Context Protocol](https://modelcontextprotocol.io) over HTTP — the same datasets, exposed as tools your agent can call:

| Server | URL |
|---|---|
| Factor MCP | `https://mcp.oooooasis.com/fct` |
| TSX MCP | `https://mcp.oooooasis.com/tsx` |

Point any MCP client at the URL and authenticate with the same API key. Typical tools mirror the REST surface: factor cross-sections and history, the factor registry, SEC facts and filings, option chains; TSX securities, bars, filings, halts, corporate actions, and short sales.

## Data notes

- Factor values are point-in-time: each value carries the date it describes and when it became available, so backtests don't leak future information.
- TSX daily bars for CSE names lag the major exchanges; the delay is stated per dataset, not hidden.
- Coverage counts (ticker × factor pairs) are metered over a rolling 30-day window per key; daily call quotas apply per tier.

## Contact

- Factor service: fct-data@oooooasis.com
- TSX service: tsx-data@oooooasis.com

---

*oooooasis is the data & services arm of a broader research company. This account publishes the public documentation and examples for the two services above.*
