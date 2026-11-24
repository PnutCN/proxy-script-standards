# Shadowrocket（小火箭）

> 手册：https://github.com/free-nodes/shadowrocket/blob/main/docs/shadowrocket_manual.md ｜ 最后核对：2026-11-24

## 复用单元

模块。写法与配置文件相同，只有特定段，规则优先级高于配置文件。安装：配置 → 模块 → `➕` → 填链接。

## 元数据（手册记录的字段）✅

```
#!name=模块名
#!desc=描述
#!arguments=证书模块:false, 证书内容:…, 证书密码:…
#!arguments-desc=参数说明（\n 换行）
```

参数占位符同样是 `{{{参数名}}}` ✅（手册里的证书模块示例就是这么写的）。

## 脚本 ✅

```
[Script]
script = type=rule,script-path=rule.js
```

- 类型：`http-request` `http-response` `event` `rule` `dns` `cron`
- 引擎：`engine=auto` / `jsc` / `webview`。**`http-request` / `http-response` 在 `engine=auto` 时默认走 WebView**，行为与 JSC 不同 → 显式写 `engine=jsc` 或 `engine=webview` 更稳。
- 脚本 API 与 Surge 兼容（`$done` / `$request` / `$response` / `$httpClient` / `$persistentStore` / `$notification.post` / `$argument`）🟡
- 运行时对象 `$rocket` 可用于识别端（Env.js 就是这么判的）🟡

## MITM ✅

```
[MITM]
hostname = %APPEND% api.example.com
```

- **必须加 `%APPEND%`**，不加会覆盖配置里对应内容并影响其它模块。
- 需在配置里开启 HTTPS 解密或装证书模块，含 MITM 的模块才完整生效。

## 坑 ✅

- 含规则的模块通常只在「全局路由 = 配置」时生效。
- iOS 15 前 NE 内存上限 15MB、之后 50MB，超了会出现「模块失效 / VPN 自动断开」。
- 模块启用状态不跨设备同步；iCloud 里自定义参数存在 `Modules/<模块名>.meta`，模块更新不会丢。
