---
title: "Send Vercel Deployment Alerts with OpenClaw"
summary: "如何用 Vercel Webhook 配合 OpenClaw hook mapping，把原始部署事件变成可读的飞书、Telegram、Slack、Discord 或最近活跃聊天告警"
sidebarTitle: "Vercel Deployment Alerts"
read_when:
  - 你想把 Vercel 部署告警发到聊天里
  - 你想让 OpenClaw 总结部署成功、失败，以及 preview / production 变化
  - 你想看一份实战型 webhook recipe，而不是自己拼 hooks 文档
---

# Send Vercel Deployment Alerts with OpenClaw

如果你的团队已经在 **Vercel** 上发布，那么最值得优先上线的 OpenClaw 自动化之一就是这个：

**把原始 Vercel 部署事件接进来，做摘要，再只把真正有用的版本发到聊天里。**

这样你拿到的就不是一坨原始 webhook JSON，而是更适合人快速扫读的告警：

- 哪个项目发生了变化
- 这是 **production** 还是 **preview**
- 部署是 **成功**、**失败**，还是需要跟进
- 人下一步应该检查什么

这页 recipe 之所以适合放进第一波高价值页面，是因为它一次串起了三件高意图事情：

1. **webhook 接入**
2. **agent 摘要**
3. **聊天投递**

如果你的 OpenClaw 已经装好，并且至少有一个聊天渠道可用，那么这通常是下一步最有杠杆的自动化之一。

## 你实际在搭什么

这条链路大概长这样：

```text
Vercel webhook
  -> OpenClaw /hooks/vercel
  -> mapping + 可选 transform
  -> isolated agent run
  -> 在聊天里发一条简洁告警
```

OpenClaw 在这里承担的是 Vercel 本身不擅长做好的那部分：

- 把噪音很多的 payload 规整出来
- 保留关键标识符
- 写一段适合人读的短摘要
- 把结果投递到飞书、Telegram、Discord、Slack、Signal，或者最近活跃的聊天会话

## 为什么这页值得优先做

和先做一个自定义部署看板相比，这个 recipe 在早期更有杠杆，原因很简单：

- **部署事件本来就已经存在**，你不需要再造一个事实来源
- **值班人和运营人本来就在聊天里**，告警会落在他们真的会看到的地方
- **OpenClaw 可以做摘要，而不是做转发**，消息可以围绕动作来写，而不是围绕 payload 结构来写
- **这条模式很容易复用**，一旦 Vercel 这条链路跑通，同样的 webhook 路径可以迁移到 GitHub、CI、事故系统或其他 ops 信号

## 最适合的首发投递面

如果你的团队已经在用下面这些渠道，这个 recipe 会尤其顺手：

- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Webhook 接入](/automation/webhook)

如果你还没有先把聊天渠道接通，建议先做那一步。因为 **Vercel 告警只有在有明确落点时才真正有价值。**

## 一条好的部署告警应该回答什么

一条像样的 OpenClaw 部署告警，通常应该第一眼就回答下面五件事：

1. **哪个项目？**
2. **哪个分支 / commit / 作者？**
3. **是 production 还是 preview？**
4. **成功、失败，还是取消？**
5. **人接下来要检查什么？**

这就是这页 recipe 试图帮助你达到的标准。

## 第 1 步：开启 OpenClaw webhook

在 OpenClaw 配置里先打开 webhook 接入：

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

有几件事要注意：

- 能走回环、本地 tailnet，或可信反向代理时，就不要直接裸暴露出去
- hook token 最好单独一套
- **不要**复用主 gateway 的 auth token

参考文档： [Webhooks](/automation/webhook)

## 第 2 步：给 Vercel 建一个独立 hook mapping

建议给它单独开一个 endpoint，比如 `/hooks/vercel`。

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
        // 或者固定投递到某个目标：
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

这个配置形状为什么适合做第一版：

- `match.path: "vercel"` 让部署事件有一个干净的专属入口
- `action: "agent"` 让 OpenClaw 负责摘要，而不是简单转发文本
- `sessionKey` 能把同一个 deployment 的后续更新串在一起
- `deliver: true` 会把最终结果发到聊天里
- 用低延迟模型加低 thinking，通常就足够处理部署告警

## 第 3 步：加一个很小的 transform 模块

把一个已审阅的 transform 模块放到你配置好的 `hooks.transformsDir` 里，比如：

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

这个模式好用，是因为 transform 只做两件事：

1. 从原始 webhook payload 里抽出关键字段
2. 把这些字段整理成一份干净 prompt，交给 OpenClaw 去生成面向人的摘要

这样自动化会更可读，也更容易排查。

## 第 4 步：把 Vercel 指向 OpenClaw endpoint

在 Vercel 里创建一个 webhook，让它把部署事件发到：

```text
https://YOUR-GATEWAY-OR-PROXY/hooks/vercel
```

并通过下面这种方式带上 OpenClaw hook token：

