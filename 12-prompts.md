# Prompt Pack: Copyable Prompts That Bridge You to Your Agent

Part of The OpenClaw Model Routing Guide + Companion Runbook. Identical to the prompt pack in [GUIDE.md](GUIDE.md) section 10. Teaching: [GUIDE.md](GUIDE.md) section 10.

**Version baseline.** Prompt behavior and command references were checked against OpenClaw 2026.9.4 on 2026-09-17. Recheck the named dated guide sections and installed help when your version changes.

Every prompt in this pack carries the same safety frame:

- It names the immutable, version-specific GUIDE.md address so the agent reads the same released text you reviewed.
- It names the exact package section to read, and tells the agent to read it before doing anything else. An agent that hasn't read the section will improvise; the prompt removes that option.
- It states the purpose, the safe behavior you expect, and a hard boundary: no action without your explicit approval of that exact action.

How to use one: copy the whole block from the code fence, paste it into a chat with your agent, and keep the files it names at hand. Answer the agent's questions, and stay in control of the approval boundary. The prompts are deliberately read-only in spirit: where a prompt can lead to a change, the change only happens after you approve the exact action.

## Prompt 1: Explain the guide to me in plain language

How to use: when the material feels dense and you want a guided, non-technical read before operating anything.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want a plain-language walkthrough of the whole guide before I touch anything on my system.

Before you do anything else, read GUIDE.md in that package, from the opening through section 9, including the glossary at the end.

Then walk me through it section by section. For each section: explain the idea in everyday words, define every OpenClaw-specific term the first time it comes up, give me a small concrete example, and finish with one comprehension check question so I can tell whether I followed you.

I expect you to stay read-only: no commands, no config changes, no model changes, nothing that touches my system. This is a reading and explanation session only.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 2: Help me inventory my setup safely

How to use: at [RUNBOOK.md](RUNBOOK.md) step 3, with the inventory worksheet open.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want to complete a safe inventory of my model routing setup, without exposing any secrets.

Before you do anything else, read section 3 of GUIDE.md, step 3 of RUNBOOK.md, and the 01-inventory-worksheet.md file in that package.

Then help me fill in every part of the inventory worksheet using only read-only commands. Show me each command before I run it, tell me what it reveals, and remind me what to record and what to leave out. Never ask me to paste secret values, and warn me before anything that could print credential details.

I expect you to stay read-only: only the commands the worksheet marks as read-only. If a step would need a write or a credential action, stop and tell me instead.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 3: Help me classify my work

How to use: at [RUNBOOK.md](RUNBOOK.md) step 4, listing your recurring tasks.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want to classify my recurring work so my routing decisions follow from my actual needs.

Before you do anything else, read section 4 of GUIDE.md and the 02-task-classification-worksheet.md file in that package.

Then help me list my recurring tasks and classify each one by difficulty, risk, value, privacy, and tool needs. Ask me questions until the worksheet is filled in. Do not suggest specific model names or rankings; this step is about my work, not about models.

I expect you to stay read-only: this is conversation and note-taking only, with no commands and no changes.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 4: Help me draft my routing policy

How to use: at [RUNBOOK.md](RUNBOOK.md) step 5, after inventory and classification are done.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want to write my routing policy on paper before I touch any configuration.

Before you do anything else, read section 5 and section 6 of GUIDE.md, the 03-routing-policy-template.md file, and the 05-fallback-retry-template.md file in that package.

Then help me draft my own routing policy: primary model, fallback order with a reason for each entry, specialist routes, auth preferences, session rules, and stop rules. Use my completed inventory and classification worksheets as input. Write it as a proposal only.

I expect you to stay read-only: drafting words, not changing configuration.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 5: Pressure-test my change proposal

How to use: at [RUNBOOK.md](RUNBOOK.md) steps 6 and 7, before anything is approved or applied.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I have a filled-in change proposal and I want it checked before anything runs.

Before you do anything else, read step 6 and step 7 of RUNBOOK.md and the 04-config-proposal-template.md file in that package.

Then review my change proposal against that procedure. Check that it is exactly one change, that the scope is named, that the baseline and rollback are recorded, that the verification plan names the command and the expected evidence, and that stop conditions exist. If anything is missing, show me what and stop. If it passes, walk me through the dry-run check in step 7 and show me the exact command before I run it.

I expect you to stay read-only until I approve the exact change: dry-run checks and read-only commands only.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 6: Apply my one approved change with me

How to use: at [RUNBOOK.md](RUNBOOK.md) step 8, with an approved proposal in hand.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I have approved exactly one change. I want to apply it and verify it, with you watching for anything unexpected.

