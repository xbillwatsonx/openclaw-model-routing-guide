# The OpenClaw Model Routing Guide

The main guide of The OpenClaw Model Routing Guide + Companion Runbook package. The step-by-step procedure lives in [RUNBOOK.md](RUNBOOK.md). Every fill-in file is listed under How this package fits together, below.

**What this is.** A complete guide to model routing for people who run their own OpenClaw. Model routing is the set of decisions that decide which AI model handles your work, what happens when that model can't, and which credential pays for it. You'll learn the whole picture, then make one careful change at a time with a rollback path.

**Who this is for.** The self-hosted operator. You installed OpenClaw yourself, you can use a terminal and edit configuration files, and you want to understand your system, not just paste commands into it. You don't need a programming background.

**What you'll be able to do.** After this package you can:

- inventory your providers, models, credentials, and billing paths without exposing a single secret
- classify your work by difficulty, risk, value, privacy, and tool needs
- choose a primary model and specialist routes deliberately, with reasons written down
- define fallback and retry boundaries with stop rules
- make one controlled change with a recorded baseline and a rollback path
- test candidate models with matched tasks and a shared scorecard instead of model reputation
- verify which model route OpenClaw reports, inspect the intended credential route, and know when provider billing records are needed
- keep, revise, or roll back based on recorded evidence, then maintain your policy over time

**When you don't need this.** Skip this package if:

- you run one model from one provider, you've never pinned a model in a chat, and you don't want fallbacks. Your routing is one line and it already works.
- you came for credential setup. This package deliberately doesn't teach entering credentials. OpenClaw's own setup documentation covers that.
- you want to spend less money. The OpenClaw Cost Savings Guide owns that topic: why routing affects cost and how to measure it. This package is the how-to companion that shows you how to actually design, change, test, and roll back routing.
- you want developer reference material. This is operator guidance, not an API reference.

**Version note.** Every key, count, flag, and command in this package was checked against OpenClaw 2026.9.4 on 2026-09-17. OpenClaw changes, and provider catalogs and prices change even faster. Run `openclaw --version` first and compare. If your version differs, treat the dated details in this package as unverified until you recheck them against the documentation installed with your copy. Facts labeled version-sensitive are the ones most likely to drift.

## How this package fits together

Read this guide top to bottom once. After that, the runbook is your operational checklist and the numbered files are the ones you fill in.

| File | What it is |
| --- | --- |
| [GUIDE.md](GUIDE.md) | This guide. The ideas, the moving parts, and the safe method. |
| [RUNBOOK.md](RUNBOOK.md) | The 13-step companion procedure. It does the work with you, one approved change at a time. |
| [01-inventory-worksheet.md](01-inventory-worksheet.md) | Record your providers, models, credentials, and billing paths with read-only commands. |
| [02-task-classification-worksheet.md](02-task-classification-worksheet.md) | Classify your recurring work by difficulty, risk, value, privacy, and tool needs. |
| [03-routing-policy-template.md](03-routing-policy-template.md) | Write your routing policy before you touch configuration. |
| [04-config-proposal-template.md](04-config-proposal-template.md) | Capture one change: scope, baseline, rollback, verification, stop conditions, approval. |
| [05-fallback-retry-template.md](05-fallback-retry-template.md) | Design your fallback order, retry expectations, and stop rules. |
| [06-test-plan.md](06-test-plan.md) | Plan matched-task tests with the same inputs for every candidate. |
| [07-scorecard.md](07-scorecard.md) | Score every candidate with the same rubric and record the route each run took. |
| [08-route-verification-checklist.md](08-route-verification-checklist.md) | Verify the reported model route, inspect auth routing evidence, and separate it from provider billing confirmation. |
| [09-rollback-checklist.md](09-rollback-checklist.md) | Restore your recorded baseline without reconstructing anything from memory. |
| [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md) | Classify a failed or surprising route from evidence, branch by branch. |
| [11-maintenance-checklist.md](11-maintenance-checklist.md) | Recheck version-sensitive details and keep your policy current. |
| [12-prompts.md](12-prompts.md) | Eleven copyable prompts that bridge you to your agent, safely. |
| [13-source-notes.md](13-source-notes.md) | Where every factual claim in this package comes from. |
| [14-glossary.md](14-glossary.md) | Every term this package uses, in plain language. |
| [15-changelog.md](15-changelog.md) | Package version, OpenClaw baseline, and change history. |
| [LICENSE.md](LICENSE.md) | The package license. |
| [README.md](README.md) | Package identity, contents, and version metadata. |

**Need a simpler explanation?** Some of this is dense on a first read. If you'd rather have your agent walk you through it, copy Prompt 1 from [12-prompts.md](12-prompts.md) or from section 10 of this guide. It asks your agent to read this guide first and then explain it section by section in plain language, with definitions, small examples, and comprehension checks, and to take no action on your system without your explicit approval. Until the public repository is live, prompts name the fixed public address and tell your agent to read your local copy of the package if that address is not live yet.

## 1. What model routing is and why one model name is not the whole route

You set a model. Then one day a different model answers, and you don't know why. Or a run fails and you can't tell what was actually tried. Or a bill arrives that doesn't match the model you thought you were using.

All three moments come from the same root cause. A model name is only one part of a route. In OpenClaw, routing is six linked decisions, and they stay linked whether or not you've ever looked at them:

1. **The primary model.** Your configured starting point for runs.
2. **The fallback order.** What takes over when the primary can't handle a run.
3. **The model-selection policy.** The override allowlist that says which explicit model selections are permitted.
4. **The authentication profiles and their order.** Which saved credentials OpenClaw may use, and in which order it prefers them.
5. **The billing path.** Which credential actually pays for a given run. This can differ from what the model reference suggests, in ways that surprise people.
6. **The session-level overrides.** What a single chat session has pinned, separately from your configuration.

If you only ever think about number 1, the other five still exist and still act. That's how you get a different model answering, a mysterious failure, or a bill that doesn't match.

On the real installation this package was written against, a configured primary, a fallback chain, aliases, an override allowlist, and several authentication profiles for one provider all exist at once. I mention that not as a template for your setup, but as evidence: these surfaces coexist and interact, so the way to stay in control is to see them separately.

The good news is that this is very learnable. The whole method in this package is one loop:

1. Inventory what you have, without exposing secrets.
2. Classify your work.
3. Write a routing policy in words, before touching configuration.
4. Capture a baseline and a rollback point.
5. Make one approved change, using an exact dry-run where supported or a documented substitute preflight plus immediate read-only verification.
6. Test it with matched tasks.
7. Verify which route actually handled the work.
8. Keep, revise, or roll back, based on recorded evidence.
9. Maintain the policy as versions, catalogs, and prices change.

The companion [RUNBOOK.md](RUNBOOK.md) turns that loop into 13 concrete steps. This guide explains the ideas underneath each step.

## 2. Models, providers, credentials, billing paths, sessions, and fallbacks

This section is the map of the territory. Every term gets defined here the first time it appears, and the glossary at the end repeats the same definitions.

*Version note: the configuration keys, counts, and flags in this section were checked against OpenClaw 2026.9.4 on 2026-09-17.*

### 2.1 Model references and providers

A **model** is the AI system that handles your work. A **model reference** is the `provider/model` string you use everywhere to point at one: commands, configuration, chat picks. Section 1 called this the model name; from here on, this package says model reference. A **provider** is the service that serves the model: an API vendor, a subscription service, or a local runtime.

Two details worth knowing:

- References split on the first slash. Some OpenRouter-style ids contain another slash after the provider prefix, so `provider/model/variant` still reads as provider plus model id.
- The provider part matters as much as the model part. The same model id under a different provider is a different route: different credentials, different billing path, sometimes different behavior.

### 2.2 The primary model and the fallback chain

Your **primary model** is the configured starting point. It lives at `agents.defaults.model.primary`, or at `agents.defaults.model` written as a plain string (checked against OpenClaw 2026.9.4, 2026-09-17).

The **fallback chain** is the ordered list at `agents.defaults.model.fallbacks`, tried in order (checked against OpenClaw 2026.9.4, 2026-09-17). A configured default normally uses its fallback chain when it can't complete a run on its own.

