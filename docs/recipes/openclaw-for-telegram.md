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

## When Telegram should win over other first-wave starting points

Choose **OpenClaw for Telegram** first when the real requirement is mobility, reachability, or lightweight team rollout.

| If your situation looks like this... | Start with Telegram? | Why |
| --- | --- | --- |
| Founder or operator needs OpenClaw on mobile all day | Yes | Telegram is the fastest path to a pocketable control surface without building extra UI. |
| Team coordinates in Telegram groups or forum topics already | Yes | Topics map cleanly to deploy alerts, PR summaries, incident triage, and ACP-heavy workflows. |
| Team mainly works in Feishu/Lark during the day | Usually no | [OpenClaw for Feishu](/recipes/openclaw-for-feishu) is the better default when you want internal adoption in the main company chat. |
| Main problem is silent scheduled jobs, not channel setup | No | Fix reliability first with [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running). |
| Buyer wants the big founder workflow story, not setup steps | Maybe later | Start here if Telegram is the delivery surface, then graduate to [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders). |

A simple rule of thumb:

- pick **Telegram** when you need **mobile-first access** and lightweight group/topic routing
- pick **Feishu** when you need **internal team adoption** inside the company chat everyone already watches
- pick **Cron troubleshooting** when trust is already broken and shipping a new workflow would only amplify the problem

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

If you are following the first-wave rollout strategy, the cleanest Telegram expansion path is:

1. **OpenClaw for Telegram** — establish the delivery surface
2. **[Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)** if your next pain is deploy noise
3. **[GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)** if your next pain is review coordination
4. **[OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)** if the owner needs one recurring morning update
5. **[AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders)** when you want the full founder/operator workflow narrative

That sequence matters because **Telegram is usually the distribution layer first, and the higher-value automations compound on top of it**.

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

## A v1 Telegram rollout that actually ships

A lot of Telegram bot guides stop at **"the bot replies in DM"**.

That is not enough for a real first deployment.

A strong v1 usually has these properties:

- DMs use pairing instead of being open to anyone who finds the bot
- one trusted group or topic is enabled first
- mention gating stays on until the prompt and routing quality are stable
- one fixed delivery destination is chosen for alerts or recurring reports
- topic routing is used deliberately so deploys, PRs, and ACP-heavy work do not collide

A practical starting config looks like this:

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "${TELEGRAM_BOT_TOKEN}",
      dmPolicy: "pairing",
      groupPolicy: "allowlist",
      groupAllowFrom: ["ops_room"],
      groups: {
        ops_room: {
          requireMention: true,
        },
      },
    },
  },
}
```

Why this is a good first version:

- it keeps **DM access explicit**
- it keeps **group rollout narrow**
- it gives you **one reliable destination** for deploy alerts, PR summaries, or recurring reminders
- it avoids the common failure mode of "the bot technically works, but the team mutes it"

## Best first automations after Telegram is live

Once the Telegram bot is working, do **one** of these next — not all at once.

### Option 1: founder daily brief into DM

Best when one founder or operator wants a reliable mobile-first morning update.

Use:

- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [Cron jobs](/automation/cron-jobs)

### Option 2: PR summaries into one engineering topic

Best when the team already coordinates code review in Telegram and wants less GitHub notification noise.

Use:

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Webhook automations](/automation/hooks)

### Option 3: deploy alerts into one ops chat or topic

Best when the main pain is production visibility rather than code review.

Use:

- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [Webhook automations](/automation/hooks)

The key is sequencing.

**Telegram first gives you reachability. One automation after that gives you value.**

## A 7-day rollout path for Telegram teams

If you are introducing OpenClaw through Telegram, this is a sane first week:

### Day 1: DM-only validation

- connect the bot
- approve pairing for 1 to 3 users
- confirm replies, tone, and basic commands feel right

### Day 2: add one trusted group or topic

- add exactly one team space
- keep `requireMention: true`
- verify the bot is helpful instead of noisy

### Day 3: ship one useful automation

Choose one:

- founder daily brief
- deploy alerts
- PR summaries

### Day 4 to Day 7: tune before expanding

- refine prompts
- separate workflows into topics if needed
- tighten delivery targets
- only then widen access or add more automations

This rollout order sounds conservative, but it is usually faster overall because it avoids cleanup after a noisy launch.

## FAQ: common decisions before you ship this internally

### Should I start with Telegram DM or a team group?

Start with **DM first**.
That gives you a safe place to verify pairing, response quality, and escalation style before the bot speaks in a shared chat.

### Should founder updates go to DM or a shared topic?

Use **DM** for high-trust executive briefs.
Use a **shared group or topic** for PR summaries, deployment alerts, and recurring operational updates multiple people should see.

### Should I disable Privacy Mode immediately?

Usually no.
Start with Privacy Mode or mention-gated behavior intact, then widen visibility only if you have a clear reason and understand the noise tradeoff.

### What is the best first automation after Telegram setup?

Pick the workflow that matches the sharpest pain:

- visibility problem -> [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- review coordination problem -> [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- founder orientation problem -> [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)

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
