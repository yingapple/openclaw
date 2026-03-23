---
title: "OpenClaw for Feishu"
summary: "How to set up OpenClaw for Feishu/Lark, approve access, and launch your first high-leverage team workflows"
sidebarTitle: "OpenClaw for Feishu"
read_when:
  - You want to run OpenClaw inside Feishu or Lark
  - You need a practical Feishu setup guide instead of raw channel reference docs
  - You want founder, ops, or engineering workflows inside team chat
---

# OpenClaw for Feishu

If your team already works in **Feishu / Lark**, this is one of the best first OpenClaw setups to ship.

It gives you a chat-native AI operator that can:

- answer in Feishu DMs and group chats
- run scheduled check-ins and daily briefings
- summarize alerts, PR activity, and operational noise
- route follow-up work back into the same team conversation

For many teams, **Feishu is the fastest path from "we installed OpenClaw" to "people actually use it every day."**

## Who this setup is best for

OpenClaw for Feishu is a strong fit if you want to:

- give founders or operators a single chat entry point for daily updates
- let a team talk to the same assistant from desktop and mobile
- keep internal automation inside Feishu instead of building a custom dashboard first
- trigger proactive workflows from cron jobs, webhooks, or message events

If your team primarily lives in Telegram instead, see [OpenClaw for Telegram](/channels/telegram).

## What you get after setup

After a clean setup, you will have:

1. a Feishu/Lark bot connected to OpenClaw
2. pairing-based DM access control by default
3. optional group-chat access with mention gating
4. a reliable place to deliver alerts, summaries, and recurring reports

<CardGroup cols={3}>
  <Card title="Feishu channel reference" icon="book-open" href="/channels/feishu">
    Full config, permissions, account, and runtime details.
  </Card>
  <Card title="Cron jobs" icon="clock-3" href="/automation/cron-jobs">
    Schedule daily briefings, reminders, and recurring operational checks.
  </Card>
  <Card title="Webhook automations" icon="webhook" href="/automation/hooks">
    Trigger OpenClaw from external systems like deploys, GitHub, or incidents.
  </Card>
</CardGroup>

## Fastest path to value

If you want a practical rollout order, do it like this:

### Phase 1: get the bot working

- create the Feishu app
- connect it to OpenClaw
- approve your first pairing request
- confirm replies work in DM

### Phase 2: enable one team workflow

Pick exactly one:

- a founder daily brief
- deployment alerts into Feishu
- GitHub PR summaries into a team chat
- an operator check-in on a recurring schedule

### Phase 3: expand carefully

- add selected groups
- tune mention requirements
- add sender/group allowlists where needed
- introduce thread-bound ACP or multi-agent routing only after the basic workflow is stable

## Setup: OpenClaw for Feishu

### Step 1: create the Feishu app

Create an enterprise app in Feishu Open Platform, then copy:

- **App ID**
- **App Secret**

If you use Lark instead of mainland Feishu, plan to set `domain: "lark"` in config.

For the exact UI flow and required permissions, use the detailed reference:

