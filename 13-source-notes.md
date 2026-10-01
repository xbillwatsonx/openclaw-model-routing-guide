# Source Notes: Claim Trace and Verification Record

Part of The OpenClaw Model Routing Guide + Companion Runbook. This is the package's evidence file: where every material factual claim comes from, what was verified directly, what remains unresolved, and what was deliberately excluded.

**Baseline.** OpenClaw 2026.9.4, commit 3a9d69d, recorded 2026-09-17 via `openclaw --version` on the install this package was drafted against. Reader-facing files say "checked against OpenClaw 2026.9.4 on 2026-09-17"; the commit hash appears only here.

**How to read this file.** Claims trace to the locked evidence map from the Phase 1 source lock (a private planning document). Row IDs below are that map's IDs; each row there carries the claim, its source, and its stability class (stable principle versus version-sensitive detail). The source documents are the OpenClaw documentation installed with the release (file names below) plus the installed CLI's verified `--help` output.

**Source documents:**

- `docs/concepts/models.md`
- `docs/concepts/model-failover.md`
- `docs/concepts/retry.md`
- `docs/gateway/configuration/common-tasks.md`
- `docs/gateway/config-tools/sessions-and-subagents.md`
- `docs/tools/subagents/tool-reference.md`
- `docs/cli/models.md`
- installed CLI `--help` output for every command named in this package

## 1. Command and flag verification record

Every command, subcommand, and flag written into this package was checked against the installed release's `--help` output on 2026-09-17:

- `openclaw --version` (run; baseline recorded)
- `openclaw models` subcommands: status (with `--check`, `--json`, `--probe` flags), list (with `--all`, `--provider`, `--refresh`), set, set-image, fallbacks (add, remove, clear, list), image-fallbacks (add, remove, clear, list), aliases (add, remove, list), auth (list, order get/set/clear, which require `--provider`), refresh, scan (with `--no-probe`, `--set-default`)
- `openclaw config` subcommands: get, patch (with `--file`, `--dry-run`), schema, set (with `--dry-run`, `--expect-current-absent`, `--expect-current-json`, `--strict-json`), validate
- `openclaw doctor` (with `--lint`, `--json`, `--fix`)

What was deliberately **not** run during drafting: `openclaw models status`, `openclaw models status --check`, `openclaw models auth list`, and the other commands whose output can carry credential metadata or live values from the drafting installation. Their documented output shapes come from `docs/cli/models.md` and verified help text (checked 2026-09-17), not from fresh runs that would pull credential metadata into the drafting workspace. Phase 3 validation (2026-09-17) then executed the safe read-only set with sanitized evidence only: exit codes, row counts, and documented-section keyword presence. No raw output was captured or stored (see U2 below).

Credential-entry commands are document-only everywhere in this package: they are never taught, never exemplified with syntax, and never executed.

**Phase 3 validation record (2026-09-17).** Every command and flag in this package was re-verified against the installed help for OpenClaw 2026.9.4. The safe read-only set was executed with sanitized evidence only. `config set --dry-run` and `config patch --file --dry-run` were exercised in a disposable isolated fixture (a clean environment plus the documented `OPENCLAW_STATE_DIR` override; the fixture config path was proven distinct and the live config file stayed byte-identical throughout). Fixture findings folded back into this package: `models auth order get/set/clear` require `--provider <id>` (with `set` also taking the profile ids to order), expectation guards are write-time checks that cannot be combined with `--dry-run`, and plain value-mode `config set --dry-run` does not run full schema checks (`--strict-json` turns them on). No credential flow, provider probe, catalog refresh, or live mutation ran anywhere during validation.

**Phase 4 validation record (2026-09-17).** Privacy, terminology, billing-evidence, scope, reader-safety, and humanizer reviews completed with repairs applied. Repeatable Phase 4 checks now live in this package's justfile. The detailed review evidence remains private and is excluded from the public manifest.

## 2. Claim trace by evidence row

