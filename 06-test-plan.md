# Matched-Task Test Plan

Part of The OpenClaw Model Routing Guide + Companion Runbook. Use at [RUNBOOK.md](RUNBOOK.md) step 9, after one approved change is live (or to compare candidates before proposing one). Teaching: [GUIDE.md](GUIDE.md) section 7. Score every run with [07-scorecard.md](07-scorecard.md).

**What this plan is.** A reusable design for comparing candidate models on your real work. The whole method rests on one idea: the same task, the same inputs, for every candidate, scored with the same rubric. Change the task between candidates and you're no longer comparing models; you're comparing two different days.

**Version note.** The behavior facts below were checked against OpenClaw 2026.9.4 on 2026-09-17.

## 1. Pick the tasks

Choose two or three recurring tasks from [02-task-classification-worksheet.md](02-task-classification-worksheet.md), spanning your actual range:

- One routine task (the bread-and-butter work your primary handles most).
- One complex or high-risk task (the kind that argues for strict selection).
- Keep this version's testing text-only. Specialist, media, per-agent, cron, and subagent route modification lacks authoritative execution evidence and recovery here; do not test or change those routes until a future route-specific procedure exists.

| Slot | Task (in your words) | Where the identical inputs live |
| --- | --- | --- |
| Routine | | (paste the same prompt, attach the same files, every run) |
| Complex / high-risk | | |

Write the inputs down once, verbatim, and reuse the identical text, files, and order of asks for every candidate. If a run needs context, give every candidate the same context.

## 2. Choose one testing mode before running

### Mode A: strict candidate comparison

Use fresh sessions pinned to one candidate. Text-model fallback is not expected. Any different text model or fallback, including the configured primary or a candidate-chain fallback, invalidates the run for candidate scoring, even if the output looks useful. Record `invalid route`; reserve fallback observations for Mode B end-to-end validation, then classify the route surprise before continuing.

### Mode B: end-to-end configured-route validation

Use a fresh unpinned session on the configured default route. This checks the applied primary and fallback policy as a system, not a candidate in isolation. Record the selected model, fallback state, and auth evidence. Do not score it as a strict candidate comparison.

Selected mode: strict candidate comparison / end-to-end configured-route validation

## 3. Predeclare privacy gate, budget, and decision criteria

Before sending any test input, complete this gate for every candidate and test task. Every eligible auth profile, fallback, inferred default, and override that could receive the input must be approved for its data class. If any destination is unset, inferred, unverified, or unapproved, stop; sanitize the input or use an approved local route.

| Candidate / route | Eligible auth profiles, fallbacks, inferred defaults, and overrides checked | Provider/destination | Data class | Approved for this data class? | Synthetic/sanitized or local route required? | Provider-boundary gate passed? |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

| Test budget field | Predeclared value |
| --- | --- |
| Maximum runs | |
| Maximum elapsed time | |
| Optional spend ceiling | not set / amount kept in private records |
| Result when any budget limit is reached without a clear result | inconclusive; stop testing |

| Decision criterion | Threshold or rule set before approval |
| --- | --- |
| Minimum quality | |
| Completion requirement | |
| Instruction-following requirement | |
| Expected route behavior | |
| Tolerated failures | |
| Privacy/provider restrictions | |
| Number of valid runs | |
| Cost objective, if cost is a reason | |

If cost is a stated objective, import confirmed, comparable baseline and changed-route evidence from the Cost Savings Guide. Record method/reference and comparability. If either side is absent, unconfirmed, or not comparable, the cost outcome is `inconclusive` and cannot justify a keep decision on cost grounds. Never infer cost from a model name, auth preference, or provider selection.

## 4. Set up each run

For every candidate, every task, every run:

1. Open a fresh session. Record the prior session model selection and auth-profile pin as not applicable because the test session is new. Session history contaminates comparisons; a model continuing an ongoing conversation is doing different work than one starting cold.
2. In strict candidate comparison only, pin the candidate in that session. This is a session mutation and must be covered by the written testing approval, which names the bounded test sessions, candidates, run limit, and cleanup action:

   `/model <provider/model> -s`

   (Chat command, session scope.) Session selections are strict: if the candidate can't handle the run, it fails visibly instead of silently falling back. That's the test working, not breaking. A silent fallback would corrupt the comparison (checked against OpenClaw 2026.9.4, 2026-09-17).
