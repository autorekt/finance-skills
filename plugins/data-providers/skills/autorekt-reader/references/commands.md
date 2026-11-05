# autorekt command reference

Checked against autorekt 0.1.0 (2026-11-05). `autorekt <verb> --help` is the source of truth.

## Verbs

| Verb | What it does |
|---|---|
| `show` | The briefing, a board, one series, several series as rows, odds, news, calendar, alerts, or a markdown report |
| `search` | Find ids, tickers, prediction markets and news; `--check` probes every source |
| `why` | One id: the biggest move in the range, what else moved as z-scores, dated news. Several ids: correlation matrix and broken pairs |
| `watch` | Rules that fire when an expression is true, timers, calendar alerts, backtests, `--wait`, Python monitors |
| `daemon` | Background service: `status`, `start`, `stop`, `restart`, `logs`; starts by itself when needed |

## Boards (`autorekt show <board>`)

`default crypto macro global fear liquidity rates commodities positioning sectors megacaps asia`

## Options shared by every verb

| Option | Meaning |
|---|---|
| `-i, --interval` | Bar timeframe: `1m 5m 15m 1h 4h 1d 1w` |
| `-r, --range` | How far back: `30m 6h 7d 90d` |
| `-n, --limit` | Number of bars or items |
| `--at` | Look at the past: the view ends at this time (`2026-10-30T20:00:00Z`) |
| `--tz` | Display time zone (`UTC`, `ET`, `HK`, `CN`, IANA names) |
| `--json`, `--csv` | Machine-readable output |
| `--changes` | Only what changed since this agent last looked |

## Identifiers

`<provider>:<symbol>[#metric]`

- Exchanges use CCXT symbols: `okx:BTC/USDT:USDT` (perp), `okx:BTC/USDT` (spot), `hyperliquid:xyz:TSLA`
- Others use native ids: `yahoo:^VIX`, `yahoo:GC=F`, `fred:DGS10`, `polymarket:<slug>`, `kalshi:<TICKER>`
- Funding and rates: `nyfed:SOFR#p99`, `nyfed:SRF`, `treasury:TGA`, `treasury:AUCTION_10Y`, `dtcc:SOFR10Y`, `mof:JGB10Y`, `ecb:EA10Y`
- China and Hong Kong: `cfets:USDCNY`, `cfets:SHIBOR_3M`, `eastmoney:CGB10Y`, `eastmoney:MARGIN`, `eastmoney:SOUTHBOUND`
- Positioning and flows: `cboe:SPX#gex`, `#skew_25d`, `#put_call_volume`, `#zero_dte_pct`, `farside:BTC`, `cftc:<code>`
- Crypto metrics: `BTC#funding_rate`, `#open_interest`, `#long_short_ratio`, `#taker_buy_sell_ratio`, `#basis`, `#liquidations`

## Expressions

Usable wherever an id is: `'fred:SOFR - fred:IORB'`, `'RSP / SPY'`, `'ta.rsi(BTC, 14)'`.

Functions: `close open high low volume(id, tf)`, `funding_rate(id)`, `open_interest(id)`, `prob(id, outcome)`, `pct(id, "1h")`, `stale(id, "2h")`, `atr(id, tf, n)`, `ta.sma ema rma wma rsi stdev highest lowest sum zscore change roc crossover crossunder cross rising falling`, `abs min max sqrt log nz hour dayofweek`, and the operators `+ - * / > < >= <= == != and or not`.

## Rules

```bash
autorekt watch                                              # list rules and their state
autorekt watch btc_dump --expr 'pct(BTC, "1h") < -3' --hook 'notify.sh'
autorekt watch vix_inv --expr 'VIX - VIX3M > 1.5' --tf 15m --for 30m --cooldown 12h
autorekt watch macro --calendar 15m --countries USD,EUR --hook '...'
autorekt watch 'pct(BTC, "1h") < -3' --test -r 30d          # backtest
autorekt watch --wait --timeout 1h                          # block until something fires
autorekt watch btc_dump --rm
```

Hooks run under `sh` on Unix and `cmd` on Windows, with `AUTOREKT_RULE`, `AUTOREKT_MESSAGE`, `AUTOREKT_VALUE`, `AUTOREKT_TIME`, `AUTOREKT_CONTEXT` (recent candles) and `AUTOREKT_CHART_URL` in the environment.

## Reports

A markdown file with chart blocks, one chart per line in `show` syntax, renders as text with `autorekt show report.md` and publishes as a page with live charts with `--share`:

````markdown
```chart
BTC -i 1h -r 7d --overlay BTC#open_interest
VIX -i 1h -r 5d
```
````
