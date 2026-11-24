# pattern、规则与 MITM

> scope: 所有端 ｜ 触发: 写 pattern、判断模块能改什么、配 MITM ｜ 复核: 2026-11-24

> 最后核对：2026-11-24。

## 1. URL 匹配

- 匹配对象是**完整请求 URL**（含 query），域名里的 `.` 必须转义成 `\.`。
- 写法对照：

| 端 | 写法 |
|---|---|
| Surge | `pattern=^https?://api\.example\.com/v1/user` ✅ |
| Shadowrocket | 同 Surge（模块/配置里的 Script 行）✅ |
| Quantumult X | `[rewrite_local]` 行首裸正则：`^https?://api\.example\.com/v1/user url script-response-body x.js` ✅ |
| Loon | 旧式 `http-response <regex> script-path=…`；v2 `response if ${url} ~= /…/i then script(…)` ✅ |
| Stash | 按 rewrite / script 配置的正则字段 ✅ |
| Egern | `match:` 正则字符串（YAML 里注意双重转义）✅ |
| LanceX | 见客户端文档 |

- 一类请求/响应**最多命中一条**脚本（Surge / Loon / QX 均如此）✅，所以一个模块要接管多个接口时，写多条 pattern，不要用万能 `.*` 去抢。
- 尽量同时钉住方法和路径（`POST` 与 `/prod-api/xxx`），避免误伤同域名的其它接口。

## 2. 模块/插件能改什么

| 端 | 可覆盖内容 | 不能做 |
|---|---|---|
| Surge | `General` `MITM` `Ponte` `DHCP` `WireGuard *` `Tailscale *` `MTProto` `Snell Server` `Ruleset *` `Rule` `Script` `URL Rewrite` `Header Rewrite` `Body Rewrite` `Map Local` `Host` `IP Rewrite` `SSID Setting` `Panel` ✅ | 改 `[Proxy]` / `[Proxy Group]`；改 MITM CA 证书；定义新策略 ✅ |
| Loon 插件 | 规则、Rewrite、脚本、Host、MitM、面板、参数 ✅ | 插件规则只能用 `DIRECT` / `REJECT` 系列 / `PROXY` ✅ |
| Egern 模块 | `dns` `rules` `url_rewrites` `header_rewrites` `body_rewrites` `map_locals` `scriptings` `mitm` `http_captures` `widgets` `bypass_tunnel_proxy` `real_ip_domains` ✅ | 见官方文档限制 |
| Stash 覆写 | 覆写配置（Override）✅ | — |
| QX | 只有 `[rewrite_local]` / `[task_local]` 行 ✅ | 没有模块概念 ✅ |

Surge 模块里写规则时：只能用内置策略（`DIRECT` / `REJECT` / `REJECT-TINYGIF`）或主配置**已存在**的策略，写不存在的策略会被报 unknown policy ✅；想让用户自己选策略，用 `#!arguments` 参数化 ✅。

## 3. MITM

| 端 | 段名 | 追加语法 |
|---|---|---|
| Surge | `[MITM]` ✅ | `hostname = %APPEND% a.com, b.com` ✅ |
| Shadowrocket | `[MITM]` ✅ | **必须**加 `%APPEND%`，否则覆盖主配置 ✅ |
| Loon | `[Mitm]` ✅ | 插件里直接给值 |
| Stash | MitM 配置段 ✅ | — |
| Egern | `mitm.hostnames`（YAML）✅ | 模块合并 |
| QX | `[mitm]` ✅ | `hostname = %APPEND% …` 🟡 |

- 段名大小写不同（`[MITM]` vs `[Mitm]`），跨端复制时最容易在这里出错。
- 忘了 `%APPEND%` 会把主配置的 MITM 列表整体覆盖，表现为"其它模块全失效"，而不是报错。

## 4. 规则表达式速记（QX / Surge / 通用）

- QX 过滤语法：`host-suffix, example.com, proxy` / `ip-cidr, 10.0.0.0/8, direct` / `geoip, cn, direct` / `final, proxy` ✅
- 同一份规则要用在多个端，先过转换器（Script-Hub 支持 QX/Surge/Loon/Shadowrocket/Clash 规则集互转）✅，再人工复核策略名是否在该端已定义。

