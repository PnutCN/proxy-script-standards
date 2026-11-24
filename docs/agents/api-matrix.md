# 跨端 API 对照

> 最后核对：2026-11-24。✅ 官方文档 / 🟡 官方仓库或社区代码 / ⚠️ 未确认。

## 1. 运行时全局对象

| 用途 | Surge | Shadowrocket | Quantumult X | Loon | Stash | Egern | LanceX | ProxyPin |
|---|---|---|---|---|---|---|---|---|
| 结束脚本 | `$done({})` ✅ | `$done({})` 🟡 | `$done({})` ✅ | `$done({})` ✅ | `$done(value)`（只有第一次生效）✅ | 见下注 | `$done()` ✅ | `return request` / `return response`，返回 `null` 中断 ✅ |
| 读请求 | `$request.url/method/headers/body` ✅ | 同 Surge 🟡 | 同 ✅ | 同 ✅ | 同 ✅ | — | 同 ✅ | 参数 `request.method/host(只读)/path/queries/headers/body/rawBody` ✅ |
| 读响应 | `$response.status/headers/body` ✅ | 同 🟡 | 同 ✅ | 同 ✅ | `$response.status/headers/body` ✅ | — | 同 ✅ | `response.statusCode/headers/body` ✅ |
| 运行环境 | `$environment`（模块参数/面板）🟡 | `$environment` 🟡 | `$environment.sourcePath`（≥1.0.25）✅ | `$loon`、`$script.name/startTime`、`$environment.params` ✅ | `$environment["stash-build"/"stash-version"]`、`.language`、`.system`、`$script.name/type/startTime` ✅ | `ctx.env` ✅ | `$lancex` ✅ | `context.os/scriptName/deviceId/session` ✅ |
| 参数 | `$argument`（模块 `#!arguments` + `{{{x}}}`）✅ | `$argument` 🟡 | 无，用 URL 后缀 + `$environment.sourcePath` ✅ | `$argument`（插件 `[Argument]`）✅ | `$argument` ✅ | `env:` → `ctx.env` ✅ | 见客户端文档 ⚠️ | 常量 |

> Egern 注：官方网页文档只写了脚本配置项和 `ctx.env`，运行时 API（`$done`、HTTP 等）未在该页列出 ⚠️，写 Egern 脚本前先在客户端内的脚本文档/示例确认。

## 2. HTTP 客户端

| 端 | 写法 | 关键差异 |
|---|---|---|
| Surge | `$httpClient.get/post({url, headers, body, timeout}, cb)` ✅ | `auto-redirect` 可控 |
| Shadowrocket | 同 Surge 🟡 | — |
| Quantumult X | `$task.fetch(req).then(...)` ✅ | `opts` 里 `redirection` / `skip-cert-verify` / `auto-cookie`（build 934 起默认**关闭**）/ `policy`；默认超时 10s |
| Loon | `$httpClient.get/post(...)` ✅ | 二进制用 `Uint8Array` |
| Stash | `$httpClient.get/post/put/delete/head/options/patch` ✅ | 默认 `timeout=5`；`auto-cookie` 与 `auto-redirect` 默认 **true**；`binary-mode` 返回 `Uint8Array`；`X-Stash-Selected-Proxy` 指定代理 |
| LanceX | `$httpClient.*` + 标准 `fetch` ✅ | `body` 传对象自动 JSON 化并设 `Content-Type` |
| ProxyPin | 标准 `fetch` ✅ | 可直接在 `onRequest` 里 `await fetch(...)` |

用统一封装（`docs/agents/env-lib.md` 的 Env）可以抹平前四家的差异。

## 3. 持久化

| 端 | API | 备注 |
|---|---|---|
| Surge | `$persistentStore` ✅ | — |
| Shadowrocket | `$persistentStore` 🟡 | — |
| Quantumult X | `$prefs.valueForKey/setValueForKey` 🟡 | 语义与 `$persistentStore` 一致 |
| Loon | `$persistentStore.write/read/remove` ✅ | — |
| Stash | `$persistentStore.write(value, key)` / `.read(key)` ✅ | 省略 key 时用脚本名做键；值必须是字符串 |
| LanceX | `$persistentStore.write/read` ✅ | 只允许字符串 |
| ProxyPin | `File`（dart API）+ `context.session` ✅ | 移动端只能写 App 支持目录 |
| Egern | ⚠️ 未在网页文档确认 | — |

## 4. 通知与日志

- 通知：Surge / Loon / Stash / LanceX 都是 `$notification.post(title, subtitle, body)` ✅；LanceX 额外支持第 4 个 `info` 参数（`{"open-url": ...}`）✅；QX 是 `$notify` 🟡；ProxyPin 无通知 API，写文件/日志 ✅。
- 日志：`console.log` 全端都有 ✅；Stash 会落到独立日志文件并自动 JSON 序列化对象 ✅。
- 不要在日志里输出 token / Cookie / 手机号。

## 5. 定时与网络事件

| 端 | 定时 | 网络变化 | 手动触发 |
|---|---|---|---|
| Surge | `type=cron` ✅ | — | `type=generic` / 面板 ✅ |
| Shadowrocket | `type=cron` ✅ | — | — |
| Quantumult X | `[task_local]` + 5/6 段 cron ✅ | `event-network` ✅ | `event-interaction` ✅ |
| Loon | `cron "…" then script()` ✅ | `network-changed` ✅ | `generic` ✅ |
| Stash | 定时任务（cron）✅ | — | 面板/Tile ✅ |
| Egern | `schedule` + cron ✅ | `network` ✅ | `generic` ✅ |
| LanceX | 见客户端文档 ⚠️ | — | — |
| ProxyPin | 无，需外部调度 ✅ | — | — |
