# Sample prompts

These are excerpts from the real prompts used to build the bot, lightly edited: personal paths removed and some sections shortened. Each phase's full prompt also includes its scope, work rules and an end-of-phase report.

| File | What it shows |
|---|---|
| [phase-3b-hard-constraints.md](phase-3b-hard-constraints.md) | How a large refactor on a live system is fenced in |
| [qc-reviewer-outline.md](qc-reviewer-outline.md) | The structure of the automated reviewer's prompt |
| [triage-protocol.md](triage-protocol.md) | How review findings are handled: evidence, not agreement |
| [phase-3d-scenarios.md](phase-3d-scenarios.md) | A simulated-clock test plan |

## The common shape

Every phase prompt follows the same pattern:

1. **Re-read the sources of truth:** CLAUDE.md, the spec, the decision log, the progress file.
2. **Plan mode first:** a read-only survey and proposal, then wait for approval.
3. **Hard constraints,** recorded as a decision.
4. **Numbered work items,** each small and separately verified.
5. **Tests and acceptance criteria.**
6. **An end-of-phase report,** including anything deferred.
