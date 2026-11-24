# Loon

> 官方文档：https://nsloon.app/docs ｜ 最后核对：2026-11-24

## 复用单元

插件（plugin）：可复用子配置，可含规则、Rewrite、脚本、Host、MitM、Arguments。

## 元数据（10 个官方字段）✅

```
#!name = 插件名
#!desc = 描述（版本号写这里，如 … v1.8.0）
#!author = 作者
#!homepage = 主页
#!icon = 图标 URL
#!system = iOS,iPadOS,tvOS,macOS
#!system_version = 15
#!loon_version = 3.5.1(978)      # 最低 Loon 版本要求，不是自身版本
#!tag = 去广告,工具                # 分类标签
#!type = normal                   # normal | parser
```

## 参数 ✅

```
[Argument]
name = input,"Loon",tag=名称,desc=输入一个名称
region = select,"CN","US","JP",tag=地区
enabled = switch,true,tag=启用
```

- 控件：`input` / `select` / `switch`（switch 默认 false）
- 脚本读 `$argument.name`；Rewrite 里用 `${参数名}`；`enable={enabled}` 可直接用开关控制是否启用

## 脚本 ✅

旧式（仍可用）：

```
[Script]
http-response ^https?:\/\/api\.example\.com\/v1\/user script-path=https://…/x.js,requires-body=true,tag=改用户信息
```

新式 v2（Loon 3.5.1 (983) 起）：

```
response if ${url} ~= /\/api\// && ${response.status} == 200 then script("x.js") with tag="改用户", timeout=20, requires_body=true
```

- 统一入口：`request if … then script(…)` / `response if … then script(…)` / `cron "…" then script(…)` / `network-changed then script(…)` / `generic then script(…)`
- **每条 Response Script 必须有强制 URL Guard**
- 同一请求/响应最多选一条脚本（按配置顺序第一条命中）

## Script API ✅

`$done()`（只能调一次）、`$request`/`$response`（二进制为 `Uint8Array`，需 `binary_body_mode=true`）、`$loon`、`$script.name/startTime`、`$argument`、`$environment.params`、`$config.*`、`$persistentStore.write/read/remove`、`$notification.post`、`$httpClient`、`$utils.geoip/ipasn/gzip(build≥988)/ungzip`、`$crypto.aes.encrypt/decrypt(build≥988)`、`$dns.query(build≥988)`

- `setInterval` 在当前运行时**只执行一次**，周期任务要在回调里再 `setTimeout` ✅
- 参数错误可能表现为 `TypeError` 或带 `error.code`

## 插件规则策略 ✅

只能是 `DIRECT` / `REJECT` 系列 / `PROXY`；未指定默认 `DIRECT`。

## MITM ✅

段名是 `[Mitm]`（不是 `[MITM]`）。
