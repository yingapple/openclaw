---
title: "OpenClaw Cron 不运行怎么办"
summary: "一份实操型排查手册，帮你定位为什么 OpenClaw 的 cron 任务不触发、不投递，或者在错误的时间执行"
sidebarTitle: "Cron 不运行"
read_when:
  - 你的 OpenClaw cron 任务看起来一直没有执行
  - 定时自动化确实触发了，但没有把消息送出去
  - cron 在错误的本地时间运行
  - 你想要的是按步骤排查，而不是只看泛泛的参考文档
---

# OpenClaw Cron 不运行怎么办

当有人搜索 **“OpenClaw cron 不运行”**，通常真正遇到的是下面三类问题之一：

1. 调度器根本没有触发
2. 任务触发了，但消息没有送达
3. 任务执行了，但时间不对

这页的目标，就是让你尽快分辨自己属于哪一类。

如果你想先看功能总览，请看 [Cron jobs and recurring automations](/zh-CN/automation/cron-jobs)。如果你已经确定是 cron 有问题，就直接按这页排查。

## 最快的诊断路径

按顺序跑这一组命令：

```bash
openclaw status
openclaw gateway status
openclaw cron status
openclaw cron list
openclaw logs --follow
```

大多数时候，这几步就能告诉你问题属于哪一类：

- **gateway / runtime 本身不健康**
- **调度器被禁用了**
- **job 配置有误**
- **消息投递链路配置错了**
- **时区或 active hours 语义不一致**

## 什么算是“健康”的 cron 状态

一个健康的 cron 配置，通常同时满足这些信号：

- `openclaw gateway status` 显示 gateway 正在运行
- `openclaw cron status` 显示 cron 已启用
- `openclaw cron list` 能看到你的 job，且它是启用状态
- 日志里能看到未来调度或最近成功执行的记录
- 如果任务本来就要往外发消息，对应频道已经连通

只要这里有一条不成立，往往真正坏掉的就不是“cron 抽象概念”，而是其中某个更具体的环节。

## 场景 1：OpenClaw cron 根本没有触发

这是最典型的 **“什么都没发生”** 场景。

先跑：

```bash
openclaw cron status
openclaw cron list
openclaw cron runs --id <jobId> --limit 20
openclaw logs --follow
```

### 重点看什么

#### 1. Cron 被禁用了

典型信号：

- `cron: scheduler disabled; jobs will not run automatically`

这意味着 job 可能是存在的，但调度器本身没有开。

先检查配置或环境变量，然后重启 gateway。

#### 2. Job 存在，但它本身被禁用了

`openclaw cron list` 可能能看到 job，但状态是 disabled 或不可执行。

这说明调度器没问题，真正不能跑的是这条 job 本身。

#### 3. 调度配置没错，但它其实还没到触发时间

典型信号：

- `reason: not-due`

这类情况很常见：人手动看了一眼，就误以为调度器坏了。

#### 4. Gateway 实际上没有正常活着

如果 gateway 停了、卡住了，或者一直崩溃重启，cron 也会表现得像“完全没反应”，哪怕你的配置表面上没问题。

检查：

```bash
openclaw gateway status
openclaw logs --follow
openclaw doctor
```

如果 gateway 本身不稳定，优先把它当成主故障处理。

## 场景 2：Cron 触发了，但消息没有送达

这是第二常见的问题。

也就是说：**调度器工作了，投递链路没工作。**

运行：

```bash
openclaw cron runs --id <jobId> --limit 20
openclaw cron list
openclaw channels status --probe
openclaw logs --follow
```

### 重点看什么

#### 1. Delivery mode 实际上等于没有投递

一条 cron run 在系统内部可以执行成功，但并不代表它一定发出了外部消息。

常见模式：

- run 状态看起来是 `ok`
- 但 delivery mode / target 其实没设、是 `none`，或者根本不是你以为的目标

如果这条 job 本来应该通知 Telegram、Feishu、Discord 或其他频道，就必须确认这条 job 明确地指向了那个投递路径。

#### 2. 频道认证或权限坏了

典型信号包括：

- `unauthorized`
- `missing_scope`
- `Forbidden`

如果是这种情况，真正出问题的不是 cron，而是你的出站频道。

先看：

- [频道排障](/zh-CN/channels/troubleshooting)
- 对应频道的参考文档

#### 3. 目标频道或收件人写错了

一条 job 可以运行成功，但如果 `channel`、`to` 或等价的目标参数写错，外部投递仍然可能被跳过或发错地方。

