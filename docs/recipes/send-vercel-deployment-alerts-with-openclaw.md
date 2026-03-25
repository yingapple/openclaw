---
title: "Send Vercel Deployment Alerts with OpenClaw"
summary: "Use Vercel webhooks and OpenClaw hook mappings to turn raw deploy events into readable alerts in Feishu, Telegram, Slack, Discord, or your last active chat"
sidebarTitle: "Vercel Deployment Alerts"
read_when:
  - You want Vercel deployment alerts in chat
  - You want OpenClaw to summarize deploy failures, successes, and preview/production changes
  - You want a practical webhook recipe instead of piecing together hooks docs yourself
---

# Send Vercel Deployment Alerts with OpenClaw

If your team already ships on **Vercel**, one of the fastest useful OpenClaw automations is this:

**take raw Vercel deployment events, summarize them, and deliver only the useful version to chat.**

That gives you alerts that are easier to scan than raw webhook JSON:

- which project changed
- whether the deployment was **production** or **preview**
- whether it **succeeded**, **failed**, or needs follow-up
- what a human should check next

This is a strong early recipe because it connects three high-intent behaviors at once:

1. **webhook ingestion**
2. **agent summarization**
3. **chat delivery**

If you have OpenClaw installed and one chat channel working already, this is one of the highest-leverage automations to launch next.

## What you are building

The flow looks like this:

```text
Vercel webhook
  -> OpenClaw /hooks/vercel
  -> mapping + optional transform
  -> isolated agent run
  -> concise alert in chat
```

OpenClaw is doing the part Vercel does not do well by itself:

- normalize noisy payloads
- preserve the important identifiers
- write a short summary for humans
- route the result to Feishu, Telegram, Discord, Slack, Signal, or your last active chat

## Why this recipe is worth building early

Compared with building a custom deploy dashboard first, this is better early-stage leverage because:

- **deployment events already exist** — you do not need to invent a new source of truth
- **operators already live in chat** — the alert lands where someone will actually see it
- **OpenClaw can summarize instead of dump** — the message can be action-oriented instead of payload-shaped
- **the pattern generalizes** — once this works, the same webhook path can be adapted for GitHub, incidents, CI, or other ops signals

## Best first destinations

This recipe is especially strong if your team already uses one of these channels:

- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Webhook ingress](/automation/webhook)

If you do not have a chat channel set up yet, do that first. Vercel alerts are much more useful when they have a clear delivery destination.

## When this page should win priority over the other first-wave recipes

If you are deciding what to build first inside the 8-page pack, this page should usually win when the immediate pain is **shipping visibility**, not channel setup or buyer storytelling.

| If your real problem is... | Start here first? | Why |
| --- | --- | --- |
| We deploy constantly and the team ignores raw Vercel notifications | Yes | This page converts noisy machine events into short, readable, action-oriented alerts. |
| We still have no stable chat surface for OpenClaw | Usually no | Start with [OpenClaw for Feishu](/recipes/openclaw-for-feishu) or [OpenClaw for Telegram](/recipes/openclaw-for-telegram) first so the alert has somewhere trustworthy to land. |
| The founder wants one morning summary, not live deploy pushes | Maybe later | Start with [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders), then feed deploy signal into that workflow. |
| Scheduled jobs are already flaky and trust is low | No | Fix reliability first with [OpenClaw Cron Not Running](/recipes/openclaw-cron-not-running). |
| Engineering coordination pain is more about PR reviews than deploys | Maybe | [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw) may be the better first alerting page. |

A simple rule:

- choose **Vercel deployment alerts** first when the team already ships on Vercel and the main issue is **"important deploy context gets lost in notification noise"**
- choose **a chat integration page** first when OpenClaw is not yet present where people actually work
- choose **cron troubleshooting** first when delivery trust is already broken

## What a good deployment alert should contain

A good OpenClaw deployment alert usually answers five things immediately:

1. **Which project?**
2. **Which branch / commit / author?**
3. **Production or preview?**
4. **Succeeded, failed, or cancelled?**
5. **What should someone verify next?**

That is the standard this recipe aims for.

## Step 1: enable OpenClaw webhooks

In your OpenClaw config, enable webhook ingress:

```json5
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    path: "/hooks",
    transformsDir: "~/.openclaw/hooks/transforms",
  },
}
```

Important notes:

- keep the hook endpoint behind loopback, tailnet, or a trusted reverse proxy when possible
- use a **dedicated** hook token
- do **not** reuse your main gateway auth token

Reference: [Webhooks](/automation/webhook)

## Step 2: create a Vercel-specific hook mapping

Add a mapping for a dedicated endpoint such as `/hooks/vercel`.

