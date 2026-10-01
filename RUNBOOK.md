# The OpenClaw Model Routing Runbook

The companion runbook of The OpenClaw Model Routing Guide + Companion Runbook package. The guide teaches the ideas; this runbook is the operational procedure. Read [GUIDE.md](GUIDE.md) sections 1, 2, and 6 before you start, then work the steps in order.

**Who this is for.** The self-hosted operator: you run your own OpenClaw, you can use a terminal and edit configuration, and you make your own approval decisions (or you know who does).

**Where commands run.** Every `openclaw` command in this runbook runs in a terminal, on the machine that hosts your OpenClaw gateway. Commands that start with a slash, like `/model`, run inside a chat session with your agent. Every command below is labeled read-only (no changes), dry-run (checks a change without writing it), or mutation (changes your configuration, state, or data). Nothing here is a credential-entry command; this runbook never teaches those.

**The one rule.** One change at a time. A change is one proposal, one approved command, one verification cycle. If you want two things changed, you run the loop twice. Batching changes destroys your ability to tell what caused what, and it makes rollback ambiguous.

**The safety pattern.** Every change step in this runbook carries all seven parts, every time: scope, proposal, baseline, rollback, verification, stop condition, and approval boundary. If any part is missing, the step isn't ready to run.

**Version note.** Every command, flag, and version-specific behavior in this runbook was checked against OpenClaw 2026.9.4 on 2026-09-17. Run `openclaw --version` first. If your version differs, recheck the dated details against the documentation installed with your copy before you rely on them.

## Step 1. Prerequisites and explicit exclusions

**Purpose.** Confirm you're ready, and be clear about what this runbook deliberately does not cover.

Prerequisites, all of them:

- OpenClaw is installed and running, and you can reach a terminal on the machine that hosts it.
- Before continuing, locate and read the exact version-matched OpenClaw backup and restore guide/procedure for your installation. Record its title/path or URL, OpenClaw version, and date; record the backup identity and cryptographic hash, timestamp, and every covered file. Confirm in writing that you understand the safe restore test or evidence required by that procedure. This runbook does not re-teach backup or restore. If you cannot locate a version-matched procedure, cannot identify the backup and covered files, or cannot explain its restore evidence, stop.
- You know who approves changes on your system: you, if you're solo, or whoever holds owner or admin authority.
- You've read GUIDE.md sections 1, 2, and 6, or you've had Prompt 1 walk you through them.

Explicit exclusions, all of them:

- No credential entry is taught. If you need to add credentials, use OpenClaw's own setup documentation first, then come back.
- No provider account-creation tutorial.
- No universal model rankings, and no promises of savings or reliability.
- No cost measurement. The OpenClaw Cost Savings Guide owns measurement; this runbook owns the routing procedure.
- No OpenClaw update or migration procedure. When your version changes, this runbook tells you to recheck facts, not to update software.
- No exhaustive provider or model catalog work.
- No `models set` workflow: it can repair runtime plugins and canonicalize model settings in addition to changing the primary. This package does not inventory, approve, or recover every possible side effect.
- No auth-order mutation: its per-agent SQLite auth-store recovery is not supported by this package.
- No catalog refresh/discovery: it has no rollback here and a hosted catalog refresh needs a Gateway restart before activation.
- No specialist, media, per-agent, cron, or subagent route modifications. This version has read-only inventory and policy design only; it cannot claim route-specific execution success or recovery.

Record your starting point:

```bash
openclaw --version
```

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the installed version, which every dated fact in this package is checked against.
- Evidence to record: the full version string, and today's date, in your baseline notes.

**Stop condition.** If any prerequisite is missing, stop at the end of this step and fix it before continuing. A missing backup is the one most people talk themselves out of; don't.

**Approval boundary.** Read-only; no approval needed.

## Step 2. Capture the current version, configuration baseline, and rollback point

**Purpose.** Write down the known-good state you'll roll back to. The baseline is recorded evidence, never memory.

