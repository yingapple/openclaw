---
title: "OpenClaw for Telegram"
summary: "如何在 Telegram 中接入 OpenClaw，完成权限批准，并上线第一批移动优先工作流"
sidebarTitle: "OpenClaw for Telegram"
read_when:
  - 你想在 Telegram 中运行 OpenClaw
  - 你需要一份偏实战的 Telegram 接入指南，而不是只看原始渠道参考文档
  - 你想把创始人、工程或运营工作流放进一个移动优先的聊天入口
---

# OpenClaw for Telegram

如果你想把 OpenClaw 真正装进自己的口袋里，**Telegram 是最值得优先上线的集成之一**。

它能把 OpenClaw 变成一个随时可达、天然适合手机使用的聊天助手，用来：

- 在 Telegram 私聊、群组和 forum topic 里回复
- 在桌面端和移动端都保持可达
- 把告警、摘要和定时自动化投递到团队已经会看的聊天里
- 用 topic 把部署、PR、运营和 ACP 工作流拆开管理

对很多团队来说，**Telegram 是让 OpenClaw 从“终端里的工具”变成“随手就能用的助手”最快的一条路。**

## 这套接入最适合谁

如果你希望做到下面这些事，OpenClaw for Telegram 很合适：

- 让创始人或 owner 在手机上随时能找到 OpenClaw
- 在不做自定义 UI 的前提下，先上线一个轻量团队机器人
- 把部署更新、GitHub 摘要或事故提醒路由到共享聊天
- 用 Telegram 的 topics 把不同工作流隔离，而不需要多开多个机器人

如果你的团队主要协作工具是飞书或 Lark，可以看 [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)。

## 完成接入后你会得到什么

完成一套干净的 Telegram 接入后，通常会有这几样东西：

1. 一个已连接到 OpenClaw 的 Telegram 机器人
2. 默认基于 pairing 的私聊访问控制
3. 可选的群组访问能力，并可结合 mention 规则控制触发
4. 一个移动友好的投递面，用来发告警、摘要和周期性报告

这很重要，因为 **OpenClaw for Telegram** 不是“多支持一个聊天渠道”而已。
它通常是下面几类高价值工作流的最短入口：

- 创始人随时可看的移动日报 / executive brief
- 把部署告警发进一个团队群或 topic，而不是刷原始 webhook 消息
- 在工程团队已经会看的 topic 里发送 GitHub PR 摘要
- 让 ACP / coding 工作流在 topic 里天然分区，而不是混成一团

<CardGroup cols={3}>
  <Card title="Telegram 渠道参考" icon="book-open" href="/channels/telegram">
    完整的配置、权限、topics、流式输出和网络行为说明。
  </Card>
  <Card title="定时任务" icon="clock-3" href="/automation/cron-jobs">
    安排日报、提醒、摘要和周期性运营检查。
  </Card>
  <Card title="Webhook 自动化" icon="webhook" href="/automation/hooks">
    从部署、GitHub、事故系统等外部事件触发 OpenClaw。
  </Card>
</CardGroup>

## 最快见效的推进顺序

如果你想要一条够实战的 rollout 顺序，建议这么做：

### 阶段 1：先让机器人稳定可用

- 在 BotFather 创建 Telegram 机器人
- 把 token 配到 OpenClaw
- 启动 gateway
- 批准第一批私聊 pairing

### 阶段 2：只开启一个真正有用的工作流

只选一个：

- 把部署告警发进 Telegram 聊天
- 把 GitHub PR 摘要发进团队群或 topic
- 给创始人发移动端日报
- 做一个周期性运营提醒 / health check

### 阶段 3：带着护栏扩张

- 先只加一个可信群
- 初始阶段保持 `requireMention: true`
- 再决定是否需要调整 Privacy Mode
- 在扩大范围前，先加好 per-group 或 per-topic allowlist

## 配置与上线：OpenClaw for Telegram

### 第 1 步：在 BotFather 创建机器人

打开 Telegram，给 **@BotFather** 发消息。

执行 `/newbot`，按提示完成创建，并保存 bot token。

更详细的 Telegram 平台侧行为，见：

- [Telegram 渠道参考](/channels/telegram)

### 第 2 步：把 Telegram token 配进 OpenClaw

Telegram 走的是 token 配置，而不是扫码登录。

最常见的配置长这样：

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

你也可以通过环境变量提供 token：

```bash
export TELEGRAM_BOT_TOKEN="123:abc"
```

### 第 3 步：启动 gateway 并批准第一条私聊 pairing