```json5
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    transformsDir: "~/.openclaw/hooks/transforms",
    mappings: [
      {
        id: "vercel-deploy-alerts",
        match: { path: "vercel" },
        action: "agent",
        wakeMode: "now",
        name: "Vercel",
        sessionKey: "hook:vercel:{{payload.id}}",
        deliver: true,
        channel: "last",
        // Or pin a fixed target:
        // channel: "feishu",
        // to: "chat:oc_xxx",
        model: "openai/gpt-5.2-mini",
        thinking: "low",
        timeoutSeconds: 120,
        transform: {
          module: "vercel.mjs",
          export: "transformVercelDeployment",
        },
      },
    ],
  },
}
```

Why this shape works well:

- `match.path: "vercel"` gives you a clean endpoint just for deploy events
- `action: "agent"` lets OpenClaw summarize instead of just forwarding text
- `sessionKey` keeps repeated updates for the same deployment correlated
- `deliver: true` sends the final result to chat
- low-latency model + low thinking is usually enough for deploy alerts

## Step 3: add a tiny transform module

Put a reviewed transform module under your configured `hooks.transformsDir`, for example:

`~/.openclaw/hooks/transforms/vercel.mjs`

```js
export function transformVercelDeployment({ payload }) {
  const project = payload.project?.name ?? payload.name ?? "unknown-project";
  const target = payload.target ?? "unknown-target";
  const state = payload.state ?? payload.readyState ?? "unknown-state";
  const branch = payload.meta?.githubCommitRef ?? payload.gitSource?.ref ?? "unknown-branch";
  const commit = payload.meta?.githubCommitSha ?? payload.gitSource?.sha ?? "unknown-commit";
  const author = payload.meta?.githubCommitAuthorName ?? payload.creator?.username ?? "unknown-author";
  const url = payload.url ? `https://${payload.url}` : "(no deployment URL)";

  return {
    kind: "agent",
    message: [
      "You are summarizing a Vercel deployment event for a busy engineering or founder chat.",
      "Write a concise alert with:",
      "- project",
      "- target (production/preview)",
      "- deployment state",
      "- branch + commit",
      "- deployment URL if present",
      "- one short 'what to check next' line",
      "Preserve identifiers exactly.",
      "",
      `project: ${project}`,
      `target: ${target}`,
      `state: ${state}`,
      `branch: ${branch}`,
      `commit: ${commit}`,
      `author: ${author}`,
      `url: ${url}`,
    ].join("\n"),
  };
}
```

This pattern is useful because the transform does only two things:

1. extract the important fields from the raw webhook payload
2. hand OpenClaw a clean prompt for the actual human-facing summary

That keeps the automation readable and easy to debug.

## Step 4: point Vercel at the OpenClaw endpoint

In Vercel, create a webhook that sends deployment events to:

```text
https://YOUR-GATEWAY-OR-PROXY/hooks/vercel
```

And configure the request to include your OpenClaw hook token via:

- `Authorization: Bearer <token>`

If your gateway is not directly internet-reachable, put it behind a trusted reverse proxy or tailnet ingress path that can receive the Vercel webhook safely.

## Step 5: choose the delivery path

You have two good rollout options.

### Option A: deliver to the last active chat

Use:

```json5
channel: "last"
```

This is good when you are still testing and want the alerts to follow the operator's most recent active chat surface.

### Option B: pin a fixed destination

Use an explicit channel + destination, for example:

```json5
channel: "feishu",
to: "chat:oc_xxx"
```

or:

```json5
channel: "telegram",
to: "-1001234567890"
```

This is better once the automation is stable and belongs in a team room.

## A production-vs-preview routing pattern worth shipping early

One of the biggest practical upgrades is to **treat production and preview deploys differently**.

A common v1 routing policy looks like this:

- **production success** -> send to the main engineering or ops room
- **production failure** -> send immediately to the main room with a stronger next-step hint
- **preview success** -> send to a quieter room, topic, or the last active operator
- **preview failure** -> send only if the preview is tied to a high-importance branch or stakeholder demo

That matters because teams usually do not have a "too few alerts" problem.
They have a **"too many alerts with the same urgency"** problem.

If you want to encode that policy directly, your transform can emit a clearer instruction set for the agent, for example:

```js
export function transformVercelDeployment({ payload }) {
  const project = payload.project?.name ?? payload.name ?? "unknown-project";
  const target = payload.target ?? "unknown-target";
  const state = payload.state ?? payload.readyState ?? "unknown-state";
  const branch = payload.meta?.githubCommitRef ?? payload.gitSource?.ref ?? "unknown-branch";
  const commit = payload.meta?.githubCommitSha ?? payload.gitSource?.sha ?? "unknown-commit";
  const author = payload.meta?.githubCommitAuthorName ?? payload.creator?.username ?? "unknown-author";
  const url = payload.url ? `https://${payload.url}` : "(no deployment URL)";
  const severity = target === "production"
    ? state === "READY" ? "high-signal" : "urgent"
    : state === "READY" ? "low-signal" : "medium";

  return {
    kind: "agent",
    message: [
      "You are summarizing a Vercel deployment event for a busy engineering or founder chat.",
      "Keep the alert concise and operational.",
      "Make the urgency obvious from the environment and state.",
      "End with one short next-step line.",
      "",
      `project: ${project}`,
      `target: ${target}`,
      `state: ${state}`,
      `severity: ${severity}`,
      `branch: ${branch}`,
      `commit: ${commit}`,
      `author: ${author}`,
      `url: ${url}`,
    ].join("\n"),
  };
}
```

You do **not** need to perfect this on day one.
You only need enough structure so production failures feel urgent and preview successes do not drown the room.

## Step 6: test with a safe sample event

Before trusting production deploy traffic, send one controlled request to the endpoint.

```bash
curl -X POST https://YOUR-GATEWAY-OR-PROXY/hooks/vercel \
  -H 'Authorization: Bearer YOUR_OPENCLAW_HOOK_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "id": "dpl_test_123",
    "target": "preview",
    "state": "READY",
    "url": "demo-preview.vercel.app",
    "project": { "name": "docs-site" },
    "meta": {
      "githubCommitRef": "main",
      "githubCommitSha": "abc1234",
      "githubCommitAuthorName": "Ada"
    }
  }'
