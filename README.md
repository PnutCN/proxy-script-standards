# proxy-script-standards

给 AI agent（和人类）用的**代理软件模块 / 请求改写脚本开发规范**。

覆盖 8 个支持脚本或模块的客户端：Surge、Shadowrocket、Quantumult X、Loon、Stash、Egern、LanceX、ProxyPin。
每条规则都标注证据等级（✅ 官方文档 / 🟡 官方仓库或社区代码 / ⚠️ 未验证）并给出出处，见 [docs/agents/sources.md](docs/agents/sources.md)。

## 怎么用

- **AI agent**：先读 [AGENTS.md](AGENTS.md)，它只做索引，按需再读具体文件。
- **人类**：直接进 [docs/agents/](docs/agents/)，从 `core.md` 和 `versioning.md` 开始。
- **写某个端的模块/插件**：读 `clients/<端>.md`，里面是元数据字段、脚本声明写法、可覆盖段、已知坑。

## 目录

```
AGENTS.md                     AI 入口（索引 + 铁律 + 分流表）
docs/agents/core.md           脚本生命周期、错误处理、性能与隐私
docs/agents/versioning.md     版本号、分类标签、模块详情字段
docs/agents/api-matrix.md     跨端 API 对照（HTTP/存储/通知/参数/二进制）
docs/agents/rules-and-regex.md pattern 写法、策略限制、MITM
docs/agents/testing.md        本地 harness 与设备验证清单
docs/agents/distribution.md   托管、自动更新、一键导入
docs/agents/env-lib.md        跨端脚本库 Env.js 的用法与坑
docs/agents/clients/*.md      每个客户端一份
docs/agents/sources.md        所有文档来源与核对日期
```

## 为什么不是一个大文件

单文件会撑爆 agent 的上下文，也不方便只改一端。这里的做法是**渐进披露**：根文件只放"什么时候读哪个文件"，细节按主题和客户端拆分。

## 贡献

改规范 = 改对应文件 + 在 `sources.md` 里补出处 + 更新该文件顶部的「最后核对」。没有出处的规则请标 ⚠️，不要伪装成已验证。

## 许可

MIT
