# The OpenClaw Model Routing Guide + Companion Runbook

**Series position:** An unnumbered special guide. It was released after the OpenClaw Cost Savings Guide and before Runbook 2. It is not part of the numbered runbook sequence.

**Edition:** Written against OpenClaw 2026.9.4 (checked 2026-09-17). Recheck version-specific details against your installed docs.

**Version:** 0.1.4

**Status:** Released.

**Release address:** https://github.com/xbillwatsonx/openclaw-model-routing-guide

## What this package is

Model routing in OpenClaw is six linked decisions, not one model name: the primary model, the fallback order, the model-selection policy, the auth profiles and their order, the billing path, and session-level overrides. The guide explains how those six fit together and how OpenClaw actually moves work through them. The companion runbook turns that picture into a safe, one-change-at-a-time procedure: inventory, classify, write a policy, baseline, dry-run, one approved change, matched-task tests, route verification, then keep, revise, or roll back.

The OpenClaw Cost Savings Guide explains why routing matters for spending. This package is the how-to: how to design, configure, test, verify, and roll back your routing with evidence at every step.

No file in this package promises savings, model rankings, or guarantees. Every claim is version-labeled, and every factual claim traces to a recorded source in [13-source-notes.md](13-source-notes.md).

## Who it's for

The self-hosted operator: you run your own OpenClaw, you can use a terminal and edit configuration files, and you want to understand your system, not just paste commands into it. No programming background assumed.

Not for: people brand new to AI agents entirely, developers wanting API reference material, or end users who consume an agent as a product and never touch its setup.

## What you'll be able to do

- Inventory your providers, models, credentials, and billing paths without exposing secrets.
- Classify your work by difficulty, risk, value, privacy, and tool needs.
- Design primary, specialist, and scoped routes deliberately, while keeping this version's executable workflow limited to the global text route.
- Define fallback and retry boundaries with stop rules.
- Make one controlled change with a recorded baseline and a rollback path.
- Test candidates with matched tasks and a shared scorecard instead of model reputation.
- Verify which model route OpenClaw reports, inspect the intended credential route, and know when provider billing records are needed for charge confirmation.
- Keep, revise, or roll back from recorded evidence, then maintain the policy as versions, catalogs, and prices change.

## How to use this package

1. Read the [guide](GUIDE.md) for the full picture. It defines every term it uses, and its section 10 holds the copyable prompts.
2. Work the [runbook](RUNBOOK.md) step by step when you're ready to operate. It carries the seven-part safety pattern (scope, proposal, baseline, rollback, verification, stop condition, approval boundary) on every change step.
3. Copy the [prompts](12-prompts.md) into your agent chat one at a time. Each prompt names the package section your agent must read first, states the safe behavior you expect, and forbids action without your explicit approval of the exact action.

## Contents

| File | What it is |
| --- | --- |
| [GUIDE.md](GUIDE.md) | The main guide. Eleven sections: the six linked decisions, inventory, classification, policy, fallback and retry design, testing, verification, maintenance, prompts, glossary. |
| [RUNBOOK.md](RUNBOOK.md) | The 13-step companion procedure, one approved change at a time. |
| [01-inventory-worksheet.md](01-inventory-worksheet.md) | Record providers, models, credentials, and billing paths with read-only commands. |
| [02-task-classification-worksheet.md](02-task-classification-worksheet.md) | Classify recurring work by difficulty, risk, value, privacy, and tool needs. |
| [03-routing-policy-template.md](03-routing-policy-template.md) | Write the routing policy before touching configuration. |
| [04-config-proposal-template.md](04-config-proposal-template.md) | Capture one change with scope, baseline, rollback, verification, stop conditions, and approval. |
| [05-fallback-retry-template.md](05-fallback-retry-template.md) | Design fallback order, retry expectations, and stop rules. |
| [06-test-plan.md](06-test-plan.md) | Plan matched-task tests with identical inputs for every candidate. |
| [07-scorecard.md](07-scorecard.md) | Score every candidate with the same rubric and record the route each run took. |
| [08-route-verification-checklist.md](08-route-verification-checklist.md) | Verify the reported model route, inspect auth routing evidence, and separate that evidence from provider billing confirmation. |
| [09-rollback-checklist.md](09-rollback-checklist.md) | Restore the recorded baseline and verify the restore. |
| [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md) | Classify a failed or surprising route from evidence, branch by branch. |
| [11-maintenance-checklist.md](11-maintenance-checklist.md) | Recheck version-sensitive details and keep the policy current. |
| [12-prompts.md](12-prompts.md) | Eleven copyable prompts that bridge you to your agent, safely. |
| [13-source-notes.md](13-source-notes.md) | Claim trace, verification record, unresolved items, and deliberate exclusions. |
| [14-glossary.md](14-glossary.md) | Every term the package uses, in plain language. |
| [15-changelog.md](15-changelog.md) | Package version, OpenClaw baseline, and change history. |
| [LICENSE.md](LICENSE.md) | The Bill Watson Limited-Use Content License 1.0. |
| [README.md](README.md) | This file: package identity, contents, and version metadata. |

## Version and validation status

- Package version 0.1.4, released 2026-09-30.
- Every command, subcommand, and flag was checked against the installed OpenClaw 2026.9.4 help output on 2026-09-17. No mutation command was executed against a live setup at any point; mutation-shaped validation ran only inside a disposable isolated fixture, and no credential flow ran at all.
- This release passed the structural checks in the package's private validation harness, the Phase 3 technical command validation, the Phase 4 privacy and terminology review, Phase 5 bounded re-simulation of the semantic-routing and Jev case-study files, 8 of 8 targeted regression checks, and an independent full-package audit. All bounded findings were repaired before release.
- The public repository is https://github.com/xbillwatsonx/openclaw-model-routing-guide. Agent-readable prompts use the immutable v0.1.4 guide at https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md.

## Related OpenClaw resources

These three resources form a practical path through recovery readiness, deliberate model routing, and safe day-to-day maintenance:

1. [Before You Trust OpenClaw](https://github.com/xbillwatsonx/openclaw-runbook-1-before-you-trust-openclaw): establish verified backups, independent host access, rollback, and recovery first.
2. [The OpenClaw Model Routing Guide + Companion Runbook](https://github.com/xbillwatsonx/openclaw-model-routing-guide): design, test, verify, and roll back model routing with evidence.
3. [Build an OpenClaw Setup You Can Understand and Repair](https://github.com/xbillwatsonx/openclaw-runbook-2-understandable-repairable-setup): map the system, record a known-good baseline, and practice one bounded safe change.

Start with the resource that matches your immediate need, or follow them in this order.

## License

Copyright © 2026 Bill Watson. All rights reserved.

Personal learning and internal operational use are permitted. Redistribution, resale, white-labeling, sublicensing, and using this protected material as the basis of an offering to others require prior written permission. The complete terms are in [LICENSE.md](LICENSE.md).