```bash
openclaw gateway
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

批准之后，你和机器人的 Telegram 私聊就会成为第一个可信的 OpenClaw 控制面。

### 第 4 步：把机器人拉进一个群或 topic

一开始不要铺太开。

更稳妥的 rollout 是：

1. 先验证私聊行为
2. 再加一个可信群
3. 保持 mention 触发
4. 确认体验稳定后，再决定是否放宽群可见性

## 接通 Telegram 后，第一件该做什么

目标不是“机器人能回复了”就结束，而是让一个真正有用的循环落进你会经常看的聊天窗口里。

如果你在犹豫接通后先上线什么，建议按这个判断：

- 如果主要使用者是创始人或 owner，先做 **移动端日报 / executive brief**
- 如果主要痛点是发布可见性，先做 **部署告警**
- 如果主要痛点是 review 协作和 GitHub 噪音，先做 **PR 摘要**
- 如果主要痛点是“人在外面也要随时能操作”，先把 **Telegram 私聊入口** 打磨好

这个顺序很重要，因为第一天大多数团队并不需要五个自动化。
他们需要的是**一个能证明 OpenClaw 值得留在 Telegram 里的工作流。**

## 适合放进 Telegram 的第一批工作流

### 1. 创始人移动日报

Telegram 很适合作为这些内容的投递面：

- 早晨优先级提醒
- 日历和邮箱摘要
- 紧急项目 blocker
- 每天收尾时的 executive summary

配套文档建议先看：

- [定时任务](/automation/cron-jobs)
- [心跳 vs cron](/automation/cron-vs-heartbeat)
- [Standing orders](/automation/standing-orders)

### 2. 真正适合手机阅读的部署告警

不要直接把原始部署事件倒进聊天。
更好的做法是让 OpenClaw 把 Telegram 里的部署告警总结成：

- 哪个项目变了
- 影响 preview 还是 production
- 部署成功、失败还是取消
- 下一步应该检查什么

配套文档建议先看：

- [Webhook 自动化](/automation/hooks)
- [Automation webhook 参考](/automation/webhook)

### 3. 在团队 topic 里发 GitHub PR 摘要

Telegram topics 很适合承接这些信息：

- PR review 队列
- 每日 merged PR 摘要
- review request 摘要
- issue / 事故分诊提醒

配套文档建议先看：

- [Webhook 自动化](/automation/hooks)
- [GitHub workflows via CLI and hooks](/cli/hooks)

### 4. 运营提醒和周期性检查

Telegram 也很适合这类按时触发的循环：

- 早晨环境健康检查
- 陈旧事故 follow-up
- 发布协调提醒
- 支持队列摘要

配套文档建议先看：

- [定时任务](/automation/cron-jobs)
- [自动化故障排查](/automation/troubleshooting)

## 群组和 topic rollout 建议

一个常见错误，是把 Telegram 当成第一天就完全放开的机器人表面。

更好的 rollout 是：

1. 先验证私聊可用
2. 再加一个群
3. 保持 mention gating 开启
4. 再判断 Privacy Mode 要不要关
5. 对高价值群 / topic 增加 per-group 或 per-topic allowlist

这样做的好处是：机器人会更像一个高信号助手，而不是一个到处插话的 bot。

## 早期最重要的配置模式

### 先用 pairing 控制私聊访问

这是最稳妥的默认设置。

- 未知用户先拿到 pairing code
- 你显式批准谁能在私聊使用机器人
- 如果后面需要，再转成持久 numeric allowlist

### 群聊默认要求 mention

对大多数团队来说，这都应该是默认起点。

这样可以避免机器人响应每一条消息，也能让第一印象更可控。

### Privacy Mode 与完整群消息可见性

Telegram 机器人默认通常带有 Privacy Mode。

这对安全是好事，但如果你希望机器人在群里看到所有非 mention 消息，通常需要：

- 在 BotFather 里用 `/setprivacy` 关闭 Privacy Mode，或
- 直接把机器人设成群管理员

如果你调整了 Privacy Mode，记得把机器人从群里移除后再重新拉回去，让新设置真正生效。

### 用 topic 做工作流分区

Telegram topics 是这个渠道最有价值的能力之一。

你可以天然把这些流分开：

- 一个 topic 发部署告警
- 一个 topic 发 PR 摘要
- 一个 topic 跑 ACP coding session
- 一个 topic 做通用助理入口

见：[Telegram forum topics and thread behavior](/channels/telegram#forum-topics-and-thread-behavior)

## 一个真正能上线的 Telegram v1 方案

很多 Telegram 接入指南到 **“机器人能回复消息”** 就结束了。

这对真实上线还不够。

一个足够像样的 v1，通常有这些特征：

- 私聊使用 pairing，而不是完全开放
- 只先接入一个可信群或 topic
- 群里默认要求 `@mention`
- 周期性简报或告警只投递到一个固定聊天
- topics 有明确分工，不把所有事情都混到一个总群里

一个实际可用的起始配置大致像这样：

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123:abc",
      dmPolicy: "pairing",
      groupPolicy: "allowlist",
      groups: {
        "-1001234567890": {
          requireMention: true,
          topics: {
            "42": {
              requireMention: false,
            },
          },
        },
      },
    },
  },
}
```

为什么这是一个好的第一版：

- **私聊访问是显式批准的**
- **群 rollout 足够收敛**
- 你有 **一个稳定的群 / topic** 用来发部署告警、PR 摘要或运营提醒
- topics 可以把工作流拆开，避免“所有信息都挤在同一个聊天里”

