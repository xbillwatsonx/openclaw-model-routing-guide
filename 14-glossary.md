# Glossary

Part of The OpenClaw Model Routing Guide + Companion Runbook. The same definitions appear at first use in [GUIDE.md](GUIDE.md) and in its section 11; this file gathers them in one place, in plain language. Every term here is one the package actually uses, with one term and one definition per concept.

**Version baseline.** Version-sensitive definitions were checked against OpenClaw 2026.9.4 on 2026-09-17. Recheck the dated guide sections and installed documentation when your version changes.


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
