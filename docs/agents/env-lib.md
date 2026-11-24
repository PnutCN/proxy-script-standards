# Env.js：跨端脚本库

> scope: 跨端（Env.js）｜ 触发: 一份脚本要跑多个端 ｜ 复核: 2026-11-24

> 最后核对：2026-11-24。库地址：`github.com/chavyleung/scripts`（`Env.js` / `Env.min.js`，单文件、无依赖、约 30KB）🟡

## 1. 它解决什么

客户端之间的运行时差异集中在三处：HTTP 请求（`$httpClient` callback vs `$task.fetch` Promise）、持久化（`$persistentStore` vs `$prefs`）、通知（`$notification.post` vs `$notify`）。Env 把它们包成同一套方法，一份脚本可以同时跑在多个端。

## 2. 环境检测

`getEnv()` 按运行时对象判断，返回 `Surge` / `Quantumult X` / `Loon` / `Shadowrocket` / `Stash` / `Egern` / `Node.js`，并给出 `isSurge() isQuanX() isLoon() isShadowrocket() isStash() isEgern() isNode()`。

识别依据（源码事实）🟡：
- `typeof $task !== 'undefined'` → Quantumult X
- `typeof $loon !== 'undefined'` → Loon
- `typeof $rocket !== 'undefined'` → Shadowrocket
- `typeof $environment !== 'undefined' && $environment['stash-version']` → Stash
- `typeof $httpClient !== 'undefined'` 且都不是上面这些 → Surge
- Node 下退化到 `axios` 等实现

这解释了「为什么同一份脚本在小火箭上能跑」：小火箭走的是 Surge 分支的兼容实现。

## 3. 常用 API

```js
const $ = new Env('我的模块');

$.http.get({ url: 'https://api.example.com/x', timeout: 5000 })
  .then(resp => {
    const body = $.toObj(resp.body, {});
    $.setdata('1.8.0', 'ver');
    $.msg('标题', '副标题', '内容');
    $.done();
  })
  .catch(e => { $.logErr(e); $.done(); });
```

- `$.http.get / $.http.post`（参数对象或 URL；`timeout` 走 Promise.race）
- `$.getdata(key) / $.setdata(val, key)`
- `$.getjson / $.setjson / $.toObj / $.toStr`
- `$.msg(title, subtitle, body)`、`$.log / $.warn / $.error / $.logErr`
- `$.wait(ms)`、`$.time(name)`、`$.done()`

## 4. 坑

- Env 是**老库**（无 TypeScript、无 Egern/Stash 的完整 API 覆盖），新端能力（如 Stash 的 `queueMicrotask`、LanceX 的 `fetch`）它不会替你包 🟡。
- QX 的 `$task.fetch` 默认超时 10s，Env 的 `timeout` 是额外加的 Promise 超时，两者别混。
- 用它就别再直接读 `$response.body`：走 `$response` 还是 `$request` 由脚本类型决定，Env 不改变这一点。
- 只在需要「一份脚本多端跑」时引入。单端模块直接写原生 API，少一层黑盒。
- 长脚本放进模块时注意体积：raw 每次更新会重新下载，30KB 的库可以接受，几百 KB 就要考虑精简。

## 5. 备份方案

- 需要更现代的实现时看 `github.com/nsnanocat/util`（同类思路，社区小体量）⚠️ 未逐条核对 API。
- 跨端格式互转（不含运行时封装）用 Script-Hub：支持 QX / Loon / Surge / Stash / Egern / LanceX / Shadowrocket ✅。

