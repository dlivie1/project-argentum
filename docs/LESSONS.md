# Lessons learned

Things I'd do the same way again, and a few I learned the hard way.

## About trading research

**Fees decide which strategies can exist.**
- At entry-tier exchange fees of 0.80% per taker trade, a round trip costs about 1.8% once slippage is included.
- Short-horizon strategies have to clear that on every trade. A daily trend strategy that trades rarely barely notices it.
- Look up the real fee schedule before designing anything.

**A failed gate is a result, not a problem to fix.**
- D1 missed part of its pre-registered bar. Tuning it until it passed would have produced a strategy that only works on the past.
- Running it unchanged as a forward test, with frozen expectations, keeps the experiment honest.

**Paper trading tests the plumbing, not the edge.**
- Confirming even a good Sharpe ratio statistically takes years.
- What paper trading does prove: orders match the backtest, stops get placed, reconciliation stays clean, and alerts arrive.

**Count everything you try.** Selection bias is invisible unless every variant is logged. The best of 100 random strategies always looks good.

**Leveraged ETFs don't do what their name suggests over time.**
- A 2× daily-reset fund gains more than 2× in a smooth uptrend.
- It loses more than 2× in choppy markets, and roughly squares a crash: a 59% drop becomes about 83%.
- Share prices can mislead too. A reverse split rescales the whole price history.

## About building with AI agents

**Ask for evidence, not agreement.** The most useful instruction I give is "confirm or push back, with file and line." Agreement on its own is cheap.

**Read the plans.** Plan mode only helps if the plan gets real review. Several plans improved at that step:
- a scheduled task that would have been left unregistered after a move;
- a test that would have written fake records into the live journal;
- a memory folder that would have leaked into the wrong project.

**Keep the reviewer independent.**
- The automated reviewer lives in its own repository, with its own memory, and has read-only tools.
- The builder has to justify every finding it rejects.

**Memory belongs in files.**
- Long sessions fade as their context gets summarized.
- A spec, a decision log, progress files and a project rules file let any fresh session pick up exactly where the last one stopped.

**Cross-check blog advice against official docs.** Checking blog-sourced best practices against official documentation caught real errors, including a PowerShell setting that silently doesn't apply to external programs, and a git-ignore pattern that can't do what it looks like it does.

**One big change at a time.** I waited a week of clean paper before a major reorganization, so if anything breaks, I know whether it was old code or new code.

## About operations

**A folder rename can be the hardest step.**
- Moving the repository failed with "folder in use", with everything apparently closed.
- Ending the process holding it turned out to be Windows Explorer itself, which also takes the taskbar with it.
- Lessons:
  - know how to restart `explorer.exe` from Task Manager;
  - plan moves with a go/no-go time and a rollback.

**Plan around the clock.**
- The bot's daily job runs just after the UTC close, so deploys avoid that window.
- Daylight-saving changes shift local times.
- A 1:30 AM task happens twice on the night clocks fall back.
- Write schedules in UTC and check the local-time edge cases deliberately.

**Python virtual environments don't survive a move.** Rebuild them from the lock file. That only works because every dependency is locked.

**Never make a private repository public to show it off.** Git history remembers everything. A separate write-up, like this one, is the safe way to share the work.
