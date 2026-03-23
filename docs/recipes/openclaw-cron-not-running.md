---
title: "OpenClaw Cron Not Running"
summary: "A practical playbook to diagnose why OpenClaw cron jobs are not firing, not delivering, or running at the wrong time"
sidebarTitle: "OpenClaw Cron Not Running"
read_when:
  - Your OpenClaw cron jobs never seem to run
  - A scheduled automation fired but no message was delivered
  - A cron job runs at the wrong wall-clock time
  - You want a step-by-step troubleshooting flow instead of a generic reference page
---

# OpenClaw Cron Not Running

When someone searches **"OpenClaw cron not running"**, they usually mean one of three things:

1. the scheduler never fires
2. the job fires but nothing gets delivered
3. the job runs, but at the wrong local time

This page is the fast path to isolating which one you have.

If you want the full feature overview first, see [Cron jobs and recurring automations](/automation/cron-jobs). If you already know cron is broken, stay here.

## The fastest diagnosis path

Run this ladder in order:

```bash
openclaw status
openclaw gateway status
openclaw cron status
openclaw cron list
openclaw logs --follow
```

That usually tells you whether the problem is:

- **gateway/runtime not healthy**
- **scheduler disabled**
- **job misconfigured**
- **delivery configured incorrectly**
- **timezone or active-hours mismatch**

## What “healthy” looks like

A healthy cron setup usually has all of these signals:

- `openclaw gateway status` shows the gateway is running
- `openclaw cron status` shows cron is enabled
- `openclaw cron list` shows your job is present and enabled
- logs show future wakes or recent successful runs
- your target delivery channel is connected if the job is supposed to message externally

If one of those is false, that is usually the real failure — not “cron” in the abstract.

## Case 1: OpenClaw cron never fires

This is the classic **"nothing happened"** case.

Start here:

```bash
openclaw cron status
openclaw cron list
openclaw cron runs --id <jobId> --limit 20
openclaw logs --follow
```

### What to look for

#### 1. Cron is disabled

Typical signal:

- `cron: scheduler disabled; jobs will not run automatically`

That means your jobs may exist, but the scheduler itself is off.

Check your config/env and then restart the gateway.

#### 2. The job exists but is disabled

`openclaw cron list` may show the job, but disabled or otherwise inactive.

That means the scheduler is fine; the specific job is not eligible to run.

#### 3. The schedule is valid, but it is simply not due yet

Typical signal:

- `reason: not-due`

This often happens when someone manually inspects a run and assumes the scheduler is broken.

#### 4. The gateway is not actually alive

If the gateway is stopped, wedged, or repeatedly crashing, cron will feel dead even if your config looks fine.

Check:

```bash
openclaw gateway status
openclaw logs --follow
openclaw doctor
```

If the gateway is unstable, treat that as the primary incident first.

## Case 2: Cron fired but no message was delivered

This is the second most common failure pattern.

The scheduler worked. The delivery path did not.

Run:

```bash
openclaw cron runs --id <jobId> --limit 20
openclaw cron list
openclaw channels status --probe
openclaw logs --follow
```

### What to look for

#### 1. Delivery mode is effectively none

A cron run can succeed internally without sending any message out.

Common pattern:

- run status looks `ok`
- but delivery mode/target is unset, `none`, or not what you expected

If the job was meant to notify Telegram, Feishu, Discord, or another channel, confirm the job explicitly targets that path.

#### 2. Channel auth or permissions are broken

Typical signals include:

- `unauthorized`
- `missing_scope`
- `Forbidden`

In that case, cron is not your real problem. Your outbound channel is.

Start with:

- [Channel troubleshooting](/channels/troubleshooting)
- the relevant channel reference page

#### 3. The target channel or recipient is wrong

A job can run successfully yet skip or misroute outbound delivery if `channel`, `to`, or equivalent targeting fields are wrong.

When debugging, do not just ask “did cron run?” Ask **“where was it supposed to deliver?”**

## Case 3: Cron runs at the wrong time

This is usually a timezone problem, not a scheduler bug.

Run:

```bash
openclaw cron list
openclaw config get agents.defaults.userTimezone || echo "agents.defaults.userTimezone not set"
openclaw config get agents.defaults.heartbeat.activeHours
openclaw config get agents.defaults.heartbeat.activeHours.timezone
openclaw logs --follow
```

### The gotchas that bite people most

#### Host timezone vs user timezone

Cron without an explicit timezone generally follows the **gateway host timezone**.

So if your server timezone changed — or your gateway is hosted somewhere unexpected — the job may run at the wrong wall-clock time.

#### Heartbeat rules are different from cron rules

People often mix up **cron** and **heartbeat**.

- cron follows schedule semantics
- heartbeat can be suppressed by `activeHours`, quiet-hours logic, or current session activity

So a quiet heartbeat is not always a broken cron job.

See also: [Cron vs heartbeat](/automation/cron-vs-heartbeat)

#### ISO timestamps without timezone

If a schedule or one-shot run uses an ISO timestamp without timezone context, it may be interpreted as UTC.

That creates the classic **"it ran eight hours off"** type of incident.

## Case 4: Cron ran, but OpenClaw skipped on purpose

Sometimes the system is working exactly as configured.

Examples:

- the job is outside allowed hours
- the run is not due yet
- the main lane is busy and a heartbeat was deferred
- the job completed internally but had no external delivery target

That is why checking the **skip reason** matters more than staring at the schedule string.

## A practical troubleshooting flow

If you want the shortest real-world checklist, use this:

### Step 1: verify the runtime

```bash
openclaw status
openclaw gateway status
```

If the gateway is not healthy, fix that first.

### Step 2: verify the scheduler

```bash
openclaw cron status
openclaw cron list
```

If cron is disabled or the job is disabled, you found the issue.

### Step 3: inspect recent run history

```bash
openclaw cron runs --id <jobId> --limit 20
```

This tells you whether the failure is:

- not due
- skipped intentionally
- run succeeded
- run failed with a concrete error

### Step 4: inspect delivery

```bash
openclaw channels status --probe
openclaw logs --follow
```

If the run succeeded but nobody got the message, delivery config or channel auth is the likely root cause.

### Step 5: inspect timezone assumptions

If the timing is wrong rather than the execution itself, verify:

- gateway host timezone
- explicit cron timezone settings
- any related `activeHours` timezone assumptions

## Common root causes in plain English

These are the issues most likely to cause an “OpenClaw cron not running” report:

1. **cron is disabled**
2. **the job is disabled**
3. **the gateway is down or unhealthy**
4. **the job is not due yet**
5. **the run succeeded but had no outbound delivery target**
6. **the channel is misconfigured or unauthorized**
7. **timezone assumptions are wrong**
8. **the user is actually debugging heartbeat behavior, not cron**

## When to use cron vs heartbeat instead

Sometimes the right fix is changing the automation model, not debugging forever.

Use **cron** when:

- exact schedule timing matters
- you want a predictable daily or hourly run
- the job should execute even without conversational context

Use **heartbeat** when:

- timing can drift a bit
- you want the assistant to batch checks intelligently
- quiet-hours and conversational awareness matter

If the workflow behaves more like a conversational check-in than a hard schedule, [heartbeat may be the better tool](/automation/cron-vs-heartbeat).

## Related pages

- [Cron jobs and recurring automations](/automation/cron-jobs)
- [Automation troubleshooting](/automation/troubleshooting)
- [Cron vs heartbeat](/automation/cron-vs-heartbeat)
- [Channel troubleshooting](/channels/troubleshooting)
- [Gateway troubleshooting](/gateway/troubleshooting)
- [OpenClaw Recipes](/recipes)
