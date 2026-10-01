# Changelog

Package: The OpenClaw Model Routing Guide + Companion Runbook
OpenClaw baseline: 2026.9.4 (checked 2026-09-17)
Release address: https://github.com/xbillwatsonx/openclaw-model-routing-guide (expected public repository; not yet live)

## 0.1.0, 2026-09-17

- First complete private draft of all 19 package components, from the locked specification and evidence map.
- [GUIDE.md](GUIDE.md): all 11 sections drafted, including the prompt pack (section 10) before the glossary (section 11).
- [RUNBOOK.md](RUNBOOK.md): all 13 steps drafted, with the seven-part safety pattern on every change step.
- Worksheets, templates, and checklists drafted: inventory, task classification, routing policy, change proposal, fallback and retry, test plan, scorecard, route verification, rollback, troubleshooting decision tree, maintenance.
- Every command, subcommand, and flag checked against the installed OpenClaw 2026.9.4 help output on 2026-09-17. No mutation commands executed during drafting; dry-run paths and help text only.
- Phase 3 deterministic and command validation completed the same day: help re-verification, sanitized live read-only execution, and isolated-fixture dry-run tests with the live config proven byte-identical before and after.
- Repairs from Phase 3: `openclaw models auth order get/set/clear` now shown with the required `--provider <id>` (and the profile ids for `set`); `pdfModel` corrected to `agents.defaults.pdfModel`; expectation guards documented as write-time checks that cannot be combined with `--dry-run`; the value-mode `config set --dry-run` schema-check nuance added; source-note cross-references corrected.
- Phase 4 privacy and terminology review completed the same day: extended privacy scanning, a terminology matrix, billing-evidence and scope audits, reader-safety checks, and a humanizer pass. Repairs added missing definitions, separated model pins from profile pins, replaced loose technical wording with the defined terms, softened two conditional cost statements, and added repeatable Phase 4 justfile checks.
- Release URL placeholder in effect everywhere; no fixed address until public release.
- Draft-stage structural, command, privacy, terminology, billing-evidence, and reader-safety checks added as justfile recipes; persona and independent validation remain ahead.
- Status: private draft. Not released. Not public-ready. Remaining validation gates (persona simulations, independent audit, freeze, and review approval) before any release.

## 0.1.1 (unreleased repair), 2026-09-17

- Repaired accepted Phase 5 findings A-F: test modes, privacy/budget/decision evidence, provider boundaries, rollback records, validation rules, troubleshooting, beginner handoffs, and maintenance evidence.
- Second repair pass: made the version-matched backup/restore handoff and byte-level config-file safeguards mandatory; excluded `models set`, auth-order writes, catalog refresh/discovery, and all specialist, per-agent, cron, and subagent mutations from this version's operational workflow; added provider-boundary, strict-comparison, cost-evidence, and unsupported session-auth-pin stops.
- Phase 5 has not passed. Re-simulation remains required.

## 0.1.2 (unreleased semantic-routing evidence integration), 2026-09-29

- Added optional semantic routing after the deterministic baseline, with explicit opt-in scope, model allowlists, confidence floors, one-attempt deadlines, budgets, a kill switch, stable response-model freezing, metadata-only logs, and run-id correlation.
- Added fail-closed guidance: classifier timeout, rate limit, malformed response, version drift, logging failure, or abstention returns no override and preserves the normal route.
- Added shadow-first natural-workload evaluation fields for precision, abstention, latency, errors, route correlation, answer quality, and comparable cost evidence.
- Recorded the bounded private lab result without implying production approval: 10 successful routine overrides and correct answers, two safe classifier timeouts, zero rate limits, 394.5 ms median and 1425 ms maximum successful classifier latency.
- Marked the lab's 1500 ms deadline as experiment-specific, not a universal default.
- No public release, main-agent routing, broader-agent routing, or Standard-tier routing was authorized by this documentation change.

## 0.1.3 (unreleased Phase 6 natural-task shadow evidence), 2026-09-29