Group A: model selection and fallback (source: `docs/concepts/models.md`, `docs/concepts/model-failover.md`, `docs/gateway/configuration/common-tasks.md`).

| Row | Claim (short) | Used in this package |
| --- | --- | --- |
| A1 | Primary at `agents.defaults.model.primary` or `agents.defaults.model` string | GUIDE 2.2, RUNBOOK 8, 01, 03 |
| A2 | Ordered fallbacks at `agents.defaults.model.fallbacks` | GUIDE 2.2, RUNBOOK 8, 01, 03, 05, 09 |
| A3 | Auth-profile rotation inside a provider before next fallback model | GUIDE 2.2, 05, RUNBOOK 8, 10 |
| A4 | Configured default normally uses its fallback chain | GUIDE 2.2 |
| A5 | Per-agent primary strict unless its model object includes fallbacks; `[]` makes strict explicit | GUIDE 2.3, 5.2 |
| A6 | User session selection exact and strict; fails visibly | GUIDE 2.3, 7.1, RUNBOOK 9, 06, 10 (tree A8) |
| A7 | Cron payload model is job primary; own fallback list; empty list strict | GUIDE 2.3 |
| A8 | Fallback execution turn-local | GUIDE 2.2, 7.1, RUNBOOK 10 (tree B1) |
| A9 | Changing configured primary doesn't rewrite session pins; `/model default -s` clears pin | GUIDE 2.4, RUNBOOK 8, 09 |
| A10 | Auto fallback temporary; reprobes origin; clears on recovery | GUIDE 2.2, 10 (tree B1) |
| A11 | `/model` scopes -s/-a/-g; owner/admin authority; modelSelectionScope default session | GUIDE 2.4, 03 |
| A12 | Candidate chain rules; fallbacks not filtered by allowlist; empty override disables fallback | GUIDE 2.2, 14 |

Group B: model policy and catalog (source: `docs/concepts/models.md`, `docs/cli/models.md`, `docs/gateway/configuration/common-tasks.md`).

| Row | Claim (short) | Used in this package |
| --- | --- | --- |
| B1 | `agents.defaults.models` stores aliases and per-model settings; never restricts; not the allowlist | GUIDE 2.8, 03, 10 (tree B3) |
| B2 | `agents.defaults.modelPolicy.allow` is the override allowlist; exact refs and trailing provider wildcards; omitted/empty allows any; per-agent policy replaces default | GUIDE 2.8, 01, 03, 10 (tree B3) |
| B3 | Unrestricted policy doesn't make unknown provider or unsupported runtime usable | GUIDE 2.8, 03 |
| B4 | Model refs provider/model, split on first slash; OpenRouter ids may contain another slash | GUIDE 2.1, 14 |
| B5 | Pickers browse catalog; browsing doesn't start discovery | GUIDE 2.9, 10 (tree B4) |
| B6 | Default views hide deprecated/disabled rows except configured refs; `--all` shows full catalog | GUIDE 2.9, 3, 01, 10 (trees A7, B4) |
| B7 | `models set` and `aliases add` write `agents.defaults.models`, never `modelPolicy.allow` | GUIDE 2.8 |
| B8 | Catalogs, prices, capabilities change independently; every example dated | every dated label in the package; 11 |
| B9 | `models list` reads published inventory, doesn't rewrite it; `--refresh` starts discovery; failed refresh warns and keeps available rows | GUIDE 2.9, 3, 11, 10 (tree B4) |

Group C: authentication, profiles, billing paths (source: `docs/concepts/model-failover.md`, `docs/cli/models.md`).

