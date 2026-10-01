# Scorecard: Same Rubric for Every Candidate

Part of The OpenClaw Model Routing Guide + Companion Runbook. Score every run from [06-test-plan.md](06-test-plan.md) here, at [RUNBOOK.md](RUNBOOK.md) step 9. Teaching: [GUIDE.md](GUIDE.md) section 7.

**The rule.** Every candidate gets the same rubric, the same anchors, and the same evidence columns. No candidate gets a gentler scale. Copy the per-run block once per run.

**Version note.** This scorecard's version-sensitive route fields were checked against OpenClaw 2026.9.4 on 2026-09-17. Recheck against installed help and the dated guide sections when your version changes.

## Quality anchors (the same for every candidate)

| Score | Meaning |
| --- | --- |
| 1 | Failed, or output unusable |
| 2 | Major errors; needs substantial rework |
| 3 | Usable with minor fixes |
| 4 | Good; minor polish only |
| 5 | Ready to use as-is |

## Per-run block (copy for every run)

| Field | Your value |
| --- | --- |
| Date | |
| Task (which slot from the test plan: routine / complex / specialist) | |
| Candidate model | |
| Run number (1, 2, 3...) | |
| Testing mode (strict candidate comparison / end-to-end configured-route validation) | |
| Session model selection before test | fresh session / recorded prior selection |
| Explicit auth-profile pin before test | none / recorded private value |
| Session mutation approval reference for set/clear, if Mode A | |
| Session pin used (`/model <ref> -s`) | |
| Completed | yes / no |
| Quality score (1 to 5, anchors above) | |
| Followed instructions | yes / partially / no |
| Intended/preferred/eligible routing evidence: selected model (from `/status` in the session) | |
| Intended/preferred/eligible routing evidence: fallback state (Mode B only; Mode A means invalid route) | |
| Intended/preferred/eligible auth profiles (ids stay private) | |
| Exact-charge status | confirmed / unconfirmed / not needed |
| Provider-side record reference, if confirmation needed | |
| Privacy gate passed before input sent? | yes / no |
| Comparable cost evidence, if cost objective applies | baseline reference; changed-route reference; method; comparable and confirmed / otherwise `inconclusive` | |
| Notes (in your words) | |

Sanitization reminder: profile ids can contain email addresses, and status output can contain credential metadata. This scorecard is private notes; redact before sharing anything.

## Comparison summary (fill after all runs)

| Task | Candidate | Runs | Quality pattern (scores across runs) | Completed? | Route surprises worth noting |
| --- | --- | --- | --- | --- | --- |
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |

## Optional semantic-routing summary

Complete this only when [06-test-plan.md](06-test-plan.md) section 9 applies.

| Metric | Recorded result |
| --- | --- |
| Human-labeled cases and accepted classifier decisions | |
| Accepted-decision precision, including routine precision | |
| Abstention and coverage | |
| Timeout / rate-limit / validation / other errors | |
| Classifier latency p50 / p95 / maximum | |
| Frozen stable response-model matches | |
| Decision-to-executor run-id correlations | |
| Dangerous under-routes or unexpected shadow overrides (distinguish raw under-classification from accepted under-route; see [06-test-plan.md](06-test-plan.md) section 9) | |
| Deterministic-baseline comparison | |
| Executor answer quality | |
| Classifier cost evidence | |
| Comparable net savings | confirmed estimate / inconclusive |
| Final kill-switch and rollback state | |

A per-request decision id is not model-version evidence. Stop on model drift, privacy leakage, route mismatch, dangerous under-routing, an unexpected shadow override, or exhausted budget.

## Verdict draft (for step 11, decided from evidence only)

| Question | Your answer |
| --- | --- |
| Did the changed route do what the routing policy line wanted? A cost-based keep needs confirmed comparable cost evidence; otherwise cost is inconclusive. | |
| Which evidence says so (scorecard rows, route verification)? | |
| Anything that must never silently happen again (goes into the fallback template's honesty column)? | |

The verdict itself happens at [RUNBOOK.md](RUNBOOK.md) step 11: keep, revise, or roll back, from recorded evidence. No evidence, no verdict.
