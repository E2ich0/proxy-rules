# proxy-rules

个人维护的 **Surge 分流规则**：新增 [AI 核心合集说明](AI.md)，原金融范围仍仅限 **Bank of America（美国银行 / BofA）、Merrill / Merrill Edge（美林）和 PayPal**。不是所有金融网站的合集，也不是美国合众银行 U.S. Bank 的规则。只维护 Surge，不生成 Clash / Mihomo / MRS。

仅发布不带策略名的域名规则及说明；不发布完整客户端配置、节点、订阅凭证、账户信息或 MITM 材料。

## 订阅

| 文件 | 内容 | 原始地址 |
| --- | --- | --- |
| `Surge/AI.list` | ChatGPT / Codex、Claude、Gemini / AI Studio / Gemini CLI、NotebookLM / Gemini Notebook、Grok 核心域名；[用法、来源、许可与边界](AI.md) | [AI.list](https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/AI.list) |
| `Surge/Finance.list` | 金融合集：BofA / 美林 10 条 + PayPal 3 条，共 13 条 | [Finance.list](https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/Finance.list) |
| `Surge/BankOfAmerica.list` | BofA / 美林专用 10 条，保留原订阅路径，不夹入 PayPal | [BankOfAmerica.list](https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/BankOfAmerica.list) |

Finance.list 中 BofA 部分与 BankOfAmerica.list 相同。两份为静态平铺的规则文件，不依赖嵌套规则集或构建器；更改 BofA 时须同步更新两份并验证集合一致。通常二选一，不必重复订阅。

## 金融规则的 Surge 用法

将规则加入现有 `[Rule]`，放在可能提前匹配的广告/劫持名单、通用代理、直连规则及 `FINAL` 之前（保留必要的私有网络及节点直连例外在前）：

```ini
RULE-SET,https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/Finance.list,金融,extended-matching
```

`金融` 必须是配置中已存在的策略或策略组。名单不含策略，不是完整配置；不要用它覆盖完整 profile。仅需要 BofA 时可将 URL 改为 `BankOfAmerica.list`。

**优先级注意：**部分广告名单含 `DOMAIN-KEYWORD,analytics.`，会抢先匹配本名单内的 `awuseb.advanced-web-analytics.com`。要让该精确主机采用金融策略，必须把金融 RULE-SET 放在该广告名单之前；这是主动保留名单内第三方分析请求的选择，不证明它对登录必不可少。其他未列入金融名单的广告请求仍按原规则处理。更新旧 BofA 订阅时也应核对这个顺序。

### 出口建议

- 金融组独立手动选择常用出口，不和 AI 的下层选择组联动；选固定节点，而不是 smart、url-test 或自动 fallback 组。保留手动备用选项，故障时再切换。
- 账号常用地区、历史出口及实际业务可用性决定选择，不因“金融”就强制所有账户走美国。
- 国内银行、支付宝、微信支付不在名单里，保留客户端原有路由。
- PayPal 被嵌入其他商户结账时，商户本身仍走原规则；不保证商户与支付平台同出口，也不为此扩大到所有电商或支付服务。
- `extended-matching` 扩大域名匹配到 SNI / HTTP Host，不修正 DNS，也不保证出口信誉、无直连回退或业务成功。

## 覆盖与依据

### BofA / 美林：10 条

| 规则范围 | 说明与依据 |
| --- | --- |
| `bankofamerica.com`、`bofa.com` | 银行核心域名及子域名；官方入口和社区名单 |
| `bac.com`、`baml.com` | 社区 Bank of America 分组的补充域名，未逐功能验证 |
| `ml.com`、`merrilledge.com` | 美林服务域名及子域名 |
| `bac-assets.com` | 静态资源及子域名；BofA 官方主页直接引用 `www1.bac-assets.com`、`secure1.bac-assets.com` 等 |
| `bankofamerica1.sc.omtrdc.net` | 社区 BOA 分组中的第三方主机，仅精确 DOMAIN 匹配 |
| `awuseb.advanced-web-analytics.com` | 社区 BOA 分组中的第三方主机，仅精确 DOMAIN 匹配；不保证仅供 BofA 使用 |
| `d2oexzqukiqsxx.cloudfront.net` | 社区 BOA 分组中的 CDN 主机，并有用户连接记录；仅精确 DOMAIN 匹配，官方归属未证实 |

社区来源：[sarkrui / US_Bank.list](https://gist.github.com/sarkrui/2157553656e8e6a48ee94ebf94ef9460) 的初始六域名，以及 [chyq92 / Bank.list 的 BOA 分组（固定提交）](https://github.com/chyq92/clash/blob/6b8a99cd080cf818b896feee3e5c94c9dc00eb3a/Bank.list#L38-L43)。只采用域名事实，不复制其全金融分组、关键词或共享平台根域名。

官方入口：[Bank of America](https://www.bankofamerica.com/)、[Merrill](https://www.ml.com/)、[Merrill Edge](https://www.merrilledge.com/)。官方网页引用是相应资源的使用证据；社区分组是关联线索，均不能证明 App 所有功能都已覆盖或第三方主机是银行自有。

### PayPal：3 条

| 域名后缀 | 依据 |
| --- | --- |
| `paypal.com` | 核心网站、登录及其子域名 |
| `paypalobjects.com` | PayPal 官网及开发者文档直接加载的静态资源，包含网页验证码资源引用 |
| `paypal.me` | 官方收款链接入口；`https://www.paypal.me/` 跳转至 `https://www.paypal.com/paypalme/` |

来源：[PayPal 官网](https://www.paypal.com/)、[官方 PayPal.Me](https://www.paypal.me/)、[PayPal Developer 文档](https://developer.paypal.com/payment-links-buttons/troubleshooting)。这是一份核心域名名单，不是全地区品牌域名或全支付生态合集；其他国家后缀按真实需要和来源另行补充。

不整份照搬外部 PayPal 公司合集，不加入宽泛的 `DOMAIN-KEYWORD,paypal`，不自动包含 Braintree、Venmo、Xoom 或国内其他支付平台；不扩大为整个 `cloudfront.net`、`omtrdc.net`、`advanced-web-analytics.com`、`online-metrix.net`、`sentry.io` 或 `datadoghq`。

## 验证边界与维护

**没有完成 BofA / PayPal App 登录、验证码、转账或结账全流程抓包验收。** 不保证全部第三方依赖、长期稳定、账号免风控或完整 DNS / UDP 出口隐私。规则遗漏和连接 reset 的因果关系仍需同设备对照验证，不能将“补齐名单”写成“已修复”。

采用**手动维护**，不声称每天自动发现域名，不部署定时任务。新增前查真实连接与用途：优先补业务后缀，第三方共享平台只补有证据的精确主机；未知归属不猜，不因报错一锅端放行共享 CDN。

更新须检查：DOMAIN / DOMAIN-SUFFIX 格式、唯一条目、BofA 两处一致、合集无意外扩张、规则及完整候选的 Surge 原生语法。静态检查通过不等于手机已加载或业务可用。仓库更新也不会改写用户主配置或选择的出口。

语法依据：[Surge 规则集合](https://manual.nssurge.com/rules/ruleset.html)、[域名规则](https://manual.nssurge.com/rules/domain.html)、[手动选择组](https://manual.nssurge.com/policy-groups/select.html)。
