# Devlog

Short, dated progress notes. Newest first.

## 2026-11-15
- **Decisions:** logged a batch of decisions on trading and operations: capping fallback market buys by available cash, a manual way to stop new entries in paper and live, a tolerance and running total for small balance differences, keying cost records to the fill, and keeping amounts out of broadcast messages.
- **Order handling:** tightened how the bot treats orders whose outcome is uncertain. It never resends a market order whose result is unknown, and a stop keeps one identity until its outcome is known. It also checks for a halt before each order and keeps a position protected when a flatten can't complete.
- **Runbook:** wrote out the runbook steps for stopping and resuming, setup, and the prerequisites for going live.
- **Change rules:** revised the change rules and the road after Phase 3d.

## 2026-11-05
- **Triage protocol:** wrote down the triage protocol for Phase 3c, which sets how each review finding gets a verdict backed by evidence.

## 2026-10-06
- **Update helper:** set up an automated helper that proposes updates to this write-up as pull requests, which I review before anything is published.

## 2026-10-05
- Published this write-up.

## 2026-10-04
- **Folder move:** moved the bot's repository into a project folder that will also hold its independent reviewer.
- **Backup:** set up a private GitHub backup with a pre-push secret scan.
- **Statistics standard:** wrote the ETF track's statistics standard (22 rules from 26 sources).
- **ETF plan:** planned the ETF track and the broker integration.
- **Phase 3d:** added a simulated-clock harness to the roadmap, to catch date and scheduling bugs before they happen.
- **Paper add-ons:** shipped an economic event calendar, security hardening, a static dashboard with a Model Health page, and tax-reserve estimates.

## 2026-10-03
- **D1:** pre-registered it (a daily trend ensemble on BTC and ETH) and evaluated it against its gates. It didn't pass all of them, so it runs unchanged as a paper forward test with frozen expectation bands.
- **Paper operations:** the daily job, Telegram (a private chat plus a broadcast group) and a daily shadow check against the backtest.

## 2026-10-02
- **Spec:** wrote the project specification.
- **Phases 1–2:** a data importer, the backtest engine, the validation suite and the first four strategies. None qualified for paper trading.
