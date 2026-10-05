# Excerpt: the QC triage protocol

The building session follows this whenever it receives a review report. It lives in the project's rules file, so every session uses it.

```
Triage makes NO code changes.

For EACH finding, give a verdict WITH EVIDENCE.
"Agreed" or "disagreed" alone is never acceptable.
- CONFIRMED: cite the code. For a bug, name a test that would fail today.
- PARTIAL: the issue is real, but the severity, scope or fix is wrong.
  Say what, and propose the better fix.
- REJECTED: explain why the reviewer is wrong, with evidence (handled at
  file:line; covered by a named test; based on a misread).
- ALREADY_FIXED or DUPLICATE: cite the commit or earlier finding ID.
- OUT_OF_SCOPE: it touches the strategy's frozen rules. Never "fix" these.
- NEEDS_INVESTIGATION: say exactly what would settle it.

Classify every fix:
- NEUTRAL: the strategy's golden files stay identical.
- CHANGING: outputs change. Flag prominently; needs its own decision
  record, a deliberate golden-file update, and a note on what it means
  for the forward test.

No bias either way. Don't accept findings to be agreeable, and don't
reject them to avoid work. "I wrote it that way on purpose" is not
evidence; a spec, decision, test or invariant is.

Any Critical/High finding you reject or downgrade goes in DISPUTES.
Don't settle it yourself.

Fix procedure (only after approval): failing test first, then the fix;
one finding per commit, citing its ID; at most one fix batch deployed
per day, with a clean shadow check in between.
```