所以排查时不要只问：**“cron 到底跑没跑？”**
而要问：**“它原本应该发到哪里？”**

## 场景 3：Cron 在错误的时间执行

这通常是时区问题，不是调度器 bug。

运行：

```bash
openclaw cron list
openclaw config get agents.defaults.userTimezone || echo "agents.defaults.userTimezone not set"
openclaw config get agents.defaults.heartbeat.activeHours
openclaw config get agents.defaults.heartbeat.activeHours.timezone
openclaw logs --follow
```

### 最容易踩坑的点

#### Host 时区 vs 用户时区

如果 cron 没有显式指定 timezone，通常会跟着 **gateway 所在主机的时区** 走。

所以如果你的服务器时区变了，或者 gateway 实际跑在你没意识到的地区，job 就会在错误的本地时间触发。

#### Heartbeat 和 cron 不是同一套规则

很多人会把 **cron** 和 **heartbeat** 混在一起看。

- cron 走的是 schedule 语义
- heartbeat 则可能被 `activeHours`、quiet hours 或当前会话活跃状态压制

所以 heartbeat 没说话，不一定等于 cron 坏了。

另见：[Cron vs heartbeat](/zh-CN/automation/cron-vs-heartbeat)

#### 不带时区的 ISO 时间

如果某个 schedule 或 one-shot 任务使用了不带时区语义的 ISO 时间戳，它很可能会被按 UTC 解释。

这就是经典的 **“整整差了 8 小时”** 故障来源。

## 场景 4：Cron 跑了，但 OpenClaw 是“有意跳过”

有时候系统其实完全正常，只是按配置决定不做事。

例如：

- 当前不在允许执行的时间段里
- 任务还没到触发时机
- 主 lane 正在忙，heartbeat 被延后
- 任务内部执行成功，但没有配置外部投递目标

所以，排查时真正重要的是 **skip reason**，不是只盯着 cron 表达式本身。

## 一份实用的排查流程

如果你只想用最短路径定位问题，就按这套走：

### 第 1 步：确认 runtime 是否健康

```bash
openclaw status
openclaw gateway status
```

如果 gateway 不健康，先修它。

### 第 2 步：确认调度器是否开启

```bash
openclaw cron status
openclaw cron list
```

如果 cron 被禁用，或者 job 本身被禁用，你就已经找到问题了。

### 第 3 步：看最近执行历史

```bash
openclaw cron runs --id <jobId> --limit 20
```

这一步通常能告诉你它属于哪一类：

- not due
- 被系统主动跳过
- 执行成功
- 执行失败并带具体错误

### 第 4 步：确认投递链路

```bash
openclaw channels status --probe
openclaw logs --follow
```

如果 run 成功了，但没人收到消息，那大概率是投递配置或频道认证出了问题。

### 第 5 步：确认时区假设

如果问题不是“没执行”，而是“执行时间不对”，就去核对：

- gateway 主机时区
- cron 显式指定的 timezone
- 和 `activeHours` 相关的 timezone 假设

## 最常见的根因，用人话说

下面这些问题，最容易导致“OpenClaw cron 不运行”：

1. **cron 被禁用了**
2. **job 被禁用了**
3. **gateway 挂了或不健康**
4. **其实还没到执行时间**
5. **任务执行成功了，但没有外发目标**
6. **频道配置错误或认证失效**
7. **时区假设错了**
8. **你真正排查的是 heartbeat，而不是 cron**

## 什么时候应该用 cron，什么时候该用 heartbeat

有时候最好的修复，不是继续死磕排障，而是换对自动化模型。

适合用 **cron** 的场景：

- 你要求精确时间执行
- 你需要稳定的每日 / 每小时调度
- 任务应该独立运行，不依赖对话上下文

适合用 **heartbeat** 的场景：

- 执行时间允许轻微漂移
- 你希望助手智能地批量检查几类事情
- quiet hours、对话语境和“别太打扰人”很重要

如果这个 workflow 更像是“带语境的对话式检查”，那你可能更应该用 [heartbeat](/zh-CN/automation/cron-vs-heartbeat)，而不是继续把它硬塞进 cron。

## 相关页面

- [Cron jobs and recurring automations](/zh-CN/automation/cron-jobs)
- [Automation troubleshooting](/zh-CN/automation/troubleshooting)
- [Cron vs heartbeat](/zh-CN/automation/cron-vs-heartbeat)
- [频道排障](/zh-CN/channels/troubleshooting)
- [Gateway 排障](/zh-CN/gateway/troubleshooting)
- [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
