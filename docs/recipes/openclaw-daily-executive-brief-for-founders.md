---
title: "OpenClaw Daily Executive Brief for Founders"
summary: "Build a founder-grade daily brief that pulls yesterday's signal into one concise chat update every morning"
sidebarTitle: "Daily Executive Brief"
read_when:
  - You want one daily founder update instead of checking five tools before 9 AM
  - You want OpenClaw to summarize revenue, product, support, deploy, and team signal on a schedule
  - You want a practical cron-based recipe instead of stitching together hooks and prompts from scratch
  - You need a high-trust executive summary delivered to Feishu, Telegram, or another chat channel
---

# OpenClaw Daily Executive Brief for Founders

Founders usually do not have an information problem.

They have a **fragmentation problem**.

Important signal lives in too many places:

- overnight deploys
- GitHub pull requests and incidents
- customer or team messages
- calendar pressure
- revenue or pipeline updates
- random things that do not matter but still steal attention

A good daily executive brief fixes that by giving you **one message that answers the real question**:

**"What do I need to know this morning, and what actually needs action?"**

This recipe shows how to build that workflow in OpenClaw.

If you want the raw scheduling primitives first, see [Cron jobs and recurring automations](/automation/cron-jobs). If you want the founder-facing workflow itself, stay here.

## Why this page is worth building early

This is one of the strongest first-wave OpenClaw pages because it sits at the intersection of:

- **role-based use case intent** — founders actively search for executive assistant and daily brief workflows
- **recurring automation intent** — the value compounds when the brief arrives every day without manual work
- **high-retention product usage** — once a brief becomes part of a morning routine, churn drops
- **cross-linkable workflow depth** — it naturally connects to Feishu, Telegram, GitHub, webhooks, cron, and troubleshooting pages

It is also a page with buyer language, not just implementation language.

People often do not search for “cron orchestration over chat.”
They search for “daily executive brief,” “founder assistant,” or “morning company summary.”

## What a good founder brief includes

A useful brief is not a wall of telemetry.

It should usually answer five things in this order:

1. **what changed** since the last brief
2. **what is blocked or at risk**
3. **what needs founder attention today**
4. **what can be safely ignored**
5. **what follow-up actions OpenClaw should take next**

In practice, that often turns into sections like:

- company pulse
- product / engineering
- customers / support
- calendar and deadlines
- top three action items

## What this workflow looks like

At a high level:

1. OpenClaw runs on a morning cron schedule
2. it gathers the most relevant signals from the sources you care about
3. it summarizes them into a compact executive brief
4. it delivers the result to the founder's preferred chat surface
5. optionally, it creates follow-up tasks or asks clarifying questions

That is the real power here: **brief first, action second**.

## Best channels for delivery

This workflow is especially strong if the founder already lives in one of these surfaces:

- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- Discord or Slack if the company already operates there

Feishu is usually strongest for China-based team operations.
Telegram is often strongest for mobile-first operators, founders on the go, or lean technical teams.

## The sources to include first

Start narrower than you think.

A high-trust v1 brief usually pulls from **three to five sources**, not twelve.

Good first sources are:

- GitHub PR / deploy activity
- yesterday's important chat messages or mentions
- urgent support or incident notes
- calendar items for today
- one business metric snapshot if you have a stable source for it

Bad first versions usually try to do too much:

- full CRM ingestion
- every analytics metric
- every email thread
- every internal chat channel

The result becomes noisy and stops being read.

## Example brief structure

A founder-ready brief often looks like this:

```text
Daily Executive Brief — 08:30

1) Company pulse
- 1 production deploy succeeded overnight
- 2 customer issues remain open
- No critical infra incidents detected

2) Product / engineering
- PR #184 is waiting on final review from Alex
- Mobile auth fix shipped to production at 01:12
- One flaky cron workflow failed and needs investigation

3) Customer / market signal
- Two feature requests repeated: SSO and export API
- Highest-risk account: Acme renewal call at 3 PM

4) Today’s constraints
- Calendar is stacked from 2 PM to 6 PM
- Decision needed before noon on pricing experiment rollout

5) Top actions
- Reply on PR #184 owner decision
- Confirm renewal strategy for Acme
- Decide whether to pause the flaky cron workflow
```

That is the standard this recipe aims for:

- short enough to read quickly
- specific enough to trust
- structured enough to act on

## A practical v1 architecture

There are many ways to build this. A solid first version usually has these pieces:

### 1. One scheduled job

Use cron for a predictable morning send.

Example:

```json
{
  "id": "daily-founder-brief",
  "schedule": "30 8 * * 1-5",
  "timezone": "Asia/Shanghai"
}
```

Use a timezone that matches the founder's day, not the server's default timezone.

Reference: [Cron jobs and recurring automations](/automation/cron-jobs)

### 2. One clear prompt contract

