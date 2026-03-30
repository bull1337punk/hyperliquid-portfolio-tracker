# Hyperliquid Portfolio Tracker

> Full portfolio monitoring for Hyperliquid accounts. Tracks open positions, unrealized PnL, realized PnL history, funding earned or paid, margin utilization, and equity curve - with daily summary alerts via Telegram.

![preview_hyperliquid-portfolio-tracker](https://github.com/user-attachments/assets/498b461a-3287-4538-9d82-e710f2169ced)

*Last updated: March 2026*

## What is Hyperliquid Portfolio Tracker?

Hyperliquid Portfolio Tracker reads one or more Hyperliquid wallet addresses and provides a complete picture of their portfolio state: open positions, entry prices, current PnL, leverage, margin used, total equity, and funding payments. It refreshes on a configurable interval and sends daily summary alerts with PnL breakdown.

The tracker maintains a historical log of all closed positions for each wallet, building a cumulative PnL curve over time. You can review win rate, average gain/loss per trade, best and worst positions, and total realized PnL for any date range from the local database.

Multiple wallets can be tracked simultaneously. This is useful for monitoring a set of wallets (such as a list of leaderboard traders you follow) or for keeping a consolidated view across accounts you own.

The bot is read-only - it never submits orders or requires a private key. It uses Hyperliquid's public REST API to read portfolio state.

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/bull1337punk/hyperliquid-portfolio-tracker/releases) |

---

## Portfolio Metrics

| Metric | Description |
|--------|-------------|
| Account equity | Total USD value including unrealized PnL |
| Open positions | All current perp positions with entry, current price, and PnL |
| Unrealized PnL | Current mark-to-market PnL on open positions |
| Realized PnL (day) | Total realized PnL for the current calendar day |
| Realized PnL (total) | Cumulative realized PnL since tracking started |
| Funding net (day) | Net funding payments received or paid today |
| Margin utilization | Used margin / total margin as percentage |
| Leverage (effective) | Total notional / equity |

---

## PnL History

The tracker logs every closed position to a local SQLite database. Queries available:

* Daily PnL summary
* Weekly PnL summary
* Per-asset win rate and average PnL
* Best and worst 10 trades
* Equity curve (day-by-day account value)
* Funding paid/received by asset

---

## How It Works

1. **Poll** - fetches current positions, fills, and funding history from Hyperliquid REST API
2. **Update** - writes new closed positions and funding events to local database
3. **Calculate** - computes current equity, margin utilization, and daily PnL totals
4. **Format** - builds structured portfolio summary
5. **Alert** - sends daily summary via Telegram at configured time, or on-demand via command

### Config Reference

```toml
[wallets]
addresses = [
  "0x8b3d6f1a4c7e2b5d9f3a7c1e5b8d2f6a4c9e3b7d"
]
labels = ["main"]              # Optional labels for each wallet

[polling]
interval_seconds = 300
daily_summary_time = "08:00"   # UTC time for daily summary alert

[alerts]
telegram_bot_token = ""
telegram_chat_id = ""
alert_on_large_pnl_change = true
large_pnl_change_usd = 500     # Alert if unrealized PnL changes by this in one interval

[database]
path = "data/portfolio.db"

[api]
rest_endpoint = "https://api.hyperliquid.xyz"
```

---

## Daily Summary Format

```json
{
  "summary_date": "2026-03-20",
  "wallet": "0x8b3d6f1a4c7e2b5d9f3a7c1e5b8d2f6a4c9e3b7d",
  "label": "main",
  "equity_usd": 18420.00,
  "equity_change_usd": +340.50,
  "open_positions": 3,
  "realized_pnl_day": +182.40,
  "unrealized_pnl": +158.10,
  "funding_net_day": -14.20,
  "margin_utilization_pct": 38.4,
  "effective_leverage": 2.1
}
```

---

## Verified on Hyperliquid Mainnet

**Wallet tracked:**
* Address: `0x8b3d6f1a4c7e2b5d9f3a7c1e5b8d2f6a4c9e3b7d`

**30-day summary:**

| | Result |
|---|---|
| Total realized PnL | +$4,830 |
| Total funding net | -$127 |
| Win rate | 67% (34 trades) |
| Best single trade | +$1,240 (BTC LONG) |
| Worst single trade | -$380 (ETH SHORT) |

---

## Bot Preview

![portfolio summary view](https://github.com/user-attachments/assets/96b4ddf7-3b62-4140-9ebf-9db422854c21)

---

<img width="1920" height="1080" alt="portfolio tracker equity curve" src="https://github.com/user-attachments/assets/8663a3ff-3f36-4a4d-b3f1-c5d00218e7b9" />

---

## Frequently Asked Questions

**What is Hyperliquid Portfolio Tracker?**
It monitors one or more Hyperliquid wallets and provides a complete portfolio view: open positions, unrealized PnL, realized PnL history, funding, and equity curve. It sends a daily summary via Telegram.

**Can I track multiple wallets?**
Yes. Add multiple addresses and optional labels to the wallets config. Each wallet gets its own portfolio state and history in the local database.

**Does it need a private key?**
No. The tracker is read-only and uses Hyperliquid's public REST API.

**How often does it refresh?**
Every 5 minutes by default (configurable). Daily summary alerts send at a configured UTC time.

**Does it track funding payments?**
Yes. It records funding paid and received for each position and aggregates it by day, week, and asset.

**Can I query historical PnL?**
Yes. The local SQLite database stores all closed position data. The Python version includes query functions for daily/weekly summaries, per-asset stats, and equity curve export.

**What happens if a wallet has no activity?**
The tracker logs the current equity and position state regardless. On days with no closed positions, the daily summary shows the equity snapshot and any funding payments.

**Can I track wallets I do not own?**
Yes. Any Hyperliquid wallet address can be tracked. This is useful for monitoring leaderboard traders.

**Does it alert on large PnL swings?**
Yes. Set `alert_on_large_pnl_change = true` and configure the USD threshold. If unrealized PnL changes by more than the threshold between polls, an alert fires immediately.

**What database does it use?**
SQLite, stored locally. No external database required.

---

## Use Cases

- **Portfolio monitoring** - track equity, PnL, and margin utilization across your Hyperliquid accounts
- **Performance review** - review win rate, average trade PnL, and equity curve over time
- **Leaderboard tracking** - monitor the portfolio state of top Hyperliquid traders you follow
- **Daily reporting** - receive a structured daily P&L summary via Telegram each morning
- **Funding tracking** - see exactly how much funding you are paying or collecting per position

---

## Repository Structure

```
hyperliquid-portfolio-tracker/
+-- hyperliquid-portfolio-tracker-v.1.4.14.exe
+-- config.toml
+-- data/
|   +-- portfolio.db
|   +-- logs/
+-- python/
|   +-- src/
|   |   +-- poller.py
|   |   +-- database.py
|   |   +-- calculator.py
|   |   +-- reporter.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
httpx, toml, python-dotenv, rich, aiosqlite, pandas
```

* Python 3.10+
* Telegram bot token for daily summary alerts
* No Hyperliquid account required (read-only)

---

*Every position. Every day. Every satoshi.*
