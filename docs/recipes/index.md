---
summary: "OpenClaw recipe hub for integrations, automations, troubleshooting, and founder/operator workflows"
read_when:
  - You want opinionated how-to recipes instead of raw reference docs
  - You are deciding which OpenClaw workflow to build first
  - You want channel, automation, troubleshooting, or founder/operator examples
  - You want one page that routes you to the highest-value OpenClaw use case fast
title: "OpenClaw Recipes"
---

# OpenClaw Recipes

OpenClaw has deep reference docs, but many people land with a more practical question:

- "What should I build first?"
- "Which workflow is highest leverage for my team?"
- "Where is the page for my exact use case?"

This page is the recipe hub for those answers.

It is designed to route high-intent readers into the **first-wave pages most likely to create real activation**:

- **Integrations** — get OpenClaw into the chat surface your team already uses
- **Alerting & automation** — summarize deploys, PRs, and scheduled reports
- **Troubleshooting** — fix silent failures before trust collapses
- **Role-based workflows** — explain OpenClaw in buyer language, not just config language

## Start with the highest-leverage recipes

<Columns>
  <Card title="OpenClaw for Feishu" href="/recipes/openclaw-for-feishu" icon="messages-square">
    Set up a Feishu/Lark bot, pair it with your gateway, and launch your first team-chat workflow.
  </Card>
  <Card title="OpenClaw for Telegram" href="/recipes/openclaw-for-telegram" icon="send">
    Put OpenClaw in your pocket with a Telegram bot for mobile access, group routing, and topic-based workflows.
  </Card>
  <Card title="OpenClaw Daily Executive Brief for Founders" href="/recipes/openclaw-daily-executive-brief-for-founders" icon="newspaper">
    Ship a founder-grade morning brief that compresses yesterday's signal into one useful message.
  </Card>
</Columns>

<Columns>
  <Card title="Send Vercel Deployment Alerts with OpenClaw" href="/recipes/send-vercel-deployment-alerts-with-openclaw" icon="rocket">
    Turn raw Vercel deployment events into concise alerts with clear next-step guidance.
  </Card>
  <Card title="GitHub PR Summary Bot with OpenClaw" href="/recipes/github-pr-summary-bot-with-openclaw" icon="git-pull-request-arrow">
    Summarize PR opens, review requests, reviews, and merges into readable team-chat updates.
  </Card>
  <Card title="OpenClaw Cron Not Running" href="/recipes/openclaw-cron-not-running" icon="alarm-clock-off">
    Diagnose why scheduled jobs are not firing, not delivering, or running at the wrong time.
  </Card>
</Columns>

<Columns>
  <Card title="AI Executive Assistant for Founders" href="/recipes/ai-executive-assistant-for-founders" icon="briefcase-business">
    See how chat, cron, deploy alerts, and PR summaries combine into a founder-facing assistant workflow.
  </Card>
  <Card title="Cron jobs and recurring automations" href="/automation/cron-jobs" icon="clock-3">
    Learn the scheduling primitives behind daily briefs, reminders, summaries, and recurring checks.
  </Card>
  <Card title="Webhook automations" href="/automation/hooks" icon="webhook">
    Connect OpenClaw to deploy systems, GitHub, incidents, and other external event sources.
  </Card>
</Columns>

## Choose the right recipe by job to be done

If you are not sure where to start, use the shortest path that matches your real intent.

## Which OpenClaw page should you open first?

Use this quick decision grid if you arrived with a concrete problem instead of a docs-reading mood.

| If your real question is... | Start here | Why this page first |
| --- | --- | --- |
| How do I get OpenClaw into Feishu/Lark fast? | [OpenClaw for Feishu](/recipes/openclaw-for-feishu) | Best first integration for internal teams already living in Feishu. |
| How do I put OpenClaw in my pocket and run it from mobile? | [OpenClaw for Telegram](/recipes/openclaw-for-telegram) | Fastest path to a mobile-first control surface. |
| How do I turn raw deploy events into readable alerts? | [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw) | Strongest first webhook/alerting use case for technical teams. |
| How do I make GitHub review traffic readable in chat? | [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw) | Converts noisy PR events into one useful team-chat stream. |
| How do I get a founder-grade daily brief every morning? | [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders) | Best first recurring workflow when the buyer is a founder or operator. |
| Why is my OpenClaw schedule not firing or not delivering? | [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running) | Fastest recovery path when trust in automation is at risk. |
| What does the full founder/operator workflow look like? | [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders) | Connects integrations, cron, alerts, and summaries into one product story. |
| I want the whole map before choosing | [OpenClaw Recipes](/recipes) | This hub is the entry point for the first-wave traffic pages. |

That table is the core logic behind the first-wave recipe pack: one integration page, one alerting page, one troubleshooting page, and one role-based page usually outperform a pile of disconnected feature docs.

## Why these 8 pages are the first-wave pack

