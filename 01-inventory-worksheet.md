# Inventory Worksheet: Providers, Models, Auth, and Billing Paths

Part of The OpenClaw Model Routing Guide + Companion Runbook. Fill this in at [RUNBOOK.md](RUNBOOK.md) step 3, using the read-only commands from [GUIDE.md](GUIDE.md) section 3. Checked against OpenClaw 2026.9.4 on 2026-09-17.

**How to fill this in.** Run each command, read the output, and write down only what a field below asks for. If a field does not apply, write `none`, `not set`, `unknown`, or `unconfirmed` as appropriate. Do not guess from a blank.

## Sanitization rules (read before you write anything)

1. **No secret values, ever.** Not keys, not tokens, not passwords, not fragments of any of them. The commands here don't print secret material, and you shouldn't go looking for it.
2. **Never paste raw `models status` output anywhere public.** It can contain credential metadata and redacted credential fragments. Record the model route facts here; share only this worksheet, after redaction, if you share anything at all.
3. **Profile ids are private.** They can contain email addresses (`provider:email` is the documented id format for OAuth profiles). Keep this worksheet in private notes. If you post an excerpt anywhere, redact emails and profile ids first.
4. **Screenshots follow the same rules.** Redact emails and profile ids before sharing.
5. **If a value isn't asked for below, don't record it.** The worksheet is the boundary.

## 1. Installed version

| Field | Your value |
| --- | --- |
| `openclaw --version` output | |
| Date you recorded it | |

## 2. Providers and models available to you

From `openclaw models list` (read-only; shows your published inventory). Use `openclaw models list --all` (read-only) only if something you expected is missing: it also shows deprecated or disabled catalog rows that the default view hides.

| Provider | Models you might actually use | Notes (capabilities you care about) |
| --- | --- | --- |
| | | |
| | | |
| | | |

Questions to answer in your own words below the table:

- Which providers do I have working credentials for today? (Cross-check with section 4.)
- Did any provider show up in the list that surprised you? Why?

## 3. Current model route (text models)

From `openclaw models status` and `openclaw models fallbacks list` (read-only).

| Field | Your value |
| --- | --- |
| Configured primary model | |
| Fallback chain, in order | 1. 2. 3. (add more as needed) |
| Aliases (`openclaw models aliases list`) | |
| Override allowlist (`openclaw config get agents.defaults.modelPolicy.allow`) | value, or "omitted/empty = any model may be explicitly selected" |

Remember: the allowlist governs explicit selections. Omitted or empty means any model may be selected, subject to provider availability, runtime compatibility, and authentication (checked against OpenClaw 2026.9.4, 2026-09-17).

## 4. Authentication profiles

From `openclaw models auth list` (read-only; prints profile ids and health, not secret material). Record ids and health only. The "What it's for" column comes from your own knowledge of how you set things up (for example: subscription sign-in versus API key); the command doesn't label that for you.

| Provider | Profile id (private) | Health | Purpose: known / unknown / unconfirmed | Cooldown/disable reason shown, if any |
| --- | --- | --- | --- | --- |
| | | | | |
| | | | | |
| | | | | |

Note: cooldown and disable entries in the auth list include their reason and recovery action (checked against OpenClaw 2026.9.4, 2026-09-17). Record the reason if one appears; it's useful context later.

## 5. Profile order

