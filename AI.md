# AI 核心分流合集

[订阅 AI.list](https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/AI.list)

## 使用

将下面一行加入现有 `[Rule]`。`AI` 必须替换为**现有策略/策略组名**，或先建立名为 `AI` 的策略组；名单不会自动创建策略组。

```ini
RULE-SET,https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/AI.list,AI,extended-matching
```

放在旧的 OpenAI / Anthropic / Gemini、通用 Global、GEOIP 国内直连及 FINAL 规则之前；必要的私有网络、代理节点和解析器直连例外仍放在前面。上线先备份并逐项测试，再删除被替代的旧 AI 规则。若名单内第三方精确主机也需要走 AI 策略，核对广告/劫持规则顺序，不关闭全局广告拦截。

**所有命中合集的服务共用这一行指定的策略。** 若某服务仍需独立国家/节点，保留它的专用规则在合集前作为覆盖。使用一个合集不可能同时自动保留多套不同的策略。

## 范围

- **OpenAI / ChatGPT / Codex**：主站、API、静态资源、用户内容和上游明确列出的部分精确第三方主机；Codex仅指这些域名下的接口，不保证任意CLI第三方依赖。
- **Anthropic / Claude**：Anthropic、Claude新旧主站、用户内容、MCP内容隔离域及上游精确CDN主机。
- **Gemini / AI Studio / Gemini CLI**：主站、生成式API、部分Code Assist端点，不接管整个Google或DeepMind综合官网。
- **NotebookLM / Gemini Notebook**：保留 `notebooklm.google.com` / `notebooklm.google`，补充当前 `notebook.google.com` / `notebook.google`。
- **Grok**：`grok.com`、`x.ai`、`grokusercontent.com` 及子域，包含 `cdn.grok.com`、`accounts.x.ai`、`media.x.ai` 等。

首版不声称覆盖所有AI：Microsoft / GitHub Copilot、Perplexity、中国大陆AI、任意模型提供商、用户自定义MCP、代码仓库和研究数据源不自动加入。

## 为什么不直接拼接上游

这是**范围可控的核心域名合集**，不是全功能防火墙放行表。仅使用 DOMAIN / DOMAIN-SUFFIX，不使用关键词、IP或整段云厂商ASN。

删除上游会影响无关流量的共享根域，例如 `auth0.com`、`stripe.com`、`sentry.io`、`segment.io`、`intercom.io`；不接管整个Google、GitHub、Cloudflare、Azure或LiveKit平台。第三方只采用有来源的有限服务主机，不把社区分组写成官方归属认证。

代价是共享登录、支付、验证码、语音、遥测和外部连接器可能继续按其他规则处理，**不保证所有步骤始终同出口或全部依赖齐全**。发现真实缺漏后补精确依赖，而不是扩大到共享平台根域。

## 来源与许可

1. 选取并修改 blackmatrix7/ios_rule_script 的 [OpenAI](https://github.com/blackmatrix7/ios_rule_script/blob/0b6897dc17e0c513d25449c935a9694818e32f0c/rule/Surge/OpenAI/OpenAI.list)、[Anthropic](https://github.com/blackmatrix7/ios_rule_script/blob/0b6897dc17e0c513d25449c935a9694818e32f0c/rule/Surge/Anthropic/Anthropic.list)、[Gemini](https://github.com/blackmatrix7/ios_rule_script/blob/0b6897dc17e0c513d25449c935a9694818e32f0c/rule/Surge/Gemini/Gemini.list)。上游文件更新时间较早，不将快照提交日期当成每个服务都刚更新。
2. Claude新入口与内容域结合公开服务及[官方网络需求](https://claude.com/docs/claude-science/network-requirements)补充；不将该页的研究数据库、包仓库全部混入合集。
3. NotebookLM旧入口在2026-11-06实际分别跳转至 `notebook.google.com`、`notebook.google`；[Google官方帮助](https://support.google.com/gemininotebook/answer/16164461?hl=en)亦链接新产品入口。
4. Grok依据[官方主站](https://grok.com/)公开页面及响应中的登录、资源引用。不包含整个X / Twitter或支付生态。
5. Code Assist端点结合 [Gemini CLI的公开实现](https://github.com/google-gemini/gemini-cli/blob/3fd7d263466b9e5d30b2da1ebf85dae956a0fb86/packages/core/src/code_assist/server.ts)及既有使用配置；未做所有CLI登录与工具调用验收。

`Surge/AI.list` 是修改后的派生名单，按 **GPL-2.0-only** 发布，保留作者归属与修改日期；随附[完整上游GPL v2文本](LICENSES/ios-rule-script-GPL-2.0.txt)。声明仅针对AI派生文件，不追溯改变本仓库独立金融文件的许可。

## 维护和验证边界

- **手动维护的静态快照**，没有自动发现、每日同步、GitHub Actions或嵌套规则。仓库更新后客户端仍须更新缓存；发布不等于已加载。
- 检查域名格式、唯一条目、正反例与Surge原生条目语法；静态预检不代表真实订阅下载、iOS导入或账号全流程通过。
- `extended-matching`扩展到SNI / HTTP Host，不能修正已得到的错误目的IP。规则可避免已知域名漏入通用直连，但依赖正确顺序、可用域名映射；系统resolver、Surge Host/DoH、Fake IP与应用自带DoH另行核验。
- 不包含DNS配置，不保证DNS / WebRTC隐私，不绕过Cloudflare、地区限制或出口IP风控。首页、登录、对话、上传、语音和工具调用须分别验收。
- 发布本文件不修改任何设备的客户端、DNS、节点或策略选择。
