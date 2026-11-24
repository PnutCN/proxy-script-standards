# 通用脚本规范（与端无关）

> scope: 所有端 ｜ 触发: 写/改任意 http-request / http-response 脚本 ｜ 上游: 见 sources.md ｜ 复核: 2026-11-24

> 适用：所有支持 JS 请求改写脚本的客户端。最后核对：2026-11-24。出处见 `sources.md`。

## 1. 结构：放行条件永远在最前面

脚本第一段必须是「我该不该干活」，而不是「我要改什么」：

```js
!(function () {
  if (MODE === 'off') return $done({});                  // 手动总开关
  let resp;
  try { resp = JSON.parse($response.body); } catch (e) { return $done({}); }
  if (!resp || typeof resp !== 'object') return $done({});
  if (resp.errno !== undefined && resp.errno !== 200) return $done({});
  if (!resp.data || typeof resp.data !== 'object') return $done({});
  // 到这里才改
  resp.data.money = 1400;
  $done({ body: JSON.stringify(resp) });
})();
```

依据：mock 脚本一旦把错误页、登录页、非 JSON 响应也改掉，用户看到的是"更奇怪的白屏"，排查成本极高。✅ 实践总结，非文档条文。

- 非 JSON / `errno≠200` / `data` 类型不符 → `$done({})` 原样放行。
- 只改要改的字段，**不重排、不删字段**：客户端按固定字段渲染，缺字段就是空白。
- 列表接口返回数组、详情接口通常返回对象，不要把形状套错。

## 1.5 先取证，再写规则

**官方文档是分页的：某一页没写 ≠ 那个字段不存在。**

- 查字段/参数，先看全站目录（Surge 是 `https://manual.nssurge.com/llms.txt`），别只看一页；`module.html` 的「元数据」段只列了 6 个字段，而 `tools/cli.html`、`tools/http-api.html` 里有 `#!version`、`#!category` 和「header 包含 Surge 不解释的 author/homepage/version」。
- 写得出来就能测的就测：本地放一份 `.sgmodule` + `surge-cli module list --raw` 一分钟就能验证元数据是否被解析，比读三页文档还准。
- 结论分三档，写清楚是哪一档：官方文档写了 / 官方工具实测出现 / 只是社区代码里见过。**别把「我没在文档里看到」写成「官方没有」。**

## 2. `$done()` 只能调用一次

- Surge / QX / Loon / Stash / LanceX / Shadowrocket 都是「调用一次即提交并释放资源」✅
- 有异步（`$httpClient`、`$task.fetch`、`fetch`）时，回调完成后再 `$done()`，不要提前调用 ✅（Loon 文档明确）
- 每条分支都要有 `$done()`，否则请求挂起；Surge 端表现为连接卡住，比报错更难查。

## 3. 拿 body 的前提

- Surge：`requires-body=true`，默认 `max-size` iOS 1MB / macOS 10MB，超了静默降级为 passthrough ✅
- Loon：`requires_body=true`（v2 语法 `with requires_body=true`）✅
- Egern：`body_required: true` + `max_size`（默认 1048576）✅
- Stash：按配置的 rewrite/script 参数 ✅
- 二进制内容必须显式开二进制模式（Loon `binary_body_mode=true`、Egern `binary_body: true`），并用 `Uint8Array` 处理 ✅

## 4. 性能与内存

- 不要无条件 `JSON.parse` 大 body：先看 `Content-Type`，必要时看长度。
- iOS 15 起 NE 内存上限 50MB（更低系统 15MB），超大 body 会导致模块失效或 VPN 断开 🟡
- 定时脚本不要 `setInterval` 轮询；Loon 的 `setInterval` 实际只执行一次，需要周期就在回调里再 `setTimeout` ✅

## 5. 安全与隐私

- token / Cookie / 手机号 / 卡号不进仓库、不进 README、不进日志。
- 调试输出包一个常量开关：`const DEBUG = false`。
- 不要把凭据写进模块参数默认值（模块参数会随模块文件公开分发）。
- 回传里若必须展示脱敏值，用 `132****6156` 形式。

## 6. 命名与持久化

- 脚本文件名 kebab-case，与模块同名时加角色后缀：`member-card.js`、`mem-level-detail.js`。
- 持久化键统一 `<作者>.<模块>.<键>`，避免与别人的模块串数据。
- 一个功能一个模块；同一功能的多条脚本放同一模块（用户一次装完），拆分/合并由维护者决定，不要按实现方便随手拆。

## 7. 交付说明模板

每次交付写三段，缺一段用户就没法有效反馈：

1. 这次改了什么（脚本/接口/字段）
2. 期待看到什么现象
3. 如果还不对，下一步要什么材料（HAR / 截图 / 哪一页）