Before you do anything else, read step 8 of RUNBOOK.md in that package, then read my approved change proposal.

Then run through step 8 with me: confirm the exact approved command matches my proposal word for word, ask me to confirm before it runs, and compare the output to the expected evidence in the proposal. If anything differs from what the proposal predicted, stop immediately and tell me. Do not improvise a fix.

I expect you to run only the exact approved change and the read-only verification commands named in the proposal, nothing else.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 7: Run matched-task tests with me

How to use: at [RUNBOOK.md](RUNBOOK.md) step 9, with the test plan and scorecard open.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want to compare candidate models on my real work with matched tasks and a shared scorecard.

Before you do anything else, read section 7 of GUIDE.md, the 06-test-plan.md file, and the 07-scorecard.md file in that package.

Then help me run the tests: same task and same inputs for every candidate, a fresh session for each run, the candidate pinned at session scope, and the same scorecard for every run. Tell me exactly what to type to pin each candidate in my test session, help me record the selected model and any fallback notice for each run, and help me score as I paste results back to you.

I expect you to change nothing outside my test chat sessions. A session-scope model pin or clear is a mutation: before either action, show me the prior selection and any explicit auth-profile pin, the exact command, the bounded test-session approval it is covered by, and the cleanup action. If no installed-version procedure can clear or restore the exact auth-profile pin, preserve that session, record `unsupported`, and use a fresh session. No configuration writes or other changes.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 8: Verify which route handled the work

How to use: at [RUNBOOK.md](RUNBOOK.md) step 10, after a change or after any run you want proof about.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want to verify the model route OpenClaw reports, inspect the intended credential route, and determine whether provider billing records are needed for final confirmation.

Before you do anything else, read section 8 of GUIDE.md and the 08-route-verification-checklist.md file in that package.

Then walk me through the route verification checklist with read-only commands only. Help me separate what is configured, what my session is pinned to, the model and fallback state OpenClaw reports, and what the auth-routing evidence can and cannot prove. If the exact charged account matters, tell me that provider usage or billing records are required. Remind me what to redact before I share any output with anyone.

I expect you to stay read-only: checks and reading only, no probes, no writes.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 9: Help me decide: keep, revise, or roll back

How to use: at [RUNBOOK.md](RUNBOOK.md) step 11, with test and verification evidence recorded.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I made one change and ran my tests. I need help deciding to keep, revise, or roll back, based only on recorded evidence.

Before you do anything else, read step 11 and step 12 of RUNBOOK.md and the 09-rollback-checklist.md file in that package.

Then compare my scorecard and my route verification evidence to the success criteria in my change proposal. Give me a clear keep, revise, or roll back recommendation with the evidence behind it. If we roll back, follow the rollback checklist exactly, one step at a time, and verify the baseline afterward.

I expect you to stay read-only during the decision. Any rollback step needs my explicit approval before it runs.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 10: Something went wrong with a route

How to use: when a run fails, a surprise model answers, or a bill doesn't match. With evidence at hand if you have it.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: a route did something I didn't expect. I want to classify the failure from evidence, not guesses.

Before you do anything else, read the 10-troubleshooting-decision-tree.md file and step 13 of RUNBOOK.md in that package.

Then work the decision tree with me. Ask for the exact error or notice I saw, collect the evidence each branch names using read-only commands only, and tell me which failure class the evidence points to and what evidence comes next. Respect the stop points: when the tree says stop, stop.

I expect you to stay read-only: no repairs, no fixes, no config changes, until we have a classified failure and an approved proposal.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Prompt 11: Time for a maintenance review

How to use: on a version change, a provider change, or your chosen review cadence.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. Confirm that you read every named file before continuing.

Purpose: I want to run a maintenance review of my routing policy.

Before you do anything else, read section 9 of GUIDE.md and the 11-maintenance-checklist.md file in that package.

Then help me run the review: check my OpenClaw version against the baseline recorded in my policy, recheck the version-sensitive items on the checklist, note any catalog or price changes that affect my routing, and update my policy document's changelog. Anything that needs a change becomes a new one-change proposal for another day, not an edit today.

I expect you to stay read-only: checks and note-taking only.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## Release address note

Every prompt uses the immutable v0.1.4 GUIDE.md address: https://raw.githubusercontent.com/xbillwatsonx/openclaw-model-routing-guide/v0.1.4/GUIDE.md. The same address appears in [GUIDE.md](GUIDE.md) section 10, [README.md](README.md), and [15-changelog.md](15-changelog.md). See [13-source-notes.md](13-source-notes.md) for the full source and exclusion record.