3. Run the task with the identical inputs.
4. Capture intended/preferred/eligible routing evidence immediately, from inside that session: `/status` for the selected model and any active fallback state, and `/model status` for the session selection and auth candidates per provider. This text evidence does not prove specialist/scoped routing or an exact provider charge.

## 5. Repeat counts

Any single run can be lucky or unlucky. Run each candidate on each task at least twice before scoring a verdict, and more (three or more) for close calls or high-risk tasks. That's method advice, not an OpenClaw fact; the point is that a one-run comparison is mostly noise.

## 6. The scoring method

Every run of every candidate gets the same [07-scorecard.md](07-scorecard.md) rubric:

- Completed: yes or no.
- Quality: 1 to 5, with the same anchors for every candidate (1 failed or unusable; 3 usable with minor fixes; 5 ready to use as-is).
- Followed instructions: yes, partially, no.
- Intended/preferred/eligible routing evidence: selected model, any fallback notice, and pinned/preferred/eligible profiles. Add provider-side exact-charge status only when needed.
- Notes, in your words.

The route evidence column is what makes this a routing test rather than a beauty contest. "Quality was fine" and "quality was fine, but it came from the fallback while the primary was in cooldown" are different facts about your system.

## 7. Fairness rules

- Same inputs, same order, same attachments, every time.
- Fresh session for every run.
- Don't help one candidate and not another. If you clarify instructions mid-run for one, either do the same for all or restart that comparison.
- If a run fails for environment reasons (authentication, rate limit, billing) rather than model quality, stop the comparison, handle the environment, and rerun; a broken environment invalidates the data. See [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md).

## 8. Read the results

Fill the comparison summary at the bottom of [07-scorecard.md](07-scorecard.md). Read patterns, not averages alone:

- A candidate that's excellent on routine work but fails the high-risk task may still be a great primary, if your policy routes the high-risk work elsewhere.
- Do not use cost to keep a candidate unless the confirmed comparable evidence above supports it.
- If two candidates tie, tie means tie; keep the one your policy has a written reason for, or run more tests.

You're not crowning a winner. You're assigning work, with evidence.

## 9. Optional semantic-routing evaluation

Use this only after the deterministic route has a written baseline and working rollback. Start in shadow mode: the classifier records what it would choose but never changes the executor.

Predeclare:

| Semantic test field | Required value |
| --- | --- |
| Exact opt-in scope | one lab agent or bounded test sessions |
| Human-labeled task set | representative, sanitized tasks from your real workload |
| Deterministic baseline | rule or normal route scored on the same tasks |
| Allowed executor routes | explicit allowlist |
| Confidence floor | recorded before testing |
| Classifier attempts | one per eligible run |
| Deadline | experiment-specific, not copied from another deployment |
| Classifier and run budgets | hard maximums |
| Kill switch and rollback | path/action verified before testing |
| Logs | metadata only, correlated by run id |

Record accepted-decision precision, especially for the cheapest route; abstention and coverage; timeout, rate-limit, validation, and other error counts; p50, p95, and maximum classifier latency; frozen stable response-model matches; route correlation; executor answer quality; classifier cost; and comparable baseline versus changed-route cost. A per-request decision id is not the stable model version. If comparable cost evidence is unavailable, mark savings `inconclusive`.

Stop immediately on any dangerous under-route, privacy leak, model drift, route mismatch, unexpected override during shadow mode, exhausted budget, or repeated rate limit. A classifier timeout is a recorded abstention, not permission to retry. Fail closed to the normal deterministic route.

Distinguish raw under-classification from accepted under-route in your stop-rule and scoring language. Raw under-classification means the classifier predicted a lower tier but abstention, a kill switch, or shadow mode blocked the override before the executor changed. Accepted under-route means the override actually ran on the wrong model. The first is a warning, though a predeclared stop gate may still halt the series on a dangerous raw under-classification. The second is an incident.

Evidence example, not a universal target: one private lab's Jev classifier reached 10 successful routine overrides with 10 correct answers on synthetic tasks, then stopped safely at case 9 of 16 on natural tasks under a predeclared stop rule. The full worked case study is in [GUIDE.md](GUIDE.md) section 7.5. That lab's 1500 ms deadline belonged to its experiments and must not be copied as a default.

## Done?

Take the completed scorecards to [RUNBOOK.md](RUNBOOK.md) step 10 (route verification) and step 11 (keep, revise, or roll back).
