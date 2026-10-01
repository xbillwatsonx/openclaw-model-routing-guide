# Fallback and Retry Policy Template: Order, Boundaries, and Stop Rules

Part of The OpenClaw Model Routing Guide + Companion Runbook. Fill this in at [RUNBOOK.md](RUNBOOK.md) step 5, alongside [03-routing-policy-template.md](03-routing-policy-template.md). Teaching: [GUIDE.md](GUIDE.md) section 6.

**What this document is.** Your deliberate design for the text-model fallback chain, your expectations about retries, and your own stop rules. It slots into section 2 and section 7 of your routing policy.

**Version note.** Every count, duration, and key below was checked against OpenClaw 2026.9.4 on 2026-09-17. These are exactly the facts most likely to change between versions; recheck them against your installed docs when your version moves, using [11-maintenance-checklist.md](11-maintenance-checklist.md).

**What this document may never contain.** Guarantee language. A fallback chain does not guarantee completion, low cost, privacy, or correctness. If you catch yourself writing "this ensures" or "this guarantees", rewrite it as "this is designed to".

## 1. What fallback covers, and what it doesn't

Fill in your understanding, in your words, before designing the order. This is a comprehension check, not a quiz; [GUIDE.md](GUIDE.md) section 6.1 has the teaching.

Failures that advance fallback (checked against OpenClaw 2026.9.4, 2026-09-17):

- authentication failures
- rate limits
- overloaded providers
- timeout-shaped failures
- billing disables
- model not found, including an eligible 404
- request-size ceiling hits

Failures that do not advance fallback:

- context overflow
- explicit aborts
- final provider refusals (a final refusal ends the turn)

My words:

Answer:

## 2. Fallback order, with reasons

The ordered list at `agents.defaults.model.fallbacks`. One written reason per entry. Remember the rotation rule: inside a provider, OpenClaw rotates auth profiles before moving to the next eligible fallback model.

| Order | Model | Why this entry is here | What I expect it to cost me | What I must never silently accept from it |
| --- | --- | --- | --- | --- |
| 1 (primary) | | | | |
| 2 | | | | |
| 3 | | | | |

The last column is the honesty column: for each entry, name the outcome you'd rather stop and notice than silently accept. That's how you turn a fallback chain from an autopilot into a designed route.

## 3. Retry expectations

What OpenClaw already does before falling back, checked against OpenClaw 2026.9.4, 2026-09-17, so you can write expectations instead of guesses:

- Bounded same-model recovery runs first: it continues the existing transcript, preserves completed work, and instructs inspection of interrupted actions. A retry indicator is visible and the run stays cancellable.
- Rate limits receive up to 10 total attempts. Other transient failures allow eight retries within a 90-second window.
- Backoff starts near one second, grows exponentially with jitter, and caps at 30 seconds. Provider retry-after hints set minimum waits.
- Billing failures, authentication errors, and provider refusals don't use the transient retry budget.
- An exhausted subscription usage window goes directly to eligible fallback.
- Cooldowns can follow: regular cooldowns scale 30 seconds, 1 minute, 5 minutes; the rate-limit bucket is broader than plain 429; model-scoped cooldowns still allow sibling models; billing disables use an initial ten-minute window.

My expectation for my own work, in my words (what I expect to see when a run hits each of these):

Answer:

## 4. Knobs: considered and consciously decided

Each knob: considered (yes/no), and the written decision. Mention-only facts, checked against OpenClaw 2026.9.4, 2026-09-17:

| Knob | What it does | My decision (change it / leave it, and why) |
| --- | --- | --- |
| `retry.provider.maxRetries` | Overrides the recovery budget per session setting; 0 disables retries; rate limits remain capped. It is an embedded session setting, not an openclaw.json key. | |
| `OPENCLAW_FALLBACK_SKIP_TTL_MS` | Opt-in cache that skips recently failed authentication for a while. | |
| `OPENCLAW_SDK_RETRY_MAX_WAIT_SECONDS` | Caps SDK retry-after waits, at a 60-second default. | |

If any decision is "change it", that change is a one-change proposal through [04-config-proposal-template.md](04-config-proposal-template.md), never a quiet edit while filling in this template.

One written guard: cooldowns, disables, and usage-window failures are things I describe by their operational effect. I never hand-edit the internal state behind them.

Confirmed:

## 5. Stop rules

OpenClaw's budgets are bounded, but they are not workflow-level attempt limits. These rules are yours, written before you need them:

| Trigger | My stop rule | Evidence I collect at the stop point |
| --- | --- | --- |
| Same approach failed or came back weak N times in a row | (pick your N and write it) | |
| A fallback notice appeared during high-risk work | | |
| The same model entered cooldown twice in one session | | |
| A bill or usage pattern looks wrong | | |

Escalation path: when I stop, who or what do I escalate to (a person, a troubleshooting-tree session, a new proposal), and what do I bring with me (this document, the scorecards, the raw error evidence)?

Answer:

## 6. Honest summary for the routing policy

Three sentences, maximum, to paste into [03-routing-policy-template.md](03-routing-policy-template.md) section 2:

Answer:

Reminder of what this design does not promise: completion, low cost, privacy, or correctness. It promises only that the order was chosen on purpose, with reasons, and that you'll know where to look when something behaves unexpectedly.
