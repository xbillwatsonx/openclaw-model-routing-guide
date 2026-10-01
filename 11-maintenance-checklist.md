# Maintenance and Version-Review Checklist

Part of The OpenClaw Model Routing Guide + Companion Runbook. Use on any trigger below, at the review cadence recorded in your routing policy, and with Prompt 11 from [12-prompts.md](12-prompts.md). Teaching: [GUIDE.md](GUIDE.md) section 9.

**Why this exists.** Provider catalogs, prices, and capabilities change independently of this package and independently of your OpenClaw version. A routing policy written once and never revisited slowly becomes fiction. Most reviews end with "no change needed"; writing that down, with the date, is the point.

**Version note.** Commands below were checked against OpenClaw 2026.9.4 on 2026-09-17.

## 1. Review triggers

Check any that apply; the review is due on any one of them:

- [ ] My OpenClaw version changed.
- [ ] A provider changed its catalog, prices, or access terms.
- [ ] I added or removed credentials.
- [ ] My work changed shape: new recurring tasks, new tool needs, new specialist routes in play.
- [ ] An incident happened and the troubleshooting tree produced a finding.
- [ ] My chosen calendar cadence came around. (Pick one you'll keep: monthly, quarterly, whatever fits. A review you do beats a perfect schedule you ignore.)

Review date and trigger(s):

## 2. Version recheck (when OpenClaw changed)

Use the exact version-matched installed OpenClaw documentation and `--help` for every command or behavior recheck. If no authoritative evidence exists for a route, record `unknown` or `unverified`, do not infer it.

First:

```bash
openclaw --version
```

- Where it runs: terminal, on the gateway machine. Type: read-only. Reveals: the installed version.

Compare against the version your policy was written for. If it differs, recheck the version-sensitive facts below against the documentation installed with your copy, because these are exactly the facts this package dates throughout:

| # | Version-sensitive fact to recheck | Where it lives in this package | Evidence source / installed version / date | Rechecked? |
| --- | --- | --- | --- | --- |
| 1 | Exact configuration keys and schema | [GUIDE.md](GUIDE.md) section 2; [01-inventory-worksheet.md](01-inventory-worksheet.md) | / / | [ ] |
| 2 | Model-selection scope behavior | [GUIDE.md](GUIDE.md) section 2.4 | / / | [ ] |
| 3 | Strict session selection versus configured fallback behavior | [GUIDE.md](GUIDE.md) section 2.3 | / / | [ ] |
| 4 | Auth-profile ordering and storage language | [GUIDE.md](GUIDE.md) sections 2.5 and 2.6 | / / | [ ] |
| 5 | Retry counts and cooldown behavior | [GUIDE.md](GUIDE.md) section 6.2 | / / | [ ] |
| 6 | Subagent, specialist, per-agent, and cron route boundaries | [GUIDE.md](GUIDE.md) section 5.2 | / / | [ ] |
| 7 | Model catalog and allowlist semantics | [GUIDE.md](GUIDE.md) sections 2.8 and 2.9 | / / | [ ] |
| 8 | CLI names, flags, and output shape | every command card in [GUIDE.md](GUIDE.md) and [RUNBOOK.md](RUNBOOK.md) | / / | [ ] |

Any fact that drifted: update your policy document's expectations, not just your memory of this package.

## 3. Catalog and price change awareness

Provider catalogs and prices move on their own schedule. Check what moved:

```bash
openclaw models list
```

- Type: read-only. Reveals: your current published inventory. Compare against your inventory worksheet and policy; note added, removed, deprecated, or renamed entries that touch your route.

Optional, public-metadata scan (checked against OpenClaw 2026.9.4, 2026-09-17):

```bash
openclaw models scan --no-probe
```

- Type: read-only (metadata only, no key required). Reveals: OpenRouter's public free catalog. The probing and set-default variants of scan require an OpenRouter key and live requests; this package doesn't use them.

Catalog refresh and provider discovery are excluded from this package's maintenance operation. They lack a rollback procedure here, and hosted catalog refresh requires a restart before activation. Do not run either as a troubleshooting or maintenance reflex. Record the concern, preserve pre/post evidence, and use a separately approved non-rollbackable procedure only after its own stop boundary is documented.

Price change notes for my route:

## 4. Route health snapshot

```bash
openclaw models status --check
```

- Type: read-only. Reveals: exit code 0 clean, 1 issue, 2 expiring credential. Record it.

```bash
openclaw doctor --lint
```

- Type: read-only. Reveals: health findings without repairs. Record anything that touches your model route. Findings can include local paths; sanitize before sharing.

## 5. Policy document review

- [ ] Primary still matches the written reason?
- [ ] Fallback order still matches the written reasons, entry by entry?
- [ ] Specialist, media, per-agent, cron, and subagent routes still have an approved provider boundary, or remain explicitly unsupported and unmodified?
- [ ] Auth preferences still what the policy says, especially where one provider has more than one profile?
- [ ] Stop rules still the ones I'd actually follow?
- [ ] Change history complete and consistent with the proposals and scorecards?

## 6. Decisions from this review

Anything that needs a change becomes a new one-change proposal for another day ([04-config-proposal-template.md](04-config-proposal-template.md)), not an edit during the review.

| Outcome | Notes |
| --- | --- |
| No change needed (write it down anyway) | |
| New proposal(s) queued | |
| Worksheet(s) to redo | |

Policy changelog line added (date, review outcome):

## What maintenance is not

Maintaining a routing policy is not updating OpenClaw; software update and migration is a separate procedure this package doesn't teach. It's also not a redesign invitation: most reviews end with "no change needed", recorded with the date. That record proves the policy was considered, not forgotten.
