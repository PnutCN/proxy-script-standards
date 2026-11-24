# ProxyPin

> scope: proxypin ｜ 触发: 改该端的模块/插件/脚本 ｜ 复核: 2026-11-24

> 官方 wiki：https://github.com/wanghongenpin/proxypin/wiki/脚本 ｜ 最后核对：2026-11-24

ProxyPin 是**抓包工具**，不是代理客户端。它的脚本模型和 `$done` 那套完全不同，别混用。

## 入口 ✅

```js
async function onRequest(context, request) { /* 改请求；返回 null 中断该请求 */ return request; }
async function onResponse(context, request, response) { /* 改响应 */ return response; }
```

## 对象字段 ✅

```js
// context
{ os: "macos", scriptName: "…", deviceId: "…", session: {} }   // session 可在两个阶段间传值

// request
{ method, host /* 只读 */, path, queries: {}, headers: {}, body: "字符串", rawBody: Uint8Array }

// response
{ statusCode, headers: {}, body: "字符串" }
```

## 内置能力 ✅

- `fetch`（标准 Fetch API，可在 `onRequest` 里 `await`）
- `md5('value')`
- `File`（与 dart 一致，异步优先，同步方法带 `Sync` 后缀）
  - 移动端只能写 App 支持目录，用 `await getApplicationSupportDirectory()` 取路径；桌面端无限制但建议一致
- `console.log`

## 没有的东西

- 没有 `$done`、`$request`、`$notification.post`、`$persistentStore`（用 `File` 自己存）
- 没有 cron：定时任务要靠外部调度
- 没有模块/插件概念：脚本在 App 里按域名/路径绑定

## 写法建议

- 只在 `onResponse` 里改 body，改完 `return response`；不想改就原样返回。
- 请求阶段要「拦截」就 `return null`，注意这会直接中断，别在调试分支里误返回 null。
- 移动端写文件前先建目录，路径用 `getApplicationSupportDirectory()` 拼接。

