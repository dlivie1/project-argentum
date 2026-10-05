# Excerpt: Phase 3d simulated-clock scenarios

Goal: run the bot's real production code through many simulated days in minutes, with a fake clock and replayed data, so date-boundary and scheduling bugs show up before they happen for real. The harness is fully isolated: a temporary data folder, a fake exchange, captured messages, no network, no keys.

```
SCENARIOS (each one a test)
A. Replay parity: 60-90 recorded days with at least one trend flip.
   Simulated paper decisions must equal the backtest.
B. Calendar boundaries: day and week boundaries; month end; year end
   (tax-year rollover); both daylight-saving switches; the 90- and
   365-day forward-test checkpoints fire on the right day.
C. Missed and late runs: PC asleep 6 h, 26 h (daily job caught up
   exactly once), 3 days; overlapping runs; a run finishing late.
D. Data and exchange faults: timeouts, rate limits, a missing candle,
   a stale feed, a late candle, exchange maintenance.
E. Restarts: mid-position, between order and fill, during the daily job.
F. Halts and kills: STOP flag, drawdown kill, strategy breaker,
   reconciliation mismatch. Stops stay in place throughout.
G. Messaging over 30+ days: exactly one daily briefing per day, the
   weekly and monthly summaries, private-only routing for amounts.
H. Endurance: 365 simulated days (journal growth, log rotation,
   generation times, backups with integrity checks).

INVARIANTS (after every simulated step)
- At most one daily job per UTC day; none missing after catch-up.
- No duplicate orders; every open position has its protective stop.
- Equity, positions and cash reconcile with the journal.
- Journal integrity check ok; foreign keys enforced.
- No broadcast message contains account data.
- Every tick finishes within its time budget.
```

It also includes one read-only check of the real world: how Windows Task Scheduler handles a 1:30 AM task on the night that hour happens twice.
