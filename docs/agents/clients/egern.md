# Egern

> scope: egern ｜ 触发: 改该端的模块/插件/脚本 ｜ 复核: 2026-11-24

> 官方文档：https://egernapp.com/zh-CN/docs/configuration/modules ｜ https://egernapp.com/zh-CN/docs/configuration/scriptings ｜ 最后核对：2026-11-24

## 复用单元

模块，**YAML 格式**（不是 ini）。可含规则、URL 重写、Header 重写、Body 重写、Map Local、脚本、MITM、HTTP 抓取、小组件。

## 模块元信息 ✅

`name`·`description`·`author`·`homepage`·`manual`·`icon`（SF Symbols 名或 URL）·`open_url`·`compat_arguments`·`compat_arguments_desc`·`env_schema`

```yaml
name: "示例模块"
description: "改某个接口的返回 v1.8.0"
author: "author"
homepage: "https://github.com/…"
icon: "shield.fill"
compat_arguments:
  BLOCK_MODE: "reject"
```

- 参数用 `{{{参数名}}}` 占位，主配置引用模块时用 `compat_arguments` 覆盖默认值。
- `env_schema` 会自动生成 UI：`options: ["true","false"]` → 开关；其它值列表 → 选择器；不写 → 文本框。

## 配置内容键 ✅

`dns`·`rules`·`url_rewrites`·`header_rewrites`·`body_rewrites`·`map_locals`·`scriptings`·`mitm`·`http_captures`·`widgets`·`bypass_tunnel_proxy`·`real_ip_domains`

## 脚本 ✅

五种类型：`http_request` · `http_response` · `schedule` · `generic` · `network`

```yaml
scriptings:
  - http_response:
      name: "改响应"
      match: "^https://api\\.example\\.com/data"
      script_url: "https://example.com/x.js?v=1.8.0"
      body_required: true
      max_size: 2097152
  - schedule:
      name: "每日签到"
      cron: "0 8 * * *"
      script_url: "https://example.com/daily.js"
```

字段：`name`（必填）`match`（HTTP 类必填）`script_url`（必填）`env` `update_interval`（默认 86400）`max_size`（默认 1048576）`timeout`（默认 10，最大 600）`body_required`（默认 false）`binary_body`（默认 false）`disabled`

- 环境变量通过 `ctx.env` 读取 ✅
- cron 支持 5 段与 6 段 ✅

## 坑

- YAML 里正则需要双重转义（`\\.`），从 ini 端复制过来最容易在这里出错。
- 运行时全局对象（`$done`、HTTP 客户端等）**未在官方网页文档列出** ⚠️，写之前先在客户端内的脚本文档/示例确认。

## 模块引用配置 ✅

主配置里 `modules:` 每项支持 `name` / `url`（必填）/ `compat_arguments` / `env` / `update_interval`（默认 86400）/ `enabled`。

