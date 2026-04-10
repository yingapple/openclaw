---
title: "OpenClaw Recipes Hub"
summary: "中文 Recipes Hub：把 Feishu、Telegram、Vercel 告警、GitHub PR 摘要、创始人日报、Cron 排障和 Founder 用例串成首批高价值流量页入口"
read_when:
  - 你搜索的是 OpenClaw recipes hub / 用例总览 / workflow hub
  - 你想在一页里比较首批 8 个高价值流量页，而不是逐页摸索
  - 你要判断应该先做集成、告警、排障，还是创始人工作流
---

# OpenClaw Recipes Hub

如果你现在不是来读底层参考文档，而是想判断 **OpenClaw 第一批最值得上线的工作流是什么**，这页就是中文总入口。

它服务的不是“已经知道该读哪一页”的人，
而是那些带着下面这些高意图问题进来的读者：

- 我应该先做 Feishu 还是 Telegram？
- 先做部署告警，还是先做 PR 摘要？
- 创始人日报和完整的 AI Executive Assistant 有什么区别？
- 如果 cron 已经不稳定，是不是应该先修可靠性？

一句话：

**这页的任务，是把高意图读者快速路由到首批 8 个最能产生真实激活的页面。**

## 首批 8 页一图看懂

| 类别 | 页面 | 解决什么问题 |
| --- | --- | --- |
| Recipes Hub | [OpenClaw Recipes](/zh-CN/recipes) | 中文 recipes 总入口，负责承接总览与分发意图。 |
| Integrations | [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) / [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram) | 把 OpenClaw 放进团队真正会看的聊天入口。 |
| Alerting | [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) / [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw) | 把原始工程事件压成可读消息。 |
| Troubleshooting | [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running) | 自动化失灵时先修复信任，而不是继续扩页面。 |
| Role / Use Case | [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) / [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders) | 用创始人 / operator 能直接理解的语言讲清价值。 |

## 如果这周只能先做 1 页，怎么选

| 真实优先问题 | 先看哪一页 | 为什么它优先级更高 |
| --- | --- | --- |
| 团队主要在飞书 / Lark 里协作 | [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) | 没有分发入口，后面的摘要和告警再好也没人看。 |
| 核心使用场景是移动端、群组、topic | [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram) | 当“随时在手机里可达”是第一需求时，它比泛化文档更值。 |
| 部署事件很多，但团队没人真正读原始 webhook | [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) | 这是最容易快速感知价值的工程自动化之一。 |
| GitHub review 噪音太大，协作上下文总是断 | [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw) | 先把 review 信号压缩清楚，工程团队最容易买单。 |
| 创始人想每天早上只看一条真正重要的消息 | [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) | 这是最容易形成日常习惯的 recurring workflow。 |
| 定时任务偶发不跑、跑错时间、跑错目标 | [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running) | 可靠性问题不先修，后续所有工作流都会掉信任。 |
| 买方要看的不是单个 recipe，而是完整 founder story | [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders) | 这页负责把聊天、摘要、告警、follow-up 串成一个可信产品叙事。 |

## 真正值得优先覆盖的 5 类搜索 / 流量意图

OpenClaw 首批高价值流量页，不是靠“多写一些文档”堆出来的。
而是要先把下面 5 类 intent 接住：

1. **Recipes Hub intent**：我想知道应该从哪页开始
2. **Integration intent**：我想把 OpenClaw 接进飞书或 Telegram
3. **Troubleshooting intent**：为什么我的 cron 没跑
4. **Role / buyer intent**：这东西对创始人到底有什么用
5. **Comparison intent**：日报、部署告警、PR 摘要，哪一个更该先上

只覆盖其中一类，流量会窄。
把这五类都接住，才更像一张能持续承接搜索意图的入口网络。

## 一个简单的优先级判断规则

按顺序判断：

