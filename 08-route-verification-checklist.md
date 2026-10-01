# Route-Verification Checklist: Reported Model Route and Intended/Preferred/Eligible Routing Evidence

Part of The OpenClaw Model Routing Guide + Companion Runbook. Use at [RUNBOOK.md](RUNBOOK.md) step 10, after any change, and any time a run feels off. Teaching: [GUIDE.md](GUIDE.md) section 8.

**What this checklist answers.** It separates three questions people tend to collapse into one: what route is configured, what model and fallback state OpenClaw reports for the session, and the intended/preferred/eligible routing evidence for credentials. A model reference, profile order, or session profile pin alone does not always prove which credential was charged after temporary rotation. Provider usage or billing records are the authoritative confirmation when the exact charge matters.

**Version note.** Every command and behavior below was checked against OpenClaw 2026.9.4 on 2026-09-17. All commands here are read-only. Terminal commands run on the machine that hosts your OpenClaw gateway; chat commands run inside the session you're checking.

## Sanitization rules (apply to every step below)

1. `openclaw models status` output can contain credential metadata and redacted credential fragments. Never paste raw output into a public chat, issue, or forum.
2. Profile ids can contain email addresses. Treat every id as private; redact before sharing anything.
3. Screenshots of chat commands follow the same rule: redact emails and profile ids.
4. Record only what a step asks for.

## Check 1: What is configured

```bash
openclaw models status
```

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the configured primary, the fallback chain, and an authentication overview. Its sections answer different questions; read them separately.
- Record: the configured primary and fallbacks, and whether they match your routing policy document. `models status --check` exit 0 is not proof that a future request succeeds.

Important boundary: the CLI status command explains configured defaults. It does not inspect a chat session's override (checked against OpenClaw 2026.9.4, 2026-09-17). If you're asking "what is my session doing?", this command is the wrong tool; check 3 answers that.

Configured matches my policy: yes / no. If no, what differs:

## Check 2: Auth health and order

```bash
openclaw models auth list
```

- Type: read-only. Reveals: profile ids and health, without printing secret material. Cooldown and disable entries include their reason and recovery action.
- Record: any cooldown or disable entries with their reasons.

```bash
openclaw models auth order get --provider <id>
```

- Type: read-only. Reveals: the stored order override for the provider you name, if one exists.
- Record: the override, or "none".

Read these as separate facts: a stored profile being present doesn't prove it's currently usable, and the status output separates credential sources, stored profile health, model route issues, and runtime auth. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

Auth facts recorded:

## Check 3: What the session actually used

Run these inside the chat session the work happened in:

- `/model status`: shows the session selection and the auth candidates per provider.
- `/status`: shows the selected model and, when fallback state differs, the active fallback model and the reason.

- Type: chat command, session inspection. Sanitize screenshots before sharing.
- Record: the session's selected model, and whether a fallback was active, with its reason.

Fallback notices (Model Fallback, Model Fallback cleared) are delivered once per state change outside group and channel conversations. In a group or channel, absence of a notice is expected and is not route evidence. Record the conversation type and use `/status` instead (checked against OpenClaw 2026.9.4, 2026-09-17).

Session evidence recorded:

## Check 4: What intended/preferred/eligible routing evidence establishes

Put checks 2 and 3 together and answer, in writing:

| Question | Your answer |
| --- | --- |
| Which model handled the run? | |
| Which auth profile was pinned, preferred, or eligible for that provider? | |
| Which other profiles were eligible, in cooldown, or disabled? | |
| Does the intended/preferred/eligible credential route match my routing policy? | yes / no |
| If no: is it a rotation I accept or a surprise to investigate? | |

Reminder from the guide: if one provider has both a subscription profile and an API-key profile, the same model can bill to either; explicit auth order controls the preference (checked against OpenClaw 2026.9.4, 2026-09-17). A temporary failure can still rotate away from that preference without replacing a persisted profile pin. These OpenClaw checks explain routing intent and eligibility, not necessarily the final provider charge.

If the exact charged account matters, use the provider's usage or billing records and compare the request time and model. Provider interfaces vary and are outside this package's command procedure.

## Optional check: semantic decision correlation

When an approved semantic-routing experiment is in scope, verify four separate facts instead of treating the classifier answer as proof:

1. The stable response-model field matches the frozen version. Ignore the per-request decision id for version checking.
2. The decision log contains only approved metadata and a run id.
3. The executor route log has exactly one matching run id and the expected provider/model.
4. In shadow mode, no override was returned. On classifier failure or abstention, the normal deterministic route remained in control.

Stop on model drift, missing or duplicate correlation, prompt or credential data in logs, an unexpected shadow override, or any executor outside the allowlist.

## Check 5: The verdict line

| Field | Your value |
| --- | --- |
| Date | |
| Run verified (which task or change) | |
| Model that handled it | |
| Intended/preferred/eligible credential route from OpenClaw evidence | |
| Exact-charge status | confirmed / unconfirmed / not needed |
| Provider-side record reference, if exact charge was needed | |
| Matches expectation: yes / no | |
| If no, next step (troubleshooting tree section, or a new proposal) | |

## Stop conditions

- The model that ran is not the model you expected: stop, and classify it via [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md) before drawing conclusions.
- The auth-routing evidence surprises you: same. Surprises here are routing facts, not model-quality facts.
- The run used a specialist, media, per-agent, cron, or subagent route: this checklist cannot prove its execution or recovery. If the destination is unset, inferred, unverified, or unapproved for the data class, stop; sanitize or use local processing and use a future route-specific procedure.
- You need proof of the exact charged account but do not have provider-side records: record the result as unconfirmed instead of inferring it.
- Auth list shows a disable or cooldown you can't explain: work the tree's cooldown and billing branches before touching anything.

This checklist never mutates anything. If verification suggests a change, the change goes through a proposal at [04-config-proposal-template.md](04-config-proposal-template.md).
