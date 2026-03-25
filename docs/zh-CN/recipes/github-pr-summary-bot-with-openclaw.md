---
title: "GitHub PR Summary Bot with OpenClaw"
summary: "如何用 GitHub Webhook 配合 OpenClaw hook mapping，把 Pull Request、review request、review 和 merge 事件变成可读的聊天摘要"
sidebarTitle: "GitHub PR Summary Bot"
read_when:
  - 你想把 GitHub PR 摘要发到聊天里
  - 你想让 OpenClaw 总结 PR 状态、reviewer 和下一步动作，而不是直接转发原始 GitHub 通知
  - 你想要一份偏实战的工程告警 / review 协作 recipe
---

# GitHub PR Summary Bot with OpenClaw

如果你的团队本来就同时活在 **GitHub** 和 **聊天工具** 里，那么最值得优先上线的 OpenClaw 自动化之一就是这个：

**把 Pull Request 相关事件压成短、准、可执行的聊天摘要。**

这比直接把 GitHub 原始通知倒进群里好得多，因为一条真正有用的 PR 更新，应该让人几秒内看懂：

- 这是哪个仓库、哪个 PR
- 现在发生的是 **新 PR**、**请求 review**、**新的 review**，还是 **已经 merge**
- 谁是当前 owner，下一步轮到谁
- 这是需要领导关注的变化，还是工程团队自己处理即可

这页之所以属于首批高价值页面，是因为它正好踩中三个高意图交叉点：

1. **GitHub 工程工作流流量**
2. **code review 协作痛点**
3. **chat-native 投递**，而这正是 OpenClaw 擅长的地方

它也天然适合和 [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw) 配套使用：
**merge 前看 PR 语境，merge 后看 deploy 状态。**

## 你实际在搭什么

整条链路大概长这样：

```text
GitHub webhook
  -> OpenClaw /hooks/github-pr
  -> mapping + 可选 transform
  -> isolated agent run
  -> 在聊天里发一条简洁 PR 摘要
```

OpenClaw 在这里承担的是 GitHub 原生通知不擅长做好的部分：

- 把不同 PR 事件统一成一种易读结构
- 保留关键标识符，而不是丢失 repo / PR 编号 / URL
- 帮人写出适合在聊天里扫读的短摘要
- 把消息发到 Feishu、Telegram、Slack、Discord、Signal，或者最近活跃的聊天

## 为什么这页值得优先做

和先造一个 PR dashboard 相比，这个 recipe 在早期通常更有杠杆，原因很简单：

- **事实源已经存在**，GitHub 就是 source of truth
- **看消息的人已经在聊天里**，不需要再教育大家打开一个新面板
- **OpenClaw 能做判断，不只是转发**
- **这条模式很容易扩展** 到 issues、CI、deploy、incident 等别的事件流

如果你这一周只能先上线一个工程告警类工作流，这个页面通常值得排在很前面。

## 最适合的首发投递面

如果你的团队已经在用下面这些渠道，这个 recipe 会尤其顺手：

