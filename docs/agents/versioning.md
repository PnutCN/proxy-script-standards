# 版本号、分类标签与模块详情

> scope: 所有端 ｜ 触发: 发版、改模块元数据、写 README 模块表 ｜ 上游: manual.nssurge.com/tools/cli.html、tools/http-api.html（Surge 侧已实测）｜ 复核: 2026-11-25

> 适用：所有端。最后核对：2026-11-24。出处见 `sources.md`。

## 1. 各端的版本字段实况（实测 + 官方文档）

| 端 | 官方支持的元数据 | 有版本字段吗 |
|---|---|---|
| Surge | `#!name` `#!desc` `#!system` `#!arguments` `#!arguments-desc` `#!requirement`，另有 **`#!version`**、`#!category`、`#!author`、`#!homepage` ✅实测 | **有**：`surge-cli module list` 会显示 `#!version`（iOS 6.0.0+ / Mac 6.11.0+）；`/v1/modules` 返回 `category` 与完整 header |
| Shadowrocket | `#!name` `#!desc` `#!arguments` `#!arguments-desc` ✅ | 无 |
| Loon | `#!name` `#!desc` `#!author` `#!homepage` `#!icon` `#!system` `#!system_version` `#!loon_version` `#!tag` `#!type` ✅ | 无（`#!loon_version` 是**最低 Loon 版本要求**，不是自身版本） |
| Egern | YAML：`name` `description` `author` `homepage` `manual` `icon` `open_url` `compat_arguments` `compat_arguments_desc` `env_schema` ✅ | 无 |
| Stash | 覆写配置（Override）YAML ✅ | 无 |
| Quantumult X | 无元数据（rewrite / task 行 + 脚本文件本身）✅ | 无 |
| LanceX | 模块/脚本文件，见 clients/lancex.md | 无 |
| ProxyPin | 只有脚本名（`context.scriptName`）✅ | 无 |

**结论：**
- **Surge 有 `#!version`**，而且 `#!category` 是真的分类字段——在 Surge Mac 6.12.0 上实测：模块头写 `#!version=9.9.9` + `#!category=#🥜PInaTe`，`surge-cli module list --raw` 返回 `"category": "#🥜PInaTe"`，header 里保留 `version/author/homepage/system`。旧版手册的「元数据」段只列了 name/desc/system/arguments/requirement，漏了这几个，别据此认为它们不存在。
- 其余端（Shadowrocket / QX / Loon / Stash / Egern / LanceX / ProxyPin）确实没有版本字段（Loon 的 `#!loon_version` 是**最低 Loon 版本要求**）。
- 即使 Surge 认 `#!version`，它也**不参与更新判断**：`script-update-interval` 管脚本拉取，模块本体更新永远是手动动作。所以 `?v=` 查询串仍然要给，作用是绕 CDN 缓存。

## 2. 我们的版本号规范（SemVer）

`MAJOR.MINOR.PATCH`，含义固定，不要凭感觉跳号：

| 位 | 什么时候加 |
|---|---|
| MAJOR | 作用范围或安装方式变化（改名、换域名集合、需要用户重装或改配置） |
| MINOR | 新增被接管接口、新增可配置参数、新增支持端 |
| PATCH | 字段值、正则、文案、默认值修正 |

约定细节：

- 从 `0.1.0` 起，第一个「用户能正常用」的版本升 `1.0.0`。
- 一个 PR / 一次发布只升一次，不跳号、不复用。
- 版本号只在**一处**维护（源文件或发布脚本），其余由脚本联动生成。
- 破坏性变更必须在 `CHANGELOG.md` 写「用户要做的事」，而不只是「改了什么」。

## 3. 各端怎么表达版本（可直接抄）

| 端 | 写法 |
|---|---|
| Surge | 头部：`#!version=1.8.0`（客户端可见）+ `#!desc=… v1.8.0` + 注释行 `# 版本: v1.8.0`；每条 `script-path` 加 `?v=1.8.0` |
| Shadowrocket | 同 Surge（`#!desc` 带版本 + `?v=`） |
| Loon | `#!desc=… v1.8.0`；脚本引用处加 `?v=`；`#!loon_version` 只写最低版本要求 |
| Egern | `description: "… v1.8.0"`；`scriptings[].script_url` 加 `?v=` |
| Stash | 覆写配置里注释 + `script-providers.<name>.url` 加 `?v=` |
| Quantumult X | 脚本头 `// @version 1.8.0`；rewrite / task 行的 URL 加 `?v=` |
| LanceX | 同 Surge 风格注释 + 脚本 URL 加 `?v=` |
| ProxyPin | 脚本头注释 `// @version 1.8.0` + 脚本名带版本 |

`?v=` 的作用：让客户端把脚本当成新 URL，绕开 CDN 旧缓存。**它消不掉 GitHub raw 自身的传播延迟**（实测 30 秒～2 分钟以上）🟡

## 4. 分类标签

| 端 | 官方字段 | 说明 |
|---|---|---|
| Loon | `#!tag` ✅ | 逗号分隔，如 `#!tag = 去广告,工具` |
| Egern | 无 tag 字段 ✅ | 用 `name`/`description` 表达；分类放在仓库目录 |
| Surge | `#!category` ✅实测 | 模块分类，`/v1/modules` 的 `category` 字段即来自它；值可以带 `#`（实测 `#!category=#🥜PInaTe` 原样返回） |
| Shadowrocket | 文档未列 `#!category` ⚠️ | 写了也只是 header 备注，未验证是否解析 |
| Stash / QX / LanceX / ProxyPin | 无 ✅ | 分类放在仓库目录 / README 表格 |

约定：**分类标签只用于仓库组织与人类检索，不承担功能语义**。仓库固定五个一级分类：

`去广告` · `功能增强` · `解锁/重写` · `面板/小组件` · `工具/信息`

## 5. 模块详情（发布时必须填全）

模块详情页 / README 表格里，一个模块至少要有这些信息：

| 字段 | 要求 |
|---|---|
| 名称 | 简短，写清它改的是哪个 App / 站点 |
| 一句话描述 | 说明「拦截了什么 / 改成了什么」，不写「优化体验」这类空话 |
| 支持端 | 逐个列出（Surge / SR / QX / Loon / Stash / Egern / LanceX / ProxyPin） |
| 版本 | SemVer |
| 影响的域名/接口 | 列出 host + path，便于判断冲突 |
| 参数 | 有哪些、默认值、取值范围 |
| 依赖 | 是否需要 Surge 版本、Loon build、证书、其它模块 |
| 已知限制 | 例如「App 需清缓存才生效」「仅 iOS」 |
| 更新方式 | 自动 / 需手动更新模块 |

示例：

```markdown
| 模块 | 版本 | 支持端 | 影响接口 | 参数 | 已知限制 |
|---|---|---|---|---|---|
| 山海泉·储值会员卡 | 1.8.0 | Surge | `room.yunvip123.cn/prod-api/room/smallProgram/*` | MODE | 仅本机显示层 mock |
```