- [Feishu setup reference](/channels/feishu#step-1-create-a-feishu-app)

### Step 2: add the channel in OpenClaw

The easiest path is the onboarding flow:

```bash
openclaw onboard
```

If OpenClaw is already installed, you can also add the channel directly:

```bash
openclaw channels add
```

Then choose **Feishu** and enter your credentials.

### Step 3: start the gateway

```bash
openclaw gateway status
openclaw gateway restart
openclaw logs --follow
```

You want to confirm the gateway is up before finishing Feishu event subscription.

### Step 4: enable long connection event delivery

In Feishu Open Platform:

- open **Event Subscription**
- choose **Use long connection to receive events**
- add `im.message.receive_v1`

This is the critical step that lets the bot receive messages without exposing a public webhook URL.

### Step 5: DM the bot and approve pairing

After the bot is live, send it a message in Feishu.

By default, OpenClaw uses pairing-based DM access. Approve the pairing request:

```bash
openclaw pairing list feishu
openclaw pairing approve feishu <CODE>
```

Once approved, the DM becomes your first verified OpenClaw control surface inside Feishu.

## Recommended first workflows in Feishu

The point of this integration is not just "bot online". It is getting one useful loop into a chat your team already watches.

### 1. Founder daily executive brief

Use Feishu as the delivery channel for:

- calendar summary
- critical inbox or mentions
- top operational risks
- project status rollups

Best paired docs:

- [Cron jobs](/automation/cron-jobs)
- [Standing orders](/automation/standing-orders)
- [Heartbeat vs cron](/automation/cron-vs-heartbeat)

### 2. Deployment alerts that are actually readable

Instead of dumping raw webhook payloads into chat, send deploy events into OpenClaw and let it summarize:

- what changed
- whether the deployment succeeded
- what environment is affected
- what a human should check next

Best paired docs:

- [Webhook automations](/automation/hooks)
- [Automation webhook reference](/automation/webhook)

### 3. GitHub PR summaries in team chat

Feishu is a good destination for:

- new PR digests
- review request summaries
- merged PR updates
- issue / PR triage nudges

Best paired docs:

- [Webhook automations](/automation/hooks)
- [GitHub CLI + workflows](/cli/hooks)

### 4. Ops check-ins and recurring reminders

Use Feishu for scheduled checklists like:

- morning health checks
- stale incident follow-ups
- daily support queue summaries
- deployment freeze reminders

Best paired docs:

- [Cron jobs](/automation/cron-jobs)
- [Automation troubleshooting](/automation/troubleshooting)

## Group chat rollout advice

A common mistake is enabling the bot everywhere too early.

A better rollout looks like this:

1. validate DM usage first
2. add one trusted group
3. keep `requireMention: true` initially
4. widen access only after you know the prompts, routing, and behavior are working well

This keeps Feishu useful instead of noisy.

## Configuration patterns that matter early

### Pairing-first DM access

This is the safest default for internal rollout.

- unknown users get a pairing code
- you explicitly approve who can use the bot
- you can move to allowlists later if needed

### Mention-gated groups

For most teams, group chats should start with mentions required.

That prevents the bot from jumping into every conversation and keeps the first impression clean.

### Lark domain support

If your tenant is on Lark, set the domain explicitly:

```json5
{
  channels: {
    feishu: {
      domain: "lark",
    },
  },
}
```

### Streaming replies in Feishu

Feishu supports streaming via interactive cards, which is useful when:

- answers are long
- the bot is summarizing a lot of context
- users need progress feedback instead of silent waiting

See: [Feishu streaming configuration](/channels/feishu#streaming)

## Troubleshooting: the failures that actually matter

### The bot does not receive messages

Usually this is one of four things:

1. the app is not published
2. event subscription is incomplete
3. long connection is not enabled
4. the gateway is not running

Start here:

- [Feishu troubleshooting](/channels/feishu#troubleshooting)
- [Channel troubleshooting](/channels/troubleshooting)
- [Gateway troubleshooting](/gateway/troubleshooting)

### The bot works in DM but not in groups

Check these in order:

1. the bot is in the group
2. you are @mentioning it
3. `groupPolicy` is not blocking the group
4. logs show the incoming event was accepted

### The app setup saved, but nothing seems live

This often means the gateway was not already running when Feishu tried to validate or persist the long-connection setup.

Check:

```bash
openclaw gateway status
openclaw logs --follow
```

## Why Feishu is a high-value first integration

Compared with building a custom internal tool first, OpenClaw for Feishu has three big advantages:

- **lower rollout friction**: your team already knows the chat surface
- **higher activation odds**: people actually see and reply to messages there
- **faster automation loops**: scheduled jobs, webhook summaries, and follow-up questions all happen in one place

That makes it a strong first page and a strong first deployment.

## Related next steps

Once Feishu is working, the most natural follow-ups are:

- [OpenClaw Recipes](/recipes)
- [Feishu channel reference](/channels/feishu)
- [Cron jobs and recurring automations](/automation/cron-jobs)
- [Webhook automations](/automation/hooks)
- [Channel troubleshooting](/channels/troubleshooting)