These pages were not chosen at random.
They cover the five search/activation buckets most likely to create useful traffic and real product adoption:

1. **Recipes hub** — the main routing page that connects all the other intents
2. **Integrations** — [OpenClaw for Feishu](/recipes/openclaw-for-feishu) and [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
3. **Alerting / automation** — [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw) and [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
4. **Troubleshooting** — [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running)
5. **Role / use case** — [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders) and [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders)

That mix matters because it matches how people actually discover products:

- some search for a **channel integration**
- some search for a **specific workflow**
- some search when something is **broken**
- some search from a **job title / buyer intent** angle

If you only build one of those categories, traffic quality stays narrow.
If you build all five, OpenClaw can meet people at multiple points in the adoption curve.

### I want OpenClaw inside team chat first

Start with an integration recipe:

- [OpenClaw for Feishu](/recipes/openclaw-for-feishu) if your team already runs on Feishu / Lark
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram) if you want a mobile-first bot with strong group/topic workflows

This is usually the best first move because a working chat surface turns every later automation into something people actually see.

### I want useful alerts, not raw webhook spam

Start with an alerting recipe:

- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)

These are strong first automations because the event sources already exist. OpenClaw's job is to make them readable and actionable.

### I want a founder or operator daily loop

Start with a role/use-case recipe:

- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders)

The daily brief is usually the first habit-forming workflow.
The executive-assistant page is the broader product story that ties the stack together.

### I already tried scheduling and it seems broken

Start with troubleshooting:

- [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running)
- [Gateway troubleshooting](/gateway/troubleshooting)
- [Automation troubleshooting](/automation/troubleshooting)

This is the recovery path that keeps cron-based workflows trustworthy instead of brittle.

## Fastest starting point by team shape

### Feishu-first teams

Use this sequence:

1. [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
2. [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders) or [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
3. [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running) as the fallback path

Best for:

- China-based teams
- ops-heavy groups already living in Feishu
- founders who want one internal control surface first

### Telegram-first teams

Use this sequence:

1. [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
2. [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw) or [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
3. [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running) when scheduled workflows need debugging

Best for:

- mobile-first founders or operators
- lean technical teams
- teams that want topic-based routing without building a dashboard

### Founder-assistant rollout

Use this sequence:

1. [OpenClaw for Feishu](/recipes/openclaw-for-feishu) or [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
2. [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
3. [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
4. [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
5. [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders)

This is the cleanest path from “chat bot” to “high-trust executive assistant workflow.”

## What to build first if you only have one week

If you want early value fast, do not scatter attention across twenty docs or ten automations.

Use this order:

1. **One chat integration** so OpenClaw is reachable where people already work
2. **One daily or recurring workflow** so value compounds automatically
3. **One structured alerting flow** so noisy operational events become useful summaries
4. **One troubleshooting path** so silent failures do not destroy trust
5. **One role-based page** so buyers understand the whole system in human terms

That is why the first-wave recipe pack focuses on exactly these pages:

- [OpenClaw Recipes](/recipes)
- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running)
- [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders)

## Recipe categories

### Integrations

Use these when the main question is **"Can OpenClaw live inside the tools we already use?"**

- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Feishu channel reference](/channels/feishu)
- [Telegram channel reference](/channels/telegram)

### Alerting and automation

Use these when the main question is **"Can OpenClaw watch, summarize, and notify for us?"**

- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Cron jobs and recurring automations](/automation/cron-jobs)
- [Webhook automations](/automation/hooks)

### Troubleshooting

Use these when the main question is **"Why is this not running the way I expect?"**

- [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running)
- [Automation troubleshooting](/automation/troubleshooting)
- [Channel troubleshooting](/channels/troubleshooting)
- [Gateway troubleshooting](/gateway/troubleshooting)

### Role-based use cases

Use these when the main question is **"What does OpenClaw do for someone like me?"**

The strongest first-wave pages today are:

- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders)

These pages matter because they translate features into a concrete workflow someone will actually buy, try, and keep.

## First-wave page status

These are the 8 high-value traffic pages this hub is designed to support:

- Recipes Hub ✅
- OpenClaw for Feishu ✅
- OpenClaw for Telegram ✅
- Send Vercel Deployment Alerts with OpenClaw ✅
- GitHub PR Summary Bot with OpenClaw ✅
- OpenClaw Daily Executive Brief for Founders ✅
- OpenClaw Cron Not Running ✅
- AI Executive Assistant for Founders ✅

The next job is not spraying out more pages too early.
It is making this first batch easier to discover, easier to navigate, and easier to trust.

## Where to go next

- New to the product: [Getting Started](/start/getting-started)
- Want the whole docs map: [Docs hubs](/start/hubs)
- Need config details: [Gateway configuration](/gateway/configuration)
- Want more workflow examples later: bookmark this page — it is the entry point for future recipe-style docs
