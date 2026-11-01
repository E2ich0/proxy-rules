# proxy-rules

个人维护的代理分流规则。当前仅维护 **Bank of America（美国银行 / BofA）与 Merrill / Merrill Edge（美林）**，不是全体美国银行的合集，也不是美国合众银行 U.S. Bank 的规则。

仅发布域名规则，不包含节点、密码、订阅凭证、账户信息或完整客户端配置。

## 订阅地址

- Surge：[BankOfAmerica.list](https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/BankOfAmerica.list)
- Clash / Mihomo：[BankOfAmerica.yaml](https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Clash/BankOfAmerica.yaml)

## 覆盖范围

| 根域名 | 范围与说明 |
| --- | --- |
| `bankofamerica.com` | Bank of America 主业务域名 |
| `bofa.com` | Bank of America 相关业务域名 |
| `bac.com`、`baml.com` | 社区 Bank of America 分组中的补充域名，未逐功能验证 |
| `ml.com`、`merrilledge.com` | Merrill / Merrill Edge 服务 |

两份文件均为相同的六条 `DOMAIN-SUFFIX`，匹配根域名及所有层级子域名。没有宽泛关键词、共享 CDN、IP 网段、其他银行或支付平台规则。

**未做 BofA App 登录、验证码、转账等全流程抓包验证，不保证包含全部第三方请求，也不保证任意代理出口能通过银行风控。** 规则只决定流量走哪个策略，不能替代出口信誉或业务可用性验证。

## Surge 用法

在现有 `[Rule]` 段加入下列规则，放在可能提前匹配的通用规则及 `FINAL` 之前：

```ini
RULE-SET,https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Surge/BankOfAmerica.list,BOFA
```

`BOFA` 是占位名称，必须替换为已有节点或策略组；需要直连则改为 `DIRECT`。名单本身不绑定出口。不要用本文件覆盖完整配置。

## Clash / Mihomo 用法

合并到现有 `rule-providers` 和 `rules`，不要重复创建顶层字段。放在可能提前匹配的通用规则及 `MATCH` 之前：

```yaml
rule-providers:
  BankOfAmerica:
    type: http
    behavior: classical
    format: yaml
    url: https://raw.githubusercontent.com/E2ich0/proxy-rules/main/Clash/BankOfAmerica.yaml
    path: ./ruleset/BankOfAmerica.yaml
    interval: 86400

rules:
  - RULE-SET,BankOfAmerica,BOFA
```

`BOFA` 同样必须替换为已有策略。这里发布的是 classical YAML，不是 MRS 或 domain-only YAML。

## 来源与维护

- 六个域名参考 [sarkrui / US_Bank.list 的 Bank of America 分组](https://gist.github.com/sarkrui/2157553656e8e6a48ee94ebf94ef9460)。只采用其中的域名事实，不沿用其宽泛的 `DOMAIN-KEYWORD,bankofamerica`。
- 官方服务入口：[Bank of America](https://www.bankofamerica.com/)、[Merrill](https://www.ml.com/)、[Merrill Edge](https://www.merrilledge.com/)。入口与域名分组不等于每项 App 功能均已验证。
- 语法参考：[Surge 域名规则](https://manual.nssurge.com/rules/domain.html)、[Mihomo 规则集合](https://wiki.metacubex.one/config/rule-providers/)。

采用**手动维护**，不声称每天自动发现新域名。新增或删除前核对真实连接记录、用途与归属，同时更新 Surge、Clash 和此覆盖表；不得因单个功能失败而加入整个共享 CDN 或模糊关键词。更改后检查两份规则集合一致，并做客户端原生语法检查；静态语法通过不代表真实业务验收。

仓库更新不会自动改写客户端主配置或选择的出口。需要变更客户端分流时另行实施。