- `Authorization: Bearer <token>`

如果你的 gateway 不能直接从公网访问，就把它放在可信反向代理或 tailnet ingress 后面，再让 Vercel 安全地调用。

## 第 5 步：选择投递路径

这一步通常有两种靠谱的 rollout 方式。

### 方案 A：发到最近活跃聊天

用：

```json5
channel: "last"
```

适合还在测试阶段、希望告警跟着当前活跃操作者走的情况。

### 方案 B：固定投递到一个明确目标

用显式的 `channel` + `to`，例如：

```json5
channel: "feishu",
to: "chat:oc_xxx"
```

或者：

```json5
channel: "telegram",
to: "-1001234567890"
```

当这条自动化已经稳定，明确属于某个团队房间时，这种方式更好。

## 第 6 步：先用一个安全样本测试

在把生产部署流量直接接进来之前，先手工打一次受控请求。

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

然后确认三件事：

1. webhook 请求被成功接受
2. transform 正常执行
3. 发到聊天里的最终消息够短、够清楚、能指导下一步动作

## 第一版输出应该长什么样

对大多数团队来说，理想告警不应该太长。

一条好的结果更像这样：

> Vercel deploy：docs-site 的 preview 在 main (abc1234) 上已 READY。URL: https://demo-preview.vercel.app。下一步：确认文档变更渲染正常、关键链接可访问。

而不是这样：

> 一整段巨长的原始 JSON payload 被直接贴进聊天

这两者之间的差别，就是这里使用 OpenClaw 的全部意义。

## 常见 rollout 错误

### 直接把原始 payload 发进聊天

这只会制造噪音，不会产生信号。

应该走 transform + agent 这条链路，让人看到的是摘要，而不是 dump。

### preview 和 production 不做明显区分

目标环境一定要保留清楚。

一条没有写明 **preview / production** 的部署告警，价值会大打折扣。

### 上来就用太贵的模型

部署告警一般发生频率不低，而且结构很固定。

建议先从更快、更便宜的模型和低 thinking 开始。

### webhook 入口没有边界条件

建议至少配好下面这些基本护栏：

- 专用 hook token
- 可信 ingress 路径
- 有上限的 timeout
- 稳定的 session key 前缀，比如 `hook:vercel:`

参考： [Webhook 安全建议](/automation/webhook#security)

## 什么时候值得把这条工作流做得更丰富

一旦第一版部署告警已经能稳定工作，下一步更值得做的是：

- 把 **production failure** 路由到团队主频道，而把 **preview success** 发到更安静的地方
- 如果 payload 里有信息，就带上 commit 作者或 PR 上下文
- 按项目拆分 mapping 或 transform 逻辑
- 和 GitHub recipe 合起来，让部署告警与 PR 摘要落到同一个聊天面

也正因为如此，这页天然适合和下面这些页面互相串联：

- [GitHub PR Summary Bot with OpenClaw](/recipes/github-pr-summary-bot-with-openclaw)
- [OpenClaw Daily Executive Brief for Founders](/recipes/openclaw-daily-executive-brief-for-founders)

如果这些页面在你的 docs build 里还没上线，把它们当成这页之后的下一批 recipe 即可。

## 故障排查

### Vercel 明明发出了 webhook，但 OpenClaw 没反应

先检查：

- hook URL 结尾是否真的是 `/hooks/vercel`
- token 是否通过 `Authorization: Bearer ...` 发送
- `hooks.enabled` 是否已打开
- gateway 是否在运行
- 日志里是否真的看到了请求进入

先看这些文档：

- [Webhooks](/automation/webhook)
- [Gateway 故障排查](/gateway/troubleshooting)

### webhook 到了 OpenClaw，但摘要质量不对

大多数情况下，问题不在传输层，而在 transform。

先确认你收到的真实 Vercel payload 结构，看看 transform 抽取的字段是否正确。

### 消息发到了错误的聊天

这通常是投递路由问题：

- `channel: "last"` 跟随了最近活跃路由
- 固定目标模式下，`to` 没填或填错
- 底层渠道认证 / 配置本身还不稳定

如果这条告警已经开始承担真实运维职责，建议从 `channel: "last"` 切到固定的 `channel` + `to` 组合。

## 为什么这页值得进入第一波

在第一波 OpenClaw recipe 页面里，这页值得优先补齐，是因为它正好处在这几个交叉点上：

- **自动化意图**
- **创始人 / 工程团队 use case**
- **实战型 webhook 接入**
- **chat-native 的运维工作流**

它不只是一个部署告警教程。
它还是一个模板，展示 OpenClaw 如何把 noisy machine events 变成 useful human messages。

## 相关页面

- [OpenClaw Recipes](/recipes)
- [OpenClaw for Feishu](/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/recipes/openclaw-for-telegram)
- [Hooks](/automation/hooks)
- [Webhooks](/automation/webhook)
- [Gateway 故障排查](/gateway/troubleshooting)
