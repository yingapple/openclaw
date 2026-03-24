---
title: "OpenClaw for Feishu"
summary: "如何在飞书 / Lark 中接入 OpenClaw，完成权限批准，并上线第一批高杠杆团队工作流"
sidebarTitle: "OpenClaw for Feishu"
read_when:
  - 你想在飞书或 Lark 中运行 OpenClaw
  - 你需要一份偏实战的飞书接入指南，而不是只看原始渠道参考文档
  - 你想把创始人、运营或工程团队工作流放进团队聊天里
---

# OpenClaw for Feishu

如果你的团队本来就主要在 **飞书 / Lark** 里协作，这是最值得优先上线的 OpenClaw 集成之一。

它能把 OpenClaw 变成一个真正落在团队聊天里的 AI 操作助手，用来：

- 在飞书私聊和群聊中回复
- 运行定时检查、日报和周期性简报
- 总结部署告警、PR 动态和各种运营噪音
- 把后续行动继续路由回同一个团队会话

对很多团队来说，**飞书是从“装好了 OpenClaw”走到“大家真的每天在用”最快的一条路。**

## 这套接入最适合谁

如果你希望做到下面这些事，OpenClaw for Feishu 很合适：

- 给创始人或运营负责人一个统一的日常更新入口
- 让团队成员在桌面端和移动端都能和同一个助手对话
- 把内部自动化留在飞书里，而不是先造一个自定义后台
- 通过 cron、webhook 或消息事件触发主动工作流

如果你的团队主要工作在 Telegram，可以看 [OpenClaw for Telegram](/recipes/openclaw-for-telegram)。

## 完成接入后你会得到什么

完成一套干净的飞书接入后，通常会有这几样东西：

1. 一个已连接到 OpenClaw 的飞书 / Lark 机器人
2. 默认基于 pairing 的私聊访问控制
3. 可选的群聊接入能力，并可结合 `@提及` 控制触发
4. 一个稳定的消息投递面，用来发送告警、摘要和周期性报告

这很重要，因为 **OpenClaw for Feishu** 不是“再加一个机器人集成”而已。
它往往是下面三类高价值生产工作流的最短路径：

- 创始人或运营负责人的飞书私聊日报
- 把部署告警汇总进一个团队房间，而不是直接刷 webhook 原始消息
- 在 reviewer 本来就讨论问题的地方发送 GitHub PR 摘要

<CardGroup cols={3}>
  <Card title="飞书渠道参考" icon="book-open" href="/channels/feishu">
    完整的配置、权限、账号与运行时说明。
  </Card>
  <Card title="定时任务" icon="clock-3" href="/automation/cron-jobs">
    安排日报、提醒和周期性运营检查。
  </Card>
  <Card title="Webhook 自动化" icon="webhook" href="/automation/hooks">
    从部署、GitHub、事故系统等外部事件触发 OpenClaw。
  </Card>
</CardGroup>

## 最快见效的推进顺序

如果你想要一条够实战的 rollout 顺序，建议这么做：

### 阶段 1：先让机器人稳定可用

- 创建飞书应用
- 把它接到 OpenClaw
- 批准第一批 pairing 请求
- 确认私聊能正常收发

### 阶段 2：只开启一个团队工作流

只选一个：

- 创始人日报
- 飞书里的部署告警
- 团队群里的 GitHub PR 摘要
- 一个定时的运营检查 / check-in

### 阶段 3：谨慎扩张

- 逐步增加需要接入的群
- 调整 mention 要求
- 按需增加 sender / group allowlist
- 等基础工作流稳定后，再考虑 thread-bound ACP 或多智能体路由

## 配置与上线：OpenClaw for Feishu

### 第 1 步：创建飞书应用

先在飞书开放平台创建企业应用，然后记下：

- **App ID**
- **App Secret**

如果你用的是 Lark 而不是大陆版飞书，需要准备在配置里设置 `domain: "lark"`。

具体的页面操作路径和权限要求，请看：