- Integrated Phase 6 natural-task shadow evaluation findings into the semantic-routing guidance.
- Phase 6 stopped safely at case 9 of 16 after a dangerous raw under-classification sentinel (N009: human-labeled complex, classifier predicted standard at confidence 0.43, abstained; no override issued, kill switch remained engaged).
- Added the distinction between raw under-classification and accepted under-route: the former is a classifier prediction mistake caught by abstention before it can affect routing; the latter is an accepted override that actually changes the executor model.
- Documented the routine-precision warning from N007: a standard-procedure task was accepted as routine at confidence 0.93. Shadow mode blocked the override, so this was a raw under-classification, not an accepted under-route. It is operationally relevant for routine-only routing because the classifier confidently mispredicted the tier.
- Recorded that the current four-tier single-prompt policy did not generalize reliably from synthetic to natural tasks. The weakness is tier-definition and task-boundary ambiguity, compounded by a classifier seeing only the current prompt rather than consequences, tools, or full workflow context.
- Reinforced the required controls list with task-specific shadow testing: a policy proven on synthetic tasks must be revalidated on natural tasks before any scope expansion.
- Stated explicitly that lowering the confidence floor, expanding to Standard tier, enabling main-agent routing, or removing the kill switch requires a new reviewed plan and explicit authorization.
- No public release, active routing, broader-agent scope, or Standard-tier activation was authorized by this documentation change.

## 0.1.4 (Jev case study; release candidate), 2026-09-30

- Added an explicit reader-facing case study: GUIDE.md section 7.5, "A worked example: one lab's Jev classifier". Section 7.4 stays model-neutral so the generic semantic-routing guidance remains reusable beyond Jev.
- Made the classifier layer's position explicit in reader text: task, classifier tier and confidence, safety gates, allowlisted executor, with the deterministic OpenClaw route as both baseline and fallback.
- Named Jev as a language model available through OpenRouter and stated plainly that the classifier was wired in externally; nothing in the case study is built-in OpenClaw functionality.
- Restated the active-routing evidence with exact bounds: 10 successful routine overrides, 10/10 correct answers, 10/10 route correlations, 10/10 frozen classifier-version matches, 2 fail-closed classifier timeouts, 0 rate limits, 394.5 ms median and 1425 ms maximum successful latency, and the 1500 ms deadline marked experiment-specific.
- Restated the natural-task shadow evidence with exact bounds: stopped at 9 of 16; 6 classifications crossed the confidence filter and 3 abstained; routine accepted precision 4/6 (66.7%); case N007 standard-to-routine crossed the filter at 0.93 but issued no override; case N009 complex-to-standard predicted at 0.43, abstained, no override; zero overrides in shadow mode; latency 310/491/491 ms p50/p95/maximum; 9/9 frozen-model matches and 9/9 route correlations.
- Recorded the supported conclusion: narrow, predefined task classes in advisory or shadow use; not a universal easy-versus-hard router.
- Explained the likely limitation: tier and task-boundary ambiguity, compounded by the classifier seeing only the current prompt without consequences, tools, or full workflow context.
- Added a Jev glossary entry, updated the 06-test-plan.md evidence pointer, and added the reader-facing boundary record in 13-source-notes.md section 5D. No cost figures, executor model names, local paths, run ids, prompts, or raw logs were added to public text.
- After bounded persona simulation, added a plain-language result, defined abstention, confidence floor, executor allowlist, executor model, and latency percentiles, repaired the semantic-policy table, and clarified that N007 crossed the confidence filter in shadow mode but never issued an override.
- Completed bounded Phase 5 re-simulation across a nine-file semantic-routing and Jev case-study fixture set. The initial parent-reviewed result was 36 PASS, 5 NEEDS REVISION, and 0 FAIL; after bounded repairs, 8 of 8 targeted regression checks passed.
- Completed the independent full-package audit and repaired its bounded table, privacy-boundary, validation-ledger, and editorial findings. Added mechanical checks for markdown table shape and private draft artifacts.
- Before first publication, replaced the draft Creative Commons Attribution 4.0 license with the Bill Watson Limited-Use Content License 1.0. Personal learning and internal operational use remain permitted; redistribution and using the protected material as the basis of an offering to others require prior written permission.
- 2026-09-30: The candidate content was frozen and prepared for public release. The URL placeholder was replaced with the fixed expected public address (https://github.com/xbillwatsonx/openclaw-model-routing-guide), and the package status moved from private draft to release candidate awaiting final approval. No substantive guide content changed.
- No public release, classifier activation, routing scope expansion, or kill switch change was authorized by this documentation change.

## Version note

This changelog records package versions, not OpenClaw versions. The OpenClaw baseline this package was checked against is recorded at the top and in [13-source-notes.md](13-source-notes.md); when OpenClaw's version moves, recheck the version-sensitive facts via [11-maintenance-checklist.md](11-maintenance-checklist.md) before trusting dated details.