- [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
- [Webhook 接入](/zh-CN/automation/webhook)

如果你还没有先把聊天渠道接通，建议先做那一步。
因为 **PR 摘要只有落到团队真的会看的聊天里，才算有价值。**

## 一条好的 PR 摘要应该回答什么

一条像样的 OpenClaw PR 摘要，通常应该在第一眼回答下面六件事：

1. **哪个 repo、哪个 PR？**
2. **发生了什么？**（opened、updated、review requested、reviewed、merged）
3. **谁在参与？**
4. **哪个分支流向哪个分支？**
5. **当前决策状态是什么？**
6. **下一步谁该做什么？**

这就是这页 recipe 想帮你做到的标准。

## 第 1 步：开启 OpenClaw Webhook

先在 OpenClaw 配置里打开 webhook ingress：

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

重要提醒：

- 尽量把 hook 入口放在 loopback、tailnet 或可信反向代理后面
- hook token 最好单独生成
- **不要**复用主 gateway 的认证 token

参考：[/zh-CN/automation/webhook](/zh-CN/automation/webhook)

## 第 2 步：创建 GitHub PR Hook Mapping

给专门的端点（例如 `/hooks/github-pr`）加一条 mapping：

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
        // 或者固定投递到某个群：
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

为什么这个配置适合作为第一版：

- `match.path: "github-pr"` 给你一个干净的专用入口
- `action: "agent"` 让 OpenClaw 负责摘要，而不是简单转发
- `sessionKey` 可以把同一个 PR 的更新关联到一起
- `deliver: true` 会把结果发回聊天
- 这类结构化事件通常不需要重模型，快模型就够用了

## 第 3 步：加一个很小的 transform 模块

把 transform 文件放到你配置的 `hooks.transformsDir` 下，例如：

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

这个 transform 故意保持很小，只做两件事：

1. 从 GitHub payload 里提取人真正关心的字段
2. 给 OpenClaw 一个清晰、稳定、可调试的摘要提示词

这样以后 GitHub payload 形状变化时，你也更容易排查问题。

## 第 4 步：在 GitHub 里指向 OpenClaw 端点

在 GitHub 仓库里创建一个 webhook，把 PR 相关事件发到：

```text
https://YOUR-GATEWAY-OR-PROXY/hooks/github-pr
```

第一版建议只勾选这几类事件：

- **Pull requests**
- **Pull request reviews**
- **Pull request review comments**

同时把 OpenClaw hook token 放到请求头里：

- `Authorization: Bearer <token>`

如果你的 gateway 不能直接暴露公网，建议放到可信反向代理或 tailnet ingress 后面安全接入。

## 第 5 步：选择投递路径

你有两种很常见的 rollout 方式。

### 方案 A：发到最近活跃聊天

使用：

```json5
channel: "last"
```

这适合测试期，因为摘要会跟着最近活跃的聊天走。

### 方案 B：固定发到某个团队房间

显式指定频道和目标，例如：

```json5
channel: "feishu",
to: "chat:oc_xxx"
```

或者：

```json5
channel: "telegram",
to: "-1001234567890"
```

一旦 PR 摘要开始承担团队协作职责，通常就应该尽快切到固定目标，而不是继续漂在 `channel: "last"`。

## 第 6 步：用一条安全样例事件测试

在把真实 PR 流量接进来之前，先打一条受控请求：

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

然后确认三件事：

1. webhook 是否被 OpenClaw 接收
2. transform 是否正常运行
3. 最终消息是否够短、够清楚、能看出下一步 owner

## 一条好的输出大概长什么样

大多数团队需要的不是长篇大论。

一条好的 PR 摘要更像这样：

> GitHub PR：acme/docs-site #42 "Improve onboarding docs" 已进入 review 阶段，分支从 `feat/onboarding` 合入 `main`。Ada 请求 Grace review。下一步：Grace 检查文档准确性与 merge 就绪度。https://github.com/acme/docs-site/pull/42

而不是这样：

> 把几段 GitHub 原始通知碎片直接贴进群里，没有结论，也没有下一步

这个差别，就是这条 recipe 的核心价值。

## 最常见的 rollout 错误

### 没过滤事件，什么 GitHub 事件都往里灌

这样很快就会把团队群变成通知垃圾桶。

第一版只接 PR 相关事件就够了。
有用以后，再往外扩。

### 没写“下一步该谁干什么”

只说“发生了什么”，不说“接下来谁来处理”，其实还不够。

真正好的 PR 摘要一定会把 owner action 讲清楚。

### review 事件和 merge 事件混成一个口吻

一定要保留事件类型。

请求 review 和 merge 完成，是完全不同的团队信号，不能写得像一回事。

### 用不稳定的投递目标承载真实协作流

`channel: "last"` 适合测试，但正式上线后，PR 摘要通常应该发到固定群或固定 topic。

参考：[/zh-CN/automation/webhook#security](/zh-CN/automation/webhook#security)

## 什么时候值得把这条工作流做得更强

当基础版 PR summary bot 已经工作正常，后面的增强通常值得按这个顺序做：

- 把 **review request** 发到更安静的工程群，但把 **merged PR** 发到更广的团队群
- 根据需要加入 changed files、labels、CI 状态
- 按 repo 分流，用不同 mapping 或 transform 逻辑处理
- 和 deploy alerts 串起来，让团队先看到 **PR 语境**，再看到 **部署结果**

所以这页会自然和下面这些页面形成互链：

- [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw)
- [OpenClaw Recipes](/zh-CN/recipes)

## 故障排查

### GitHub 显示 webhook 已送达，但 OpenClaw 没反应

先检查：

- hook URL 是否真的以 `/hooks/github-pr` 结尾
- token 是否以 `Authorization: Bearer ...` 形式发送
- `hooks.enabled` 是否已打开
- gateway 是否正在运行
- 日志里是否能看到请求进入

从这里开始排：

- [Webhook 参考](/zh-CN/automation/webhook)
- [Gateway 故障排查](/zh-CN/gateway/troubleshooting)

### 摘要能发出来，但质量不高

大多数时候，问题不在传输，而在 transform 或 prompt 形状。

检查你是否从真实订阅的 GitHub 事件里提取到了正确字段。

### 消息发到了错误的聊天

这通常是路由配置问题：

- `channel: "last"` 跟着最近活跃聊天走了
- 固定目标的 `to` 没填或填错了
- 底层聊天渠道配置还不稳定

如果这个工作流已经承担真实团队协作，就该从 `channel: "last"` 切到固定 `channel + to`。

## 为什么这页属于第一波高价值页面

在首批 OpenClaw 流量页里，这页配得上一个名额，因为它回答的是一个非常真实、也非常常见的问题：

**“OpenClaw 能不能不让我再造一个 dashboard，就把 GitHub PR 流量变得可读？”**

这是一个兼具搜索意图和产品激活价值的问题。
回答得好，既能带来新流量，也能提升真实采用率。

## 相关推荐

- [OpenClaw Recipes](/zh-CN/recipes)
- [OpenClaw for Feishu](/zh-CN/recipes/openclaw-for-feishu)
- [OpenClaw for Telegram](/zh-CN/recipes/openclaw-for-telegram)
- [Send Vercel Deployment Alerts with OpenClaw](/zh-CN/recipes/send-vercel-deployment-alerts-with-openclaw)
- [Hooks](/zh-CN/automation/hooks)
- [Webhook 参考](/zh-CN/automation/webhook)
- [Gateway 故障排查](/zh-CN/gateway/troubleshooting)
