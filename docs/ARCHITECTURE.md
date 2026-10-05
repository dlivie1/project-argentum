# Architecture

Argentarius is one Python process built from small modules with clean interfaces. This page walks through them in the order a trading decision flows.

## Design rules

- **Strategies are pure.** A strategy receives closed-candle data and returns *intents* ("target 50% exposure in BTC"). It never places orders, never knows which mode it's in, and never talks to the exchange.
- **One code path for every mode.** Backtest, paper and live run the same strategy, risk and position code. Only the data feed and the executor differ.
- **Decisions only on closed candles,** and nothing at time *t* may use data from after *t*. A dedicated test checks this for every indicator and signal.
- **All timestamps in UTC.** Local time appears only in reports and messages.
- **Live trading needs two locks:** the config must say live, AND an environment flag must be set. Without both, the bot refuses to start live.

## Data

| Layer | Source | Purpose |
|---|---|---|
| Bulk history | Kraken's downloadable OHLCVT archives | Deep history for research and backtests |
| Bridge | Kraken public trades, rebuilt into candles | Fills the gap between the latest archive and now |
| Live window | Kraken's candle API | The newest candles, cross-checked against the bridge |

- **Storage:** candles are stored in Parquet, partitioned by exchange, symbol, timeframe and year.
- **Quality report:** every backfill produces one. Gaps are flagged and **never interpolated**, and strategies skip signals whose lookback window contains a gap.

## Strategy: D1, a daily trend ensemble

- **Sleeves:** two fixed halves, one BTC and one ETH.
- **Trend checks:** each coin has four moving-average checks at different lookbacks, with a small buffer band so prices hovering near a line don't flip the signal back and forth.
- **Exposure:** steps in quarters (0, 25, 50, 75 or 100% of the sleeve) according to how many checks are on.
- **Catastrophe stop:** a volatility-based stop protects each position. After a stop-out, a cooldown applies before re-entry.
- **Frozen rules:** the rules and parameters were fixed before testing and are frozen. No parameter was optimized.

## Risk management

- **Allocation-based sizing** for D1, with no leverage and a maximum of 100% exposure.
- **Kill switches:** a portfolio drawdown kill (manual reset only), a per-strategy drawdown breaker, and a manual STOP flag.
- **Operational halts:**
  - a reconciliation mismatch;
  - a failed protective stop;
  - repeated order errors;
  - stale data.

  These matter most for a bot that runs unattended.
- **Open positions keep their stops** even when new entries are halted.

## Execution and reconciliation

- **Paper mode** uses live market data with the same fill model as the backtest engine. That fill model handles gaps through stops, taker vs maker fees, and slippage.
- **The exchange is the source of truth.** Reconciliation compares the journal with the exchange's orders, fills and balances. Any mismatch halts new entries and raises an alert.
- **Idempotency:** client order IDs prevent double submission on retries.

## Journal

- **SQLite via SQLAlchemy,** with every schema change done through Alembic migrations.
- **What it records:** every run, signal (including rejected ones), order, fill, position, equity snapshot and risk event.
- **Safety:**
  - foreign keys are enforced;
  - backups use SQLite's backup API, never a plain file copy;
  - an integrity check runs on every backup.

## Scheduling

- **Hourly tick:** checks stops and data freshness.
- **Daily job:** shortly after the UTC daily close, it makes D1's decisions, sends the briefing and runs a **shadow check**, which replays the day in the backtest engine and confirms paper made the same decisions.
- **Nightly journal backup.**
- **Isolation and locking:** each job runs under a lock, so runs never overlap, and a failure in one part (say, the dashboard) is logged without blocking trading.

## Monitoring and messaging

- **Telegram, two destinations:**
  - a **private master chat** gets everything, including account amounts;
  - a **broadcast group** gets market-level information only, and never account data.
- **Message safety rule:** bot messages never contain links (except configured local file paths), and never ask for credentials or funds. A test scans every message template for violations.
- **Check-in pings:** an external service sends an alert if the bot goes quiet.
- **Event calendar:** major US macro releases appear in the briefing. Display only; it never affects a decision.

## Dashboard

- **Two static, read-only HTML pages,** regenerated after every tick. They work offline, make no external requests and have no buttons, so they can never place or change anything.
- **The main page:** status, positions, performance, recent activity, costs and forward-test progress.
- **The Model Health page:** whether live behavior matches the backtest, plus the frozen expectation bands and the cost-stress status.
- **A redaction mode** shows percentages only, with no amounts.

## Forward test and health

- **Frozen expectation bands:** D1's results are compared to bands fixed in advance and rated GREEN, YELLOW or RED.
- **Cost stress:** a separate check confirms the strategy would still meet its targets with higher fees and slippage.
- **Paper's job:** it verifies operations. Proving an edge takes years of data, not weeks (see [RESEARCH_METHODS.md](RESEARCH_METHODS.md)).

## Tax reserve (estimates)

The bot estimates tax owed on net realized gains year-to-date and shows it as a reserve:
- in paper mode, it's display-only;
- in live mode, the reserve is excluded from position sizing;
- it never triggers a sale.

These are estimates for planning, not tax advice.

## Deployment

```
ai-trading/                 plain folder (not a repo, no shared AI instructions)
  mensa-argentaria/         the bot's repository  ("the banker's table")
  regimen-morum/            the reviewer's repository  ("the supervision of conduct")
```

- **Paper:** runs on a Windows PC via Task Scheduler.
- **Live:** will run on a small Linux server with systemd timers. The planned hardening: a non-root service user, a read-only filesystem except the data folder, and secrets delivered as systemd credentials.
- **Backup:** a private GitHub repository. A pre-push hook scans every outgoing commit for secrets, sensitive files and large files.

## Quality control: the Censor

- **What it is:** a separate tool that runs Claude Code headless (`claude -p`) on a schedule against a **snapshot** of the bot's last commit.
- **Read-only:** its only tools are Read, Grep and Glob, and it has no network access.
- **Schedule:**
  - weekly: a review of what changed, plus the critical code;
  - monthly: a full review, one pass per area of the codebase.
- **Tracking:** every run's tokens, a list-price cost equivalent and the share of usage limits it consumed are logged. The tool also estimates what the run would have cost on other models or effort levels.
- **Its findings never change code directly.** The building session triages each one with evidence, and I approve fixes. See [WORKFLOW.md](WORKFLOW.md).