1. **先做分发入口**：如果 OpenClaw 还没进团队真正会看的聊天面，优先 [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) 或 [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
2. **先做可靠性修复**：如果 cron 已经开始不稳定，先看 [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running)
3. **先做事件压缩**：如果最大痛点是工程噪音，优先 [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) 或 [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw)
4. **再做角色叙事**：如果买方需要的是完整理解，再看 [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) 和 [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders)

核心原则不复杂：

**分发和可靠性，通常都比更高级的故事更优先。**

## 搜索意图路由：按你脑子里的真实问题跳转

很多人虽然点进的是 hub，但脑子里其实已经有一个更具体的问题。

| 如果你脑子里更像是在搜…… | 最该先看的页面 | 为什么它应该优先 |
| --- | --- | --- |
| "OpenClaw recipes" / "OpenClaw 工作流" / "我能用 OpenClaw 做什么" | [OpenClaw Recipes](/zh-CN/recipes) | 当你想先看完整地图，而不是直接钻某个单点场景时，总入口仍然是最好的第一站。 |
| "OpenClaw for Feishu" / "Lark bot with OpenClaw" | [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) | 当真实需求是把 OpenClaw 放进公司聊天主战场时，这页应该先赢。 |
| "OpenClaw for Telegram" / "Telegram AI bot" | [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram) | 当真实需求是移动端可达、群组协作、topic 路由时，这页优先级最高。 |
| "Vercel deployment alerts in chat" / "deployment summary bot" | [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) | 这是把原始部署事件变成可读告警流的最快入口。 |
| "GitHub PR summary bot" / "PR updates in team chat" | [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw) | 当 review 噪音大、责任归属总断档时，这页最值得先做。 |
| "daily executive brief for founders" / "创始人日报" | [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) | 当买方真正要的是每天早上一条有用消息，这是最容易形成习惯的第一工作流。 |
| "OpenClaw cron not running" / "cron 没跑" | [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running) | 只要调度可靠性已经出问题，排障就应该比继续扩故事更优先。 |
| "AI executive assistant for founders" / "创始人 AI 助理" | [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders) | 当对方想理解完整 founder operating model，而不是单个 setup guide，这页最适合承接。 |

## 按团队形态选第一条路径

当问题不只是“哪页能排到流量”，而是 **哪条路径最可能真的带来激活** 时，用这张表。

| 团队形态 | 最该先做的页面 | 第二步最适合接哪页 | 为什么这个顺序更有效 |
| --- | --- | --- | --- |
| Feishu / Lark 原生团队 | [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) | [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) 或 [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw) | 先拿到公司聊天里的分发入口，再用日报或 PR 摘要证明价值，转化更快。 |
| 移动优先的创始人或精简技术团队 | [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram) | [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) | 先解决可达性，再用部署告警制造最快的“立即有用”体验。 |
| 正在评估完整 founder workflow 的买方 | [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders) | [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) | 先讲清系统故事，再落到最容易形成使用习惯的日报。 |
| 已经被工程噪音淹没的团队 | [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) 或 [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw) | [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running) | 先做信号压缩最容易出价值，但要尽快补上可靠性兜底。 |
| 调度不稳定、自动化常常沉默的团队 | [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running) | [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu) 或 [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram) | 当自动化都不可信时，先修信任，再扩分发，整体效率更高。 |

## FAQ：首个 OpenClaw recipe 到底怎么选

### 我应该先做 Feishu 还是 Telegram？

如果团队本来就活在 Feishu / Lark 里，优先 **Feishu**。
如果第一需求是移动端可达、轻量群组和 topic 路由，优先 **Telegram**。

### 我应该先做 founder 页面，还是先做工程告警页面？

如果买方最想要的是“每天早上一条真正有用的更新”，先做 **founder 页面**。
如果最尖锐的痛点是部署噪音或 PR 协作混乱，先做 **工程告警页面**。

### 什么情况下 cron 排障应该压过新工作流页面？

只要定时任务已经开始沉默、延迟、偶发不送达，就应该先修 cron。
否则后面的日报、告警、摘要页做得再好，用户也不会真正信任它。

### 如果我只想看 OpenClaw 的完整故事，应该从哪页开始？

想看创始人 / operator 视角的完整系统故事，先看 [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders)。
想先看整个首批工作流地图，先看 [OpenClaw Recipes](/zh-CN/recipes)。

## 从这页下一步去哪

- 想看总入口与完整导航： [OpenClaw Recipes](/zh-CN/recipes)
- 想直接落地飞书： [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- 想直接落地 Telegram： [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
- 想先做部署可见性： [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw)
- 想先做 PR 协作： [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw)
- 想先做创始人日报： [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders)
- 想先修调度可靠性： [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running)
- 想看完整 founder 工作流： [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders)