Run each of these and record the results in the baseline section of [04-config-proposal-template.md](04-config-proposal-template.md), or in a private baseline note you keep next to it:

```bash
openclaw models status
```

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: configured primary model, fallback chain, and an authentication overview.
- Evidence to record: configured primary and fallback list.
- Watch out: output can contain credential metadata and redacted credential fragments. Never paste raw output into a public chat, issue, or forum. Record only the model route facts.

```bash
openclaw models status --check
```

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: a readiness exit code: 0 clean, 1 issue, 2 expiring credential (checked against OpenClaw 2026.9.4, 2026-09-17).
- Evidence to record: the exit code and date.

```bash
openclaw models fallbacks list
```

- Type: read-only. Reveals: your fallback chain in order. Record: the full ordered list.

```bash
openclaw models image-fallbacks list
```

- Type: read-only. Reveals: your image fallback chain. Record: the list.

```bash
openclaw models aliases list
```

- Type: read-only. Reveals: your aliases. Record: the list.

```bash
openclaw models auth list
```

- Type: read-only. Reveals: auth profile ids and health, without printing secret material (checked against OpenClaw 2026.9.4, 2026-09-17).
- Evidence to record: profile ids and health only.
- Watch out: profile ids can contain email addresses; treat the output as private. Cooldown and disable entries include their reason and recovery action; record those reasons, they're useful context.

```bash
openclaw models auth order get --provider <id>
```

- Type: read-only. Reveals: the stored order override for the provider you name (the `--provider` option is required). Record: whatever it shows, or "none". Run it once per provider with more than one profile.

```bash
openclaw config get agents.defaults.model
openclaw config get agents.defaults.modelPolicy.allow
```

- Type: read-only. Reveals: the configured model route and the override allowlist. Record: both values.
- Watch out: use config get for routing keys only; never point it at secret-valued paths.

Optional but recommended:

```bash
openclaw doctor --lint
```

- Type: read-only. Reveals: health findings, no repairs applied. Record: any findings that touch your model route.
- Watch out: findings can include local paths; sanitize before sharing.

Then confirm your rollback point in writing:

- Backup procedure: exact version-matched guide/procedure, version/date, and the evidence that its safe restore process is understood or safely tested.
- Backup identity: location, timestamp, cryptographic hash, and every covered file.
- Baseline values: the primary, fallback order, and any other values you just recorded.
- Config-file baseline: active path from `openclaw config file`, byte hash, file copy backup, and whether comments/JSON5 formatting must be preserved.
- The statement: "If anything goes wrong, I use exact-key reversal first; whole-file restore follows the recorded version-matched procedure, after preserving current state and checking unrelated drift."

**Stop condition.** If `models status --check` returned 1 or 2, or doctor --lint shows a route-relevant finding, resolve it or accept it in writing before changing anything. Exit 0 means the readiness check found no listed issue; it does **not** prove a real request will succeed. A known issue discovered after your change gets blamed on your change, and you'll have no clean way to argue otherwise.

**Approval boundary.** Read-only; no approval needed.

## Step 3. Complete the inventory worksheet

**Purpose.** A full map of what you have, with no secrets recorded.

Open [01-inventory-worksheet.md](01-inventory-worksheet.md) and fill in every section using the read-only commands from GUIDE.md section 3. Follow the worksheet's sanitization rules exactly: record values the worksheet asks for, never secret values, never raw status output.

- Where the commands run: terminal, on the gateway machine; all read-only.
- Evidence to record: the completed worksheet, kept private.
- Watch out: profile ids can contain emails. If you ever share the worksheet, redact first.

**Stop condition.** If the inventory shows zero auth profiles, stop: this runbook doesn't teach credential entry. Use OpenClaw's own documentation to add one credential, then return to step 2 and re-baseline.

**Approval boundary.** Read-only; no approval needed.

## Step 4. Complete the task-classification worksheet

