# Excerpt: Phase 3b hard constraints

Context: a two-stage refactor (a multi-strategy reorganization, then multi-coin plumbing) on a repository that runs a paper-trading bot every hour. The constraints below are the part that keeps it safe.

```
HARD CONSTRAINTS (record as a decision)
- INFRASTRUCTURE ONLY. D1 is mid-forward-test. No change to D1's rules,
  parameters, sleeves, benchmark, gates, bands, health logic or results.
- Scope freeze: no best-practice fixes, no lint-rule rollouts, no new
  features, no dependency upgrades. If you find a bug, record it in the
  progress file and tell me; don't fix it inside this phase unless I
  approve it as a separate commit.
- Keep the repo folder, the package name and every CLI command unchanged.
  The scheduled task, venv, local config and data directory keep working
  with no action from me.
- GOLDEN FILES, generated ONCE before any change and used for BOTH stages:
  - D1 development and holdout validation outputs (summary, gate values,
    fills, equity curves), regenerated from the frozen data and config.
    No new holdout access.
  - Paper-mode decisions replayed over the paper period.
  - The current add-on outputs (tax-reserve values, dashboard inputs,
    health and band measures, cost-stress values).
  - Store them with a hash. After EVERY step, D1 outputs must be
    byte-identical. Any difference is a failure, not a judgment call:
    stop and tell me.
- Paper keeps running:
  - work in a worktree;
  - merge only after the full suite and the golden-file check pass,
    outside :03-:10 past each hour and the daily-job window;
  - back up the journal first (SQLite backup API or VACUUM INTO,
    never a file copy);
  - after each merge, confirm the next hourly tick is clean.
- Stage gates: Stage B deploys only after Stage A is merged AND at least
  one daily shadow check on the Stage A code is clean.
- Journal migration (only if unavoidable): reversible, tested on a COPY
  of the real journal (upgrade, downgrade, upgrade, integrity check),
  with named constraints so batch rebuilds never drop CHECK constraints.
```

Why it works: each constraint turns a judgment call into a check that either passes or fails.
