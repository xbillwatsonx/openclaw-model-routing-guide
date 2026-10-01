# Rollback Checklist: Restore the Recorded Baseline

Part of The OpenClaw Model Routing Guide + Companion Runbook. Use at [RUNBOOK.md](RUNBOOK.md) step 11 when the evidence says roll back. Teaching: [GUIDE.md](GUIDE.md) sections 1, 6, and 8.

**What this checklist does.** Restores your recorded known-good baseline from evidence you wrote down at [RUNBOOK.md](RUNBOOK.md) step 2, without reconstructing anything from memory, and then proves the restore worked.

**Version note.** Commands and behaviors below were checked against OpenClaw 2026.9.4 on 2026-09-17. Terminal commands run on the machine that hosts your OpenClaw gateway; chat commands run inside the affected session.

## Before you start

- [ ] The change being undone is exactly one, and you know which proposal it came from. If two changes are live, this checklist is not usable; that situation is an incident, see below.
- [ ] The step 2 baseline values are in front of you, in writing, including whether every changed key was present with a value or absent.
- [ ] The exact prior ordered override, prior session model selection, and any explicit auth-profile pin are recorded separately.
- [ ] The active config path, byte hash, private byte-for-byte backup identity/hash, and JSON5 comment/formatting decision are recorded.
- [ ] The exact version-matched backup/restore procedure, its covered files, backup identity/hash, and safe-restore evidence are recorded.
- [ ] You have explicit approval for the rollback (rollbacks are mutations too). Approver and time recorded:

## Semantic-routing emergency boundary

If an optional semantic router has a verified local kill switch, engage it first so no new override can be returned while you inspect or restore configuration. Confirm that the normal deterministic route still works. The kill switch is containment, not a completed rollback: keep it engaged while following the recorded reversal below, and verify the loaded mode after any required restart.

## 1. Reverse the one change

Use the exact reverse action recorded in the proposal's rollback field. Not a fresh idea; the one you wrote down before the change.

Typical reversals, for reference (all mutations; your proposal names yours):

```bash
openclaw config set <path> <value>
```

- Type: mutation. Restores a baseline value in openclaw.json. Use the mandatory mutation-to-validation plan from [04-config-proposal-template.md](04-config-proposal-template.md): dry-run, byte-level backup/hash, and post-write diff. If the baseline was absent, first verify that fact, dry-run the exact approved `openclaw config unset <path> --dry-run`, then use the separately approved `openclaw config unset <path>` rather than setting a guessed value.

Whole-backup recovery is a last resort when exact-key reversal is unsuitable. Before restoring, preserve current state, compare it with the recorded baseline for unrelated drift, and record backup timestamp, identity/hash, covered files, and expected baseline. Then follow the exact version-matched OpenClaw backup and restore procedure recorded at runbook step 1. A missing procedure is a stop, not permission to improvise.

Exact reversal performed, and when:

## 2. Handle session pins deliberately

Changing the configured primary does not rewrite existing session pins (checked against OpenClaw 2026.9.4, 2026-09-17). Preserve a session with an explicit auth-profile pin unless you have an installed-version procedure to clear and restore that exact pin. `/model default -s` may leave a compatible auth-profile pin; it is not a complete recovery command. If no exact procedure is available, record `unsupported`, do not claim session recovery complete, and use a fresh unpinned session for validation.

## 3. Verify the restore

Re-run the exact read-only commands from [RUNBOOK.md](RUNBOOK.md) step 2 and compare, value for value, against the written baseline:

```bash
openclaw models status
openclaw models fallbacks list
openclaw config get <path>
```

- Type: read-only, all of them. Same sanitization rules as always: status output stays private; profile ids can contain emails.

| Baseline item | Recorded baseline | After rollback | Match? |
| --- | --- | --- | --- |
| Primary model | | | |
| Fallback chain (in order) | | | |
| Other key(s) your change touched | | | |
| Config file byte hash and expected key-only diff | | | |
| `openclaw models status --check` exit code | | | |

## 4. Post-rollback behavior check

Run the smallest representative task from [06-test-plan.md](06-test-plan.md) on the restored route, inspect model and fallback state with `/status`, rerun relevant health checks, and complete one route verification with [08-route-verification-checklist.md](08-route-verification-checklist.md). Keep the incident open on any mismatch.

- Test ran, and the route evidence matched the restored configuration: yes / no
- Verification recorded (reported model route, intended credential route, and provider-side charge confirmation if needed):

## 5. Record it

- [ ] Proposal result field updated: rolled back, with date and evidence.
- [ ] Routing policy document updated: the change-history line, and the baseline section confirmed as the known-good values.
- [ ] Policy changelog line added (date, what was rolled back, why, from what evidence).
- [ ] Scorecards and verification lines filed with the proposal.

## Stop conditions

- Any value after rollback doesn't match the written baseline, or the full-file diff shows unrelated drift: stop. Re-check the reversal command. If it still doesn't match, preserve current state and use the recorded version-matched backup/restore procedure, then verify again.
- The rollback itself errors: assume nothing about state; verify read-only before deciding anything.
- After a backup restore, verification still doesn't match the baseline: stop. You are now in "treat as an incident" territory: record everything (commands, outputs, dates), and do not keep trying repairs without a fresh proposal and approval. Two changes in flight plus a failed rollback is exactly the pile-up the one-change rule exists to prevent.
