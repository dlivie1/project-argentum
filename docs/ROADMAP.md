# Roadmap

Each phase starts only when the one before it is clean. Dates are estimates; quality comes before speed.

## Done

### Foundation and research
- **Data:** a Kraken history importer, with quality reports and gap flagging.
- **Backtest engine:** event-driven, with a fill model shared by backtest and paper.
- **Validation suite:**
  - a development/holdout split, with the holdout used once;
  - walk-forward tests;
  - Monte Carlo resampling;
  - parameter-stability sweeps;
  - pass/fail gates.
- **The first four strategies** traded 1-hour and 4-hour charts, and none qualified for paper trading. At entry-level exchange fees (0.40% maker, 0.80% taker), short-horizon ideas need a very large edge to survive costs.

### D1: a pre-registered daily trend strategy
- **The design:** a daily trend ensemble on BTC and ETH, written down with its gates before any test ran.
- **The result:** it did not pass all of its gates. It runs unchanged as a paper forward test, judged against frozen expectation bands.

### Paper operations
- **Scheduled runs:** the hourly tick, the daily decision job and the daily shadow check.
- **Telegram:** a private master chat and a broadcast group, with strict routing.
- **Security hardening:**
  - secret scanning;
  - a locked dependency set;
  - the message safety rule;
  - a security checklist in the runbook.
- **A static read-only dashboard** and the Model Health page.
- **Tax-reserve estimates** and the cost-stress checks.

### Repository move and backup
- **The move:** the repository went into a project folder that will also hold its independent reviewer. It was planned in three parts, because a Claude Code session can't move the folder it's running in: prepare, then move by hand, then a new session finishes up.
- **The backup:** a private GitHub repository, with a pre-push hook that scans outgoing commits.

### Phase 3b: engine readiness
- **Golden files:** D1's outputs, generated once and stored with a hash. Every later change is checked against them, and any unexplained difference stops the work.
- **Change rules until live:** while D1's forward test runs, the code changes only for a few shared support tools (the reviewer, the helper that updates this write-up, a shared Telegram sender), approved review fixes and the simulated-clock harness. Fewer changes mean less risk to the test.
- **A tidy-up** of the builder's working rules, plus a map of the repository for the reviewer.
- **Replaced plan:** the multi-strategy platform and multi-coin plumbing planned for this phase were cancelled, for the same reason: they would have changed a lot of code in the middle of the forward test.

## Next

### Phase 3c: the QC loop
- **Connect the Censor:** the scheduled, read-only Claude Code reviewer is built, in its own repository; this phase points it at the bot's code.
- **Install the triage protocol:** verdicts with evidence, behavior-impact labels, and a disputes log.
- **Then:** triage the first full review and fix the approved findings, at most one batch per day, each followed by a clean shadow check. The findings Phase 3b recorded are settled here too.

### Phase 3d: simulated-clock harness
Run the bot's real code through months of scheduled runs in minutes, using a fake clock and replayed data. Scenarios include:
- month and year ends, including the tax-year rollover;
- both daylight-saving switches;
- missed and overlapping runs;
- restarts in the middle of a position;
- exchange faults;
- message cadence;
- a 365-day endurance run.

### 14 days of clean paper
On the final pre-live code. Only critical safety fixes are allowed, and any behavior-changing fix restarts the clock.

### Phase 4: server and live
- Move to a small Linux server.
- Start small: a fraction of the intended size, with explicit approval at each step.

## Future

### Phase 5: crypto ETF track
- **Where:** a second strategy track running **alongside** D1, trading US-listed crypto ETFs through a stock broker's API (paper first).
- **The idea:** signals come from the coin's own price history. The ETFs' history is rebuilt synthetically back to 2016, then checked against the real funds since they launched.
- **The standard:** every test follows a written statistics standard (see [RESEARCH_METHODS.md](RESEARCH_METHODS.md)).

### Further ideas, gated
- **Moving D1 to a different broker,** if that broker becomes available where I live. D1's signal data would stay the same; only where orders go would change.
- **A standalone research toolkit:** the statistics and simulation code as its own tested library.
