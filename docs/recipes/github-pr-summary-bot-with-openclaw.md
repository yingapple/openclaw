---
title: "GitHub PR Summary Bot with OpenClaw"
summary: "Use GitHub webhooks and OpenClaw hook mappings to turn pull requests, review requests, and review comments into concise chat summaries for engineering leads and founders"
sidebarTitle: "GitHub PR Summary Bot"
read_when:
  - You want GitHub pull request summaries in chat
  - You want OpenClaw to summarize PR status, reviewers, and next actions instead of forwarding raw GitHub notifications
  - You want one practical recipe for review alerts, review-request routing, and concise engineering updates
---

# GitHub PR Summary Bot with OpenClaw

If your team already lives in **GitHub** and **chat**, one of the best first OpenClaw automations is this:

**turn pull request events into short, readable summaries that tell people what changed and what needs attention next.**

That is better than dumping raw GitHub notifications into chat because a useful PR update should answer, fast:

- what PR changed
- whether it is a **new PR**, a **review request**, a **new review**, or a **merged PR**
- who owns the next move
- whether leadership should care or just let the team handle it

This recipe is a strong first-wave page because it sits at the intersection of:

1. **high-intent GitHub traffic**
2. **engineering-team workflow pain**
3. **chat-native delivery**, where OpenClaw is naturally strong

It also pairs cleanly with [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw), because most teams want both: **PR context before deploy, deploy status after merge**.

## What you are building

The flow looks like this:

```text
GitHub webhook
  -> OpenClaw /hooks/github-pr
  -> mapping + optional transform
  -> isolated agent run
  -> concise PR summary in chat
```

OpenClaw handles the part GitHub notifications usually do poorly by themselves:

- normalize different PR event types into one readable shape
- preserve the important identifiers
- summarize changes for humans who are scanning chat quickly
- route the result to Feishu, Telegram, Slack, Discord, Signal, or your last active chat

## Why this recipe is worth building early

Compared with building a custom PR dashboard first, this tends to be the better early move because:

- **the source of truth already exists** in GitHub
- **the people who need the update already live in chat**
- **OpenClaw can add judgment**, not just forwarding
- **the pattern generalizes** to issues, CI, deploys, and incident workflows later

If you only launch one engineering alerting workflow first, this one is often the best place to start.

## Best first destinations

This recipe is especially strong if your team already uses one of these channels:

- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Webhook ingress](/automation/webhook)

If you do not have a stable chat route yet, set that up first. PR summaries are only useful if they land somewhere operators actually watch.

## What a good PR summary should contain

A good OpenClaw PR summary usually answers six questions immediately:

1. **Which repo and PR?**
2. **What happened?** (opened, updated, review requested, reviewed, merged)
3. **Who is involved?**
4. **What branch or scope changed?**
5. **What is the current decision state?**
6. **What should happen next?**

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

## Step 2: create a GitHub PR hook mapping

Add a mapping for a dedicated endpoint such as `/hooks/github-pr`.

```json5
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    transformsDir: "~/.openclaw/hooks/transforms",
    mappings: [
      {
        id: "github-pr-summary-bot",
        match: { path: "github-pr" },
        action: "agent",
        wakeMode: "now",
        name: "GitHub",
        sessionKey: "hook:github-pr:{{payload.pull_request.number}}",
        deliver: true,
        channel: "last",
        // Or pin a fixed target:
        // channel: "feishu",
        // to: "chat:oc_xxx",
        model: "openai/gpt-5.2-mini",
        thinking: "low",
        timeoutSeconds: 120,
        transform: {
          module: "github-pr.mjs",
          export: "transformGitHubPrWebhook",
        },
      },
    ],
  },
}
```

Why this shape works well:

- `match.path: "github-pr"` gives you a clean endpoint just for PR events
- `action: "agent"` lets OpenClaw summarize instead of forwarding raw GitHub text
- `sessionKey` keeps updates for the same PR correlated in one thread of activity
- `deliver: true` sends the final summary to chat
- a fast model is usually enough because PR events are structured and repetitive

## Step 3: add a tiny transform module

Put a reviewed transform module under your configured `hooks.transformsDir`, for example:

`~/.openclaw/hooks/transforms/github-pr.mjs`

