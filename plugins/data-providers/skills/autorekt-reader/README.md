# autorekt-reader

Cross-asset market data and macro liquidity for agents via the [autorekt](https://github.com/autorekt/autorekt) CLI.

## What it does

autorekt fetches free public sources directly and prints compact text with the age of every value:

- **Briefing** — cross-asset moves, volatility, dollar liquidity, crypto positioning, odds, calendar and headlines on one screen
- **Dollar liquidity** — SOFR vs IORB and the repo rate distribution, standing repo usage, swap spreads from DTCC trades, TGA, reserves, credit spreads
- **Positioning** — CFTC futures positioning, S&P 500 dealer gamma, 25-delta skew, put/call and 0DTE share from Cboe chains, FINRA short volume, spot ETF flows
- **Crypto** — perp funding across venues, open interest, long/short ratios, basis, liquidations, Deribit volatility
- **China and Hong Kong** — CNY fixing vs CNH, SHIBOR, CGB yields, margin debt, southbound flows
- **Odds** — Polymarket and Kalshi
- **Explain and alert** — `why` for what moved together, `watch` rules that run a hook when a condition holds

Read-only: autorekt has no trading or account commands.

## Authentication

None. All sources are public; autorekt has no account and sends nothing to a server of its authors.

## Triggers

- "what's happening in markets", "is there panic", "why did BTC / NQ / gold move"
- "is dollar liquidity tight", "repo stress", "swap spreads", "TGA"
- "dealer gamma", "VIX curve", "positioning", "CFTC"
- "what is China doing with the yuan", "southbound flows"
- "Fed odds", "Polymarket / Kalshi odds"
- "alert me when ..."

## Platform

Works on **Claude Code**, Codex and other CLI-based agents on Linux, macOS and Windows. Does **not** work on Claude.ai, whose sandbox has no network access for binaries.

## Setup

```bash
# Choose finance-data-providers when prompted.
npx plugins add himself65/finance-skills

# Or install just this skill
npx skills add himself65/finance-skills --skill autorekt-reader
```

## Prerequisites

- The `autorekt` binary: `curl -fsSL https://autorekt.com/install.sh | sh` on Linux and macOS, `irm https://autorekt.com/install.ps1 | iex` on Windows, or a binary from [GitHub Releases](https://github.com/autorekt/autorekt/releases)

## Reference files

- `references/commands.md` — commands, boards, identifiers, expressions and rule syntax
