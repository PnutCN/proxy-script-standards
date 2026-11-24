# Surge

> 官方文档：https://manual.nssurge.com ｜ 全站 markdown 索引：`/llms.txt` ｜ 最后核对：2026-11-24

## 复用单元

模块（`.sgmodule`）：一份**补丁**，不是完整配置。可本地放、也可用 URL 安装；启用状态不跨设备同步。

## 元数据（官方，只有这些）✅

```
#!name=模块名
#!desc=一句话描述（版本号写在这里，如 … v1.8.0）
#!system=mac            # 可选，限制平台（ios / mac）
#!arguments=a:1,b:true  # 可选，参数表（Mac 5.5.0+）
#!arguments-desc=参数说明
#!requirement=CORE_VERSION>=20   # 可选，版本/系统条件
```

- 参数占位符是 `{{{name}}}`，只用字母数字下划线，大小写敏感；不支持 `?a=1&b=2` 形式，也不支持 `%PARAMETER%`。
- 社区模块常见 `#!category=` `#!author=` `#!homepage=` `#!raw-url=` 🟡 —— **官方文档没有这些字段**，能否被解析未验证 ⚠️；可以写（不影响运行），但不要依赖它做逻辑判断。

## 可覆盖段 ✅

`General` `MITM` `Ponte` `DHCP` `WireGuard *` `Tailscale *` `MTProto`（iOS 5.21+ / Mac 6.8+）`Snell Server`（iOS 5.23+ / Mac 6.10+）`Ruleset *`（iOS 5.23+ / Mac 6.10+）`Rule` `Script` `URL Rewrite` `Header Rewrite` `Body Rewrite` `Map Local` `Host` `IP Rewrite` `SSID Setting` `Panel`

- `key = value` 覆盖；`%APPEND%` 追加；`%INSERT%` 前插（键值段）。
- 只有 `[MITM]` 段能操作 `hostname`、`client-source-address`、`skip-server-cert-verify`、`tcp-connection`、`h2`。
- 不能改 `[Proxy]` / `[Proxy Group]`，不能改 MITM CA 证书；`[Replica]` 自 Mac 5.4.0 起移除。

## 脚本声明 ✅

```
[Script]
mem-card-list = type=http-response,pattern=^https?://api\.example\.com/v1/user,requires-body=true,max-size=1048576,script-path=https://…/x.js?v=1.8.0,script-update-interval=3600
```

脚本类型：`http-request` `http-response` `cron` `dns` `event` `generic` `rule`。

- 改 body 必须 `requires-body=true`；默认 `max-size` iOS 1MB / macOS 10MB，超限静默 passthrough。
- 同一响应只跑**第一条**匹配的 http-response 脚本。
- `argument=` 传参，脚本里读 `$argument`。

## 一键导入 ✅

`surge:///install-module?url=<percent-encoded 模块URL>`；Mac 6.7.0+ 也接受 `surgeconfig:///`。
