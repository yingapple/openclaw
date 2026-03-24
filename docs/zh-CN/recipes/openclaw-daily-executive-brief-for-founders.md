---
title: "OpenClaw Daily Executive Brief for Founders"
summary: "给创始人搭一份每天早上自动送达的执行摘要，把昨天真正重要的信号压成一条可行动消息"
sidebarTitle: "创始人日报"
read_when:
  - 你不想在早上 9 点前切五六个工具，只想看一条创始人级别的摘要
  - 你想让 OpenClaw 按固定时间汇总产品、交付、客户、日程和风险信号
  - 你需要一份基于 cron 的可上线 recipe，而不是自己从 webhook、prompt、定时任务拼起来
  - 你想把高信任执行摘要投递到 Feishu、Telegram 或其他聊天渠道
---

# OpenClaw Daily Executive Brief for Founders

创始人通常不缺信息。

他们真正缺的，是 **把碎片化信息压成决策输入** 的能力。

重要信号散落在太多地方：

- 夜里的部署和回滚
- GitHub PR、review 和 incident
- 客户反馈与团队消息
- 今天的会议和截止时间
- 营收、pipeline、支持工单等经营信号
- 以及大量其实不重要、却会偷走注意力的噪音

一份好的创始人执行摘要，解决的是这个核心问题：

**“今天早上我真正需要知道什么？哪些事值得我亲自介入？”**

这页会告诉你，怎么用 OpenClaw 把这套工作流搭起来。

如果你想先看底层调度能力，可以先读 [Cron jobs and recurring automations](/zh-CN/automation/cron-jobs)。
如果你要的就是面向创始人的完整 recipe，继续看这页。

## 为什么这页值得优先做

这是一页非常值得放进首批流量包的角色型页面，因为它同时吃到几类高价值意图：

- **角色 / use case 搜索意图**：创始人会直接搜“执行摘要”“AI executive assistant”“morning brief”这类词
- **重复性自动化意图**：摘要一旦每天稳定送达，价值会复利累积
- **高留存工作流**：如果它变成晨间习惯，OpenClaw 的粘性会明显上升
- **强内链能力**：它天然能连接 Feishu、Telegram、GitHub、Webhook、Cron、排障页

而且，这页讲的是 **买家能直接理解的价值语言**，不只是技术实现语言。

很多人不会搜索“chat-native cron orchestration”。
他们会搜索的是：

- founder daily brief
- executive summary bot
- AI executive assistant for founders
- morning company summary

## 一份好的创始人摘要应该包含什么

真正有用的摘要，不是 telemetry 墙。

大多数情况下，它应该按这个顺序回答 5 件事：

1. **昨天到现在，什么变了**
2. **什么被卡住了，或者有风险**
3. **今天哪些事需要创始人注意**
4. **哪些东西可以放心忽略**
5. **OpenClaw 接下来应该推动什么动作**

落到实际结构里，通常会变成这些 section：

- 公司整体脉搏
- 产品 / 工程进展
- 客户 / 支持信号
- 今日日程与约束
- top 3 行动建议

## 这个工作流长什么样

高层看，其实就是 5 步：

1. OpenClaw 在早上固定时间运行 cron
2. 它从你关心的几个信号源里抓取重点
3. 它把这些内容压成一条紧凑的执行摘要
4. 它把结果投递到创始人最常看的聊天入口
5. 可选地，它继续做后续动作，比如提醒、追问、建任务

这套 workflow 的关键是：

**先给摘要，再推动动作。**

## 最适合的投递渠道

如果创始人已经在以下渠道里工作，这个 workflow 会特别顺手：