| Row | Claim (short) | Used in this package |
| --- | --- | --- |
| C1 | Secrets and runtime auth state in per-agent SQLite auth store (documented path pattern) | GUIDE 2.5 |
| C2 | `auth.profiles`/`auth.order` in config are routing metadata, not secret storage | GUIDE 2.5 |
| C3 | Credential types: api_key, oauth, token | GUIDE 2.5 |
| C4 | Profile ids `provider:default` / `provider:email` | GUIDE 2.5, 01, 08 |
| C5 | Rotation order: stored override, then config order, then configured, then stored; round-robin tiers otherwise | GUIDE 2.6, 03, 14 |
| C6 | Session stickiness: auto pin may rotate/clear; user pin via `/model ref@profileId -s` survives resets while eligible | GUIDE 2.6, 14 |
| C7 | Model ref alone doesn't prove billing path; OpenClaw auth state shows routing intent and eligibility, while provider records may be needed to confirm the exact charge | GUIDE 2.7, 8, 08, 01 |
| C8 | Status separates credential sources, profile health, model route issues, runtime auth; stored profile doesn't prove usability | GUIDE 8.2, 08, RUNBOOK 10 |
| C9 | Cooldowns scale 30s/1m/5m; rate-limit bucket broader than plain 429; model-scoped cooldowns allow siblings; billing disables ten-minute window; exhausted usage windows to fallback | GUIDE 6.2, 05, 10 (trees A1, A3, A4), 14 |
| C10 | `OPENCLAW_FALLBACK_SKIP_TTL_MS` opt-in auth failure skip cache (mention only) | GUIDE 6.3, 05 |
| C11 | `OPENCLAW_SDK_RETRY_MAX_WAIT_SECONDS` caps SDK retry-after waits, 60s default (mention only) | GUIDE 6.3, 05 |
| C12 | Describe cooldown/disable effects; never hand-edit internal state | GUIDE 6.2, 05, 10, RUNBOOK 13 |
| C13 | Legacy credential files import only via `doctor --fix`; runtime fails closed until migrated | 10 (tree C1) |
| C14 | OpenAI auth/runtime separate; `openai/gpt-*` can rotate between subscription and API-key profiles; explicit order controls preference | GUIDE 2.7, 8.2, 08, 01 |
| C15 | `models auth order get/set/clear --provider <id>` manages a provider's stored order override in the auth store; clear falls back to config or rotation | GUIDE 3, 8.2, RUNBOOK 2, 8, 01, 08, 09, 10 |
| C16 | `models auth list` prints ids and health, no secret material; cooldown/disable entries include reason and recovery action | GUIDE 3, 8.2, RUNBOOK 2, 01, 08, 10 (trees A1, A3) |
| C17 | Personal model accounts (models accounts) distinct from auth profiles; terminology guard | GUIDE 2.5, 14 |

Group D: specialist routes (source: `docs/concepts/models.md`, `docs/gateway/config-tools/sessions-and-subagents.md`, `docs/tools/subagents/tool-reference.md`, `docs/cli/models.md`).

| Row | Claim (short) | Used in this package |
| --- | --- | --- |
| D1 | Per-agent model configuration overrides the shared default | GUIDE 5.2 |
| D2 | Subagents inherit caller's model unless subagents model / per-agent / explicit spawn model; invalid spawn value falls back with warning | GUIDE 5.2, 03 |
| D3 | `agents.defaults.utilityModel` routes short internal tasks; unset uses provider small-model default then primary; empty string disables | GUIDE 5.2, 03 |
| D4 | `agents.defaults.imageModel` applies when primary can't accept images; `agents.defaults.pdfModel` backs pdf tool, falls back to imageModel then session/default | GUIDE 5.2, 03 |
| D5 | `mediaModels` image/music/video back media tools; unset infer auth-backed defaults with cross-provider fallback | GUIDE 5.2, 03 |
| D6 | Image fallbacks parallel managed list at `agents.defaults.imageModel.fallbacks`, same CLI shape | GUIDE 3, 5.2, RUNBOOK 8, 01, 03 |
| D7 | Specialist routes introduced as separate surfaces, not mixed into the core text-model fallback lesson | GUIDE 5 intro, 6 intro; structure of 03 |