When a run needs a model, OpenClaw builds a **candidate chain**:

- the requested model first
- your explicit fallbacks next, deduplicated and tried in order, and not filtered by the override allowlist
- the configured primary appended when no override was supplied

An empty fallback override disables fallback for that run. (Candidate chain behavior checked against OpenClaw 2026.9.4, 2026-09-17.)

Inside a provider, OpenClaw rotates authentication profiles before it moves to the next eligible fallback model. So the order of trying is: same model with another credential first, then the next model in the chain.

And one detail that clears up a lot of confusion: fallback execution is **turn-local**. If a run falls back to a different model, that does not silently become the session's model. When the origin recovers, OpenClaw reprobes it, clears the temporary fallback state, and announces the transition. Fallback notices like Model Fallback and Model Fallback cleared are delivered once per state change, and not in group or channel conversations. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

### 2.3 Strict and non-strict selection

Some selections are **strict**: if the model fails, the run fails visibly instead of silently falling back.

- A **session selection** you make (with `/model`, a picker, or the API) is exact and strict. If it fails, you see the failure. OpenClaw does not quietly answer from an unrelated fallback.
- A per-agent primary is strict unless that agent's model object explicitly includes fallbacks. An empty fallback list, `fallbacks: []`, makes strict explicit.
- A cron job's model is that job's primary. It normally uses the configured fallbacks, unless the job's own payload supplies a fallback list. An empty list makes that job strict.

(Checked against OpenClaw 2026.9.4, 2026-09-17.)

Strictness is a feature, not a bug. When you test a specific model, strictness means a failure is information about that model, not a silent detour.

### 2.4 Selection scopes

The `/model` chat command writes at three scopes:

- `-s` sets the session only
- `-a` sets the agent default
- `-g` sets the global default

Writes that change configured defaults require owner or admin authority. Unscoped writes follow `agents.defaults.modelSelectionScope`, whose default is session. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

Changing the configured primary does not rewrite existing session pins. A fresh unpinned session is the safe way to validate the new default. `/model default -s` clears a session model selection but can leave a compatible auth-profile pin; this package does not supply an installed-version procedure to clear or restore that exact pin. If one is unavailable, preserve the session, record `unsupported`, and do not claim recovery complete. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

### 2.5 Credentials: authentication profiles

An **auth profile** is a saved credential setup OpenClaw uses for provider requests. Profiles come in three credential types: an API key (a secret string the provider issues you), OAuth (a signed-in account connection to the provider), and a static token (a fixed access string). Profile ids look like `provider:default`, or `provider:email` for OAuth profiles that carry an email. That means profile ids can contain an email address, so treat every profile id as private. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

Secrets and runtime auth-routing state live in the per-agent SQLite **auth store**, documented at the path pattern `~/.openclaw/agents/<agentId>/agent/openclaw-agent.sqlite`. The `auth.profiles` and `auth.order` entries in your configuration are routing metadata, not secret storage. That separation matters: editing your configuration never touches your secrets, and your secrets never sit in a file you might copy-paste somewhere. (Path and separation checked against OpenClaw 2026.9.4, 2026-09-17.)

One terminology guard, because it trips people: OpenClaw also has personal model accounts, managed with `openclaw models accounts`. Those are Gateway person-owned accounts, a different surface from the system and agent auth profiles. This package works with auth profiles only and doesn't cover personal model accounts.

This package also doesn't teach entering credentials. If your inventory in section 3 comes up empty, adding credentials is OpenClaw setup work: use OpenClaw's own documentation and onboarding for that, then come back here.

### 2.6 Profile order and session stickiness

When one provider has several auth profiles, which one is used first? The **profile order** works like this:

1. a stored per-agent order override, if one exists
2. then the `auth.order` configuration
3. then configured profiles
4. then stored profiles

With no explicit order anywhere, OpenClaw rotates profiles in documented tiers: OAuth profiles first, then static tokens, then API keys; a usable token wins over an unusable one; the oldest `lastUsed` goes first; profiles in cooldown go last. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

**Session stickiness** is the other half. When OpenClaw automatically picks an auth profile, it pins that profile for the session. The pin may rotate or clear on session reset, context compaction (when OpenClaw compresses older conversation history to manage space), or cooldown. A pin you set yourself, with `/model <ref>@<profileId> -s`, survives resets as long as the profile stays eligible. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

### 2.7 The billing path

Here is the idea that saves people the most surprise: a model reference alone does not prove which credential or billing path paid for the request. OpenClaw's authentication state can show your configured order, session profile pin, profile health, and fallback possibilities. Because temporary profile rotation can occur during a run, those surfaces do not always prove which credential was ultimately charged. Provider usage or billing records are the authoritative confirmation when that distinction matters.

The clearest example is OpenAI. Authentication and runtime are separate there, so an `openai/gpt-*` model can rotate auth between a ChatGPT or Codex subscription profile and an API-key profile. Same model reference, two different bills. If you have both profiles, explicit `auth.order` controls which one OpenClaw prefers. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

So "which model route did OpenClaw report" and "which account was charged" are two different questions. Section 8 shows what OpenClaw can verify with read-only evidence and when you need provider-side records for the final billing answer.

### 2.8 The override allowlist and the model settings map

Two configuration surfaces look alike and do completely different jobs. Keeping them straight prevents a whole category of confusion.

The **override allowlist** is `agents.defaults.modelPolicy.allow`. It governs which explicit model selections are permitted. It accepts exact references and trailing provider wildcards (an entry that permits every model from one provider). If it's omitted or empty, any model may be explicitly selected, subject to provider availability, runtime compatibility, and authentication. A per-agent policy replaces the default policy for that agent. One caution: an unrestricted policy doesn't make an unknown provider or an unsupported runtime usable. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

The **model settings map** is `agents.defaults.models`. It stores aliases and per-model settings. An **alias** is a short name for a model reference. Adding an entry to the model settings map never restricts model overrides: it is not the allowlist. `openclaw models aliases add` writes to the settings map and never changes `modelPolicy.allow`. `openclaw models set` writes the primary and may canonicalize related settings; its documented plugin-repair side effect means this package excludes it from the one-change workflow. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

Short version: `modelPolicy.allow` decides what may be selected. `agents.defaults.models` stores names and settings. Never call the settings map an allowlist, and never expect adding an alias to restrict anything.

### 2.9 The catalog and the published inventory

Chat surfaces like `/model`, `/models`, and pickers browse the hosted model **catalog**. Browsing does not start provider discovery. Acquiring fresh inventory is an explicit act.

The **published inventory** is the model inventory OpenClaw has discovered and listed for you. `openclaw models list` reads it, and reading it never rewrites the inventory. `--refresh` starts provider discovery; a failed refresh warns you and keeps the rows that are available. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

One more dated detail: by default, list and picker views hide deprecated or disabled catalog rows, except for references you've configured. `--all` shows the full catalog. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

### 2.10 The six decisions and where each lives

Here's the whole section as one table. This is the map you'll use for everything that follows.

| Decision | Where it lives | How you check it (read-only) |
| --- | --- | --- |
| Primary model | `agents.defaults.model.primary` in configuration | `openclaw models status` |
| Fallback order | `agents.defaults.model.fallbacks` in configuration | `openclaw models fallbacks list` |
| Model-selection policy | `agents.defaults.modelPolicy.allow` in configuration | `openclaw config get agents.defaults.modelPolicy.allow` |
| Auth profiles and order | Auth store plus `auth.order` in configuration | `openclaw models auth list`, `openclaw models auth order get --provider <id>` |
| Billing path | Effective auth state at run time | `openclaw models auth list`, `/model status` in the session |
| Session-level overrides | The session itself | `/model status` in the session |

Six decisions, six homes, six checks. None of them is interchangeable. When something surprises you later, come back to this table and ask: which of the six am I actually looking at?

## 3. Inventory what is available without exposing secrets

You can't design a route through territory you haven't mapped. So the first operational step is an inventory, and the rule for the whole inventory is this: read-only commands only, and nothing secret gets written down.

*Version note: every command in this section was checked against OpenClaw 2026.9.4 on 2026-09-17. All of them run in a terminal, on the machine that hosts your OpenClaw gateway, unless the card says otherwise.*