- [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
- 如果公司本来就在用，Discord / Slack 也可以

通常来说：

- **Feishu / Lark** 更适合中国团队、内部协作密集型团队
- **Telegram** 更适合移动优先、经常在路上、或者想要轻量控制面的创始人 / operator

## 第一版先接哪些信号源

第一版一定要比你想象得更窄。

一个高信任 v1，通常只应该接 **3 到 5 个来源**，而不是 12 个。

比较好的起点：

- GitHub PR / deploy 动态
- 昨天最重要的聊天消息或 mention
- 紧急支持 / incident 记录
- 今天的日程
- 一个稳定可用的业务指标快照

最容易做坏的第一版，往往会一口气接这些：

- 全量 CRM
- 所有分析指标
- 全部邮件线程
- 所有内部聊天群

结果就是：**太吵，最后没人看。**

## 示例摘要结构

一条创始人能真的读完的摘要，大概长这样：

```text
Daily Executive Brief — 08:30

1) Company pulse
- 昨夜 1 次生产部署成功
- 2 个客户问题仍未关闭
- 未发现严重基础设施事故

2) Product / engineering
- PR #184 还在等 Alex 最后 review
- 移动端登录修复已于 01:12 发到生产
- 有 1 个不稳定 cron workflow 失败，需要调查

3) Customer / market signal
- SSO 和 export API 各被重复提到 2 次
- 风险最高客户：Acme，今天下午 3 点续约沟通

4) Today’s constraints
- 今天 14:00–18:00 日程较满
- 中午前需要拍板 pricing experiment 是否上线

5) Top actions
- 决定 PR #184 owner 方案
- 确认 Acme 的续约策略
- 判断是否先暂停不稳定 cron workflow
```

这就是这页 recipe 追求的标准：

- 足够短，能快速读完
- 足够具体，读者愿意相信
- 足够结构化，读完就能行动

## 一个务实的 v1 架构

做法很多，但一个靠谱的第一版通常有这几块：

### 1. 一个固定定时任务

用 cron 在早上固定发出。

示例：

```json
{
  "id": "daily-founder-brief",
  "schedule": "30 8 * * 1-5",
  "timezone": "Asia/Shanghai"
}
```

时区一定要按创始人的真实工作日来设，不要偷懒用服务器默认时区。

参考： [Cron jobs and recurring automations](/zh-CN/automation/cron-jobs)

### 一份真的能上线的最小配置

很多“日报”文档只讲策略，不讲怎么落地。
这不够。

一个可信的 v1，至少要把三件事明确写下来：

1. **什么时候发**
2. **发到哪里**
3. **允许总结哪些内容**

一个实用起点可以长这样：

```json5
{
  cron: {
    jobs: [
      {
        id: "daily-founder-brief",
        schedule: "30 8 * * 1-5",
        timezone: "Asia/Shanghai",
        enabled: true,
        prompt: `Prepare the founder's daily executive brief.

Prioritize:
1. critical blockers, incidents, or deadlines
2. meaningful product / engineering movement
3. repeated customer signal
4. today's calendar constraints
5. exactly 3 recommended actions

Keep it concise. Prefer signal over completeness.`,
        deliver: {
          channel: "feishu",
          to: "user:ou_xxx"
        }
      }
    ]
  }
}
```

这个例子故意写得很“朴素”。
这是优点，不是缺点。

对于创始人日报来说：

- 固定发送时间，通常优于动态时间
- 固定收件人，通常优于“发给最后活跃的人”
- 固定输出结构，通常优于看起来很聪明但不稳定的自由发挥

如果你投递到 Telegram，对应目标大致会像这样：

```json5
{
  deliver: {
    channel: "telegram",
    to: "123456789"
  }
}
```

就算你的部署风格导致 cron schema 长得不完全一样，也尽量保持设计意图不变：

**一个稳定 schedule、一个稳定目标、一个稳定摘要契约。**

### 2. 一个清晰的 prompt 合同

你的摘要 prompt 应该明确告诉 OpenClaw：

- 哪些来源重要
- 哪些内容应该忽略
- 摘要大概应该多长
- 紧急程度怎么排序
- action items 应该怎么写

很多时候，**把 prompt contract 写清楚**，比接第六个数据源更重要。

### 3. 一个稳定的投递目标

v1 先发到一个固定的创始人私聊，或者一个固定领导群。

不要从一开始就做“动态路由给谁最后活跃就发给谁”。
创始人执行摘要是高信任工作流，最重要的是可预期。

### 4. 可选的后续动作

摘要发出去以后，OpenClaw 还可以继续做这些事：

- 创建任务
- 在当天稍后发 reminder
- 用户追问时展开某个部分
- 某个风险越阈值时通知对应负责人

但这些都应该是第二阶段增强，不应该成为上线阻塞项。

## 最小实现模式

这类工作流，实践里通常有两种模式。

### Pattern A：prompt 驱动的晨间摘要

适合这些情况：

- 相关数据已经能通过 memory、channels、hooks 或已有上下文拿到
- 你想最快上线第一版

这是最快的起步方式。

### Pattern B：结构化输入 + 总结

适合这些情况：

- 你希望摘要尽量基于更确定的输入
- 你要汇总 webhook、GitHub 变更、支持摘要、日指标快照等结构化来源

这种方式准备工作更多，但可靠性更高，也更不容易“摘要重点跑偏”。

大多数团队应该先用 **Pattern A** 上线，再把真正高价值的部分逐步加固成 **Pattern B**。

## 一个好用的 cron prompt 模板

你可以用下面这个模式作为日报 body 的起点：

```text
You are preparing the founder's daily executive brief.

Goal:
Produce one concise update that helps the founder decide where to spend attention today.

Prioritize:
1. critical risks, blockers, incidents, or deadlines
2. meaningful product / engineering progress
3. repeated customer or market signal
4. today's important calendar constraints
5. the top three recommended actions

Rules:
- Be concise and concrete.
- Prefer signal over completeness.
- Do not list routine noise unless it changes a decision.
- If an item is uncertain, say so briefly.
- End with exactly 3 action items.
```

这个 prompt 故意很有立场。

目标不是生成一份公司流水账。
目标是帮助创始人 **更好分配今天的注意力**。

## 一个能保住信任的 7 天上线顺序

如果你想快速把这套摘要推上线，又不想一开始就把它做成噪音机器，建议按这个顺序来：

### Day 1：先锁定投递目标和时区

- 只选 **一个** 目标：创始人私聊，或者一个 leadership room
- 明确写死真实业务时区
- 第一次定时运行前，先手动 dry run 一次

### Day 2：只用 3 到 5 个输入把第一版发出去

适合 v1 的输入：

- 夜间部署变化
- 重要 PR 变化
- 日程压力
- 一个客户风险信号
- 一条人工整理的经营备注

### Day 3：优化可读性，不要急着扩覆盖面

只问一个问题：

**这条摘要有没有帮创始人更快决定今天该把注意力放在哪？**

如果没有，先删 section，而不是继续加数据源。

### Day 4 到 Day 5：补一个更强的结构化信号

通常最值得先补的是：

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)

