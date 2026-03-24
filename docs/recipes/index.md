---
summary: "OpenClaw recipe hub for integrations, automations, and operator playbooks"
read_when:
  - You want opinionated how-to recipes instead of raw reference docs
  - You are deciding which OpenClaw workflow to build first
  - You want channel, automation, or troubleshooting examples
title: "OpenClaw Recipes"
---

# OpenClaw Recipes

OpenClaw has deep reference docs, but many people land with a more practical question:

- "How do I set this up for my team?"
- "What can I automate first?"
- "Which workflow should I ship this week?"

This page is the recipe hub for those answers.

It groups **high-intent OpenClaw workflows** into a few buckets:

- **Integrations**: connect OpenClaw to the channel your team already uses
- **Automation**: wire alerts, summaries, and scheduled jobs
- **Troubleshooting**: fix common setup failures fast
- **Role-based use cases**: founder, operator, or engineering workflows
- **Next-step references**: jump from a recipe to the underlying docs

## Start with the highest-leverage recipes

<Columns>
  <Card title="OpenClaw for Feishu" href="/recipes/openclaw-for-feishu" icon="messages-square">
    Set up a Feishu/Lark bot, pair it with your gateway, and use OpenClaw inside team chat.
  </Card>
  <Card title="OpenClaw for Telegram" href="/recipes/openclaw-for-telegram" icon="send">
    Connect a Telegram bot for fast mobile access, group routing, and topic-based workflows.
  </Card>
  <Card title="Cron jobs and recurring automations" href="/automation/cron-jobs" icon="clock-3">
    Run OpenClaw on a schedule for daily briefs, reminders, summaries, and background ops.
  </Card>
</Columns>

<Columns>
  <Card title="Send Vercel Deployment Alerts with OpenClaw" href="/recipes/send-vercel-deployment-alerts-with-openclaw" icon="rocket">
    Turn raw Vercel deployment events into concise, readable alerts delivered to the team chat that actually matters.
  </Card>
  <Card title="OpenClaw Cron Not Running" href="/recipes/openclaw-cron-not-running" icon="alarm-clock-off">
    Troubleshoot the highest-friction scheduler failure: jobs that do not fire, deliver, or run at the right time.
  </Card>
  <Card title="Gateway troubleshooting" href="/gateway/troubleshooting" icon="shield-alert">
    Diagnose gateway startup, config, network, and service-management failures.
  </Card>
</Columns>

## What to build first

If you want early value fast, these are the best first OpenClaw workflows to launch:

1. **A chat integration** so you can reach your agent from where you already work
2. **One scheduled automation** that saves time every day
3. **One alerting workflow** that turns noisy system events into useful summaries
4. **A troubleshooting playbook** so setup friction does not stall adoption

That is why the first wave of recipe pages focuses on **Feishu**, **Telegram**, **deployment alerts**, **PR summaries**, **daily executive briefs**, and **cron troubleshooting**.

## Recipe categories

### Integrations

Use these when the main question is **"Can OpenClaw live inside the tools we already use?"**

- [Feishu bot](/channels/feishu)
- [Telegram bot](/channels/telegram)
- [Discord bot](/channels/discord)
- [Slack bot](/channels/slack)

### Automation

Use these when the main question is **"Can OpenClaw watch, summarize, and notify for us?"**

- [Cron jobs](/automation/cron-jobs)
- [Webhook automations](/automation/hooks)
- [Polls and proactive prompts](/automation/poll)
- [Gmail Pub/Sub](/automation/gmail-pubsub)

### Troubleshooting

Use these when the main question is **"Why is this not running the way I expect?"**

- [Automation troubleshooting](/automation/troubleshooting)
- [Channel troubleshooting](/channels/troubleshooting)
- [Gateway troubleshooting](/gateway/troubleshooting)
- [Node troubleshooting](/nodes/troubleshooting)

### Role-based use cases

Use these when the main question is **"What does OpenClaw do for someone like me?"**

Today, the strongest early use cases are:

- founders who want a daily executive brief
- engineering leads who want GitHub and deployment summaries in chat
- operators who want scheduled checks and escalation flows
- developers who want an always-available assistant across mobile and desktop channels

## In-progress high-value pages

These are the first recipe pages worth building out from this hub:

- Recipes Hub ✅
- OpenClaw for Feishu
- OpenClaw for Telegram ✅
- Send Vercel Deployment Alerts with OpenClaw ✅
- GitHub PR Summary Bot with OpenClaw
- OpenClaw Daily Executive Brief for Founders
- OpenClaw Cron Not Running ✅
- AI Executive Assistant for Founders

## Where to go next

- New to the product: [Getting Started](/start/getting-started)
- Want the whole map: [Docs hubs](/start/hubs)
- Need config details: [Gateway configuration](/gateway/configuration)
- Want more examples later: bookmark this page — it is the entry point for future recipe-style docs
