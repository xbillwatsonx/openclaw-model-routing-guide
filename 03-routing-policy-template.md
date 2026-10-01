# Routing Policy Template: My Model Routing Policy

Part of The OpenClaw Model Routing Guide + Companion Runbook. Fill this in at [RUNBOOK.md](RUNBOOK.md) step 5, from your completed [01-inventory-worksheet.md](01-inventory-worksheet.md) and [02-task-classification-worksheet.md](02-task-classification-worksheet.md). Teaching: [GUIDE.md](GUIDE.md) sections 5 and 6.

**What this document is.** Your routing policy is a written record, not a config file. You write it before you touch configuration, and every later change traces back to a line in it. This is a template for your own decisions; nothing here is pre-filled, because your setup should reflect your inventory and your work, not somebody else's.

**Version note.** Every configuration key named below was checked against OpenClaw 2026.9.4 on 2026-09-17.

## Header

| Field | Your value |
| --- | --- |
| Policy version and date | |
| OpenClaw version at writing (from `openclaw --version`) | |
| Scope of this policy (which agent or agents it covers) | |

## 1. Primary model

| Field | Your value |
| --- | --- |
| Primary model (the configured starting point, `agents.defaults.model.primary`) | |
| Why this one (written reason, from inventory and classification) | |

If the reason reads "everyone says it's good", it isn't a reason yet. Either test it properly (via [06-test-plan.md](06-test-plan.md) once a change is live) or write what actually convinced you.

## 2. Fallback order

The ordered list at `agents.defaults.model.fallbacks`, tried in order. One row per entry, a written reason for each. The fallback and retry design details live in [05-fallback-retry-template.md](05-fallback-retry-template.md); this section is the summary.

| Order | Model | Why it's here (what failure it covers, what it costs me, what it must not do) |
| --- | --- | --- |
| 1 (primary) | | |
| 2 | | |
| 3 | | |

Remember what fallback is not: it does not guarantee completion, low cost, privacy, or correctness. It's a bounded, ordered set of attempts. Design it like you mean that.

## 3. Specialist routes

Each specialist route is its own policy decision, separate from the text-model chain. This version supports read-only inventory and policy design only, not modifying or claiming success/recovery for specialist, media, per-agent, cron, or subagent routes. Treat `not set`, inferred, or unverified as an unresolved destination, not a safe default.