### Day 6：验证失败路径

在把它称为 production-ready 之前，先确认“如果今天没发出来，我能不能很快定位问题”。

```bash
openclaw status
openclaw gateway status
openclaw cron status
openclaw cron list
openclaw logs --follow
```

如果这一套排查路径还不清晰，先补这个，不要等第一次静默失效后再补。

参考： [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running)

### Day 7：定义一个升级通知规则

当某一类事件不应该等到第二天早上才出现时，这套 workflow 才真正有操作模型。

比如：

- 生产部署失败 -> 立刻推送
- 高风险客户问题 -> 立刻推送
- 日常 preview deploy 成功 -> 等下一次摘要

这样它就不再只是“一天一条总结”，而是一个真正会区分节奏的执行系统。

## 和其他创始人向页面怎么分工

如果你在判断“第一步到底先上哪一页 / 哪个工作流”，可以用下面这个规则：

| 页面 | 最适合什么情况 | 它最先解锁什么 |
| --- | --- | --- |
| [OpenClaw Daily Executive Brief for Founders](/zh-CN/recipes/openclaw-daily-executive-brief-for-founders) | 创始人最需要的是一个稳定晨间定向循环 | 一个低协作成本、容易形成习惯的每日 workflow |
| [AI Executive Assistant for Founders](/recipes/ai-executive-assistant-for-founders) | 读者需要的是更完整的产品故事 | 把聊天、摘要、告警、follow-up 串起来的总叙事 |
| [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw) | 眼前最痛的是部署可见性 | 用极少行为改变拿到快速运营信号 |
| [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw) | 眼前最痛的是工程 review 噪音 | 更好的 review 协作和更易读的工程动态 |