```

Then verify three things:

1. the webhook is accepted
2. the transform runs cleanly
3. the final chat message is short, readable, and actionable

## A good first prompt/output shape

For most teams, the ideal output is not long.

A strong alert looks more like this:

> Vercel deploy: docs-site preview is READY on main (abc1234). URL: <https://demo-preview.vercel.app>. Next check: confirm the docs change renders correctly and links are healthy.

And less like this:

> giant raw JSON payload pasted into chat

That difference is the whole point of using OpenClaw here.

## Common rollout mistakes

### Sending raw payloads straight to chat

This creates noise, not signal.

Use the transform + agent flow so humans get a summary instead of a dump.

### Mixing preview and production alerts without labels

Always preserve the target environment clearly.

A deploy alert that omits **preview vs production** is much less useful.

### Using an overly expensive model

Deploy alerts are usually frequent and structurally simple.

Start with a fast, lower-cost model and low thinking.

### Letting the webhook endpoint float without guardrails

Use:

- a dedicated hook token
- a trusted ingress path
- a bounded timeout
- a stable session key prefix like `hook:vercel:`

Reference: [Webhooks security guidance](/automation/webhook#security)

## When to make this workflow richer

Once the basic alert is working, the next useful upgrades are:

- route **production failures** to a team channel, but send **preview successes** somewhere quieter
- include commit author or PR context when available
- split by project using separate mappings or transform logic
- combine this with a GitHub recipe so deploy alerts and PR summaries land in the same chat surface

That is also why this page pairs naturally with:

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)

If those pages are not live yet in your docs build, treat them as the next recipe pages to add after this one.

## Troubleshooting

### Vercel says the webhook fired, but OpenClaw did nothing

Check:

- the hook URL path really ends with `/hooks/vercel`
- the token was sent as `Authorization: Bearer ...`
- `hooks.enabled` is on
- the gateway is running
- logs show the request arriving

Start here:

- [Webhooks](/automation/webhook)
- [Gateway troubleshooting](/gateway/troubleshooting)

### The webhook reaches OpenClaw, but the summary is bad

Usually the issue is the transform, not the transport.

Check whether your transform is extracting the right payload fields from the actual Vercel event body you receive.

### Messages are delivered to the wrong chat

That is usually a delivery-routing issue:

- `channel: "last"` followed the last active route
- `to` is missing or wrong for a fixed target
- the underlying channel auth/config is not stable yet

If this matters operationally, move from `channel: "last"` to a fixed `channel` + `to` pair.

## OpenClaw vs native Vercel notifications

Vercel can already notify you.
The reason to involve OpenClaw is not "because alerts exist."
It is because **default notifications rarely compress decision-making well enough for busy teams**.

| Approach | Good at | Weak at |
| --- | --- | --- |
| Native Vercel notifications | Fast setup, direct event delivery, basic environment visibility | Limited judgment, limited routing nuance, easy to ignore when deploy volume rises |
| OpenClaw deployment alerts | Summary quality, chat-native routing, better next-step guidance, easy extension into founder briefs or ops workflows | Slightly more setup because you need webhook ingress and mapping |

A good rule of thumb:

- use **native Vercel notifications alone** if you only want raw awareness and low message volume
- use **OpenClaw** when you want deploy events to become a readable operational workflow that can later connect to PR summaries, daily briefs, and escalation rules

## Why this page matters in the first wave

Among first-wave OpenClaw recipe pages, this one earns its spot because it sits at the intersection of:

- **automation intent**
- **founder / engineering use cases**
- **practical webhook adoption**
- **chat-native operational workflows**

It is not just a deployment recipe. It is a template for how OpenClaw turns noisy machine events into useful human messages.

## Related pages

- [OpenClaw Recipes](/recipes)
- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Hooks](/automation/hooks)
- [Webhooks](/automation/webhook)
- [Gateway troubleshooting](/gateway/troubleshooting)