**Purpose.** Classify your recurring work by difficulty, risk, value, privacy, and tool needs, with no model names.

Open [02-task-classification-worksheet.md](02-task-classification-worksheet.md) and work through it. GUIDE.md section 4 explains each dimension. If a task is hard to classify, that's information too: write down what made it ambiguous.

- Evidence to record: the completed worksheet.
- **Stop condition.** If you find you have no recurring tasks worth routing deliberately, stop. Your setup may genuinely not need this procedure yet; that's a valid outcome, and writing it down with the date is the whole point.
- **Approval boundary.** Conversation and note-taking only; no commands.

## Step 5. Write the proposed routing policy

**Purpose.** Turn inventory plus classification into a written policy, before touching configuration.

Fill in [03-routing-policy-template.md](03-routing-policy-template.md), including the fallback and retry design in [05-fallback-retry-template.md](05-fallback-retry-template.md). GUIDE.md sections 5 and 6 carry the teaching.

Your policy should end up with:

- a primary model, with a written reason
- a fallback order, with a reason for each entry
- specialist/scoped routes inventoried and policy-boundary decisions recorded only; this package version does not modify or claim recovery for them
- auth preferences per provider where you have more than one profile
- session rules: who may pin what, and when pins get cleared
- stop rules: when to stop retrying and record evidence instead
- no guarantee language anywhere. A fallback chain does not guarantee completion, low cost, privacy, or correctness.

- Evidence to record: the completed policy document.
- **Stop condition.** If you can't write a reason for an entry, you don't have a policy for it yet; you have a guess. Either test it (step 9, later, once a candidate change is live) or leave it out of this round.
- **Approval boundary.** Words only; no configuration changes.

## Step 6. Review exact scope and obtain approval

**Purpose.** Pick the one change you're making first, and get it approved in writing before anything runs.

**Scope.** This version's executable scope is exactly one global text-route key: `agents.defaults.model.primary` or `agents.defaults.model.fallbacks`. Session testing pins are separate, bounded chat mutations. Specialist, media, per-agent, cron, subagent, auth-order, catalog, and discovery changes are excluded. Anything broader than "one value in one place" is not one change.

Fill in the proposal in [04-config-proposal-template.md](04-config-proposal-template.md). The safety pattern, checked line by line:

| Part | What it must contain |
| --- | --- |
| Scope | The exact surface: which key, which agent, which session, which route |
| Proposal | The exact command, word for word, plus the reason |
| Baseline | The recorded values from step 2, plus backup location |
| Rollback | The exact reverse action: the command that restores the baseline, or the backup restore |
| Verification | The command and the expected evidence that proves the change took effect |
| Stop conditions | What output or behavior means "stop and investigate, do not proceed" |
| Approval | Who approved, when, and the exact approved command |

Review checklist before approval:

1. Is this exactly one change?
2. Does the rollback reverse the change without reconstructing state from memory?
3. Does the verification command actually test the thing you changed, or something adjacent?
4. Are the stop conditions specific enough that you'll recognize them?

**Approval boundary.** This is the gate. Whoever holds approval authority on your system signs the exact command, in writing. Nothing that writes runs before this line is filled. If you're solo, the discipline still applies: write it down, then approve it. The approval is for the exact command, not for "the general idea".

- **Stop condition.** If review changes the proposal at all, the changed proposal needs its own approval before step 7.

## Step 7. Validate the configuration before applying it

**Purpose.** Prove the change shape is valid before it touches your live configuration.

First, confirm the current configuration is healthy:

```bash
openclaw config validate
```

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: whether the current config validates against the schema, without starting the gateway (checked against OpenClaw 2026.9.4, 2026-09-17).
- Evidence to record: pass or the exact finding.
- Watch out: if validation fails before your change, stop. Record it. You don't change configuration on top of a config that already fails; you'll never be able to attribute what broke.