Group E: retry and loop control (source: `docs/concepts/retry.md`, `docs/concepts/model-failover.md`).

| Row | Claim (short) | Used in this package |
| --- | --- | --- |
| E1 | Bounded same-model recovery before rotation/fallback; continues transcript; preserves work; visible indicator; cancellable | GUIDE 6.2, 05 |
| E2 | Rate limits up to 10 attempts; other transient 8 retries in 90s; backoff ~1s to 30s cap with jitter; retry-after minimums | GUIDE 6.2, 05, 10 (tree A1) |
| E3 | Billing, auth errors, refusals don't use transient budget | GUIDE 6.2, 05, 10 (trees A2, A3) |
| E4 | Exhausted usage windows go directly to eligible fallback | GUIDE 6.2, 05, 10 (tree A4) |
| E5 | `retry.provider.maxRetries` embedded session setting, not openclaw.json key; 0 disables; rate limits stay capped | GUIDE 6.3, 05 |
| E6 | Built-in budgets aren't workflow-level limits; stop, record, change strategy | GUIDE 6.4, 05, RUNBOOK 5 |
| E7 | Context overflow, explicit aborts, final refusals don't advance fallback; final refusal ends turn | GUIDE 6.1, 10 (trees A5, A6) |
| E8 | Failover-worthy classes list | GUIDE 6.1, 05, 10 (tree A7) |
| E9 | `/status` shows selected model and, when differing, active fallback and reason | GUIDE 3, 8.1, RUNBOOK 10, 08, 06 |
| E10 | Fallback notices once per state change, outside group/channel conversations | GUIDE 2.2, 8, 08 |
| E11 | Harness internal request retries separate from OpenClaw's continuation budget | not used in reader content (reserved; no need arose) |

Group F: verification and inventory surfaces (source: `docs/cli/models.md`, `docs/concepts/models.md`, verified `--help`, research packet safety note).

| Row | Claim (short) | Used in this package |
| --- | --- | --- |
| F1 | `models status` shows resolved default, fallbacks, auth overview; `--check` exit 0/1/2 | GUIDE 3, RUNBOOK 2, 01, 08, 11 |
| F2 | `models status --json` can expose credential metadata; sanitize; never paste raw output publicly | every command card and sanitization block: GUIDE 3, RUNBOOK 2, 01, 08 |
| F3 | `--probe` performs live provider requests; consumes tokens; rate-limit risk; exclusive state | GUIDE 3 |
| F4 | CLI status explains configured defaults, not session override; `/model status` shows session selection and auth candidates per provider | GUIDE 3, 8.1, RUNBOOK 10, 08 |
| F5 | `models list` reads published inventory; `--all`; `--provider` | GUIDE 3, 11, 01 |
| F6 | `models refresh` checks hosted catalog only; no sign-in; restart applies downloads | GUIDE 9.3, 11, 10 (tree B4) |
| F7 | `models set` requires declared provider; unknown provider exits nonzero, no config change; uncataloged saves with warning; `config set` stricter for text models | RUNBOOK 8, 04, 10 (trees A7, C2) |
| F8 | `fallbacks` subcommands manage the fallback list; image-fallbacks parallel | GUIDE 3, RUNBOOK 8, 09 |
| F9 | `config validate` checks config against schema without starting gateway | GUIDE 3, RUNBOOK 7, 01 |
| F10 | `config patch/set --dry-run` validate without writing; expectation guards | GUIDE 6.6, RUNBOOK 7, 04 |
| F11 | `doctor --lint` read-only findings; `--json` advisory read-only; `--fix` applies repairs | GUIDE 3, RUNBOOK 2, 10 (trees C1, C2), 11 |
| F12 | `doctor --json` reports configured unknown providers | 10 (tree C2) |
| F13 | `models scan` reads OpenRouter public free catalog; `--no-probe` metadata-only, no key; probing/set variants need key | GUIDE 9.3, 11 |

## 3. Local evidence note

