---
title: "OpenClaw Recipes"
summary: "OpenClaw 中文 Recipes 总入口：集成、自动化、故障排查，以及面向创始人 / 运营者的高价值工作流"
read_when:
  - 你想先看实战 recipe，而不是从底层参考文档开始读
  - 你在判断 OpenClaw 第一批最值得上线的工作流
  - 你想快速找到适合 Feishu、Telegram、定时任务、告警、创始人场景的页面
---

# OpenClaw Recipes

OpenClaw 的参考文档已经很全，但很多人第一次进入文档时，真正想问的是：

- 我应该先做哪一个工作流？
- 哪一页最接近我现在的业务问题？
- 哪些页面是第一批最值得上线、最容易带来真实激活的？

这页就是中文 Recipes 总入口。

它的任务不是解释所有底层能力，而是把高意图读者快速分发到 **首批 8 个高价值流量页**：

- **集成页**：先把 OpenClaw 放进团队已经在看的聊天入口
- **自动化 / 告警页**：把部署、PR、日报这类信号压缩成可读消息
- **排障页**：在定时任务失灵时快速修复信任
- **角色 / 场景页**：用创始人 / 运营者能直接理解的语言讲清产品价值

## 先从最值得做的页面开始

<Columns>
  <Card title="OpenClaw for Feishu" href="/zh-CN/recipes/openclaw-for-feishu" icon="messages-square">
    适合已经在飞书 / Lark 里协作的团队。先接入聊天入口，再叠加日报、PR 摘要、部署通知。
  </Card>
  <Card title="OpenClaw for Telegram" href="/zh-CN/recipes/openclaw-for-telegram" icon="send">
    适合移动优先、希望随时随地可达的创始人和技术团队。先把 OpenClaw 放进手机，再叠加部署告警、PR 摘要和 topic 工作流。
  </Card>
  <Card title="OpenClaw Daily Executive Brief for Founders" href="/zh-CN/recipes/openclaw-daily-executive-brief-for-founders" icon="newspaper">
    给创始人做每天早上的执行摘要，把前一天的重要变化压成一条真正有用的消息。
  </Card>
</Columns>

<Columns>
  <Card title="Send Vercel Deployment Alerts with OpenClaw" href="/recipes/send-vercel-deployment-alerts-with-openclaw" icon="rocket">
    把 Vercel 部署事件变成可读、可跟进的聊天通知，而不是原始 webhook 噪音。
  </Card>
  <Card title="GitHub PR Summary Bot with OpenClaw" href="/recipes/github-pr-summary-bot-with-openclaw" icon="git-pull-request-arrow">
    把 PR 创建、请求 review、review 结果、合并这些事件，压成团队真正看得懂的一条流。
  </Card>
  <Card title="OpenClaw Cron Not Running" href="/zh-CN/recipes/openclaw-cron-not-running" icon="alarm-clock-off">
    当定时任务不触发、发错时间、或者没有送达时，这是最应该先打开的排障页。
  </Card>
</Columns>

<Columns>
  <Card title="AI Executive Assistant for Founders" href="/zh-CN/recipes/ai-executive-assistant-for-founders" icon="briefcase-business">
    看 OpenClaw 如何把聊天、定时任务、部署告警、PR 摘要串成一个创始人工作流。
  </Card>
  <Card title="Cron jobs and recurring automations" href="/zh-CN/automation/cron-jobs" icon="clock-3">
    理解日报、提醒、巡检、定时摘要背后的调度能力。
  </Card>
  <Card title="Webhook automations" href="/zh-CN/automation/hooks" icon="webhook">
    把 OpenClaw 接到 GitHub、Vercel、告警系统或其他外部事件源。
  </Card>
</Columns>

## 这 8 页为什么是第一波

这不是随便挑出来的页面清单。

它们覆盖了最容易拿到高意图流量和真实激活的 5 类需求：

1. **Recipes Hub**：总入口，负责把用户路由到正确页面
2. **Integrations**：先把 OpenClaw 接进真实聊天入口
3. **Alerting / Automation**：把已有事件源变成可读消息
4. **Troubleshooting**：一旦自动化失灵，先修复信任
5. **Role / Use Case**：直接回答“这东西对创始人 / 运营到底有什么用？”

如果只做其中一类，流量会很窄。
如果这五类都覆盖，OpenClaw 才能在用户的不同发现阶段都接得住。

## 按真实问题选入口

| 如果你真正的问题是… | 先看哪一页 | 为什么它值得先看 |
| --- | --- | --- |
| 怎么尽快把 OpenClaw 放进飞书 / Lark？ | [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) | 对飞书团队来说，这是最快的分发入口。 |
| 怎么把 OpenClaw 放进手机里随时可用？ | [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram) | 最适合移动优先团队的控制面。 |
| 怎么把部署事件变成可读告警？ | [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw) | 最快落地、最容易感知价值的 webhook 场景之一。 |
| 怎么让 GitHub review 流量在聊天里变清楚？ | [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw) | 直接把嘈杂 PR 事件压成团队看得懂的摘要。 |
| 怎么让创始人每天早上收到一份执行摘要？ | [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) | 这是最容易形成日常习惯的高价值循环。 |
| 为什么我的 OpenClaw 定时任务没跑？ | [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running) | 自动化一旦失灵，排障优先级最高。 |
| OpenClaw 能不能真正做成创始人的 AI 执行助理？ | [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders) | 这页负责讲完整产品故事，而不只是配置说明。 |
| 我想先看总览，再决定从哪页开始 | [OpenClaw Recipes](/zh-CN/recipes) | 这页就是中文总入口。 |

## 一周内只做一批页面，建议顺序

如果你现在就是在做首批流量页，而不是长期慢慢补文档，建议按这个顺序理解：

1. **先做分发入口**：通常是 [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) 或 [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
2. **再做能快速体现价值的自动化页**：比如部署告警或 PR 摘要
3. **补上排障页**：确保定时任务失灵时有回退路径
4. **再做角色型页面**：把产品讲成创始人 / 运营真正会买单的工作流

核心原则很简单：

**分发和可靠性，通常都比“更高级的故事”更优先。**

## 第一批 8 页当前清单

- Recipes Hub
- OpenClaw for Feishu
- OpenClaw for Telegram
- Send Vercel Deployment Alerts with OpenClaw
- GitHub PR Summary Bot with OpenClaw
- OpenClaw Daily Executive Brief for Founders
- OpenClaw Cron Not Running
- AI Executive Assistant for Founders

这批页面的下一步，不是立刻扩成 30 页。
而是先把它们做得更容易被发现、更容易导航、更容易建立信任。

## 下一步去哪

- 新用户起步：[/zh-CN/start/getting-started](/zh-CN/start/getting-started)
- 想看文档导航总览：[/zh-CN/start/hubs](/zh-CN/start/hubs)
- 想看飞书通道配置：[/zh-CN/channels/feishu](/zh-CN/channels/feishu)
- 想看自动化基础能力：[/zh-CN/automation/cron-jobs](/zh-CN/automation/cron-jobs)