一个实用经验是：

- 如果目标是形成习惯、建立创始人定向入口，先上 **Daily Executive Brief**
- 如果目标是先压缩某一条很吵的信号流，先上 **Vercel** 或 **GitHub PR**
- 如果你需要讲完整产品故事，再用 **AI Executive Assistant for Founders** 把上面几页串起来

## 这份摘要的信号从哪来

这页最适合和下面这些页面一起看：

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)

因为这些页面负责生成日报真正依赖的原始输入：

- PR 摘要提供工程进展和 review 卡点
- 部署告警提供交付信心和 incident 信号
- Feishu / Telegram 提供低摩擦的投递和后续互动入口

换句话说，创始人日报不是孤立内容。
它是 OpenClaw 其他工作流之上的 **聚合层**。

## 最容易把日报做坏的几个错误

### 太长

如果创始人每天早上都要滚太久，采纳率会掉得很快。

### 把低信号噪音也报进去

不要把每次部署、每条消息、每个低优先级工单都塞进去。
只报会改变决策的内容。

### 状态和行动混在一起

强摘要通常会明确分开：

- 发生了什么
- 哪些有影响
- 接下来做什么

混在一起，就会读起来像杂讯。

### 太早依赖不稳定输入

只要一个输入源经常飘，这整份摘要都会失去信任。
先从你最稳定、最容易可靠总结的来源开始。

### 忘了时区和 quiet hours

内容再好，如果发错时间，用户也会觉得它坏掉了。
时区和调度语义一定要先验证。

## 什么时候这页会特别有价值

当下面这些情况出现时，这个 workflow 的价值会明显放大：

- 创始人每天在产品、销售、运营之间高频切换
- 公司交付频繁，夜里经常有变化
- 团队横跨多个聊天工具或多个时区
- 创始人希望先拥有一种 executive assistant 体验，而不是马上招一个人

最后这一点很关键。

日报是 OpenClaw 最像 **AI Executive Assistant for Founders** 的切入方式之一。
它不只是个转发告警的 bot，而是一个会把碎片压成决策输入的系统。

## Troubleshooting

### 摘要能发出来，但内容很空泛

通常不是模型问题，而是 prompt 质量或输入质量问题。

修法：

- 缩窄输入范围
- 明确风险、决策、行动项的排序逻辑
- 显式要求输出 decisions、risks、action items

### 摘要发晚了，或者发错时间

重点检查：

- cron schedule
- 配置里的 timezone
- 你现在是在排查 cron，还是排查 heartbeat

先看：

- [Cron jobs and recurring automations](/zh-CN/automation/cron-jobs)
- [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running)
- [Cron vs heartbeat](/zh-CN/automation/cron-vs-heartbeat)

### 摘要里混进太多聊天噪音

先减 source set。

大多数时候，问题不是“模型还不够会总结所有东西”。
而是“你一开始就不该把所有东西都喂进去”。

### 创始人想继续往下问细节

这通常是好信号。

一个健康模式是：

- 早上先发一条摘要
- 需要追问时，在同一个线程 / 对话里继续问
- 如果要展开某个子系统，再跳到对应 recipe

## 为什么它属于第一波高价值页面

在首批 8 个高价值流量页里，这页的价值不只是“又一个 recipe”。

它同时承担三件事：

- 承接 **founder / executive assistant** 相关搜索意图
- 把 OpenClaw 讲成一个容易理解的日常工作流
- 把产品叙事跨越集成、自动化和排障串起来

所以它既是 recipe，也是很强的产品故事页。

## 相关页面

- [OpenClaw Recipes](/zh-CN/recipes)
- [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [Send Vercel Deployment Alerts with OpenClaw](/recipes/send-vercel-deployment-alerts-with-openclaw)
- [OpenClaw Cron Not Running](/zh-CN/recipes/openclaw-cron-not-running)
- [Cron jobs and recurring automations](/zh-CN/automation/cron-jobs)
- [Gateway troubleshooting](/zh-CN/gateway/troubleshooting)
