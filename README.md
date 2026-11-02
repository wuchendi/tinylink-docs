<div align="center">

# TinyLink

**Short links you own — routed at the edge, counted without tracking.**

TinyLink is a privacy-first link shortener with geo / device routing, gates, custom
domains, styled QR codes, link-in-bio pages and self-hosted analytics. It runs entirely
on Cloudflare: one Worker serves the redirects, the API and the dashboard.

**▶ Live: [tinylink.cdlab.workers.dev](https://tinylink.cdlab.workers.dev/)**

**English | [简体中文](README.zh-CN.md)**

</div>

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/cdLab996/picture-lib/wudi/tinylink/og-image.png" alt="The TinyLink dashboard" width="100%">
</p>

---

## What is TinyLink?

Off-the-shelf shorteners make you choose: the hosted ones track your visitors for you, the
self-hosted ones are a server you babysit. TinyLink is neither — a single Cloudflare Worker
that resolves redirects at the edge, keeps your data in your own database, and counts
visitors without ever storing their IP address.

---

## Shorten and route.

*One short link, the right destination for every visitor.*

- **Short links →** Custom or generated slugs, a configurable redirect status, optional
  query forwarding, and per-IP rate limiting on the resolve path.
- **Smart routing →** Send visitors to different targets by country or by device — iOS to the
  App Store, Android to Google Play.
- **Gates →** Password and unsafe-link interstitials, plus cloaking with Open Graph previews
  for social crawlers.
- **Custom domains →** Connect your own hostname (e.g. `links.brand.com`) over Cloudflare for
  SaaS; your links resolve on it with a Cloudflare-issued TLS certificate.
- **Styled QR codes →** Generated for any link.

## Share and measure.

*Know what works — without shipping your visitors' data anywhere.*

- **Launchpads →** Link-in-bio pages at `/m/<slug>` with social blocks, an iPhone-framed live
  preview while you edit, and a share button covering copy link, the major social networks,
  email, native share and a QR code.
- **Self-hosted analytics →** Visit stats and a realtime globe, backed by Cloudflare Analytics
  Engine. Raw IPs are never stored — visitors are counted through a daily-rotating hash.
- **Import / export →** Move links in and out as JSON or CSV.

## Work as a team.

*Workspaces, not a shared password.*

- **Workspaces →** Invite teammates, manage members, and keep an activity log of what changed.
- **API keys →** Script your links against the same API the dashboard uses.
- **Notifications →** Send clicks, link and launchpad changes, abuse reports, members, invites
  and domain events to signed webhooks or a Telegram chat through your own bot — with a
  per-channel delivery log and redelivery.
- **Sign-in →** Google / GitHub through better-auth, with an optional email allow-list.
- **Everywhere →** English and Chinese out of the box.

## Keep it clean.

*A public shortener needs a way to say no.*

- **Abuse reporting →** A public report form and an acceptable use policy; blocked links and
  launchpads answer with a 451 page.
- **Appeals →** A path back for blocked content and suspended accounts, decided by an admin and
  recorded in the audit log.
- **Human verification →** Optional Cloudflare Turnstile on the public forms.
- **Admin console →** Overview metrics, suspending users or workspaces, blocking links and
  launchpads, blocked destination domains, reserved slugs, and an audit log of every admin
  action.
- **Daily maintenance →** One scheduled pass handles backups, expired-link cleanup, log
  pruning and a Safe Browsing rescan.

---

## Architecture

```
            Visitor  /<slug>                 Dashboard  (React SPA)
                 │                                  │
                 └──────────────┬───────────────────┘
                                │   same origin
              ┌─────────────────┴─────────────────┐
              │    Cloudflare Worker  (Hono)       │
              │    redirects · /api · /m/<slug>    │
              └──┬──────────┬───────────┬──────────┘
                 │          │           │
           KV cache   libSQL / D1   Analytics Engine
                                    (hashed visitors)
```

The dashboard is a pre-built React app served by the same Worker through its assets binding,
so the front end and the API share one origin. Redirects are answered from a KV cache; the
database is only read on a cache miss.

| Layer | Stack |
| --- | --- |
| Worker | Hono on Cloudflare Workers, with KV, Analytics Engine, Workers AI, Rate Limiting and optional R2 |
| Dashboard | React 19, Vite, TanStack Router + Query, shadcn/ui (Base UI), Tailwind CSS v4, i18next, Zustand |
| Data | Drizzle ORM over libSQL (Turso or a self-hosted `sqld`) by default, or Cloudflare D1 |
| Auth | better-auth (Google / GitHub) |
| Tooling | Bun workspaces, TypeScript, Biome, Vitest, Playwright |

---

## Source code

TinyLink is closed-source for now. This repository holds its public documentation; try it at
**[tinylink.cdlab.workers.dev](https://tinylink.cdlab.workers.dev/)**.