Then record the active config file, a byte-level baseline hash, and a private byte-for-byte backup copy. OpenClaw-owned config writers can reserialize JSON5 as standard JSON and remove comments. If preservation matters, stop and use an approved, recoverable procedure rather than treating the writer as format-preserving. Record the exact pre-write diff boundary so unrelated drift can be detected after the write.

**Preservation warning.** Before any config write, capture the active path from `openclaw config file`, compute a byte-level hash, and keep a private byte-for-byte backup. Record the hash and backup identity in your baseline. If the hash changes after the write, unrelated drift occurred; stop and record it rather than assuming the change is clean. When restoring from backup, capture the post-restore config file hash and compare it against the pre-restore hash. Record both hashes with the restoration procedure.

```bash
openclaw config file
```

- Type: read-only. Record: active path, byte hash, backup-copy identity/hash, and whether comments or JSON5 formatting are present.

Then dry-run the exact change, using whichever form matches your approved proposal:

```bash
openclaw config patch --file ./routing-change.json5 --dry-run
```

- Type: dry-run. Validates the patch object's schema and resolvability without writing openclaw.json (checked against OpenClaw 2026.9.4, 2026-09-17).
- What it reveals: whether the change would apply cleanly.
- The file is a JSON5 patch object (JSON5 is a JSON variant that permits comments) you write per your proposal; one change, one patch.

```bash
openclaw config set agents.defaults.model.primary <provider/model> --dry-run
```

- Type: dry-run. Validates a single set without writing. In plain value mode it does not run full schema checks (the CLI prints a note saying so); add `--strict-json` for JSON values to turn them on. Model-reference paths are still reference-checked (checked against OpenClaw 2026.9.4, 2026-09-17).
- Expectation guards, if your proposal calls for them: `--expect-current-absent` (only write if the path is absent) or `--expect-current-json <json>` (only write if the current value matches exactly). They cannot be combined with `--dry-run`; they are enforced when the real set runs at step 8. If the current value drifted from your step 2 baseline, the guarded set refuses to write instead of silently overwriting.

If the baseline key was absent and the approved rollback must remove the new key, the installed 2026.9.4 help supports this preflight:

```bash
openclaw config unset agents.defaults.model.primary --dry-run
```

Use the exact approved absent-at-baseline key in place of the example only after confirming it was absent at baseline. `config unset` dry-run validates removal without writing; the actual `openclaw config unset <path>` is a separately approved rollback mutation.

**Stop conditions.** Dry-run fails: stop, fix the proposal, re-approve (back to step 6), then retry the dry-run. Config validate fails: stop and record; resolve or explicitly accept before proceeding. The active path, hash, backup, JSON5-preservation warning, baseline key state, or unrelated-drift guard is missing: stop. Anything unexpected in the dry-run output: stop.

**Approval boundary.** Dry-runs are checks, not changes; the approved command is still the only mutation that may run, and only after step 8's confirmation.

## Step 8. Make one change

**Purpose.** Apply the exact approved command, and verify it took effect, with no improvisation.

Run the exact approved command from your proposal. The common mutation commands, for reference; your proposal names one:

```bash
openclaw config set agents.defaults.model.primary <provider/model>
```

- Type: mutation. Writes openclaw.json. Only after the step 7 dry-run passed for this same path and value. Expectation guards, if your proposal uses them, are enforced here at write time. It can reserialize JSON5 as standard JSON and remove comments; record that warning, compare the post-write file against the byte-level baseline, and stop on unrelated drift.
- Rollback: restore the exact baseline value with the separately approved `config set`, or, only when the baseline key was verified absent, the separately approved `openclaw config unset <path>` after its dry-run. Whole-file recovery is last resort and must follow the step 1 version-matched procedure, preserving current state and checking unrelated drift first.

Then verify immediately, read-only, with the commands named in your proposal's verification plan. Typically:

```bash
openclaw models status
```