- [飞书接入参考](/channels/feishu#step-1-create-a-feishu-app)

### 第 2 步：在 OpenClaw 里添加渠道

最简单的方式是用引导流程：

```bash
openclaw onboard
```

如果 OpenClaw 已经装好了，也可以直接添加渠道：

```bash
openclaw channels add
```

然后选择 **Feishu**，填入你的凭据。

### 第 3 步：启动 gateway

```bash
openclaw gateway status
openclaw gateway restart
openclaw logs --follow
```

在继续做飞书事件订阅前，先确认 gateway 已经正常运行。

### 第 4 步：开启长连接收事件

在飞书开放平台里：

- 打开 **Event Subscription**
- 选择 **Use long connection to receive events**
- 添加 `im.message.receive_v1`

这是关键一步：这样机器人就能接收消息，而不用先暴露一个公网 webhook URL。

### 第 5 步：给机器人发私信并批准 pairing

机器人上线后，先在飞书里给它发一条消息。

默认情况下，OpenClaw 会使用基于 pairing 的私聊访问控制。批准 pairing：

```bash
openclaw pairing list feishu
openclaw pairing approve feishu <CODE>
```

批准之后，这个私聊窗口就成了你在飞书里第一个可信的 OpenClaw 控制面。

## 飞书接通后，第一件该做什么

这个集成的目标不是“机器人在线了”就结束，而是让某个真正有用的循环落进团队每天都会看的聊天里。

如果你在犹豫应该接通后马上上线什么，建议按这个判断：

- 如果主要购买方 / 使用方是创始人或运营负责人，先做 **日报 / executive brief**
- 如果主要痛点是生产可见性，先做 **部署告警**
- 如果主要痛点是 review 协作和 GitHub 噪音，先做 **PR 摘要**

这个顺序很重要，因为大多数团队在第一天并不需要五个自动化。
他们需要的是**一个足以证明 OpenClaw 值得留在飞书里的自动化。**

## 适合放进飞书的第一批工作流

### 1. 创始人日报 / executive brief

把飞书作为这些内容的投递渠道：

- 日历摘要
- 重要邮件或 mention
- 关键运营风险
- 项目状态汇总

配套文档建议先看：

- [定时任务](/automation/cron-jobs)
- [Standing orders](/automation/standing-orders)
- [心跳 vs cron](/automation/cron-vs-heartbeat)

### 2. 真正可读的部署告警

不要直接把原始 webhook payload 倒进聊天里。
更好的做法是把部署事件送给 OpenClaw，让它总结：

- 发生了什么变化
- 部署是否成功
- 影响的是哪个环境
- 人应该下一步检查什么

配套文档建议先看：

- [Webhook 自动化](/automation/hooks)
- [Automation webhook 参考](/automation/webhook)

### 3. 在团队聊天里发 GitHub PR 摘要

飞书很适合承接这些信息：

- 新 PR 摘要
- review request 摘要
- merged PR 更新
- issue / PR 分诊提醒

配套文档建议先看：

- [Webhook 自动化](/automation/hooks)
- [GitHub CLI + workflows](/cli/hooks)

### 4. 运营检查和周期性提醒

飞书也很适合这类定时 checklist：

- 早晨健康检查
- 陈旧事故的 follow-up
- 每日支持队列摘要
- 发布冻结提醒

配套文档建议先看：

- [定时任务](/automation/cron-jobs)
- [自动化故障排查](/automation/troubleshooting)

## 飞书 vs Telegram：应该先从哪个集成开始？

两者都属于高价值的第一波集成页，但解决的 rollout 问题略有不同。

在下面这些情况下，优先从 **飞书** 开始：

- 团队本来就把内部协作放在飞书 / Lark
- 你想让创始人、运营、工程在同一个内部聊天空间协同
- 你更在意公司内 adoption，而不是移动端优先的个人访问
- 你希望部署告警、PR 摘要、日报都落在大家已经盯着的工作聊天里

在下面这些情况下，可以先看 [OpenClaw for Telegram](/recipes/openclaw-for-telegram)：

- owner 是明显的 mobile-first 使用方式
- 团队本来就用 Telegram 群或 topic 管理运营
- 你想要一个更轻量的外部 / 跨团队控制面

实际落地里，**飞书通常更适合作为内部团队激活的第一集成；Telegram 更适合作为移动优先的创始人入口。**

## 群聊 rollout 建议

一个常见错误，是太早把机器人放到所有群里。

更好的 rollout 是：

1. 先验证私聊可用
2. 再加一个可信的群
3. 初始阶段保持 `requireMention: true`
4. 只有在你已经确认 prompt、路由和行为都稳定后，再逐步放宽

这样做的好处是：飞书里的 OpenClaw 会更“有用”，而不是更“吵”。

## 早期最重要的配置模式

### 先用 pairing 控制私聊访问

这是内部 rollout 最稳妥的默认设置。

- 未知用户先拿到 pairing code
- 你显式批准谁能用机器人
- 以后如果需要，再转成 allowlist

### 群聊默认要求 @mention

对大多数团队来说，群聊一开始都应该要求 mention。

这样可以避免机器人跳进每一段对话，也能让第一印象更干净。

### 支持 Lark 域名

如果你的租户在 Lark，要显式设置域名：

```json5
{
  channels: {
    feishu: {
      domain: "lark",
    },
  },
}
```

### 飞书里的流式回复

飞书支持通过 interactive cards 做流式输出，这在下面这些场景尤其有用：

- 回答本身较长
- 机器人正在汇总大量上下文
- 用户不想在等待时面对“完全静默”

见：[飞书流式输出配置](/channels/feishu#streaming)

## 一个真正能上线的飞书 v1 方案

很多飞书接入指南到 **“机器人能回消息”** 就结束了。

这对真实上线还不够。

一个足够像样的 v1，通常有这些特征：

- 私聊使用 pairing，而不是完全放开
- 只启用一个可信的运营 / 管理群
- 群里初始要求 `@mention`
- 周期性简报或告警只投递到一个固定聊天
- 保持 streaming 开启，让长摘要看起来不是“卡住了”

一个实际可用的起始配置大致像这样：

```json5
{
  channels: {
    feishu: {
      enabled: true,
      dmPolicy: "pairing",
      groupPolicy: "allowlist",
      groupAllowFrom: ["oc_ops_room"],
      streaming: true,
      groups: {
        oc_ops_room: {
          requireMention: true,
        },
      },
      accounts: {
        default: {
          appId: "cli_xxx",
          appSecret: "xxx",
          botName: "OpenClaw",
        },
      },
    },
  },
}
```

为什么这是一个好的第一版：

- **私聊访问是显式批准的**
- **群聊 rollout 足够收敛**
- 你有 **一个稳定的房间** 可用来发部署告警、PR 摘要或创始人日报
- 它避免了一个常见失败模式：**机器人技术上能用，但最后所有人都把它静音了**

## 飞书接通后，最值得优先上的自动化

一旦飞书机器人已经稳定工作，下一步只做 **下面三选一**，不要一起上：

### 方案 1：把创始人日报发到私聊

适合一个创始人或运营负责人想要稳定早报的场景。

用这些文档继续：

- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [定时任务](/automation/cron-jobs)

### 方案 2：把 PR 摘要发进一个工程群

适合团队已经在飞书里协作 code review，希望减少 GitHub 通知噪音的场景。

用这些文档继续：

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Webhook 自动化](/automation/hooks)

### 方案 3：把部署告警发进一个 ops 群

适合主要痛点不是 code review，而是发布可见性的场景。

用这些文档继续：

- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [Webhook 自动化](/automation/hooks)

关键在于顺序。

**先有飞书分发面，再补一个自动化价值点。**

## 团队版 7 天 rollout 建议

如果你是在一个本来就使用飞书 / Lark 的公司里导入 OpenClaw，第一周可以这样推进：

### Day 1：只验证私聊

- 接通机器人
- 给 1 到 3 个内部用户批准 pairing
- 确认回复、记忆和基础操作都正常

### Day 2：加一个可信群

- 只加一个群
- 保持 `requireMention: true`
- 观察机器人是“有帮助”还是“有噪音”

### Day 3：上线一个真正有用的自动化

只选一个：

- 创始人日报
- 部署告警
- PR 摘要

### Day 4 到 Day 7：先打磨，再扩张

- 优化 prompt
- 收紧投递目标
- 按需增加 sender / group allowlist
- 之后再考虑开更多房间或更多工作流

这个 rollout 顺序看起来保守，但整体通常更快，因为你不用在过度扩张后再回来收拾现场。

## 故障排查：最常见也最关键的失败点

### 机器人收不到消息

通常是下面四类问题之一：

1. 应用还没发布
2. 事件订阅没配完整
3. 长连接没有开启
4. gateway 没有在运行

先从这里查：

- [飞书故障排查](/channels/feishu#troubleshooting)
- [渠道故障排查](/channels/troubleshooting)
- [Gateway 故障排查](/gateway/troubleshooting)

### 私聊能用，但群里不工作

按这个顺序检查：

1. 机器人是否已被拉进群
2. 你是否有 `@mention` 它
3. `groupPolicy` 有没有把这个群挡住
4. 日志里是否显示该入站事件已被接受

### 配置保存了，但看起来还是没有生效

这通常意味着飞书尝试验证或持久化长连接设置时，gateway 其实没有提前启动。

检查：

```bash
openclaw gateway status
openclaw logs --follow
```

## 为什么飞书是高价值的第一集成页

和先做一个自定义内部工具相比，OpenClaw for Feishu 有三个明显优势：

- **上线阻力更低**：团队已经熟悉这个聊天界面
- **激活概率更高**：大家真的会在这里看到并回复消息
- **自动化闭环更快**：定时任务、webhook 摘要和后续追问都能在一个地方完成

所以它既是一个很强的第一集成页，也是一个很强的第一上线场景。

## FAQ：正式在团队里推之前，常见的几个判断

### 我应该先从飞书私聊还是群聊开始？

先从 **私聊** 开始。
这样你能在机器人进入共享空间前，先验证 pairing、prompt 质量和回复风格。

### 创始人更新应该发私聊还是团队群？

高信任的 executive brief 适合发 **私聊**。
PR 摘要、部署告警和需要多人看到的周期性运营更新，更适合发 **团队群**。

### 飞书接收消息一定要暴露公网 webhook 吗？

如果你正确使用 **long connection**，基本消息接收并不需要先暴露公网 webhook。
这也是飞书成为高便利度首发集成的原因之一。

### 接通后最该先做哪个自动化？

选最尖锐痛点对应的那个：

- 可见性问题 -> [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- review 协作问题 -> [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- 创始人定向信息问题 -> [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)

## 下一步推荐阅读

飞书接通后，最自然的下一组页面是：

- [OpenClaw Recipes](/recipes)
- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [飞书渠道参考](/channels/feishu)
- [定时任务与周期性自动化](/automation/cron-jobs)
- [Webhook 自动化](/automation/hooks)
- [渠道故障排查](/channels/troubleshooting)