| Route | Config key | My value | Reason (or "not set, on defaults, because...") |
| --- | --- | --- | --- |
| Utility model (short internal tasks) | `agents.defaults.utilityModel` | | |
| Image model (when the primary can't accept images) | `agents.defaults.imageModel` | | |
| Image fallbacks (parallel list, same CLI shape as text fallbacks) | `agents.defaults.imageModel.fallbacks` | | |
| PDF model (backs the PDF tool; falls back to image model, then session/default) | `agents.defaults.pdfModel` | | |
| Media models (image, music, video generation tools) | `agents.defaults.mediaModels` | | |
| Subagent model (helper sessions inherit the caller's model unless this overrides) | `agents.defaults.subagents.model` | | |

## 3A. Mandatory provider-boundary policy

Complete one row for the text route and each specialist route. `Unset` is not an approval for sensitive inputs.

| Route | Local/remote execution | Approved providers/destinations | Prohibited providers/destinations | Allowed data classes | Retention/training assumption | Evidence source, version, date | Can fallback/profile rotation broaden the boundary? | Action if destination is unverified |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Text primary and fallbacks | | | | | | | | |
| Utility | | | | | | | | |
| Image | | | | | | | | |
| PDF | | | | | | | | |
| Media | | | | | | | | |
| Subagent | | | | | | | | |
| Per-agent / cron route | | | | | | | | |

For every eligible text fallback, auth profile, inferred default, and explicit override, record a provider-boundary approval for the data class or stop, sanitize, or use an approved local route. For sovereignty-sensitive work, select an explicit route or document a decision not to use the relevant tool. Defaults are not sufficient until their possible destinations are enumerated. A specialist/scoped route with no authoritative execution evidence is `unsupported`: do not modify it or claim success/recovery; use a future route-specific procedure.

## 4. Model-selection policy (override allowlist)

| Field | Your value |
| --- | --- |
| `agents.defaults.modelPolicy.allow` | (the list, or "omitted/empty: any model may be explicitly selected") |
| Why this stance | |

Naming guard, worth writing into your own policy so future-you doesn't trip: the allowlist is `agents.defaults.modelPolicy.allow` and it governs explicit selections. The model settings map (`agents.defaults.models`) stores aliases and per-model settings and is never the allowlist. Adding an alias never restricts anything. An unrestricted allowlist doesn't make an unknown provider or an unsupported runtime usable.

## 5. Authentication preferences

For each provider with more than one auth profile:

| Provider | Profiles I have (ids stay private) | Which I prefer to pay, and why | How preference is expressed (stored order override, `auth.order` config, or documented rotation) |
| --- | --- | --- | --- |
| | | | |

Reminder: the model reference doesn't decide who pays. If one provider has both a subscription profile and an API-key profile, the same model can bill to either; explicit order controls the preference, not a guarantee of the exact profile charged after temporary rotation.

## 6. Session rules

| Question | My rule |
| --- | --- |
| Who may pin a model in a session (and with what scope: `-s` session, `-a` agent default, `-g` global default)? | |
| Are bounded test-session pins covered by one written testing approval? State the session boundary, candidates, maximum runs, and cleanup rule. | |
| How will an explicit auth-profile pin be recorded separately from the model selection? | If an installed-version procedure to clear or restore that exact pin is unavailable, state `unsupported`, preserve the session, and use a fresh session. |
| When do session selections end? | Use a fresh unpinned session for configured-route validation. Any session mutation is separately approved; do not assume `/model default -s` clears an auth-profile pin. |
| Changing the configured primary doesn't rewrite existing session pins; who is responsible for noticing a stale pin? | |

## 6A. Optional semantic-routing policy

Leave this section blank if routing is deterministic only. A semantic classifier is a separately tested optional layer, not the baseline.

| Control | My policy |
| --- | --- |
| Exact opt-in agent/session scope | |
| Allowed executor models | |
| Confidence floor and abstention rule | |
| One-attempt deadline | experiment-specific; evidence reference: |
| Daily classifier and override budgets | |
| Kill switch and rollback action | |
| Stable response-model freeze | |
| Metadata-only logs and run-id correlation | |
| Normal route on classifier timeout, error, drift, or abstention | no override / other recorded rule |
| Local workload evidence required before activation | |
| Cost claim status | confirmed comparable evidence / inconclusive |
| Raw under-classification versus accepted under-route | Record raw under-classification as a warning; treat an accepted under-route as an incident requiring immediate stop | |

## 7. Stop rules

The boundaries no configuration key draws for you. Write them before you need them.

| Trigger | My stop rule |
| --- | --- |
| After how many failed or weak attempts on the same approach do I stop and record evidence? | |
| What evidence do I collect at the stop point (for the troubleshooting tree)? | |
| When do I escalate or change strategy instead of retrying? | |

## 8. Review triggers

| Trigger | My response |
| --- | --- |
| OpenClaw version changed | Recheck the version-sensitive list in [11-maintenance-checklist.md](11-maintenance-checklist.md) |
| Provider catalog, price, or access terms changed | |
| Credentials added or removed | |
| New recurring task type appeared | |
| An incident happened | |
| My chosen calendar cadence came around (pick one you'll keep) | |

## 9. Change history

One line per accepted change, newest last. Date, what changed, the decision (kept/revised/rolled back), and the proposal it traces to.

| Date | Change | Decision | Proposal reference |
| --- | --- | --- | --- |
| | | | |

## Done?

Next: [04-config-proposal-template.md](04-config-proposal-template.md) for your first one-change proposal, at [RUNBOOK.md](RUNBOOK.md) step 6.
