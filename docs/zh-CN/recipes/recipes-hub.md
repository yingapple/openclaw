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

## 从这页下一步去哪

- 想看总入口与完整导航： [OpenClaw Recipes](/zh-CN/recipes)
- 想直接落地飞书： [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- 想直接落地 Telegram： [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
- 想先做部署可见性： [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw)
- 想先做 PR 协作： [GitHub PR Summary Bot with OpenClaw](/zh-CN/recipes/github-pr-summary-bot-with-openclaw)
- 想先做创始人日报： [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders)
- 想先修调度可靠性： [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running)
- 想看完整 founder 工作流： [AI Executive Assistant for Founders](/zh-CN/recipes/ai-executive-assistant-for-founders)
