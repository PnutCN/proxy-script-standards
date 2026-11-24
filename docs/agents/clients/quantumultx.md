# Quantumult X

> scope: quantumultx ｜ 触发: 改该端的模块/插件/脚本 ｜ 复核: 2026-11-24

> 官方仓库：https://github.com/crossutility/Quantumult-X ｜ 中文文档站：https://doc.repcz.link/quantumultx/ ｜ 最后核对：2026-11-24

## 没有模块

QX 没有「模块」概念，能力靠两段配置：`[rewrite_local]`（改写）和 `[task_local]`（脚本任务）。也没有元数据头字段 ✅。

## 改写 ✅

```
[rewrite_local]
^https?://api\.example\.com/v1/user url script-response-body https://…/x.js?v=1.8.0
```

行格式：`<正则> url script-response-body|script-request-body|script-request-header <脚本地址>`。

## 任务 ✅

```
[task_local]
0 8 * * * https://…/daily.js#key1=value1, tag=每日签到, img-url=https://…/icon.png, enabled=true
```

- 5 或 6 段 cron；`event-network`（网络切换）、`event-interaction`（UI 交互，长按节点执行）。
- `tag`、`img-url`、`require-devices`、`enabled` 为可选参数，顺序不限。
- `#` 之后的内容不会发给服务器，脚本里用 `$environment.sourcePath` 取回（QX ≥ 1.0.25）✅
- 需 Tunnel 运行且计划任务开关打开；默认脚本超时 10s ✅

## 脚本 API ✅

- `$done({})` / `$request` / `$response`
- `$task.fetch(request).then(...)`：`opts.redirection`、`opts.skip-cert-verify`、`opts.auto-cookie`（build 934 起默认**关闭**）、`opts.policy`
- `$prefs.valueForKey / setValueForKey`（等价持久化）🟡
- `$notify(title, subtitle, body)` 🟡
- `$iCloud.writeFile / readFile / removeFile`（≥ 1.0.31）
- 配置/策略类 API 见官方示例 `sample-configuration-api.js`

## 规则语法 ✅

`host-suffix, example.com, proxy` / `ip-cidr, 10.0.0.0/8, direct` / `geoip, cn, direct` / `final, proxy`
（官方 `filter.snippet` 就是这个格式）

## 坑

- 没有模块 → 版本号只能写在脚本注释和 URL 的 `?v=` 里。
- `auto-cookie` 默认值在 build 934 变过一次，依赖 Cookie 的脚本要显式开。

