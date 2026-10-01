# Troubleshooting Decision Tree: Classify the Failure Before You React

Part of The OpenClaw Model Routing Guide + Companion Runbook. Use at [RUNBOOK.md](RUNBOOK.md) step 13. All behavior classes below were checked against OpenClaw 2026.9.4 on 2026-09-17.

## First, collect evidence

Record exact error or notice, timestamp, conversation type (direct, group, or channel), selected model and fallback state from `/status`, session selection and auth candidates from `/model status`, and relevant sanitized `openclaw models auth list` or `openclaw models status --check` results. `--check` exit 0 does not prove a request succeeds. Evidence collection is read-only. Any repair, session pin, catalog refresh, or configuration command is a mutation requiring a separate proposal and explicit approval.

## Classify one branch

| Evidence class | Expected fallback behavior | Next evidence | Stop point |
| --- | --- | --- | --- |
| Rate limit | Bounded recovery, then eligible auth/profile or model fallback | Auth cooldown reason, `/status` | Record cooldown and result; change policy only by proposal |
| Authentication failure | No transient retry budget; eligible profile rotation then fallback | Auth health and `--check` | Identify affected profile(s); credential renewal is outside this package |
| Billing disable or exhausted usage window | Routes around credential or goes to eligible fallback | Auth disable reason and `/status` | Exact-charge status is `unconfirmed` unless provider records confirm it |
| Overloaded provider | Eligible fallback may occur after bounded recovery | Exact error and `/status` | Record model and reason; otherwise `unclassified` |
| Timeout-shaped failure | Eligible fallback may occur after bounded recovery | Exact error, timestamp, `/status` | Record result; otherwise `unclassified` |
| Request-size ceiling | Eligible fallback may occur | Exact error, input-size description, `/status` | Stop sending sensitive or oversized input; redesign by proposal |
| Context overflow | Does not advance fallback | Exact error and input-size description | Stop; reduce context or propose another route |
| Explicit abort | Does not advance fallback | Cancellation evidence | Stop; do not retry automatically |
| Final provider refusal | Does not advance fallback | Exact refusal text | Stop; route redesign needs proposal |
| Model not found or eligible 404 | Eligible fallback may occur | `models list`, then `--all` if needed | Identify stale, hidden, deprecated, or absent reference; otherwise `unclassified` |
| Unrecognized/transient retry exhaustion | Documented eligible fallback behavior may apply | Exact error, retry indication, `/status` | Record `unclassified` if docs do not establish a class |
| Strict session selection failure | No configured fallback for the pinned selection | `/model status` | Confirm intended pin; set or clear needs session-mutation approval |
| Different model answered | Turn-local fallback may explain it | `/status`, config and auth evidence | In group/channel conversations, missing notice is not evidence because notices are suppressed |
| Billing-path surprise | Pins, preference, health, eligibility do not prove charge | Checklist 08 plus provider billing records if needed | Mark `unconfirmed` until provider billing records confirm charge |
| Override allowlist refusal | Selection is refused, not repaired by an alias | `config get agents.defaults.modelPolicy.allow` | Change only by proposal |
| Catalog or picker surprise | Browsing differs from discovery and refresh | Catalog map below | Do not refresh as reflex; refresh/discovery are excluded from this package |
| Semantic classifier under-route (raw or accepted) | Shadow-mode or kill-switch evidence, classifier prediction versus human label | Abstention blocked it (raw) or override ran (accepted) | Raw under-classification: review policy, tier definitions, and task boundary clarity before scope expansion. Accepted under-route: stop, verify kill switch and confidence floor, and do not expand routing without a new reviewed plan and explicit authorization. |
| Specialist, media, per-agent, cron, or subagent route surprise | No authoritative execution evidence or recovery in this package | Read-only inventory and provider-boundary record | Do not send sensitive input to unset/inferred/unverified destination; use a future route-specific procedure |
| Legacy credentials or unknown provider | No route conclusion until classified | Sanitized doctor finding, inventory | Repair separately proposed and approved |

## Catalog and inventory map

| Need | Surface | Does it discover or mutate? |
| --- | --- | --- |
| Browse picker/catalog | `/model`, `/models`, picker | No provider discovery |
| Read published inventory | `openclaw models list` | No discovery or mutation |
| Read full catalog view | `openclaw models list --all` | No discovery or mutation |
| Filter published inventory | `openclaw models list --provider <id>` | No discovery or mutation |
| Refresh provider discovery before listing | `openclaw models list --refresh` | Excluded: discovery/update has no rollback in this package |
| Refresh hosted catalog | `openclaw models refresh` | Excluded: no rollback here; a restart is required before activation |

## Universal stop rules

1. If evidence does not support a branch, record `unclassified` and stop.
2. Do not infer destination, exact charge, profile use, or fallback from a model name, preference, or missing notice.
3. Do not hand-edit internal cooldown, disable, or usage-window state.
4. Two unexplained changes in play means stop all mutations and resolve state read-only.
5. Every eligible profile, fallback, inferred default, and override must be approved for the input data class. Otherwise stop, sanitize, or use local processing.
