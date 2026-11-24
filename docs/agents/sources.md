# 文档来源与核对记录

> 所有规则的最后核对日期：**2026-11-24**。改规范时请同步更新这里的日期与出处。

## 客户端

| 端 | 地址 | 覆盖内容 | 证据 |
|---|---|---|---|
| Surge | https://manual.nssurge.com/profile/module.html | 模块可覆盖段、`%APPEND%`/`%INSERT%`、元数据、`#!arguments`、`#!requirement`、参数占位 | ✅ |
| Surge | https://manual.nssurge.com/scripting/overview.html 及 `scripting/*.html` | 脚本类型（http-request/http-response/cron/dns/event/generic/rule）与 API | ✅ |
| Surge | https://manual.nssurge.com/llms.txt | 全站 markdown 索引，适合 agent 批量读 | ✅ |
| Shadowrocket | https://github.com/free-nodes/shadowrocket/blob/main/docs/shadowrocket_manual.md | `[Script]` 类型、`engine`、模块 `%APPEND%`、`#!arguments`、NE 内存限制 | ✅ |
| Quantumult X | https://github.com/crossutility/Quantumult-X | 官方示例脚本、`filter.snippet` 规则语法 | ✅ |
| Quantumult X | https://doc.repcz.link/quantumultx/http-js/ | `[task_local]` 格式、`$task.fetch`、`$environment.sourcePath`、`$iCloud` | ✅ |
| Loon | https://nsloon.app/docs/Plugin/ | 插件头 10 字段、`[Argument]`、插件规则策略 | ✅ |
| Loon | https://nsloon.app/docs/Script/script_v2/ | 新版脚本语法、`requires_body`、第一条命中 | ✅ |
| Loon | https://nsloon.app/docs/Script/script_api/ | `$done` 语义、`$request`/`$response`、`$argument`、`$environment`、build 门槛 | ✅ |
| Stash | https://stash.wiki/script/syntax-and-interface | `$script` / `$environment` / `$done` / `$persistentStore` / `$httpClient` / 定时器 | ✅ |
| Stash | https://stash.wiki/script/manage-script | `script-providers` 结构与 `headers` | ✅ |
| Egern | https://egernapp.com/zh-CN/docs/configuration/modules | YAML 模块元信息、内容键、`compat_arguments`、`env_schema` | ✅ |
| Egern | https://egernapp.com/zh-CN/docs/configuration/scriptings | 五种脚本类型与字段、`max_size`/`timeout`/`body_required` | ✅ |
| LanceX | https://shadowboat.app/lancexapp/zh/scripting/ | `$lancex`、`$done`、`$persistentStore`、`$httpClient`、`fetch`、通知 | ✅ |
| ProxyPin | https://github.com/wanghongenpin/proxypin/wiki/脚本 | `onRequest`/`onResponse`、对象字段、`md5`、`File` | ✅ |

## 通用参考

| 来源 | 用途 |
|---|---|
| https://github.com/Script-Hub-Org/Script-Hub | QX / Loon / Surge / Stash / Egern / LanceX / Shadowrocket 脚本与规则集互转，跨端差异的一手对照 |
| https://github.com/chavyleung/scripts | `Env.js` 跨端封装（HTTP / 存储 / 通知 / 环境检测） |
| https://doc.repcz.link | 中文多端文档站（QX / Loon / Stash / Surge / 小火箭） |
| https://wiki.surge.community | 中文 Surge 社区 wiki（模块/规则入门） |
| https://agents.md | AGENTS.md 的格式规范（本仓库根文件遵循它） |

## 实证样本（社区模块的实际写法）

| 仓库 | 观察到的字段 |
|---|---|
| Rabbit-Spec/Surge | `#!category=`（官方未列） |
| Keywos/rule | `#!category=`，`#!desc` 里写日期（如 `2023.3.17`） |
| fmz200/wool_scripts | `#!author=` `#!category=` `#!homepage=` `#!raw-url=`（均非官方字段） |

结论：**跨端都没有版本字段**，社区的 `#!category` / `#!author` / `#!raw-url` 属于事实约定，不要当成功能字段依赖 ⚠️。