**Privacy rules first.** I'm going to ask you to keep secrets out of your notes, because notes travel:

- `openclaw models status` output can include credential metadata and redacted credential fragments. Never paste raw status output into a public chat, issue, or forum.
- Profile ids can contain email addresses. Treat them as private.
- Record only what the [01-inventory-worksheet.md](01-inventory-worksheet.md) asks for. If a value isn't on the worksheet, it doesn't need to be written down.
- Screenshots of chat commands go through the same rule: redact emails and profile ids before sharing anything.

### The inventory commands

**`openclaw --version`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the installed OpenClaw version.
- Record: the full version string. Every later fact in this package is checked against a specific version, so yours matters.

**`openclaw models status`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: your configured default model, the fallback chain, and an authentication overview. Its sections answer different questions, so read them separately: what's configured, and what's healthy.
- Record: the configured primary and fallback list for your baseline notes.
- Watch out: output can contain credential metadata and redacted credential fragments. Sanitize before sharing, and never paste raw output publicly.

**`openclaw models status --check`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: a readiness exit code. An **exit code** is the number a command reports when it finishes. Here, 0 means clean, 1 means an issue, 2 means an expiring credential. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- Record: the exit code and the date.
- Boundary: exit 0 does not prove a request will succeed. Readiness and catalog metadata can remain clean while a later provider request fails.

**`openclaw models list`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the published inventory: the models OpenClaw currently lists for you.
- Record: the providers and models you actually might use, in the worksheet.

**`openclaw models list --all`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the full catalog, including deprecated or disabled rows that the default view hides.
- Watch out: deprecated or disabled rows are usually noise. You want `--all` when something you expected is missing from the default view, not as your everyday list.

**`openclaw models fallbacks list`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: your current text-model fallback chain, in order.

**`openclaw models image-fallbacks list`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: your current image fallback chain. This is a parallel, separately managed list with the same command shape as the text fallbacks. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

**`openclaw models aliases list`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: your saved aliases from the model settings map.

**`openclaw models auth list`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: your auth profile ids and their health, without printing secret material. Cooldown and disable entries include their reason and recovery action. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- Record: profile ids and health states only. Never write down key or token values; the command doesn't print them, and you shouldn't go looking.
- Watch out: profile ids can contain emails. Treat the output as private.

**`openclaw models auth order get --provider <id>`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the stored order override for the provider you name, if one exists. The `--provider` option is required; run the command once per provider with more than one profile.

**`openclaw config get <path>`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: the value at a configuration path. Use it for routing keys like `agents.defaults.model` or `agents.defaults.modelPolicy.allow`.
- Watch out: never point it at secret-valued paths. Routing keys only.

**`openclaw config validate`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: whether your current configuration validates against the schema (the documented shape your configuration must match), without starting the gateway. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

**`openclaw doctor --lint`**

- Where it runs: terminal, on the gateway machine.
- Type: read-only.
- What it reveals: health findings without applying repairs. Findings can include local paths, so sanitize before sharing. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- Watch out: there is a `--fix` flag. It applies repairs and migrations, so it's a **mutation** (a command that changes your configuration, state, or data), not an inspection. This package never runs it without a proposal and your approval.

**Chat commands, in a session with your agent**

- `/model status`: shows the session's model selection and the auth candidates per provider.
- `/status`: shows the selected model and, when fallback state differs, the active fallback model and the reason.
- `/model list`: browses models by provider (the same view as `/models`).
- Type: chat command, session inspection.
- Watch out: sanitize screenshots; never show emails or profile ids.

Two commands you should know exist but that this package deliberately doesn't use:

- `openclaw models status --probe` performs live provider requests. It may consume tokens, trigger rate limits, and expects the gateway stopped with exclusive state access. Plain `models status` answers every question this procedure asks. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- `openclaw models list --refresh` starts provider discovery. A plain list reads what you already have; leave discovery for a deliberate maintenance decision.

Fill everything into [01-inventory-worksheet.md](01-inventory-worksheet.md) as you go. The worksheet's sanitization rules are part of the inventory, not an afterthought.

## 4. Classify work by difficulty, risk, value, privacy, and tool needs

Once you know what you have, the next question is what your work actually needs. This is the step people skip, and it's the step that makes every later decision defensible.

Classify your recurring work on five dimensions. You're not classifying everything you've ever typed, just the work that repeats: the daily summaries, the weekly analysis, the coding help, the document questions, the image generation, whatever shows up week after week.

- **Difficulty.** Is it routine (a capable general model handles it comfortably) or complex (it needs long context, careful reasoning, or specialized knowledge)?
- **Risk.** What breaks if the output is wrong? A wrong dinner suggestion is annoying. A wrong config edit or a wrong number in a report is expensive.
- **Value.** What is the work worth when it goes right? High-value work justifies a deliberate route and real testing.
- **Privacy.** What data does the task touch, and where are you willing for it to go? This is where the billing path and the provider choice earn their keep. A task that touches private files may deserve a provider you trust with that data, and that decision is yours to make consciously.
- **Tool needs.** Does the task involve only text? Images going in? Images, PDFs, or media coming out? Delegated helper sessions (subagents)? Tool needs point at the specialist routes in section 5.

Classification feeds everything downstream:

- High-risk work argues for strict selection and explicit stop rules, not silent fallbacks.
- Private work argues for a conscious provider and billing path decision.
- Routine, high-volume work is where a deliberate cheaper route can pay off, if your matched-task tests say quality holds.
- Tool needs tell you which specialist routes you actually have to configure, versus read about.

One rule from the start: no model names in the classification. Not in the worksheet, not in your head. Classification describes the work. Choosing models comes later, from evidence. Model reputation is a poor predictor of how a model handles your specific tasks. Section 7 shows you how to get real evidence.

The worksheet for this is [02-task-classification-worksheet.md](02-task-classification-worksheet.md).

## 5. Choose a primary route and specialist routes

Now you're ready to make choices, on paper first. The output of this section is a routing policy: a written document, not a config edit. The template is [03-routing-policy-template.md](03-routing-policy-template.md).

*Version note: the configuration keys in this section were checked against OpenClaw 2026.9.4 on 2026-09-17.*

### 5.1 Choosing the primary

Your primary model is the workhorse: the starting point for most runs, most days. Choose it from your inventory and classification together:

- It should be able to handle the middle of your difficulty range comfortably, with your harder tasks covered either by it or by a conscious decision to route them differently.
- It should be reachable through credentials you actually have and a billing path you understand.
- It should be a choice you can write a reason for. If the only reason is "everyone says it's good", that's a sign to test it properly in section 7 instead of trusting the reputation.

Write the reason down. Six months from now, "why is this my primary?" is the first question any incident will ask.

### 5.2 Specialist routes: separate surfaces for separate work

Some kinds of work get their own model settings, separate from the primary. These are the **specialist routes**. Each one is its own policy decision, with its own place in configuration. None is part of the text-model fallback chain you'll design in section 6; they're parallel surfaces. This package version supports only their read-only inventory and provider-boundary policy design. It lacks authoritative route-specific execution evidence and recovery for specialist, media, per-agent, cron, and subagent changes: do not modify them here, do not send sensitive inputs to an unset/inferred/unverified destination, and do not claim text `/status` proves them. Use a future route-specific procedure.

- **Utility model** (`agents.defaults.utilityModel`). Routes short internal tasks to a model you designate for them. When unset, the provider's declared small-model default applies, and if there is none, the primary. An empty string disables utility routing. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- **Image model** (`agents.defaults.imageModel`). Applies when the primary model can't accept images. You set it with `openclaw models set-image <ref>` (a mutation; runbook rules apply). It has its own parallel fallback list at `agents.defaults.imageModel.fallbacks`, managed with the same command shape as text fallbacks: `openclaw models image-fallbacks add`, `remove`, `clear`, `list`. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- **PDF model** (`agents.defaults.pdfModel`). Backs the PDF tool. When unset it falls back to the image model, then to the session or default model. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- **Media models** (`agents.defaults.mediaModels`). Image, music, and video entries back the media generation tools. Tools you leave unset infer auth-backed provider defaults, with cross-provider fallback. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- **Subagent model** (`agents.defaults.subagents.model`). A **subagent** is a helper session your agent can spawn for delegated work. Native subagents inherit the caller's model unless this setting, a per-agent subagent model, or an explicit spawn-time model overrides it. An invalid spawn-time model value falls back with a warning. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