- Type: read-only. Compare what it shows against the expected evidence written in your proposal.
- Watch out: same sanitization rule as step 2; never paste raw output publicly.

After the write, compare the active config file byte-for-byte and semantically: expected key-only difference, no unrelated drift, `openclaw config validate` passes, and the documented restart/hot-reload hint is recorded. If a clean route test is needed, use a fresh unpinned session. Do **not** use `/model default -s` without a separately approved session-mutation action and an installed-version procedure that can clear or restore any explicit auth-profile pin.

**Stop conditions.** Any output that differs from your proposal's expected evidence: stop, record it, do not run any further mutation. If the command errored, assume nothing about state; re-verify with read-only commands before deciding anything. If the change applied but verification fails, go to the troubleshooting tree ([10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md)) before touching anything else.

**Approval boundary.** Only the exact approved command. Any other mutation, however small and obviously related, needs its own proposal and approval.

## Step 9. Run matched-task tests

**Purpose.** Test the change on your real work with matched tasks and a shared scorecard.

Follow [06-test-plan.md](06-test-plan.md) and score with [07-scorecard.md](07-scorecard.md). The method, in short:

- Same task, same inputs, for every candidate, in fresh sessions.
- Pin the candidate in each test session with `/model <provider/model> -s` (chat command, session scope). Session selections are strict: a candidate that can't do the work fails the run visibly, and that's your test working, not breaking (checked against OpenClaw 2026.9.4, 2026-09-17).
- Record, per run: completion, the quality score (same 1 to 5 anchors for every candidate), instruction-following, and route evidence: the selected model from `/status` in the session, any fallback notice, and auth-routing evidence: pinned/preferred/eligible profiles. If exact charged account confirmation is needed, note confirmed provider-side / unconfirmed / not needed. Leave the billing-path field for step 11.
- Run each candidate on each task more than once before concluding.

- Where the commands run: chat commands inside your test sessions; `/status` and `/model status` for evidence.
- Evidence to record: the completed scorecards.
- Watch out: sanitize any shared output; profile ids can contain emails.
- **Stop condition.** Any different text model/fallback invalidates strict candidate scoring; reserve fallback data for end-to-end configured-route validation. If a test run fails in a way the troubleshooting tree classifies as an environment problem (auth, rate limit, billing) rather than a model-quality problem, stop testing and handle the environment first; a broken environment invalidates the comparison. Any unapproved provider boundary, profile, fallback, inferred default, or override is a hard stop: sanitize or use local.
- **Approval boundary.** Session-scoped pins inside test sessions only. No configuration writes during testing.

## Step 10. Verify selection, fallback, authentication, and billing evidence

**Purpose.** Verify the model route OpenClaw reports, inspect the intended authentication route, and decide whether provider billing records are needed to confirm the exact charge.

Work [08-route-verification-checklist.md](08-route-verification-checklist.md). The four checks:

1. What is configured: `openclaw models status` (terminal, read-only).
2. What the session is pinned to: `/model status` (chat). It shows the session selection and auth candidates per provider (checked against OpenClaw 2026.9.4, 2026-09-17).
3. What actually handled the run: `/status` (chat) shows the selected model and, when fallback state differs, the active fallback model and the reason.
4. Which credential route OpenClaw preferred or could use: `openclaw models auth list` (terminal, read-only) for health, reasons, and recovery actions, plus `openclaw models auth order get --provider <id>` for any order override on the provider in question.

Remember the CLI status command shows configured state and does not inspect a session's override; the chat commands are the ones that answer session questions (checked against OpenClaw 2026.9.4, 2026-09-17). And a stored profile being present doesn't prove it's currently usable; read health and cooldown entries as separate facts from existence.

