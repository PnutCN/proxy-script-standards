# Stash

> scope: stash ｜ 触发: 改该端的模块/插件/脚本 ｜ 复核: 2026-11-24

> 官方文档：https://stash.wiki/script/ ｜ 最后核对：2026-11-24

## 复用单元

覆写配置（Override），YAML。脚本通过 `script-providers` 声明后，在改写、面板、定时任务里引用。

## 脚本源 ✅

```yaml
script-providers:
  your-fancy-script:
    url: https://example.com/x.js?v=1.8.0
    interval: 86400
    headers:
      Authorization: Bearer …      # 仅私有脚本需要
      Accept: application/javascript
      User-Agent: Stash
```

`headers` 的值可以是字符串或字符串数组（同名多值）。

## Script API ✅

- `$script.name` / `$script.type`（`request` / `response` / `tile`）/ `$script.startTime`
- `$environment["stash-build"]`、`$environment["stash-version"]`、`$environment.language`、`$environment.system`
- `$argument`（仅在配置了 argument 时存在）
- `$done(value)`：**必须调用**，只有第一次生效；脚本编辑器里会显示 `$done` 的值便于调试
- `$persistentStore.write(value, key)` / `.read(key)`：值必须是字符串；省略 key 时以脚本名为键，同脚本在改写/定时/面板之间共享
- `$httpClient.get/post/put/delete/head/options/patch`：`url` 必填；`timeout` 默认 5s；`insecure`、`binary-mode`、`auto-cookie`（默认 true）、`auto-redirect`（默认 true）；`X-Stash-Selected-Proxy` 指定代理（需 encodeURIComponent）
- 回调 `callback(error, response, data)`，`response.status`/`statusCode` + `headers`（键名大小写不敏感）
- `$notification.post(title, subtitle, body)`
- 定时器：`setTimeout`、`setInterval`、`clearTimeout`、`clearInterval`、`queueMicrotask`（iOS/tvOS 3.6+、macOS 4.3+）；脚本结束时未触发的定时器会被取消

## 坑

- 未捕获异常会立即终止本次运行，只记日志不弹窗 → 本地要自己 try/catch。
- `$done` 忘记调用会导致运行一直挂着。
- 存储默认键是脚本名：同一份脚本在不同场景共享数据是**预期行为**，要隔离就显式传 key。