From `openclaw models auth order get --provider <id>` (read-only; shows that provider's stored order override, if one exists). The `--provider` option is required; run the command once per provider with more than one profile.

| Field | Your value |
| --- | --- |
| Stored order override, per provider checked | (the override, or "none") |
| For each provider with multiple profiles: which one do I expect to be preferred, and why? | |

## 6. Billing path notes

For each provider, write down the billing path you intend: which credential or account you expect to pay when a model under that provider runs. Later, [08-route-verification-checklist.md](08-route-verification-checklist.md) checks OpenClaw's routing evidence. If the exact charge matters, provider usage or billing records provide the final confirmation.

| Provider | Model(s) you use | Intended billing path | OpenClaw evidence status | Exact-charge status | Provider-side record location, if needed |
| --- | --- | --- | --- | --- | --- |
| | | | pinned / preferred / eligible / unknown | confirmed / unconfirmed / not needed | |
| | | | | | |

Reminder: a model reference alone doesn't prove which credential paid. If one provider has both a subscription profile and an API-key profile, the same model can bill to either path; explicit auth order controls the preference, but temporary rotation can use another eligible profile (checked against OpenClaw 2026.9.4, 2026-09-17).

## 7. Specialist and scoped routes: read-only inventory only

From `openclaw config get <key>` for each key (read-only; routing keys only).

| Route | Config key | Your value | Or "not set" |
| --- | --- | --- | --- |
| Utility model | `agents.defaults.utilityModel` | | |
| Image model | `agents.defaults.imageModel` | | |
| Image fallbacks | `agents.defaults.imageModel.fallbacks` | | |
| PDF model | `agents.defaults.pdfModel` | | |
| Media models | `agents.defaults.mediaModels` | | |
| Subagent model | `agents.defaults.subagents.model` | | |

"Not set", inferred, and unverified are real answers. They are unresolved destinations, not approval to send data. This package has no authoritative execution evidence or recovery procedure for modifying specialist, per-agent, cron, or subagent routes. Inventory and policy design only; direct a future change to a route-specific procedure.

## 8. Provider-boundary and scoped-route inventory

Complete this before any test input leaves your system. Every eligible text fallback, auth profile, inferred default, and explicit override must be approved for the proposed data class. An unset, inferred, or unverified specialist/scoped route is an unresolved destination, not an approved destination. Stop, sanitize, or use an approved local route. For sovereignty-sensitive work, use an explicit route or record the decision not to use that tool.

| Route or scope | Local or remote execution | Approved providers/destinations | Prohibited providers/destinations | Allowed data classes | Retention/training assumption | Evidence source, version, date | Can fallback or profile rotation broaden the boundary? | Destination verified with a non-sensitive canary? |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Text primary, each fallback, each eligible auth profile, and each override | | | | | | | | |
| Utility | | | | | | | | |
| Image | | | | | | | | |
| PDF | | | | | | | | |
| Media: image / music / video | | | | | | | | |
| Native subagent | | | | | | | | |
| Per-agent override | | | | | | | | |
| Cron job payload | | | | | | | | |

If installed OpenClaw docs or non-sensitive route evidence cannot establish a destination, write `unverified`; do not send sensitive inputs to that route. This worksheet cannot claim specialist/scoped success or recovery.

## 9. Scoped-route inventory

Inventory each per-agent and cron route in scope with a read-only procedure you have verified for your installed version. If no verified procedure is available, write `unsupported` and do not infer behavior from defaults. Modification is out of scope for this package version; a future route-specific procedure must establish execution evidence and recovery before any change.

| Scope identifier kept private | Route source/key or payload field | Primary/fallback state | Provider-boundary row | Verification procedure or `unsupported` | Evidence source, version, date |
| --- | --- | --- | --- | --- | --- |
| | | | | | |
| | | | | | |


## 10. Health snapshot

| Field | Your value |
| --- | --- |
| `openclaw models status --check` exit code (0 clean, 1 issue, 2 expiring credential) | |
| `openclaw config validate` result | |
| `openclaw doctor --lint` findings that touch your model route (optional) | |

## 11. What I deliberately did not record

List anything you noticed but chose not to write down (credential details, health specifics you don't need, deprecated models you'll never use). This field exists to make the boundary deliberate.

| |
| --- |
| |

## Done?

Next: [02-task-classification-worksheet.md](02-task-classification-worksheet.md) at [RUNBOOK.md](RUNBOOK.md) step 4. If this worksheet showed zero auth profiles, stop and see [RUNBOOK.md](RUNBOOK.md) step 3's stop condition first.