```js
export function transformGitHubPrWebhook({ payload }) {
  const action = payload.action ?? "unknown-action";
  const pr = payload.pull_request ?? {};
  const repo = payload.repository?.full_name ?? "unknown-repo";
  const number = pr.number ?? payload.number ?? "unknown-number";
  const title = pr.title ?? "(no title)";
  const state = pr.state ?? "unknown-state";
  const draft = pr.draft ? "draft" : "ready-for-review";
  const author = pr.user?.login ?? "unknown-author";
  const branch = pr.head?.ref ?? "unknown-branch";
  const base = pr.base?.ref ?? "unknown-base";
  const url = pr.html_url ?? payload.review?.html_url ?? payload.comment?.html_url ?? "(no url)";
  const requestedReviewers = (pr.requested_reviewers ?? [])
    .map((reviewer) => reviewer.login)
    .join(", ") || "none";
  const reviewState = payload.review?.state ?? "none";
  const merged = pr.merged ? "yes" : "no";

  return {
    kind: "agent",
    message: [
      "You are summarizing a GitHub pull request event for a busy engineering lead, founder, or operator chat.",
      "Write a concise update with:",
      "- repo + PR number + title",
      "- event action",
      "- author",
      "- branch flow",
      "- review state or requested reviewers if relevant",
      "- URL",
      "- one short 'what to do next' line",
      "Preserve identifiers exactly.",
      "Do not dump raw JSON.",
      "Keep it crisp and operational.",
      "",
      `repo: ${repo}`,
      `pr: #${number}`,
      `title: ${title}`,
      `action: ${action}`,
      `state: ${state}`,
      `draft: ${draft}`,
      `author: ${author}`,
      `branch: ${branch} -> ${base}`,
      `requested_reviewers: ${requestedReviewers}`,
      `review_state: ${reviewState}`,
      `merged: ${merged}`,
      `url: ${url}`,
    ].join("\n"),
  };
}
```

This transform is intentionally small.

It does two jobs only:

1. extract the fields humans care about from GitHub's webhook payload
2. hand OpenClaw a clean prompt for the final chat summary

That keeps the workflow easy to debug when your payload shape changes.

## Step 4: point GitHub at the OpenClaw endpoint

In GitHub, create a webhook that sends selected PR-related events to:

```text
https://YOUR-GATEWAY-OR-PROXY/hooks/github-pr
```

A good first event set is:

- **Pull requests**
- **Pull request reviews**
- **Pull request review comments**

And configure the request to include your OpenClaw hook token via:

- `Authorization: Bearer <token>`

If your gateway is not directly internet-reachable, put it behind a trusted reverse proxy or tailnet ingress path that can receive GitHub webhooks safely.

## Step 5: choose the delivery path

You have two good rollout options.

### Option A: deliver to the last active chat

Use:

```json5
channel: "last"
```

This is good when you are still testing and want the summaries to follow the operator's most recent active chat.

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

This is better once the bot belongs in a team room and should not drift with personal activity.

## Step 6: test with a safe sample event

Before trusting real PR traffic, send one controlled request to the endpoint.

```bash
curl -X POST https://YOUR-GATEWAY-OR-PROXY/hooks/github-pr \
  -H 'Authorization: Bearer YOUR_OPENCLAW_HOOK_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "action": "review_requested",
    "repository": { "full_name": "acme/docs-site" },
    "pull_request": {
      "number": 42,
      "title": "Improve onboarding docs",
      "state": "open",
      "draft": false,
      "html_url": "https://github.com/acme/docs-site/pull/42",
      "head": { "ref": "feat/onboarding" },
      "base": { "ref": "main" },
      "user": { "login": "ada" },
      "requested_reviewers": [{ "login": "grace" }],
      "merged": false
    }
  }'
```

Then verify three things:

1. the webhook is accepted
2. the transform runs cleanly
3. the final message is short, readable, and obvious about the next owner action

## A good first prompt/output shape

For most teams, the ideal output is not long.

A strong PR summary looks more like this:

> GitHub PR: acme/docs-site #42 "Improve onboarding docs" is ready for review from `feat/onboarding` to `main`. Ada requested review from Grace. Next step: Grace reviews blocking doc accuracy and merge readiness. https://github.com/acme/docs-site/pull/42

And less like this:

> several noisy GitHub notification fragments pasted into chat with no recommendation

That difference is the whole point of using OpenClaw here.

## Common rollout mistakes

### Forwarding every GitHub event without filtering

This creates notification sludge fast.

Start with PR-focused events only, then expand later if the summaries prove useful.

### Omitting the next owner action

A summary that says what happened but not what happens next is still half-finished.

The best OpenClaw PR summaries always tell someone what to do next.

### Mixing review events and merge events without labels

Always preserve the event type clearly.

A review request and a merged PR should not read like the same kind of message.

### Using an unstable delivery route for operational workflows

`channel: "last"` is great in testing, but real team workflows usually want a fixed room.

If the summary matters, pin the destination.

Reference: [Webhooks security guidance](/automation/webhook#security)

## When to make this workflow richer

Once the basic PR summary bot works, the next useful upgrades are:

- route **review requests** to a quieter engineering room but send **merged PRs** to a broader team room
- include changed-file counts, labels, or CI state when they matter
- split by repository using separate mappings or transform logic
- pair this with deploy alerts so a team sees **PR context before deploy status**

That is why this page pairs naturally with:

- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [OpenClaw Recipes](/recipes)

## Troubleshooting

### GitHub says the webhook delivered, but OpenClaw did nothing

Check:

- the hook URL path really ends with `/hooks/github-pr`
- the token was sent as `Authorization: Bearer ...`
- `hooks.enabled` is on
- the gateway is running
- logs show the request arriving

Start here:

- [Webhooks](/automation/webhook)
- [Gateway troubleshooting](/gateway/troubleshooting)

### The summary arrives, but it is low quality

Usually the problem is the transform or prompt shape, not the transport.

Check whether you are extracting the right payload fields from the exact GitHub events you subscribed to.

### Messages go to the wrong chat

That is usually a routing issue:

- `channel: "last"` followed the last active route
- `to` is missing or wrong for a fixed target
- the underlying channel auth/config is not stable yet

If this matters operationally, move from `channel: "last"` to a fixed `channel` + `to` pair.

## Why this page matters in the first wave

Among first-wave OpenClaw recipe pages, this one earns its spot because it captures a very common buyer and operator question:

**"Can OpenClaw make GitHub activity legible without me building another dashboard?"**

That is a strong search and product-fit question. A good answer helps with both acquisition and activation.

## Related pages

- [OpenClaw Recipes](/recipes)
- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [Hooks](/automation/hooks)
- [Webhooks](/automation/webhook)
- [Gateway troubleshooting](/gateway/troubleshooting)
