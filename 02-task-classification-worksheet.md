# Task-Classification Worksheet: Difficulty, Risk, Value, Privacy, and Tool Needs

Part of The OpenClaw Model Routing Guide + Companion Runbook. Fill this in at [RUNBOOK.md](RUNBOOK.md) step 4, after [01-inventory-worksheet.md](01-inventory-worksheet.md). The teaching for each dimension is in [GUIDE.md](GUIDE.md) section 4.

**How to fill this in.** List the work that actually repeats on your system: the daily summaries, the weekly analysis, the coding help, the document questions, the image generation. You're not cataloguing everything you've ever typed; you're finding the work that shows up week after week, because that's what routing decisions are worth building on.

**One rule: no model names.** Not in the table, not in the notes. Classification describes the work. Model choice comes later, from matched-task tests. If you catch yourself writing "this task needs a better model", rewrite it as "this task needs X, Y, and Z", describing the work, not a model.

## The five dimensions

- **Difficulty.** Routine (a capable general model handles it comfortably) or complex (long context, careful reasoning, specialized knowledge).
- **Risk.** What breaks if the output is wrong? Low risk means annoyance; high risk means real cost: money, broken systems, wrong decisions.
- **Value.** What the work is worth when it goes right. High value justifies deliberate routing and real testing.
- **Privacy.** What data the task touches, and where you're willing for that data to go. This dimension connects directly to your provider and billing path decisions. The trust call is yours to make consciously; nobody can make it for you.
- **Tool needs.** Text only? Images in? Images, PDFs, or media out? Delegated helper sessions (subagents)? Tool needs point at the specialist routes.

## Your recurring tasks

| Task (in your words) | How often | Difficulty (routine / complex) | Risk if wrong (low / high, and what breaks) | Value when right | Privacy (data touched, where it may go) | Tool needs (text / images / PDF / media / subagents) |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |

## What the classification tells your routing policy

Answer these from your table above, in words, with no model names:

1. **Which tasks are high risk?** These argue for strict selection and explicit stop rules, not silent fallbacks. List them.

   Answer:

2. **Which tasks touch the most private data?** These deserve a conscious provider and billing path decision. List them.

   Answer:

3. **Which tasks are routine and high-volume?** These are where a deliberate cheaper route can pay off, if matched-task tests later show quality holds. List them.

   Answer:

4. **Which tasks involve tool needs beyond plain text?** Each of these points at a specialist route you'll decide in the policy: utility, image, PDF, media, or subagent. List them and name the route.

   Answer:

   | Task | Specialist route it points at (utility / image / PDF / media / subagent) |
   | --- | --- |
   | | |
   | | |

5. **Where was classification hard?** Tasks you couldn't cleanly classify: write down what made them ambiguous. Ambiguity is information; it usually means the task mixes difficulty levels or tool needs, and your policy may need to route it by component rather than as one blob.

   Answer:

## Done?

Next: [03-routing-policy-template.md](03-routing-policy-template.md) at [RUNBOOK.md](RUNBOOK.md) step 5. If you found you have no recurring tasks worth routing deliberately, that's a valid outcome; see [RUNBOOK.md](RUNBOOK.md) step 4's stop condition.