Your brief prompt should explicitly tell OpenClaw:

- what sources matter
- what to ignore
- how long the brief should be
- how to rank urgency
- how to phrase action items

A good prompt contract is more important than adding a sixth data source.

### 3. One stable delivery target

For v1, send the brief to one founder DM or one leadership channel.

Do not start with “route dynamically to whoever was last active.”
A daily executive brief is a high-trust workflow and should be predictable.

### 4. Optional follow-up behavior

After the brief lands, OpenClaw can optionally:

- create a task
- send a follow-up reminder later in the day
- expand one section on request
- notify a team member when a risk crosses a threshold

Keep these as follow-on upgrades, not launch blockers.

## Minimal implementation pattern

There are two practical patterns.

### Pattern A: prompt-driven morning summary

Use this when the relevant data is already available to the agent through memory, channels, hooks, or prior context.

This is the fastest way to launch.

### Pattern B: structured ingestion + summary

Use this when you need the brief to summarize more deterministic inputs such as:

- webhook events
- GitHub changes
- support digests
- daily metrics snapshots

This takes more setup, but it improves reliability and reduces hallucinated emphasis.

Most teams should launch with **Pattern A**, then harden the highest-value parts into **Pattern B** over time.

## Example cron prompt

Here is a strong starting pattern for the daily brief body:

```text
You are preparing the founder's daily executive brief.

Goal:
Produce one concise update that helps the founder decide where to spend attention today.

Prioritize:
1. critical risks, blockers, incidents, or deadlines
2. meaningful product / engineering progress
3. repeated customer or market signal
4. today's important calendar constraints
5. the top three recommended actions

Rules:
- Be concise and concrete.
- Prefer signal over completeness.
- Do not list routine noise unless it changes a decision.
- If an item is uncertain, say so briefly.
- End with exactly 3 action items.
```

That prompt is intentionally opinionated.

The goal is not an exhaustive company log.
The goal is better founder attention allocation.

## Where the brief gets its signal

Some strong pairings for this page are:

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)

These pages matter because they create the raw inputs for a better brief:

- PR summaries give engineering progress and review bottlenecks
- deployment alerts give shipping confidence and incident signal
- Feishu / Telegram make delivery and follow-up friction low

In other words, the executive brief is not isolated content.
It is the **aggregation layer** across other OpenClaw workflows.

## Common mistakes that make the brief bad

### Making it too long

If the founder has to scroll too much every morning, adoption drops.

### Including low-signal noise

Do not report every deploy, every message, or every low-priority ticket.
Report what changes decisions.

### Mixing status and action poorly

A strong brief separates:

- what happened
- what matters
- what to do

If those are mixed together, it reads like clutter.

### Using unstable inputs too early

If one input source is flaky, the whole brief can lose trust.
Start with the sources you can reliably summarize.

### Forgetting timezone and quiet-hours semantics

If the brief arrives at the wrong time, it feels broken even if the content is good.
Use the right timezone and verify scheduling behavior.

## When this workflow becomes especially valuable

This page gets stronger when one of these is true:

- the founder is context-switching across product, sales, and operations
- the company ships frequently and something changes overnight
- the team works across multiple chat surfaces or time zones
- the founder wants an executive assistant experience without hiring one first

That last point matters.

A daily executive brief is one of the clearest ways OpenClaw behaves like an **AI executive assistant for founders**, not just a bot that forwards alerts.

## Troubleshooting

### The brief runs, but it is generic

That is usually a prompt-quality or source-quality problem.

Fix:

- narrow the inputs
- sharpen the ranking instructions
- explicitly ask for decisions, risks, and action items

### The brief is late or arrives at the wrong hour

Check:

- the cron schedule
- the configured timezone
- whether you are debugging cron or heartbeat behavior

Start here:

- [Cron jobs and recurring automations](/automation/cron-jobs)
- [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running)
- [Cron vs heartbeat](/automation/cron-vs-heartbeat)

### The brief contains channel noise

Reduce the source set.

Usually the fix is not “better summarization of everything.”
Usually the fix is “stop feeding everything into the brief.”

### The founder wants to drill deeper

That is a healthy sign.

A good pattern is:

- morning brief in one message
- follow-up questions in the same thread or chat
- linked recipes for specific subsystems when more detail is needed

## Why this page matters in the first wave

Among the first 8 high-value traffic pages, this one earns its place because it does three jobs at once:

- captures **founder / executive assistant** search intent
- turns OpenClaw into a clearly understandable daily workflow
- connects the product story across integrations, automations, and troubleshooting

That makes it more than a recipe.
It is one of the clearest product narrative pages in the docs.

## Related pages

- [OpenClaw Recipes](/recipes)
- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running)
- [Cron jobs and recurring automations](/automation/cron-jobs)
- [Gateway troubleshooting](/gateway/troubleshooting)