The first-person line in [GUIDE.md](GUIDE.md) section 1 (a real installation with a configured primary, a fallback chain, aliases, an override allowlist, and several authentication profiles for one provider, all coexisting) comes from the sanitized local evidence in the research packet. It names no providers, no counts, no values, and no host details, and it is presented as evidence that the surfaces coexist, never as a template.

## 4. Unresolved items (carried from the Phase 1 lock; do not guess past them)

- **U1, isolation fixture.** Resolved in Phase 3 validation (2026-09-17). A disposable fixture using a clean environment plus the documented `OPENCLAW_STATE_DIR` override proved sufficient for config commands: the CLI resolved its config file inside the fixture, and the live config file was byte-identical before and after every fixture test. Auth-store mutations could not be exercised even in the fixture: the CLI compares its selected state and config paths with the installed Gateway service and refuses the write on divergence (its message states no credentials or configuration were written), which is the documented safety behavior. Auth order and other auth-store commands are therefore validated from installed help, docs, and read-only error text only.
- **U2, sanitized output shapes.** `models status --json` and `auth list` output shapes were verified during research on 2026-09-17 but not re-inspected during drafting or Phase 3 validation, to keep credential metadata out of the workspace. Reader content deliberately teaches no `models status --json` examples (the credential-metadata-risk command); Phase 3 validated the documented section behavior with sanitized keyword-presence checks on live runs instead, and `doctor --json` appears only as a documented read-only diagnostic in the troubleshooting tree.
- **U3, personal model accounts.** `models accounts` exists and is out of package scope. It appears only as a terminology guard (auth profiles are a different surface).
- **U4, provider-side billing evidence.** Provider dashboards and invoices are outside the package's command procedure because interfaces vary. The package now states that OpenClaw auth evidence can establish preference, pins, health, and eligible rotation paths, but provider records may be required to confirm the exact account charged.
- **U5, release identity.** Final public filenames, repository name, and the release address are Phase 8 decisions. The release candidate now names the fixed expected address https://github.com/xbillwatsonx/openclaw-model-routing-guide in place of the earlier URL placeholder (see [12-prompts.md](12-prompts.md)); the repository is not yet created, and final publication remains pending.

## 5. Deliberate exclusions (claims considered and excluded, with reasons)

1. **Whether config changes require a gateway restart:** no verified evidence row covers it. Readers are taught to confirm effective state with read-only commands instead.
2. **Exact `models status` section names:** described functionally from `docs/cli/models.md` rather than quoting unverified output layout.
3. **`models status --json` examples in reader content:** excluded to avoid credential-metadata risk (U2); `--check` exit codes carry the machine-readable story instead. (`doctor --json` remains in the troubleshooting tree as a documented read-only diagnostic.)
4. **Prices, model rankings, savings numbers, and guarantees:** excluded by specification; catalogs and prices change independently of any guide (B8).
5. **Credential entry:** excluded by specification. Only safe inventory and verification concepts are taught.
6. **`models list --refresh` in workflows:** starts provider discovery; plain list suffices, and discovery is a deliberate maintenance act, not a step in the procedure.
7. **`models status --probe` in workflows:** live provider requests with token and rate-limit cost; plain status answers every question this procedure asks.
8. **Retry and cooldown knob instructions:** mention-only (E5, C10, C11); any change to them is a proposal, never a walk-through.
9. **`doctor --fix` walk-through:** documented from help only ("applies repairs"); its use in the legacy-credentials branch is a proposal-gated mention (C13).
10. **Provider-specific dashboard instructions:** out of scope (U4). The package identifies provider usage or billing records as the authoritative final check when exact charge attribution matters, without inventing provider-specific steps.

## 5A. Phase 5 repair evidence, 2026-09-17

Installed OpenClaw 2026.9.4 help was rechecked for `models status`, `models list`, `models auth order set`, `config set`, `config patch`, and `doctor`. The repairs distinguish testing modes, bound auth evidence, and retain provider records as the exact-charge boundary. No live config, auth, provider, probe, or session change was performed.

