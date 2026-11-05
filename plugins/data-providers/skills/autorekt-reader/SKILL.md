---
name: autorekt-reader
description: >
  Read cross-asset market data and macro liquidity through the autorekt CLI, which fetches free
  public sources directly with no API key: a one-screen market briefing, US dollar liquidity
  (SOFR vs IORB, repo distribution, swap spreads, TGA, reserves, standing repo usage), rates and
  credit spreads, CFTC positioning, S&P 500 dealer gamma, skew and put/call from Cboe option
  chains, crypto perp funding, open interest, long/short ratios and liquidations, US spot ETF
  flows, China and Hong Kong money markets (CNY fixing, SHIBOR, CGB yields, margin debt,
  southbound flows), Polymarket and Kalshi odds, the macro calendar and headlines. Use it when
  the user asks what is happening in markets, why an asset moved, whether there is panic or
  stress in funding markets, or wants alerts on a market condition. For single-stock analysis,
  earnings and options payoff, use the market-analysis skills; for Hyperliquid order books, use
  hyperliquid-reader. Read-only: autorekt never trades.
---

# autorekt Reader (Read-Only)

If your host has not already executed the shell probes below (for example, in Codex), run the commands inside the `!` wrappers with its shell tool before choosing a setup path. Use the actual output; if shell access is unavailable, report that limitation.

[autorekt](https://github.com/autorekt/autorekt) is an open-source (MIT) command-line tool. It fetches market data straight from the public sources themselves (exchanges, the New York Fed, Treasury, FRED, CFTC, Cboe, DTCC, the ECB, China's interbank market, Polymarket, Kalshi and news feeds) and prints it as compact text, with every value labelled by its age. There is no API key, account or hosted service; the first command starts a small local daemon that keeps fetched data fresh in `~/.autorekt`.

---

## Step 1: Ensure autorekt Is Installed

**Current environment status:**

```
!`(command -v autorekt && autorekt --version && echo "READY" || echo "NOT_INSTALLED") 2>/dev/null || echo "NOT_INSTALLED"`
```

If the status shows `READY`, go to Step 2. Otherwise ask the user before installing; the installers put one binary in `~/.local/bin` after checking its SHA-256:

```bash
# Linux and macOS
curl -fsSL https://autorekt.com/install.sh | sh
# Windows PowerShell
irm https://autorekt.com/install.ps1 | iex
```

Release binaries for Linux, macOS and Windows (x64 and arm64) are also on [GitHub Releases](https://github.com/autorekt/autorekt/releases).

---

## Step 2: Identify What the User Needs

| User request | Command |
|---|---|
| What is happening in markets, is there panic | `autorekt show` (the briefing) |
| Dollar liquidity, funding stress, repo, TGA | `autorekt show liquidity` |
| Rates, curve, credit spreads | `autorekt show rates` |
| Positioning: CFTC, dealer gamma, put/call, ETF flows | `autorekt show positioning` |
| Fear gauges: VIX curve, VVIX, MOVE, OVX, crypto vol | `autorekt show fear` |
| Sector or megacap rotation | `autorekt show sectors`, `autorekt show megacaps` |
| China and Hong Kong | `autorekt show asia` |
| Crypto overview, funding across venues | `autorekt show crypto`, `autorekt show funding BTC ETH` |
| One asset in detail | `autorekt show NQ -i 15m -r 1d` |
| Why did X move, what moved with it | `autorekt why BTC -r 2d` |
| How assets relate, which pairs broke | `autorekt why SPX BTC GOLD US10Y -r 30d` |
| Prediction-market odds | `autorekt show odds`, `autorekt search fed december` |
| Macro calendar, headlines | `autorekt show calendar USD`, `autorekt show news macro` |
| Alert me when a condition holds | `autorekt watch NAME --expr '...'` (see Step 3) |

Default to the briefing when the request is broad or names no asset.

---

## Step 3: Execute

1. **Start broad, then drill down.** Run the briefing first for any market-wide question, then the board or series that explains what stands out. Identifiers are `provider:symbol[#metric]` or aliases (`BTC`, `SPX`, `NQ`, `VIX`, `US10Y`, `DXY`, `GOLD`, `SOFR`, `TGA`, `SPX_GEX`); find unknown ones with `autorekt search <words>`.
2. **Expressions work wherever an id does**: `autorekt show 'RSP / SPY' -i 1d -r 120d`, `autorekt show 'fred:SOFR - fred:IORB' -i 1d -r 90d`.
3. **Use `--json`** when another tool will consume the output; the default text is designed for model reading.
4. **Alerts** run only at the user's request. Backtest first, then create the rule; the daemon runs the hook command whenever the rule fires, so show the user the exact hook before saving it:

```bash
autorekt watch 'VIX - VIX3M > 1.5' --test -r 90d
autorekt watch vix_inv --expr 'VIX - VIX3M > 1.5' --tf 15m --for 30m --hook 'notify.sh'
```

5. **Read-only.** autorekt has no order or account commands; `show FILE.md --share` publishes a report through a public Cloudflare tunnel, so use it only when the user asks for a link.

---

## Step 4: Respond to the User

Lead with the conclusion the numbers support, then the evidence: each value with its time and change, grouped by what it shows (price action, volatility, liquidity, positioning). Keep the caveats autorekt prints: Yahoo and Cboe data lag about 15 minutes, FRED, Treasury and CFTC data lag one or more days, dealer gamma assumes dealers are long calls and short puts, and crypto positioning comes from OKX. A row listed as missing means the source failed; say so rather than filling the gap from memory. Keep tables short: quote the rows that matter instead of pasting whole boards.

---

## Step 5: Diagnostics

```bash
autorekt search --check
```

It probes every source and reports which are reachable from this network. A source that refuses the network (for example, Binance from the US) is reported, not hidden; `autorekt daemon logs` shows the daemon's recent errors.

---

## Reference Files

- `references/commands.md` — commands, boards, identifiers, expressions and rule syntax