- Evidence to record: which model handled each test run and the intended/preferred/eligible routing evidence. Do not claim text `/status` proves specialist, media, per-agent, cron, or subagent execution.
- Watch out: sanitize everything before sharing; the privacy rules from step 2 never expire.
- **Stop condition.** If the reported model route or auth-routing evidence surprises you, stop and classify it with the troubleshooting tree before drawing conclusions. If the exact charge matters, confirm it in the provider's usage or billing records rather than inferring it from profile order alone.
- **Approval boundary.** Read-only throughout.

## Step 11. Keep, revise, or roll back

**Purpose.** Decide from recorded evidence, then act on exactly that decision.

The safety pattern applies to this step too:

| Part | For a keep decision | For a revise decision | For a roll back decision |
| --- | --- | --- | --- |
| Scope | The one change you made | A new aspect, to be proposed separately | The one change you made |
| Proposal | None; record the keep | New proposal, new cycle from step 5 or 6 | The reversal, from your step 8 rollback field |
| Baseline | Becomes the new baseline after step 12 | Unchanged until the next change is approved | The step 2 baseline |
| Rollback | Documented in the record | N/A until the new change runs | If rollback itself fails: follow the recorded version-matched backup/restore procedure |
| Verification | Step 10 checks recorded | N/A | Re-run the step 2 read-only commands and compare to baseline |
| Stop condition | Evidence missing = no decision | Same | Rollback doesn't restore baseline = stop, record, treat as an incident |
| Approval boundary | Yours to record | New approval required | Rollback needs explicit approval before each step |

Decision guidance:

- **Keep** when the scorecard shows the change did what your policy wanted and intended/preferred/eligible routing evidence confirms the expected text route. A cost-based keep additionally requires confirmed, comparable baseline and changed-route evidence; otherwise cost is inconclusive and cannot justify keep.
- **Revise** when the evidence points at a different adjustment. A revision is a new one-change proposal; it never piggybacks on the change you just made.
- **Roll back** when the change didn't produce the intended effect, or produced a side effect your policy doesn't accept. Follow [09-rollback-checklist.md](09-rollback-checklist.md) exactly: restore the recorded baseline, re-run the step 2 read-only commands, compare, and record.

**Stop condition.** No decision without recorded evidence. If your tests and verification didn't produce clear evidence, the honest outcome is "run more tests", not a coin flip.

## Step 12. Record the final known-good state

**Purpose.** Whatever you decided, write it down so the next change starts from truth.

Update, in this order:

1. Your routing policy document: the current primary, fallback order, provider-boundary approvals, and the date. Specialist/scoped routes remain inventory/policy-only until a route-specific procedure exists.
2. Your baseline record: if you kept the change, the step 2 baseline values are replaced by the verified new values; if you rolled back, confirm the old values still hold.
3. Your own changelog entry, in the policy document: date, OpenClaw version, what changed, the evidence, and the decision.
4. The proposal's result field in [04-config-proposal-template.md](04-config-proposal-template.md): kept, revised, or rolled back, with the date.

- Evidence to record: all four updates, complete.
- **Stop condition.** If any of the four records disagree with each other, fix the records before doing anything else. An inconsistent record is worse than none; it's confidently wrong.
- **Approval boundary.** Documentation only; nothing here touches the system.

## Step 13. Troubleshooting decision tree

**Purpose.** When a route fails or surprises you, classify before you react.

Open [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md) and work it branch by branch, read-only until a class is confirmed. Each leaf names the next evidence to collect and a stop point. The tree distinguishes the failure classes OpenClaw treats differently: rate limits, authentication failures, billing disables, exhausted usage windows, cooldowns, context overflow, refusals, model-not-found, unknown providers, fallback surprises, strict session selections, and legacy credential files.

Two habits that make the tree work: describe effects instead of hand-editing internal state, and stop when a branch says stop. Repairs, including `openclaw doctor --fix`, are mutations: they go through the same proposal, approval, and rollback path as any change in this runbook (checked against OpenClaw 2026.9.4, 2026-09-17).

**Approval boundary.** Read-only evidence collection needs no approval. Any repair needs a proposal and explicit approval first.