Per-agent model configuration can also override the shared default for a specific agent. If you set a per-agent primary, remember the strictness rule from section 2.3: that agent is strict unless its model object explicitly includes fallbacks.

For each specialist route, the policy question is the same: which provider destination could handle this kind of work, which data class is approved there, and what happens if it is unset or inferred? If you don't have authoritative evidence, write `unverified` and stop, sanitize, or use approved local processing. "Unset, using defaults" is an inventory fact, never a sensitive-data approval.

## 6. Design fallback, retry, and stop rules

This section designs the text-model chain: your fallback order and your expectations about retries. The specialist routes from section 5 keep their own settings; this design doesn't cover them.

*Version note: every count, duration, and key in this section was checked against OpenClaw 2026.9.4 on 2026-09-17. These are exactly the facts most likely to change between versions.*

### 6.1 What fallback covers, and what it doesn't

OpenClaw moves to the next eligible fallback for these failure classes:

- authentication failures
- rate limits
- overloaded providers
- timeout-shaped failures
- billing disables
- model not found, including an eligible 404
- request-size ceiling hits

These classes do not advance fallback: context overflow, explicit aborts, and final provider refusals. A final refusal ends the turn. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

That list is worth internalizing, because it kills a common wrong belief: fallback is not a universal safety net. If your context is too big, no fallback saves the run. If a provider flatly refuses, no fallback answers for it.

### 6.2 What OpenClaw already does before falling back

Before rotating credentials or models, OpenClaw runs bounded same-model recovery. A **retry** is another attempt at the same work on the same model after a failure; the recovery continues the existing transcript, preserves completed work, and instructs inspection of interrupted actions before retrying. You see a retry indicator while it works, and the run stays cancellable. (Bounded recovery principle verified; details checked against OpenClaw 2026.9.4, 2026-09-17.)

The built-in **retry budget**, checked against 2026.9.4:

- Rate limits receive up to 10 total attempts.
- Other transient failures allow eight retries within a 90-second window.
- Backoff starts near one second, grows exponentially with jitter, and caps at 30 seconds.
- Provider retry-after hints set minimum waits.
- Billing failures, authentication errors, and provider refusals do not use the transient retry budget.
- An exhausted subscription usage window goes directly to eligible fallback.

A **cooldown** can follow certain failures: a temporary pause on a credential or model. Regular cooldowns scale 30 seconds, 1 minute, 5 minutes. The rate-limit bucket is broader than plain 429 responses. Model-scoped cooldowns still allow sibling models under the same provider. A **billing disable** uses an initial ten-minute window, and an exhausted **usage window** sends the run to fallback. (All checked against OpenClaw 2026.9.4, 2026-09-17.)

You describe these effects in your policy. You never hand-edit the internal state behind them; there is no supported story for that, and a hand edit can make things worse in ways nobody can diagnose later.

### 6.3 The knobs, and why you mostly shouldn't touch them

These exist, they're documented, and this package teaches them as mention-only facts, not as steps:

- `retry.provider.maxRetries` overrides the recovery budget per session setting. Zero disables retries. Rate limits remain capped. It is an embedded session setting, not an openclaw.json key. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- `OPENCLAW_FALLBACK_SKIP_TTL_MS` is an opt-in cache that skips recently failed authentication for a while. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- `OPENCLAW_SDK_RETRY_MAX_WAIT_SECONDS` caps SDK retry-after waits, at a 60-second default. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

If your policy needs one of these, it goes through the same proposal, dry-run or documented plan, approval, and rollback path as any other change. The defaults are bounded and sane; change them for a reason you can write down, or not at all.

### 6.4 Stop rules: the boundary the machine won't draw for you

Here's the part no configuration key does for you. OpenClaw's recovery budgets are bounded, but they are not workflow-level attempt limits. If you keep sending a task at a model that keeps failing weakly, the built-in budgets will happily let you try again, and again, each attempt costing time or money or both.

So your policy needs stop rules, written by you, in advance:

- After how many failed or weak attempts on the same approach do I stop and record evidence instead of retrying?
- When do I escalate to a person, or switch strategy entirely?
- What evidence do I collect at the stop point, so the troubleshooting tree in [10-troubleshooting-decision-tree.md](10-troubleshooting-decision-tree.md) has something to work with?

A good default shape: stop, capture what happened, classify it, then decide with a cool head. Repeated weak retries are not persistence; they're an unrecorded incident.

### 6.5 What fallback promises, honestly

A fallback chain does not guarantee completion, low cost, privacy, or correctness. It is a bounded, ordered set of attempts across models and credentials. Sometimes every candidate fails. Sometimes the fallback that answers is one you'd rather have controlled consciously. That's why the order is a policy decision, and why section 8 teaches you to verify the route rather than assume it.

Write your design into [05-fallback-retry-template.md](05-fallback-retry-template.md), and fold the summary into your routing policy.

### 6.6 Before any change: the dry-run habit

When the policy is written and a specific change comes out of it, the runbook validates the change shape before applying it:

- `openclaw config patch --file <path> --dry-run` validates a patch object (schema and secret-reference checks) without writing openclaw.json.
- `openclaw config set <path> <value> --dry-run` validates a single set, also without writing. One dated nuance: in plain value mode it does not run full schema checks, and the CLI tells you so in its output. Use `--strict-json` for JSON values to turn those checks on. Model-reference paths are still reference-checked in value mode.
- Expectation guards (`--expect-current-absent`, `--expect-current-json`) make the actual set refuse to write when the current value isn't what you recorded in your baseline. That catches "someone changed this since I looked". They are write-time checks: they cannot be combined with `--dry-run`, and they fire when the real set runs.

(All checked against OpenClaw 2026.9.4, 2026-09-17.)

A **dry-run** is a check, not a change. Use an exact dry-run where installed help supports one. For other mutations, use the documented substitute preflight and immediate read-only verification; never label either as a dry-run.

Config writers have an additional boundary: successful OpenClaw-owned writes can reserialize JSON5 as standard JSON and remove comments. Before any configuration write, record the active file (`openclaw config file`), byte-level hash, private byte-for-byte backup identity/hash, baseline key state, and JSON5/comment preservation decision. After the write, compare the complete file with the baseline; an expected key-only difference is required, and unrelated drift is a stop. If a key was absent at baseline, verify that fact and use the installed dry-run form `openclaw config unset <path> --dry-run` before an explicitly approved actual unset rollback. Whole-file recovery follows the exact version-matched backup/restore procedure recorded before the change; a missing procedure is a stop.

## 6.7 Mutation validation, not a universal dry-run rule

Not every OpenClaw mutation has a dry-run. Use [04-config-proposal-template.md](04-config-proposal-template.md) section 5: it pairs each common mutation with its exact dry-run where supported or a documented substitute preflight and immediate read-only verification. Session model set and clear are mutations. A bounded set of test-session pins can be approved in one written testing approval only when it names the test sessions, candidates, maximum runs, prior-state capture, and cleanup action.

### Minimal JSON5 patch example

```json5
{ agents: { defaults: { model: { primary: 'provider/model' } } } }
```

This is non-secret and illustrative only. Validate it with `openclaw config patch --file ./routing-change.json5 --dry-run`; expected evidence is successful validation without a write. Equivalent set form: `openclaw config set agents.defaults.model.primary provider/model --dry-run`. Use raw strings only for strings. For arrays or objects, pass valid JSON with `--strict-json`.

## 7. Test models with matched tasks and a shared scorecard

You have a policy. Now you want evidence that a candidate model actually handles your work, not someone else's benchmark of their work.

The method is the **matched-task test**: the same task, the same inputs, for every candidate, scored with the same rubric. The plan separates strict candidate comparison from end-to-end configured-route validation and requires a privacy gate, predeclared budget, and decision criteria. The plan is [06-test-plan.md](06-test-plan.md) and the rubric is [07-scorecard.md](07-scorecard.md).

### 7.1 Setting up a clean comparison

