<div align="center">

# TinyLink

**属于你自己的短链——在边缘完成路由，统计访问却不追踪访客。**

TinyLink 是一个隐私优先的短链服务，支持按地域 / 设备路由、访问关卡、自定义域名、
样式化二维码、link-in-bio 页面和自托管的访问统计。它完全运行在 Cloudflare 上：
同一个 Worker 负责跳转、API 和管理后台。

**▶ 在线体验：[tinylink.cdlab.workers.dev](https://tinylink.cdlab.workers.dev/)**

**[English](README.md) | 简体中文**

</div>

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/cdLab996/picture-lib/wudi/tinylink/og-image.png" alt="TinyLink 管理后台" width="100%">
</p>

---

## TinyLink 是什么？

现成的短链服务只给两个选择：托管服务会替你追踪访客，自托管方案又是一台需要照看的服务器。
TinyLink 两者都不是——它是一个 Cloudflare Worker，在边缘完成跳转，数据留在你自己的数据库里，
统计访客时也从不保存对方的 IP 地址。

它是 [flnk](https://github.com/wuchendi/projects/tree/main/archived/flnk) 的继任者，
重新做成了一个独立产品，加入了工作区、自定义域名和管理后台。

---

## 生成与路由

*一条短链，把每位访客送到合适的地方。*

- **短链 →** 自定义或自动生成短码，可配置跳转状态码，可选透传查询参数，跳转路径上按 IP 限流。
- **智能路由 →** 按国家或设备把访客送往不同目标——iOS 去 App Store，Android 去 Google Play。
- **访问关卡 →** 密码页和不安全链接提示页，并可对社交平台爬虫返回 Open Graph 预览（隐藏真实目标）。
- **自定义域名 →** 通过 Cloudflare for SaaS 接入你自己的域名（如 `links.brand.com`），
  短链在该域名下生效，由 Cloudflare 签发 TLS 证书。
- **样式化二维码 →** 任何短链都能生成。

## 分享与统计

*知道什么有效——又不把访客数据交给任何人。*

- **Launchpad 落地页 →** 位于 `/m/<slug>` 的 link-in-bio 页面，支持社交卡片；编辑时有 iPhone
  外框的实时预览；分享按钮覆盖复制链接、主流社交平台、邮件、系统分享和二维码。
- **自托管统计 →** 访问统计和实时地球，基于 Cloudflare Analytics Engine。从不保存原始 IP——
  访客通过每天轮换的哈希来计数。
- **导入 / 导出 →** 以 JSON 或 CSV 批量迁入、迁出短链。

## 团队协作

*用工作区协作，而不是共享一个密码。*

- **工作区 →** 邀请同事、管理成员，并保留变更的操作记录。
- **API Key →** 用后台同一套 API 以脚本方式管理短链。
- **通知 →** 把点击、短链与落地页变更、举报、成员、邀请和域名事件推送到带签名的 Webhook，
  或通过你自己的机器人推送到 Telegram 群聊；每个渠道都有投递记录，并可重新投递。
- **登录 →** 基于 better-auth 的 Google / GitHub 登录，可选邮箱白名单。
- **随处可用 →** 内置中英双语。

## 保持干净

*公开的短链服务，需要有说"不"的办法。*

- **举报 →** 公开的举报表单与可接受使用政策；被封禁的短链和落地页返回 451 页面。
- **申诉 →** 被封禁的内容和被停用的账号可以申诉，由管理员处理并记入审计日志。
- **人机验证 →** 公开表单可选接入 Cloudflare Turnstile。
- **管理后台 →** 概览指标、停用用户或工作区、封禁短链与落地页、目标域名黑名单、保留短码，
  以及记录每一次管理操作的审计日志。
- **每日维护 →** 一次定时任务完成备份、过期短链清理、日志修剪和 Safe Browsing 重新扫描。

---

## 架构

```
            访客  /<slug>                     管理后台（React SPA）
                 │                                  │
                 └──────────────┬───────────────────┘
                                │   同源
              ┌─────────────────┴─────────────────┐
              │    Cloudflare Worker  (Hono)       │
              │    跳转 · /api · /m/<slug>          │
              └──┬──────────┬───────────┬──────────┘
                 │          │           │
            KV 缓存    libSQL / D1   Analytics Engine
                                     （哈希后的访客）
```

管理后台是预先构建好的 React 应用，由同一个 Worker 通过静态资源绑定提供，前端和 API
因此同源。跳转优先读 KV 缓存，只有缓存未命中时才查询数据库。

| 层 | 技术栈 |
| --- | --- |
| Worker | Cloudflare Workers 上的 Hono，使用 KV、Analytics Engine、Workers AI、Rate Limiting，可选 R2 |
| 管理后台 | React 19、Vite、TanStack Router + Query、shadcn/ui（Base UI）、Tailwind CSS v4、i18next、Zustand |
| 数据 | Drizzle ORM，默认 libSQL（Turso 或自托管 `sqld`），也可使用 Cloudflare D1 |
| 认证 | better-auth（Google / GitHub） |
| 工具链 | Bun workspaces、TypeScript、Biome、Vitest、Playwright |

---

## 源代码

TinyLink 目前暂未开源。这个仓库只存放它的公开文档；欢迎到
**[tinylink.cdlab.workers.dev](https://tinylink.cdlab.workers.dev/)** 体验。
