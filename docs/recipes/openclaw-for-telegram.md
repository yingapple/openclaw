---
title: "OpenClaw for Telegram"
summary: "How to set up OpenClaw for Telegram, approve access, and launch your first mobile-first workflows"
sidebarTitle: "OpenClaw for Telegram"
read_when:
  - You want to run OpenClaw inside Telegram
  - You need a practical Telegram setup guide instead of raw channel reference docs
  - You want mobile-first founder, engineering, or operator workflows in chat
---

# OpenClaw for Telegram

If you want the fastest way to put OpenClaw in your pocket, **Telegram is one of the best first integrations to ship**.

It gives you a chat-native assistant that can:

- reply in Telegram DMs, groups, and forum topics
- stay reachable from both desktop and mobile
- deliver alerts, summaries, and recurring automations into the chat your team already checks
- support topic-based routing for ops, engineering, and ACP-heavy workflows

For many teams, **Telegram is the highest-leverage way to make OpenClaw feel always available instead of trapped in a terminal**.

## Who this setup is best for

OpenClaw for Telegram is a strong fit if you want to:

- keep an owner or founder connected to OpenClaw from mobile
- ship a lightweight team bot before building any custom UI
- route deployment updates, GitHub summaries, or incident nudges into a shared chat
- use Telegram topics to separate workflows without running multiple bots

If your team primarily lives in Feishu or Lark instead, see [OpenClaw for Feishu](/recipes/openclaw-for-feishu).

## What you get after setup

After a clean setup, you will have:

1. a Telegram bot connected to OpenClaw
2. pairing-based DM access by default
3. optional group access with mention gating
4. a mobile-friendly place to deliver alerts, summaries, and recurring reports

<CardGroup cols={3}>
  <Card title="Telegram channel reference" icon="book-open" href="/channels/telegram">
    Full config, access control, topics, streaming, and network details.
  </Card>
  <Card title="Cron jobs" icon="clock-3" href="/automation/cron-jobs">
    Schedule daily briefs, reminders, summaries, and recurring operational checks.
  </Card>
  <Card title="Webhook automations" icon="webhook" href="/automation/hooks">
    Trigger OpenClaw from deploys, GitHub, incidents, and other external systems.
  </Card>
</CardGroup>

## Fastest path to value

If you want a practical rollout order, do it like this:

### Phase 1: get the bot working

- create the Telegram bot in BotFather
- configure the token in OpenClaw
- start the gateway
- approve your first DM pairing

### Phase 2: enable one workflow

Pick exactly one:

- deployment alerts in a Telegram chat
- GitHub PR summaries in a team group or topic
- a founder daily brief in DM
- a recurring ops reminder or health-check loop

### Phase 3: expand with guardrails

- add one trusted group first
- keep `requireMention: true` initially
- decide whether Privacy Mode should stay on
- add per-group or per-topic allowlists before widening access

## Setup: OpenClaw for Telegram

### Step 1: create the bot in BotFather

Open Telegram and message **@BotFather**.

Run `/newbot`, follow the prompts, then save the bot token.

For the detailed platform-side behavior, see [Telegram channel reference](/channels/telegram).

### Step 2: add the Telegram token to OpenClaw

Telegram uses token-based setup rather than a login flow.

Add it in your config:

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123:abc",
      dmPolicy: "pairing",
      groups: {
        "*": { requireMention: true },
      },
    },
  },
}
```

You can also provide the token via environment variable:

```bash
export TELEGRAM_BOT_TOKEN="123:abc"
```

### Step 3: start the gateway and approve your first DM

```bash
openclaw gateway
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

Once approved, your Telegram DM becomes your first verified OpenClaw control surface.

### Step 4: add the bot to one group or topic

Start small.

A good first rollout is:

1. verify DM behavior first
2. add one trusted group
3. keep mentions required
4. only relax group visibility after you confirm the bot behavior is clean

## Recommended first workflows in Telegram

The point is not just making the bot respond. The point is building one tight loop that people actually use.

### 1. Founder mobile brief

Telegram is a strong delivery surface for:

- morning priorities
- calendar and inbox rollups
- urgent project blockers
- end-of-day executive summaries

Best paired docs:

- [Cron jobs](/automation/cron-jobs)
- [Heartbeat vs cron](/automation/cron-vs-heartbeat)
- [Standing orders](/automation/standing-orders)

### 2. Deployment alerts that stay readable on mobile

Instead of forwarding raw deploy events, send them through OpenClaw so Telegram receives:

- what changed
- whether production or preview is affected
- whether the deployment succeeded or failed
- what someone should verify next

Best paired docs:

- [Webhook automations](/automation/hooks)
- [Automation webhook reference](/automation/webhook)

### 3. GitHub PR summaries in a team topic

Telegram topics are a strong fit for:

- PR review queues
- daily merged-PR digests
- review request summaries
- issue and incident triage nudges

Best paired docs:

- [Webhook automations](/automation/hooks)
- [GitHub workflows via CLI and hooks](/cli/hooks)

### 4. Ops reminders and recurring checks

Use Telegram for scheduled loops like:

- morning environment health checks
- stale incident follow-ups
- release coordination reminders
- support queue summaries

Best paired docs:

- [Cron jobs](/automation/cron-jobs)
- [Automation troubleshooting](/automation/troubleshooting)

## Group and topic rollout advice

A common mistake is treating Telegram like a wide-open bot surface on day one.

A better rollout looks like this:

1. validate DMs first
2. add one group
3. keep mention gating on
4. decide whether Privacy Mode should remain enabled
5. use per-group or per-topic allowlists for higher-signal spaces

This preserves signal and reduces surprise.

## Configuration patterns that matter early

### Pairing-first DM access

This is the safest default for personal or team rollout.

- unknown users get a pairing code
- you explicitly approve who can use the bot in DM
- you can later move to durable numeric allowlists if needed

### Mention-gated groups

For most teams, this should be the default starting posture.

It prevents the bot from reacting to every message and keeps the first experience intentional.

### Privacy mode vs full group visibility

Telegram bots often start with Privacy Mode on.

That is good for safety, but if you want the bot to see all non-mention group messages, you may need to:

- disable Privacy Mode via BotFather `/setprivacy`, or
- make the bot a group admin

If you change Privacy Mode, remove and re-add the bot so Telegram applies the new behavior.

### Topic-based routing

Telegram topics are one of the highest-value reasons to use this channel for team operations.

They let you separate workflows like:

- one topic for deploy alerts
- one topic for PR summaries
- one topic for an ACP coding session
- one topic for general assistant access

See [Telegram forum topics and thread behavior](/channels/telegram#forum-topics-and-thread-behavior).

## Troubleshooting: the failures that matter first

### The bot works in DM but not in groups

Usually this is one of four things:

1. the group is not allowlisted
2. `requireMention` is still on and you did not mention the bot
3. Privacy Mode is hiding group messages
4. sender allowlists are blocking the message

Start here:

- [Telegram troubleshooting](/channels/telegram#troubleshooting)
- [Channel troubleshooting](/channels/troubleshooting)

### The bot sees some commands but misses normal group chat

This usually points to Privacy Mode or group visibility configuration.

Check:

- BotFather `/setprivacy`
- whether the bot is a group admin
- whether the bot was removed and re-added after the setting change

### Polling or network behavior seems flaky

Telegram issues are often network or DNS problems rather than pure bot config failures.

Check:

```bash
openclaw logs --follow
dig +short api.telegram.org A
dig +short api.telegram.org AAAA
```

Then review:

- [Telegram polling or network instability](/channels/telegram#polling-or-network-instability)
- [Gateway troubleshooting](/gateway/troubleshooting)

## Why Telegram is a high-value first integration

Compared with building a custom dashboard first, OpenClaw for Telegram has three big advantages:

- **faster activation**: people already live in Telegram on both desktop and mobile
- **better reachability**: the assistant is available wherever the operator is
- **clean workflow isolation**: groups and topics give you lightweight structure without extra product work

That makes Telegram a strong integration page, a strong product entry point, and a strong SEO target for mobile-first AI assistant workflows.

## Related next steps

Once Telegram is working, the most natural follow-ups are:

- [OpenClaw Recipes](/recipes)
- [Telegram channel reference](/channels/telegram)
- [Cron jobs and recurring automations](/automation/cron-jobs)
- [Webhook automations](/automation/hooks)
- [Channel troubleshooting](/channels/troubleshooting)