## 5B. Private semantic-routing lab evidence, 2026-09-29

The optional semantic-routing material is based on private lab evidence, not an OpenClaw product guarantee. The dedicated lab agent used the documented `before_model_resolve` and `model_call_started` hooks, a Jev classifier through OpenRouter, metadata-only logs, a frozen stable response-model field, explicit opt-in scope, one classifier attempt, a 0.8 confidence floor, daily budgets, a local kill switch, and run-id route correlation.

Measured active-canary evidence: 10 successful routine overrides to the approved low-cost executor, 10/10 correct deterministic answers, 10/10 correlated executor routes, two classifier timeouts that safely returned no override, and zero rate limits. Successful classifier latency was 394.5 ms median and 1425 ms maximum. The 1500 ms deadline was specific to this experiment. Main-agent routing, broader agents, Standard-tier activation, and production activation were not tested or approved.

Private evidence files are kept outside this public package. Public text states only the bounded result and its limitations; it includes no local paths, session keys, credentials, prompts, or raw logs.

## 5C. Private Phase 6 natural-task shadow evidence, 2026-09-29

The Phase 6 shadow evaluation tested whether the Phase 5 synthetic routine result generalizes to natural operator tasks. It used a dedicated lab agent in shadow mode with a four-tier single-prompt policy, a 0.8 confidence floor, a 1500 ms deadline, and an engaged kill switch.

Measured evidence: 9 of 16 cases attempted; 6 accepted decisions, 3 abstentions; routine accepted precision 4/6 (66.7%); no classifier API errors; p50/p95/maximum latency 310/491/491 ms; 9/9 frozen-model matches; 9/9 route correlations; zero unexpected overrides.

Stop condition at case 9: N009 (human-labeled complex) predicted standard at confidence 0.43, abstained, no override issued. This was a raw under-classification, not an accepted under-route. N007 (human-labeled standard) predicted routine at confidence 0.93, crossed the confidence floor, but in shadow mode issued no override; this was a routine-precision warning for routine-only routing.

Assessment: the current four-tier single-prompt policy did not generalize reliably from synthetic to natural tasks. The main weakness is tier-definition and task-boundary ambiguity, compounded by the classifier seeing only the current prompt rather than consequences, tools, or full workflow context.

Private evidence file is outside this public package. Public text states only the bounded result and its limitations; it includes no local paths, run IDs, session keys, credentials, prompts, or raw logs.

## 5D. Reader-facing case study boundary, 2026-09-29

GUIDE.md section 7.5 presents the 5B and 5C evidence as an explicit reader-facing case study, named for the classifier the lab used, and every number there traces to 5B or 5C above. The classifier (Jev, a public OpenRouter model), the tier labels, and the aggregate measured numbers appear in reader text. The executor models, internal project identifiers, verbatim task prompts and expected answers, local paths, run ids, counters, and raw logs do not; reader text describes task categories only. The case numbers N007 and N009 appear in GUIDE.md and [15-changelog.md](15-changelog.md) as neutral sequential case labels only. No cost figure from either phase appears in reader text, because comparable executor billing evidence was never collected; savings remain inconclusive. The 0.8 confidence floor and the 1500 ms deadline are presented as lab-specific values, not defaults. GUIDE section 7.4 stays model-neutral so the generic semantic-routing guidance remains reusable beyond Jev.

## 6. Version labeling convention

Every version-sensitive claim in reader-facing files carries an inline "(checked against OpenClaw 2026.9.4, 2026-09-17)" label or sits under a section-level version note. Stable operating principles (inventory before change, one change at a time, separate model-route evidence from billing confirmation, fallback is turn-local, bounded recovery before failover, stop rules over blind retries) are taught without per-claim recheck requirements but still trace to the rows above. When OpenClaw's version moves, recheck via [11-maintenance-checklist.md](11-maintenance-checklist.md) section 2.
