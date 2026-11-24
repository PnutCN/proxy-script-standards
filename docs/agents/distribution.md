# 分发、自动更新与一键导入

> scope: 所有端 ｜ 触发: 托管、自动更新、一键导入 ｜ 复核: 2026-11-24

> 最后核对：2026-11-24。

## 1. 托管

| 方式 | 适用 | 注意 |
|---|---|---|
| GitHub 仓库 raw | 多文件、要目录结构 | 用 `raw.githubusercontent.com`，不要用带 `blob` 的网页链接 |
| GitHub Gist | 单模块、更新频繁 | no-hash 直链永远取最新版；本身就是 git 仓库，可 clone |
| 自有服务器 / Gitea | 想立刻生效 | raw 直链形式 `https://host/owner/repo/raw/branch/main/x.sgmodule` |
| jsDelivr / statically 等 CDN | 只做加速 | 缓存更久，更新更慢，别拿它当主链接 |

- 目标必须**公开**：客户端下载模块/脚本时不会带鉴权头，私有会 401。
- `raw.githubusercontent.com` 常返 503，取仓库文件排查问题时可用 `https://cdn.jsdelivr.net/gh/<owner>/<repo>@<branch>/<path>` 镜像 🟡。

## 2. 自动更新

| 端 | 脚本刷新 | 模块本体刷新 |
|---|---|---|
| Surge | `script-update-interval=<秒>`，默认 24h ✅ | 需手动「更新模块」✅ |
| Shadowrocket | 按模块/脚本设置 🟡 | 需更新模块 |
| Loon | 插件内脚本引用可带更新间隔 🟡 | 需更新插件 |
| Stash | `script-providers.<name>.interval`（默认 86400）✅ | 更新覆写配置 |
| Egern | `update_interval`（模块与脚本各 86400）✅ | 更新模块 |
| QX | 任务/重写使用远程脚本时按资源更新 ✅ | — |

**改 `.js` 靠自动更新；改模块/插件本体必须重装或手动更新** —— 这两条路径要分清，否则用户永远看不到新版。

## 3. 一键导入（scheme）

| 端 | scheme |
|---|---|
| Surge | `surge:///install-module?url=<percent-encoded 模块地址>` ✅ |
| Surge Mac 6.7.0+ | 也接受 `surgeconfig:///` ✅ |
| Shadowrocket | `shadowrocket://add/…`（模块/订阅，按版本有差异）⚠️ 未逐版本核对 |
| Loon / Stash / Egern / LanceX | 各有 URL Scheme，见各自文档 ⚠️ 抄之前先查一次当前版本 |

硬规则：

- **Markdown 里的自定义 scheme 会被 GitHub 过滤**（`[x](surge:///...)` 渲染后 href 被剥掉）🟡，必须夹一层 https 落地页，页面里再 `location.replace('surge:///install-module?url=' + encodeURIComponent(url))`。
- gist raw 返回 `text/plain`，浏览器不渲染 HTML；落地页要么放仓库 Pages，要么用能输出 `text/html` 的入口 🟡。
- 内层 URL 必须 percent 编码，裸 `https://` 会被 scheme 截断。
- 给「复制链接」兜底：微信等内嵌浏览器会拦自定义 scheme。

## 4. 版本与缓存

- 脚本 URL 加 `?v=<版本>`，让客户端当成新 URL 绕开 CDN 缓存。
- 模块安装链接也带 `?v=`，方便肉眼确认装的是哪版。
- 发布后按 `testing.md` 的双步校验确认线上内容。

