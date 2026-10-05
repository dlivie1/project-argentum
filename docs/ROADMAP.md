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

## Next

### Phase 3b: multi-strategy platform
- **Stage A:** reorganize the code so several strategies can run side by side, each with its own config, kill switches, health rating and tax scope. A crash in one never affects another.
- **Stage B:** multi-coin plumbing, plus a research-safe coin check tool. It reports data quality and liquidity only, never backtest performance, so coins are never picked by past returns.
- **The constraints:**
  - zero behavior change, proven with golden files after every step;
  - a scope freeze, meaning no new features;
  - any journal migration must be reversible and tested on a copy first.

### Phase 3c: the QC loop
- **Build the Censor:** a scheduled, read-only Claude Code reviewer with cost and usage tracking.
- **Install the triage protocol:** verdicts with evidence, behavior-impact labels, and a disputes log.
- **Then:** triage the first full review and fix the approved findings, at most one batch per day, each followed by a clean shadow check.

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