## Telegram 接通后，最值得优先上的自动化

一旦 Telegram 机器人已经稳定工作，下一步只做 **下面四选一**，不要一起上：

### 方案 1：把创始人日报发到私聊

适合创始人或 owner 想随时在手机上看到高信任摘要的场景。

用这些文档继续：

- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [定时任务](/automation/cron-jobs)

### 方案 2：把 PR 摘要发进一个团队 topic

适合工程团队已经在 Telegram 群 / topic 里协作 review，希望减少 GitHub 通知噪音的场景。

用这些文档继续：

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Webhook 自动化](/automation/hooks)

### 方案 3：把部署告警发进一个 ops 群或 topic

适合主要痛点是发布可见性，而不是 code review 的场景。

用这些文档继续：

- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [Webhook 自动化](/automation/hooks)

### 方案 4：把 ACP / 编码工作流固定到一个 topic

适合想在 Telegram 里直接维护某条长期 coding 线程的团队。

用这些文档继续：

- [ACP agents](/tools/acp-agents)
- [Telegram 渠道参考](/channels/telegram#forum-topics-and-thread-behavior)

关键在于顺序。

**先有 Telegram 分发面，再补一个自动化价值点。**

## 团队版 7 天 rollout 建议

如果你是在一个移动优先、经常在外也要处理事情的团队里导入 OpenClaw，第一周可以这样推进：

### Day 1：只验证私聊

- 接通机器人
- 给 1 到 3 个核心用户批准 pairing
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
- 运营 check-in

### Day 4 到 Day 7：先打磨，再扩张

- 优化 prompt
- 收紧投递目标
- 把高价值工作流拆进不同 topic
- 按需增加 sender / group allowlist
- 之后再考虑开更多群或更多自动化

这个 rollout 顺序看起来保守，但整体通常更快，因为它能避免 Telegram 机器人一开始就变成“所有群都很吵”。

## 故障排查：最常见也最关键的失败点

### 私聊能用，但群里不工作

通常是下面几类问题之一：

1. 群没有被 allowlist 放行
2. `requireMention` 仍然开启，而你没有 mention 机器人
3. Privacy Mode 让机器人看不到普通群消息
4. sender allowlist 把消息挡住了

先从这里查：

- [Telegram 故障排查](/channels/telegram#troubleshooting)
- [渠道故障排查](/channels/troubleshooting)

### 命令能用，但普通群消息不工作

这通常是 Privacy Mode 或群可见性配置问题。

检查：

- BotFather `/setprivacy`
- 机器人是否是群管理员
- 修改设置后是否已经移除并重新拉回机器人

### 轮询或网络表现不稳定

Telegram 的问题经常不是机器人配置本身，而是 DNS / 网络层。

检查：

```bash
openclaw logs --follow
dig +short api.telegram.org A
dig +short api.telegram.org AAAA
```

然后继续看：

- [Telegram polling or network instability](/channels/telegram#polling-or-network-instability)
- [Gateway 故障排查](/gateway/troubleshooting)

## 为什么 Telegram 是高价值的第一集成页

和先做一个自定义 dashboard 相比，OpenClaw for Telegram 有三个明显优势：

- **激活更快**：大家本来就在 Telegram 的桌面端和移动端里
- **可达性更强**：人不在电脑前也能随时找到助手
- **结构足够轻**：群组和 topics 提供了天然分区，不用先造产品层结构

所以它既是一个强集成页，也是一个强产品入口，尤其适合移动优先和 owner-driven 的使用方式。

## FAQ：正式上线前最常见的几个判断

### 应该先从 Telegram 私聊还是群聊开始？

先从 **私聊** 开始。
这样你能在机器人进入共享聊天空间前，先验证 pairing、prompt 质量和回复风格。

### 创始人更新应该发私聊还是团队群？

高信任的 executive brief 更适合发 **私聊**。
PR 摘要、部署告警和需要多人看到的运营更新，更适合发 **团队群或 topic**。

### 机器人要想看见所有群消息，一定要改 Privacy Mode 吗？

如果你只是想用 mention 触发，不一定。
如果你希望它看见非 mention 的普通群消息，通常要调整 Privacy Mode，或者把机器人设成管理员。

### 接通后最该先做哪个自动化？

选最尖锐痛点对应的那个：

- 可见性问题 -> [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- review 协作问题 -> [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- 创始人定向信息问题 -> [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- mobile-first 控制面问题 -> 先把 Telegram 私聊和一个可信 topic 打磨好

## 下一步推荐阅读

Telegram 接通后，最自然的下一组页面是：

- [OpenClaw Recipes](/zh-CN/recipes)
- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Telegram 渠道参考](/channels/telegram)
- [定时任务与周期性自动化](/automation/cron-jobs)
- [Webhook 自动化](/automation/hooks)
- [渠道故障排查](/channels/troubleshooting)