- Pick two or three real recurring text tasks from your classification worksheet. Specialist/scoped routes are excluded from this package's executable test workflow.
- Run every candidate on the identical input. Same prompt, same attachments, same instructions, same order of asks.
- Use a fresh session for each run. Session history contaminates comparisons: a model continuing an ongoing conversation is doing different work than one starting cold.
- Pin the candidate in that session with `/model <ref> -s` (chat command, session scope). Session selections are strict: if the candidate can't handle the run, it fails visibly instead of silently falling back. That's not a problem with your test; that's the test working. A silent fallback would corrupt your comparison.
- In strict candidate comparison, **any** different text model or fallback invalidates the run, even if its output is useful. Record `invalid route`; reserve fallback observations for end-to-end configured-route validation rather than candidate scoring.
- Before sending input, approve every eligible profile, fallback, inferred default, and override for that data class. If any possible destination is unapproved, unset, inferred, or unverified, stop, sanitize, or use a local route.

Run each candidate on each task more than once before concluding anything. Any single run can be lucky or unlucky. Two or three runs per candidate per task is a sensible floor, and more is better for close calls. (That's method advice, not an OpenClaw fact.)

### 7.2 Scoring with the same rubric

Every run gets the same scorecard:

- Did it complete?
- Quality, 1 to 5, with the anchors defined once in [07-scorecard.md](07-scorecard.md): 1 means failed or unusable, 3 means usable with minor fixes, 5 means ready to use as-is.
- Did it follow the instructions?
- Intended/preferred/eligible routing evidence: which model `/status` reports, fallback state, and pinned/preferred/eligible profiles. That evidence does not prove an exact charge or specialist/scoped execution.
- Notes, in your words.

The route evidence column is what turns a scorecard into a routing instrument. "Quality was fine" and "quality was fine, but it came from the fallback while my primary was in cooldown" are different facts about your system.

### 7.3 Reading the results

Compare candidates on the same tasks, and read the pattern, not the average alone. A candidate that's excellent on routine work but fails your high-risk task may still be a great primary, if your policy routes the high-risk task elsewhere. If cost is part of a keep decision, it needs confirmed comparable baseline and changed-route evidence from the Cost Savings Guide; otherwise cost is inconclusive and cannot justify keep. You're not crowning a winner; you're assigning work.

### 7.4 Optional semantic routing, only after deterministic routing works

The routing policy you can inspect directly should remain the baseline. A **semantic classifier** is an optional service that reads a task description and predicts a route before the executor model runs. It can help with varied natural-language work, but it adds another provider call, another failure surface, and measurable delay. Test it as a layer on top of a known-good deterministic route, never as a substitute for one.

A safe semantic experiment needs all of these controls:

- explicit opt-in scope, such as one lab agent or a dedicated session prefix
- an allowlist of possible executor models
- a confidence floor below which the classifier abstains
- one classifier attempt per eligible run, with a client-side deadline
- daily classifier and override budgets
- a local kill switch
- metadata-only decision logs and a run id that correlates each decision with the executor route
- a frozen stable response-model field, checked separately from any per-request decision id
- task-specific shadow testing on natural examples from the exact workload before any scope expansion

The failure rule is **fail-closed**: a timeout, rate limit, malformed answer, version drift, logging failure, or missing control returns no override and preserves the normal OpenClaw route. Semantic availability is not executor availability. A healthy provider can still produce an occasional classifier timeout.

Measure this on your own workload before claiming quality or savings. Record accepted-decision precision, abstention, errors, latency, route correlation, answer quality, classifier cost, and comparable executor cost. If comparable cost evidence is missing, savings are inconclusive.

Two lessons generalize before any lab is named. A policy that passes on synthetic tasks can still fail on natural ones, so revalidate on the real workload before expanding scope. And always record what the classifier predicted separately from what actually ran.

One private lab ran that full control list twice: once live on a narrow, predefined task class, and once in shadow mode on natural tasks. The routing, answer, and latency numbers were recorded; comparable cost evidence was never completed, so the case study makes no savings claim. The next subsection tells that story with the actual measurements, because the two runs together are the honest picture: a narrow pass, then a measured broadening failure.

### 7.5 A worked example: one lab's Jev classifier

Everything in 7.4 is the general pattern. This subsection is that pattern applied once, in a real private lab, with the real numbers. It is a case study, not a recommendation, and none of it is built-in OpenClaw functionality.

**Plain-language result.** Jev successfully sent ten tightly defined practice tasks to one approved lower-cost model. When the same policy met natural operator tasks, it classified only four of six accepted routine decisions correctly and the test stopped early. The useful conclusion is narrow: Jev is a candidate for carefully bounded classifier experiments, not proof that it can safely route arbitrary work. Keep the normal deterministic route in charge, and test any classifier in shadow mode on your own workload before it can change a route.

**What Jev is, and why it was interesting.** Jev is a language model available through OpenRouter. OpenClaw does not ship a semantic classifier; the lab wired Jev in externally through OpenClaw's documented hook events (the extension points where custom code can observe and adjust model resolution). The idea was worth testing because the classifier's job is small: read the incoming prompt, return one tier label with a confidence score, and never do the work itself. The **executor model** is the model that actually handles the task. If a classifier can choose reliably, routine tasks can go to a small executor while everything else stays on the normal route. The catch is the one from 7.4: an extra call, an extra failure surface, and a decision maker that can be confidently wrong.

**Where the classifier sat.** The lab's pipeline, in order:

1. A task arrives at the lab agent.
2. Before the executor model is chosen, the classifier reads the current prompt and returns a tier label (routine, standard, complex, or a fourth label meaning no recommendation) plus a confidence score.
3. Safety gates check the answer: the **confidence floor** (the minimum score needed before a classification can proceed; the lab used 0.8), the opt-in scope, the single-attempt rule and the daily budgets, the frozen model version, the kill switch, and the **executor allowlist** (the only executor models the classifier is permitted to recommend).
4. If every gate passes, the run goes to the allowlisted executor model for that tier.
5. If anything fails or abstains (timeout, low confidence, malformed answer, version drift, spent budget, engaged kill switch, anything out of scope), no override is returned, and the normal deterministic OpenClaw route handles the run as if the classifier did not exist.

That last step is the whole safety story: the deterministic route was the baseline before the experiment and stayed the fallback during it. The classifier could only pick from the allowlist; anything uncertain fell back to the normal route.

**Experiment 1: live routing on a narrow synthetic task class.** The lab started where correctness is cheap to check: a small set of synthetic routine tasks, each with a known expected answer, things like reformatting a short list or extracting a value. Decisions were live, so an accepted routine decision really did change the executor. Every case ran alone, the kill switch was re-engaged after each one, and the scope stayed fixed: one lab agent, the routine tier, one allowlisted executor. The measured result:

- 10 successful routine overrides, every accepted decision sending its task to the one approved low-cost executor
- 10 of 10 correct answers, each validated against the exact expected result
- 10 of 10 route correlations, each classifier decision matched to its executor run through the run id
- 10 of 10 frozen classifier-version matches
- 2 classifier timeouts, both fail-closed: no override, and the normal route produced the correct answer anyway
- 0 rate limits
- successful classifier latency: 394.5 ms median, 1425 ms maximum

The lab's 1500 ms classifier deadline belonged to this experiment. It is a recorded value, not a default; pick your own deadline from your own latency budget. Nothing beyond this slice was tested or approved: no main-agent routing, no other agents, no other tiers, no production.

**Experiment 2: shadow routing on natural tasks.** The follow-up question was the obvious one: does a policy that passes on synthetic tasks also pass on real ones? The lab switched to shadow mode, where the classifier records what it would choose but never changes the executor, and gave it sixteen natural tasks from the operator's real work, each labeled by a human in advance. A predeclared stop rule watched the raw predictions, not just the accepted ones. The series stopped at case 9 of 16:

- 9 of 16 cases attempted before the stop rule fired
- 6 accepted decisions and 3 abstentions
- routine accepted precision 4 of 6 (66.7%)
- case N007: a human-labeled standard procedure task, predicted routine at confidence 0.93; the classification crossed the confidence filter, but shadow mode issued no override
- case N009: a human-labeled complex planning task, predicted standard at confidence 0.43, below the floor, so the classifier abstained and no override was issued
- zero overrides, because shadow mode never issues them
- classifier latency percentiles: 310 ms p50 (half of classifications completed within 310 ms), 491 ms p95 (95 percent completed within 491 ms), and 491 ms maximum
- 9 of 9 frozen-model matches and 9 of 9 route correlations

Two entries in that list do most of the teaching. Case N009 shows the guardrail working: the prediction leaned in the dangerous direction, offering a lower tier than the task deserved, but the confidence was low, the floor caught it, the classifier abstained, and the normal route stayed in control. That is a raw under-classification, not an accepted under-route; the wrong model never ran. Case N007 is the warning the floor cannot catch: a standard procedure task was confidently labeled routine and crossed the confidence filter. Because the run was in shadow mode, no override was issued, so N007 was also a raw under-classification, not an accepted under-route. A confidence floor turns uncertainty into abstention. It cannot detect a decision that is confidently wrong; only human review of shadow results can, which is why the stop rule also scored raw predictions.

**What the pair of experiments proves.** Experiment 1 shows the pattern can work at all: on one narrow, predefined task class, with every gate live and one allowlisted executor, the classifier routed ten tasks correctly, and its only failures were the two fail-closed timeouts. Experiment 2 shows that the same policy did not generalize: on natural tasks, two of the six accepted decisions did not match the human tier, and the series stopped on a dangerous raw under-classification. The lab's conclusion is the operating boundary this guide carries forward: semantic classifiers are plausible for narrow, predefined task classes in advisory or shadow use. They are not supported as a universal easy-versus-hard router.

**The likely limitation, in plain words.** The evidence points to two weaknesses working together. First, the tier boundaries were ambiguous. The lab's short definitions (routine means simple formatting, extraction, or transformation; standard means normal analysis or bounded coding; complex means hard reasoning, architecture, or high ambiguity) leave real tasks genuinely arguable, and case N009 sits exactly on that line: a substantial planning request that reads as architecture work to a human and as ordinary analysis to a classifier. The classifier's own low confidence there shows it found the boundary uncertain too. Second, the classifier saw only the current prompt: not what would happen if the task ran on a small executor, not which tools the task needed, and not the workflow the task belonged to. A prompt that reads like routine formatting can be one step in a high-stakes procedure. The remedy for missing context is not a lower confidence floor. It is a narrower task class, clearer tier definitions, or a task-specific classifier design, each of which is a new experiment, not a tweak.

**The safe operating boundary, restated.** If you try semantic routing, keep every control from 7.4, stay in shadow mode until you have your own numbers on your own natural tasks, treat the classifier's output as advisory, and keep the deterministic route as the baseline and the fallback. Do not tune the failure away: lowering the confidence floor, adding tiers mid-run, or widening scope after a weak result rewrites the question instead of answering it. When the numbers disappoint, that is the measurement working. The lab kept its negative result, stayed stopped, and left the deterministic route in charge.

## 8. Verify which route actually handled the work

After a change, and honestly whenever something feels off, you want two kinds of evidence: the model route OpenClaw reports, and the intended/preferred/eligible authentication routing evidence. That's route verification, and the checklist is [08-route-verification-checklist.md](08-route-verification-checklist.md). If you need to prove which account was charged, finish with the provider's usage or billing records.

### 8.1 Three different questions people collapse into one

- **What is configured?** `openclaw models status` shows your configured default, fallbacks, and an auth overview. It's a configuration view.
- **What is this session pinned to?** `/model status`, inside the chat session, shows the session selection and the auth candidates per provider.
- **What actually handled the last run?** `/status` shows the selected model and, when fallback state differs, the active fallback model and the reason.

The CLI status command explains configured defaults. It does not inspect a chat session's override. If you ask the CLI what your session is doing, you're asking the wrong question of the wrong tool. (Checked against OpenClaw 2026.9.4, 2026-09-17.)

### 8.2 What OpenClaw can show about the credential route

Back to section 2.7: the model reference alone doesn't prove the billing path. OpenClaw's auth state helps you inspect the intended route and explain likely rotation:

- `openclaw models auth list` shows profile health, and cooldown and disable entries include their reason and recovery action. A stored profile being present doesn't prove it's currently usable; the status output separates credential sources, stored profile health, model route issues, and runtime auth. Read them as separate answers. (Checked against OpenClaw 2026.9.4, 2026-09-17.)
- `openclaw models auth order get --provider <id>` shows that provider's stored order override, which is part of the preference story.
- If you run one provider with both a subscription profile and an API-key profile, the effective pick is exactly the thing to verify after any change in that area.

These checks can establish configuration, preference, session profile pins, eligibility, cooldowns, and fallback state. They may not establish the exact credential charged for a past request after temporary rotation. If the exact charge matters, compare the time and model with the provider's own usage or billing records. This package does not teach provider-dashboard procedures because those interfaces vary by provider.

Sanitize every piece of this before sharing it with anyone. Profile ids can contain emails; status output can contain credential metadata. The rule from section 3 never turns off.

## 9. Maintain the policy as models, prices, and OpenClaw change

Provider catalogs, prices, and capabilities change independently of this package and independently of your OpenClaw version. A routing policy written once and never revisited slowly becomes fiction. The maintenance checklist is [11-maintenance-checklist.md](11-maintenance-checklist.md).

### 9.1 Triggers for a review

Revisit the policy when:

- your OpenClaw version changes
- a provider changes its catalog, prices, or access terms
- you add or remove credentials
- your work changes shape: new recurring tasks, new tool needs, new specialist routes
- an incident happened (the troubleshooting tree produced a finding)
- a calendar cadence you chose comes around. Pick a cadence you'll actually keep: monthly, quarterly, whatever fits. A review you do beats a perfect schedule you ignore.

### 9.2 What a version recheck covers

When your OpenClaw version moves, recheck the version-sensitive facts against your installed documentation before trusting this package's dated details:

1. exact configuration keys and schema
2. model-selection scope behavior
3. strict session selection versus configured fallback behavior
4. auth-profile ordering and storage language
5. retry counts and cooldown behavior
6. subagent inheritance and override behavior
7. model catalog and allowlist semantics
8. CLI names, flags, and output shape

That list is the same one this package dates throughout, on purpose. When the facts drift, you'll know exactly which sections to re-read.

### 9.3 Maintenance commands

*Version note: checked against OpenClaw 2026.9.4 on 2026-09-17.*

- `openclaw --version` (read-only): compare against the version your policy was written for.
- `openclaw models list` (read-only): see what changed in your published inventory.
- Catalog refresh and provider discovery are deliberately excluded from this package's operation. They are non-rollbackable here, and hosted catalog refresh requires a Gateway restart before activation. Record a catalog concern and use a separately approved procedure with pre/post evidence; do not refresh as maintenance reflex.
- `openclaw models status --check` (read-only): the 0, 1, 2 readiness exit codes from section 3.
- `openclaw models scan --no-probe` (read-only, optional): reads OpenRouter's public free catalog, metadata only, no key required. The probing and set-default variants of scan require an OpenRouter key and live requests; this package doesn't use them. OpenRouter is a provider that aggregates many models behind one API.
- `openclaw doctor --lint` (read-only): health findings, sanitized before sharing.

### 9.4 What maintenance is not

Maintaining a routing policy is not updating OpenClaw. Software update and migration practice is a separate topic with its own procedure; this package only asks you to notice when your version has moved and to recheck the dated facts. It's also not an invitation to redesign on every trigger. Most reviews end with "no change needed", written down, with the date. That record is the point: it proves the policy was considered, not forgotten.

## 10. How to use the prompts

The prompt pack is [12-prompts.md](12-prompts.md). It contains eleven copyable prompts that bridge you to your agent: one for a plain-language walkthrough, one for each phase of the loop, one for troubleshooting, and one for maintenance.

Every prompt carries the same safety frame:

- It names the package address. That address is the fixed public address https://github.com/xbillwatsonx/openclaw-model-routing-guide, and every prompt tells your agent: if the address isn't live, ask me for the local package folder or every named file instead.
- It names the exact package section to read, and tells the agent to read it before doing anything else. An agent that hasn't read the section will improvise; the prompt removes that option.
- It states the purpose, so the agent knows what outcome you want.
- It states the safe behavior you expect: read-only by default, evidence quoted back to you, no improvisation.
- It forbids action without your explicit approval of that exact action. Your agent should propose, and you dispose.

How to use one: copy the whole block from the code fence, paste it into a chat with your agent, and keep the files it names at hand. Answer the agent's questions, and stay in control of the approval boundary. The prompts are deliberately read-only in spirit: where a prompt can lead to a change, the change only happens after you approve the exact action.

Here is the full pack, identical to [12-prompts.md](12-prompts.md).

### Prompt 1: Explain the guide to me in plain language

How to use: when the material feels dense and you want a guided, non-technical read before operating anything.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want a plain-language walkthrough of the whole guide before I touch anything on my system.

Before you do anything else, read GUIDE.md in that package, from the opening through section 9, including the glossary at the end.

Then walk me through it section by section. For each section: explain the idea in everyday words, define every OpenClaw-specific term the first time it comes up, give me a small concrete example, and finish with one comprehension check question so I can tell whether I followed you.

I expect you to stay read-only: no commands, no config changes, no model changes, nothing that touches my system. This is a reading and explanation session only.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 2: Help me inventory my setup safely

How to use: at runbook step 3, with the inventory worksheet open.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want to complete a safe inventory of my model routing setup, without exposing any secrets.

Before you do anything else, read section 3 of GUIDE.md, step 3 of RUNBOOK.md, and the 01-inventory-worksheet.md file in that package.

Then help me fill in every part of the inventory worksheet using only read-only commands. Show me each command before I run it, tell me what it reveals, and remind me what to record and what to leave out. Never ask me to paste secret values, and warn me before anything that could print credential details.

I expect you to stay read-only: only the commands the worksheet marks as read-only. If a step would need a write or a credential action, stop and tell me instead.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 3: Help me classify my work

How to use: at runbook step 4, listing your recurring tasks.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want to classify my recurring work so my routing decisions follow from my actual needs.

Before you do anything else, read section 4 of GUIDE.md and the 02-task-classification-worksheet.md file in that package.

Then help me list my recurring tasks and classify each one by difficulty, risk, value, privacy, and tool needs. Ask me questions until the worksheet is filled in. Do not suggest specific model names or rankings; this step is about my work, not about models.

I expect you to stay read-only: this is conversation and note-taking only, with no commands and no changes.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 4: Help me draft my routing policy

How to use: at runbook step 5, after inventory and classification are done.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want to write my routing policy on paper before I touch any configuration.

Before you do anything else, read section 5 and section 6 of GUIDE.md, the 03-routing-policy-template.md file, and the 05-fallback-retry-template.md file in that package.

Then help me draft my own routing policy: primary model, fallback order with a reason for each entry, specialist routes, auth preferences, session rules, and stop rules. Use my completed inventory and classification worksheets as input. Write it as a proposal only.

I expect you to stay read-only: drafting words, not changing configuration.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 5: Pressure-test my change proposal

How to use: at runbook steps 6 and 7, before anything is approved or applied.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I have a filled-in change proposal and I want it checked before anything runs.

Before you do anything else, read step 6 and step 7 of RUNBOOK.md and the 04-config-proposal-template.md file in that package.

Then review my change proposal against that procedure. Check that it is exactly one change, that the scope is named, that the baseline and rollback are recorded, that the verification plan names the command and the expected evidence, and that stop conditions exist. If anything is missing, show me what and stop. If it passes, walk me through the dry-run check in step 7 and show me the exact command before I run it.

I expect you to stay read-only until I approve the exact change: dry-run checks and read-only commands only.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 6: Apply my one approved change with me

How to use: at runbook step 8, with an approved proposal in hand.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I have approved exactly one change. I want to apply it and verify it, with you watching for anything unexpected.

Before you do anything else, read step 8 of RUNBOOK.md in that package, then read my approved change proposal.

Then run through step 8 with me: confirm the exact approved command matches my proposal word for word, ask me to confirm before it runs, and compare the output to the expected evidence in the proposal. If anything differs from what the proposal predicted, stop immediately and tell me. Do not improvise a fix.

I expect you to run only the exact approved change and the read-only verification commands named in the proposal, nothing else.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 7: Run matched-task tests with me

How to use: at runbook step 9, with the test plan and scorecard open.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want to compare candidate models on my real work with matched tasks and a shared scorecard.

Before you do anything else, read section 7 of GUIDE.md, the 06-test-plan.md file, and the 07-scorecard.md file in that package.

Then help me run the tests: same task and same inputs for every candidate, a fresh session for each run, the candidate pinned at session scope, and the same scorecard for every run. Tell me exactly what to type to pin each candidate in my test session, help me record the selected model and any fallback notice for each run, and help me score as I paste results back to you.

I expect you to change nothing outside my test chat sessions. A session-scope model pin or clear is a mutation: before either action, show me the prior selection and any explicit auth-profile pin, the exact command, the bounded test-session approval it is covered by, and the cleanup action. If no installed-version procedure can clear or restore the exact auth-profile pin, preserve that session, record `unsupported`, and use a fresh session. No configuration writes or other changes.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 8: Verify which route handled the work

How to use: at runbook step 10, after a change or after any run you want proof about.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want to verify the model route OpenClaw reports, inspect the intended credential route, and determine whether provider billing records are needed for final confirmation.

Before you do anything else, read section 8 of GUIDE.md and the 08-route-verification-checklist.md file in that package.

Then walk me through the route verification checklist with read-only commands only. Help me separate what is configured, what my session is pinned to, the model and fallback state OpenClaw reports, and what the auth-routing evidence can and cannot prove. If the exact charged account matters, tell me that provider usage or billing records are required. Remind me what to redact before I share any output with anyone.

I expect you to stay read-only: checks and reading only, no probes, no writes.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 9: Help me decide: keep, revise, or roll back

How to use: at runbook step 11, with test and verification evidence recorded.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I made one change and ran my tests. I need help deciding to keep, revise, or roll back, based only on recorded evidence.

Before you do anything else, read step 11 and step 12 of RUNBOOK.md and the 09-rollback-checklist.md file in that package.

Then compare my scorecard and my route verification evidence to the success criteria in my change proposal. Give me a clear keep, revise, or roll back recommendation with the evidence behind it. If we roll back, follow the rollback checklist exactly, one step at a time, and verify the baseline afterward.

I expect you to stay read-only during the decision. Any rollback step needs my explicit approval before it runs.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 10: Something went wrong with a route

How to use: when a run fails, a surprise model answers, or a bill doesn't match. With evidence at hand if you have it.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: a route did something I didn't expect. I want to classify the failure from evidence, not guesses.

Before you do anything else, read the 10-troubleshooting-decision-tree.md file and step 13 of RUNBOOK.md in that package.

Then work the decision tree with me. Ask for the exact error or notice I saw, collect the evidence each branch names using read-only commands only, and tell me which failure class the evidence points to and what evidence comes next. Respect the stop points: when the tree says stop, stop.

I expect you to stay read-only: no repairs, no fixes, no config changes, until we have a classified failure and an approved proposal.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

### Prompt 11: Time for a maintenance review

How to use: on a version change, a provider change, or your chosen review cadence.

```text
I'm working through The OpenClaw Model Routing Guide + Companion Runbook.
Package address: https://github.com/xbillwatsonx/openclaw-model-routing-guide. If that address is not live yet, I will give you the local package folder or every named file instead. Ask me for them if you cannot reach the address. Confirm that you read every named file before continuing.

Purpose: I want to run a maintenance review of my routing policy.

Before you do anything else, read section 9 of GUIDE.md and the 11-maintenance-checklist.md file in that package.

Then help me run the review: check my OpenClaw version against the baseline recorded in my policy, recheck the version-sensitive items on the checklist, note any catalog or price changes that affect my routing, and update my policy document's changelog. Anything that needs a change becomes a new one-change proposal for another day, not an edit today.

I expect you to stay read-only: checks and note-taking only.

Do not take any action or change anything on my system without my explicit approval of that exact action first.
```

## 11. Glossary

The same definitions appear in [14-glossary.md](14-glossary.md). Every term here is defined at first use in the guide above; this list gathers them in one place, in plain language.


- **approval boundary**: The recorded point where a proposed mutation may proceed only after the named approver accepts the exact action.
- **CLI**: Command-line interface: the terminal program and commands used to inspect or change OpenClaw.
- **JSON5**: A JSON-compatible configuration notation that permits comments and trailing commas.
- **read-only**: An inspection action that does not write configuration, state, or data.
- **run**: One attempt to handle a task within a session.
- **schema**: The documented structure and allowed values used to validate configuration.
- **transcript**: The recorded conversation content and events for a session.

- **agent**: One configured assistant instance in OpenClaw, with its own settings. Self-hosted operators often run one main agent. Each agent has its own configuration section and its own authentication store.
- **alias**: A short name for a model reference, saved in the model settings map. Adding an alias never restricts which models you can select.
- **auth profile**: A saved credential setup (an API key, an OAuth sign-in, or a static token) that OpenClaw uses for provider requests. Profile ids look like provider:default or provider:email, so treat them as private.
- **auth store**: The per-agent SQLite database, at the documented path pattern ~/.openclaw/agents/<agentId>/agent/openclaw-agent.sqlite, where OpenClaw keeps secrets and runtime authentication state, separate from your openclaw.json configuration file.
- **baseline**: The recorded known-good state of your routing before a change: version, configuration values, and where your backup lives. You roll back to the baseline, never to memory.
- **billing disable**: When a provider says a credential can't spend more, OpenClaw disables that credential for an initial ten-minute window and routes around it. Different from a rate limit.
- **billing path**: Which credential and account is actually charged for a run. OpenClaw auth state shows routing intent and eligibility, but provider usage or billing records may be required to confirm the exact charge.
- **candidate chain**: The ordered list OpenClaw builds for a run: the requested model first, then your explicit fallbacks, and the configured primary appended when nothing else was requested.
- **catalog**: The hosted model catalog OpenClaw maintains. Chat pickers and model lists browse it; browsing never starts provider discovery.
- **cooldown**: A temporary pause on a credential or model after certain failures. Regular cooldowns scale 30 seconds, 1 minute, 5 minutes. Cooldowns are internal state; describe them, don't hand-edit them.
- **cron**: A scheduled, unattended job in OpenClaw. A job can carry its own model, which acts as that job's primary.
- **dry-run**: A mode that checks whether a change is valid without writing it. config set and config patch both support it.
- **exit code**: The number a terminal command reports when it finishes. Zero usually means success; for models status --check, 0 means clean, 1 means an issue, 2 means an expiring credential.
- **fallback**: When the model a run asked for can't handle it, OpenClaw tries the next eligible model in the fallback chain. Fallback is turn-local: it doesn't become the session's model.
- **fallback chain**: The ordered list of models at agents.defaults.model.fallbacks, tried in order.
- **fail-closed**: When an optional routing component fails, it returns no override and preserves the known-good normal route.
- **gateway**: The OpenClaw service process that runs your agents, tools, and channels on your machine.
- **Jev**: A language model available through OpenRouter. This package names it only as the classifier one private lab wired in externally for the case study in section 7.5; it is not part of OpenClaw.
- **matched-task test**: A comparison where every candidate model gets the same task, the same inputs, and the same scoring rubric.
- **model**: The AI system that handles your work. You point OpenClaw at a model by its model reference.
- **model reference**: The `provider/model` string you use in commands and configuration. References split on the first slash; some OpenRouter-style references contain another slash.
- **model settings map**: The agents.defaults.models configuration. It stores aliases and per-model settings. It is not the override allowlist.
- **mutation**: A command that changes your configuration, state, or data. In this package, every mutation needs an approved proposal and a rollback path.
- **OpenRouter**: A provider that aggregates many models behind one API. models scan reads its public free catalog.
- **override allowlist**: The agents.defaults.modelPolicy.allow list, which governs which explicit model selections are permitted. Omitted or empty allows any model, subject to provider availability, runtime compatibility, and authentication.
- **personal model account**: A Gateway person-owned account for model access, managed with models accounts. Separate from the system and agent auth profiles this package works with.
- **primary model**: Your configured starting point for runs: agents.defaults.model.primary, or agents.defaults.model as a plain string.
- **profile order**: The preference order among auth profiles: a stored order override first, then auth.order in configuration, then configured profiles, then stored profiles. Without any of those, OpenClaw rotates profiles in documented tiers.
- **provider**: The service that serves a model: an API vendor, a subscription service, or a local runtime.
- **published inventory**: The model inventory OpenClaw has discovered and listed for you. models list reads it; reading it never starts a new discovery.
- **rate limit**: A provider response that says you're asking too fast. OpenClaw retries rate limits within its own bounded budget, and cooldowns can follow.
- **retry**: Another attempt at the same work on the same model after a failure, before OpenClaw rotates credentials or falls back. Retries happen inside the retry budget.
- **retry budget**: The bounded number of same-model recovery attempts OpenClaw makes before rotating credentials or falling back. It is not a workflow-level attempt limit.
- **rollback**: Restoring your recorded baseline after a change, then verifying the restore with the same read-only commands you recorded beforehand.
- **route**: The specific model and credential path one run takes: the six linked decisions from section 1, resolved for that run.
- **route verification**: Checking the model and fallback state OpenClaw reports, inspecting intended/preferred/eligible routing evidence, and separating it from provider-side charge confirmation.
- **routing policy**: Your written record of your primary, fallback order, specialist routes, and stop rules. It's a document, not a config file.
- **session**: One conversation between you and an agent.
- **session selection**: The exact model pinned for one session, set with /model at session scope. Session selections are strict: if the model fails, the run fails visibly rather than silently falling back.
- **abstention**: A classifier decision not to recommend a route. Low confidence, an error, or a failed safety gate can cause abstention; the normal deterministic route remains in control.
- **confidence floor**: The minimum confidence score a classification must reach before it may proceed past that safety gate. It blocks uncertain decisions but cannot catch a confidently wrong one.
- **executor allowlist**: The small recorded list of executor models a semantic classifier is permitted to recommend. It is a lab safety control, not the OpenClaw override allowlist.
- **executor model**: The model that actually handles the task after routing is resolved. A semantic classifier recommends a route; the executor performs the work.
- **latency percentile**: A timing summary. p50 means half of measured calls finished within that time; p95 means 95 percent finished within that time.
- **semantic classifier**: An optional service that reads a task's meaning and predicts its route before the executor model runs.
- **semantic routing**: Using a semantic classifier's accepted prediction to choose an executor route, with deterministic fallback when the classifier abstains or fails.
- **raw under-classification**: A classifier prediction that assigns a lower tier than the human label. It did not change the executor because abstention, a kill switch, or shadow mode blocked the override. Distinct from an accepted under-route because the wrong model never ran.
- **accepted under-route**: A classifier decision that was accepted and actually changed the executor model to a lower tier than intended. More dangerous than raw under-classification because the wrong model is already running.
- **shadow mode**: A testing mode where the classifier records what it would choose but never changes the executor model. Used to evaluate a semantic classifier before enabling live overrides.
- **tier**: A difficulty label assigned to a task by a semantic classifier, such as routine, standard, or complex. The tier determines which executor model the classifier recommends.
- **session stickiness**: When OpenClaw automatically picks an auth profile, it pins that profile for the session. The pin can rotate or clear on session reset, compaction, or cooldown. A pin you set yourself survives resets while the profile stays eligible.
- **specialist route**: A separate model setting for a specific kind of work: the utility model for short internal tasks, the image model, the PDF model, the media models, or the subagent model.
- **strict selection**: A selection that fails visibly instead of falling back. Session selections are strict. A per-agent primary is strict unless its model object explicitly includes fallbacks.
- **subagent**: A helper session your agent can spawn for delegated work. Native subagents inherit the caller's model unless configured otherwise.
- **turn-local**: Contained within a single run of the conversation. Fallback execution is turn-local; it doesn't persist as the session's selected model.
- **usage window**: A subscription's usage allowance. When it's exhausted, OpenClaw moves to eligible fallback rather than retrying.
- **version-sensitive**: A fact tied to exact keys, counts, or flags that can change between OpenClaw versions. This package dates every version-sensitive fact it teaches.
