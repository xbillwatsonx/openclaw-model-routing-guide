# Configuration-Change Proposal Template: One Change Per Proposal

Part of The OpenClaw Model Routing Guide + Companion Runbook. Fill this in at [RUNBOOK.md](RUNBOOK.md) step 6; it gets pressure-tested at step 6, validated at step 7, applied at step 8, and decided at step 11.

**The one rule, stated once more.** One change per proposal. If the change touches two values, it is two proposals and two cycles. Batching is how you lose the ability to say what caused what, and it's how rollback becomes guesswork. Copy this template per proposal; number your proposals.

**Version note.** Every command named below was checked against OpenClaw 2026.9.4 on 2026-09-17. All commands run in a terminal on the machine that hosts your OpenClaw gateway, unless marked chat.

## Proposal header

| Field | Your value |
| --- | --- |
| Proposal number and date | |
| Which routing policy line does this trace to? | |
| Which inventory and classification evidence supports it? | |

## 1. The exact change (mutation)

Write the exact command, word for word, the one you will copy-paste at step 8. Not a description; the command.

| Field | Your value |
| --- | --- |
| Exact command | `openclaw config set agents.defaults.model.primary <provider/model>` (or the one permitted text-route key) |
| Exact configuration key it writes | (for example, `agents.defaults.model.primary`) |
| Command type | mutation |

Common one-change examples, for reference only; yours is whatever your policy produced:

- `openclaw config set agents.defaults.model.primary <provider/model>`: writes the global text primary after the same path/value dry-run passes.
- `openclaw config set agents.defaults.model.fallbacks '<JSON-array>' --strict-json`: writes the global text fallback list after the same path/value dry-run passes.

`models set` is excluded: its documented plugin-repair and canonical-settings side effects are not separately inventoried, approved, or recoverable here. Auth-order, catalog/discovery, specialist, media, per-agent, cron, and subagent mutations are also excluded until a route-specific procedure supplies authoritative execution evidence and recovery.

## 2. Reason

| Field | Your value |
| --- | --- |
| Why this change, in one honest paragraph | |

## 3. Scope

Check exactly one:

- [ ] Global text primary: `agents.defaults.model.primary`
- [ ] Global text fallback list: `agents.defaults.model.fallbacks`
- [ ] Bounded test-session model pin (separate session-mutation approval)

And in one sentence: what does this change *not* touch? (A scope statement that only says what it touches is half a scope statement.)

## 4. Baseline (recorded evidence, not memory)

From [RUNBOOK.md](RUNBOOK.md) step 2:

| Field | Your value |
| --- | --- |
| Current key state | present with this exact value / absent (choose one) |
| Recorded with which read-only command, on what date | |
| Active config path, byte hash, and private byte-for-byte backup identity/hash | |
| JSON5 comments/formatting present and preservation decision | |
| Exact version-matched backup/restore procedure, version/date, covered files, and safe-restore evidence | |
| Unrelated-drift comparison boundary before write | |
| `openclaw models status --check` exit code at baseline | |

## 5. Mutation-to-validation plan

Select the row for this mutation. Do not claim a dry-run where installed help does not support one. For a route with no documented dry-run, use the named substitute preflight and immediate read-only verification.

| Mutation | Exact dry-run where supported | Otherwise, documented substitute preflight | Immediate read-only verification |
| --- | --- | --- | --- |
| `config patch` | `openclaw config patch --file <patch>.json5 --dry-run` | N/A | `openclaw config validate` and relevant `config get` |
| `config set` global text scalar or JSON | `openclaw config set <path> <value> --dry-run`; use `--strict-json` for arrays/objects | N/A | `config validate`, relevant `config get`, and post-write file diff against the byte baseline |
| `config unset` for a key verified absent at baseline | `openclaw config unset <path> --dry-run` | N/A | `config validate`, `config get`, and post-write file diff against the byte baseline |
| Bounded session `/model` pin | None documented | Record prior model selection and explicit auth-profile pin separately; approve the session mutation. If an exact pin clear/restore procedure is unavailable, preserve the session and use a fresh one. | `/model status` and `/status` in that session |
| `models set`, auth order, catalog/discovery, specialist, media, per-agent, cron, subagent mutation | Excluded from this version | Stop; use a future route-specific, side-effect-inventoried procedure | No success or recovery claim here |

## 6. Dry-run plan

| Field | Your value |
| --- | --- |
| Exact validation command or substitute preflight from section 5 | |
| Expected dry-run result | |

Expectation guards if the proposal calls for them: `--expect-current-absent` (write only if the path is absent) or `--expect-current-json <json>` (write only if the current value matches exactly). They cannot be combined with `--dry-run`; they are enforced when the real set runs. They catch baseline drift: if the value changed since step 2, the set refuses to run.

OpenClaw-owned config writers can reserialize JSON5 as standard JSON and remove comments. The dry-run does not prove format preservation. Before a real write, record a byte hash and backup; after it, diff the complete file against the baseline and stop on any unrelated change. Whole-file restore follows the recorded version-matched procedure, not a guessed file copy.

## 7. Rollback

| Field | Your value |
| --- | --- |
| Exact reverse action (restore exact prior value, or remove only a key verified absent at baseline) | |
| Session model selection before mutation | |
| Explicit auth-profile pin before mutation, if any | |
| Whole-backup details, only if exact-key reversal is unsuitable | exact version-matched procedure, backup timestamp, identity/hash, covered files, expected baseline, current-state preservation and unrelated-drift check |
| Rollback type | mutation (it needs its own explicit go-ahead at step 11) |
| If rollback itself fails | Stop; preserve current state and use the recorded version-matched backup/restore procedure, then verify with the step 2 read-only commands |

## 8. Verification

| Field | Your value |
| --- | --- |
| Read-only command(s) that prove the change took effect | |
| Full-file post-write diff/hash result: expected key-only change and no unrelated drift | |
| Expected evidence, written before the change (what output, exactly, means success) | |
| How I'll verify the route in a real run (chat checks like `/model status` and `/status`, plus the auth-side check) | |

## 9. Stop conditions

| What I might see | What I do |
| --- | --- |
| Output differs from the expected evidence | Stop. Record it. No further mutations. |
| The command errors | Assume nothing about state; re-verify read-only before deciding anything. |
| Dry-run failed earlier | Proposal is not ready; back to step 6. |
| A test run fails for environment reasons (auth, rate limit, billing) | Stop testing; handle the environment first; see [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md). |
| An eligible profile, fallback, inferred default, or override lacks provider-boundary approval for the data class | Stop; sanitize the input or use an approved local route. |
| The writer removes comments/formatting unexpectedly or post-write diff shows unrelated drift | Stop. Preserve current state; do not paper over it with another write. |
| Anything else unexpected | Stop. Classify it before touching anything. |

## 10. Approval

| Field | Your value |
| --- | --- |
| Approver (you, or whoever holds owner or admin authority) | |
| Approval date and time | |
| Exact approved command (must match section 1 word for word) | |
| Approval is for this exact command, not for the general idea | Confirmed: |

## 11. Result

Filled at [RUNBOOK.md](RUNBOOK.md) steps 8 through 12, never before.

| Field | Your value |
| --- | --- |
| Applied (date, output matched expectation: yes/no) | |
| Test and verification evidence (scorecard and route verification references) | |
| Decision (kept / revised / rolled back) | |
| Baseline updated in the routing policy? | |
| Policy change-history line added? | |

## Done?

Take this filled proposal to [RUNBOOK.md](RUNBOOK.md) step 7 (validation) with Prompt 5 from [12-prompts.md](12-prompts.md) if you want your agent to pressure-test it first.
