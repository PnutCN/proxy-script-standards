# LanceX

> 官方文档：https://shadowboat.app/lancexapp/zh/scripting/ ｜ 最后核对：2026-11-24

## Script API ✅

- `$done()`：所有脚本都是异步执行，即使不返回结果也必须调用，否则超时会打警告
- `$lancex`：运行环境标识对象
- `$persistentStore.write(data, [key])` / `.read([key])`：只允许字符串
- `$httpClient.get/post/put/delete/head/options/patch(url|options, callback)`
  - `options`：`url`（必填）、`headers`、`body`（字符串或对象；对象会自动 JSON 序列化并设 `Content-Type: application/json`）、`timeout`、`callback`
- 标准 `fetch(url, options)` 也可用，返回 Promise
- `console.log/warn/error/info`；`setTimeout` / `setInterval`
- `$notification.post(title, subtitle, body, info)`：需先开「允许通知」；`info` 可带 `{"open-url": "https://…"}`，点击通知时打开

## 相关能力（文档另有专页）

- 前置脚本（Prepend Script）
- 模块解析器（把其它端的模块/规则转成 LanceX 格式）
- Proxy Provider（远程代理集）

以上三者的细节未在本规范核对 ⚠️，用之前读原文。

## 建议写法

与 Surge 同构：`$done` + `$request`/`$response` + `$persistentStore` + `$httpClient`，所以从 Surge 脚本移植成本低；差异主要在通知的第 4 个参数和 `$lancex`。
