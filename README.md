<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/header-dark.svg">
  <img src="assets/header-light.svg" alt="Maciej Wiśniewski, software architect for digital business" width="100%">
</picture>

<p align="center">
  <a href="https://berserkrush.pl"><b>berserkrush.pl</b></a>&nbsp;&nbsp;·&nbsp;&nbsp;<a href="mailto:maciej.wisniewski@aol.com">maciej.wisniewski@aol.com</a>&nbsp;&nbsp;·&nbsp;&nbsp;<a href="https://discord.com/invite/berserkrush">Discord</a>
</p>

## About

I'm 22 and I build software for digital businesses from the first conversation to production: requirements, data model, backend, frontend, mobile apps, deployment and support after launch. My strongest ground is backend architecture with Laravel and PostgreSQL.

I'm also a Computer Science engineering student specializing in **network and systems security**, and it shapes how I build: authorization lives on the server, money and items only move inside transactions, important actions leave an audit trail, and nothing is trusted just because the client sent it.

## Flagship: Berserk Rush

**[Berserk Rush](https://berserkrush.pl)** is a multiplayer browser RPG that I designed, built and operate on my own: turn-based combat, a player-driven market, guilds, a PvP arena and a 3D world map. It runs on the web, Android, iOS and Windows, with a Discord community of 300.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/berserk-rush-dark.svg">
  <img src="assets/berserk-rush-light.svg" alt="Berserk Rush architecture: browser, mobile, Windows and Discord clients talk to a Laravel 12 application on Octane, backed by PostgreSQL, Reverb, queue workers and integrations" width="100%">
</picture>

- **Backend:** Laravel 12 on Octane (FrankenPHP), 32 domain modules, PostgreSQL with a hub database plus one database per game server.
- **Realtime and background work:** Reverb WebSockets for chat and live events; queue workers that run server-side expeditions and a population of bots that plays like real users.
- **Frontend:** Livewire and Alpine with Vue 3 islands; a Three.js world with a KTX2 / meshopt asset pipeline tuned for phones.
- **Apps:** Capacitor builds on [Google Play](https://play.google.com/store/apps/details?id=pl.berserkrush.app) and the [App Store](https://apps.apple.com/pl/app/berserk-rush/id6817840892) (in-app purchases, Sign in with Apple, push notifications), an Electron launcher for Windows and a discord.js bot linked to game accounts.
- **Open data:** a documented, read-only REST API (`/api/v1`) with scoped tokens for guild leaders.
- **AI:** an in-game wiki assistant built on the Claude API.
- **Quality:** 2,400+ Pest tests, k6 load tests, 97 module docs, 5 languages.

<sub>The source is private because the game is a live commercial product. I'm happy to walk through the code in an interview.</sub>

## Products for businesses

| Product | What it does | Built with |
|---|---|---|
| **[Client Reminder](https://github.com/KenjiWriter/client-reminder-app)** | **Client project delivered for Fundacja Centrum Pro Bono.** Appointment portal for small clinics: drag-and-drop calendar, online booking with approval, automatic SMS confirmations and 24 h reminders, client medical card with risk flags, two-way Google Calendar sync, built-in mail client. | Laravel 12, Inertia, Vue 3, TypeScript |
| **[IT Helpdesk](https://github.com/KenjiWriter/helpdesk-it)** | Internal IT service desk: an employee portal plus a control center for technicians, a strict ticket lifecycle, read-only audit trail, SLA and workload reporting, 2FA. | Laravel 12, Livewire 4, Filament 5 |
| **[QrMe](https://github.com/KenjiWriter/QrMe)** | Digital business cards for teams: a mobile-first page and a QR code for every employee, scan tracking that counts real scans only, one-tap vCard save, PL / EN. | Laravel 13, Vue 3, Tailwind 4 |
| **[Neighborly](https://github.com/KenjiWriter/Neighborly)** | Housing-community management: deny-by-default roles, data scoped to community, building and unit, an immutable audit log. | Laravel 12, Vue 3, Inertia |

In progress: **[RideIdle](https://github.com/KenjiWriter/RideIdle)**, an idle racing game built as an API-first engine (Laravel + Vue 3 / TypeScript), with server-authoritative settlement in integer math and CI against both MariaDB and PostgreSQL.

## Security focus: anti-cheat in Berserk Rush

In a live online game the real attack surface is the players: multi-account farms, bots and modified clients. I built the game's anti-cheat as a detection and analysis pipeline, and tuned every threshold against production data so that honest players are not punished.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/anticheat-dark.svg">
  <img src="assets/anticheat-light.svg" alt="Anti-cheat pipeline: signals from login history, an account graph, detectors every minute, then a review queue and escalating lockouts" width="100%">
</picture>

- **Signals.** Login history stores one row per account, signal and day: the network (IPv6 grouped by /64) and a browser fingerprint, kept for 30 days. The client also reports tab visibility, and the server tracks the pace of fights.
- **Correlation.** Every 10 minutes a job links accounts that share a fingerprint, or an IP on the same day. Signals used by more than 4 accounts (carrier-grade NAT, public Wi-Fi, identical phone models) are discarded, IP-only links count after 3 separate days, and linked accounts are grouped transitively into one person.
- **Detection.** Detectors run every minute on each game server: kill-rate anomalies in a 3-minute window, a third account on one device, 5+ characters on one IP, and one person running more busy characters than allowed across linked accounts. A repeated hit updates the open flag instead of adding a new one, so active cases rise to the top of the review panel.
- **Analysis.** The admin panel searches flags by account or character, and per-character activity logs, an item ledger and a gem history show where every item and currency came from. Every transfer runs in a transaction with row locks and idempotency keys, so duplicated loot leaves a trace.
- **Server-side enforcement.** The server sets the pace, not the client: a minimum gap between fights that grows to 1.5 s once the tab has been hidden for 8 s, one active tab per screen group (ten tabs used to open chests ten times faster), and per-person caps on expeditions, dungeon series and raids.
- **Response.** A lockout fires only after 3 consecutive confirming runs, keeps the oldest accounts playing and escalates 1 h, 2 h, 4 h, resetting after 7 clean days. Sign-up adds Cloudflare Turnstile and soft per-network limits.
- **Tuned on real data.** Production showed 26 unrelated accounts sharing one fingerprint (same phone model). Since then lockouts use the network signal only, a fingerprint groups accounts only when a shared IP on the same day confirms it, and only characters that are actually busy count, not ones that are merely online.

<sub>Outside the game: deny-by-default authorization with scoped queries (Neighborly), read-only audit trails (IT Helpdesk), TOTP 2FA and encrypted credentials (IT Helpdesk, Client Reminder).</sub>

## Toolbox

| Area | Tools |
|---|---|
| **Backend** | <img src="https://img.shields.io/badge/PHP-21262D?style=flat-square&amp;logo=php&amp;logoColor=9B9FE8" alt="PHP" height="22"> <img src="https://img.shields.io/badge/Laravel-21262D?style=flat-square&amp;logo=laravel&amp;logoColor=FF2D20" alt="Laravel" height="22"> <img src="https://img.shields.io/badge/Octane-21262D?style=flat-square&amp;logo=laravel&amp;logoColor=FF2D20" alt="Octane" height="22"> <img src="https://img.shields.io/badge/FrankenPHP-21262D?style=flat-square&amp;logo=php&amp;logoColor=9B9FE8" alt="FrankenPHP" height="22"> <img src="https://img.shields.io/badge/Livewire-21262D?style=flat-square&amp;logo=livewire&amp;logoColor=FB70A9" alt="Livewire" height="22"> <img src="https://img.shields.io/badge/Filament-21262D?style=flat-square&amp;logo=filament&amp;logoColor=FDAE4B" alt="Filament" height="22"> <img src="https://img.shields.io/badge/Inertia-21262D?style=flat-square&amp;logo=inertia&amp;logoColor=B57FFF" alt="Inertia" height="22"> <img src="https://img.shields.io/badge/Pest-21262D?style=flat-square&amp;logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI%2BPHBhdGggZmlsbD0iI0Y0NzJCNiIgZD0iTTEyIDJhMTAgMTAgMCAxIDAgMCAyMCAxMCAxMCAwIDAgMCAwLTIwem0tMS4yIDE0LjItNC00IDEuNC0xLjQgMi42IDIuNiA1LjYtNS42IDEuNCAxLjQtNyA3eiIvPjwvc3ZnPg%3D%3D&amp;logoColor=F472B6" alt="Pest" height="22"> |
| **Frontend** | <img src="https://img.shields.io/badge/Vue_3-21262D?style=flat-square&amp;logo=vuedotjs&amp;logoColor=4FC08D" alt="Vue 3" height="22"> <img src="https://img.shields.io/badge/TypeScript-21262D?style=flat-square&amp;logo=typescript&amp;logoColor=3178C6" alt="TypeScript" height="22"> <img src="https://img.shields.io/badge/Alpine.js-21262D?style=flat-square&amp;logo=alpinedotjs&amp;logoColor=8BC0D0" alt="Alpine.js" height="22"> <img src="https://img.shields.io/badge/Tailwind_CSS-21262D?style=flat-square&amp;logo=tailwindcss&amp;logoColor=06B6D4" alt="Tailwind CSS" height="22"> <img src="https://img.shields.io/badge/Three.js-21262D?style=flat-square&amp;logo=threedotjs&amp;logoColor=FFFFFF" alt="Three.js" height="22"> |
| **Data and realtime** | <img src="https://img.shields.io/badge/PostgreSQL-21262D?style=flat-square&amp;logo=postgresql&amp;logoColor=6F9EEB" alt="PostgreSQL" height="22"> <img src="https://img.shields.io/badge/MySQL-21262D?style=flat-square&amp;logo=mysql&amp;logoColor=5D9BD1" alt="MySQL" height="22"> <img src="https://img.shields.io/badge/MariaDB-21262D?style=flat-square&amp;logo=mariadb&amp;logoColor=C0765A" alt="MariaDB" height="22"> <img src="https://img.shields.io/badge/Reverb-21262D?style=flat-square&amp;logo=laravel&amp;logoColor=FF2D20" alt="Reverb" height="22"> <img src="https://img.shields.io/badge/WebSockets-21262D?style=flat-square&amp;logo=socketdotio&amp;logoColor=FFFFFF" alt="WebSockets" height="22"> |
| **Apps** | <img src="https://img.shields.io/badge/Android-21262D?style=flat-square&amp;logo=android&amp;logoColor=3DDC84" alt="Android" height="22"> <img src="https://img.shields.io/badge/iOS-21262D?style=flat-square&amp;logo=apple&amp;logoColor=FFFFFF" alt="iOS" height="22"> <img src="https://img.shields.io/badge/Capacitor-21262D?style=flat-square&amp;logo=capacitor&amp;logoColor=119EFF" alt="Capacitor" height="22"> <img src="https://img.shields.io/badge/Electron-21262D?style=flat-square&amp;logo=electron&amp;logoColor=9FEAF9" alt="Electron" height="22"> <img src="https://img.shields.io/badge/discord.js-21262D?style=flat-square&amp;logo=discorddotjs&amp;logoColor=7289DA" alt="discord.js" height="22"> |
| **Infrastructure** | <img src="https://img.shields.io/badge/Linux-21262D?style=flat-square&amp;logo=linux&amp;logoColor=FCC624" alt="Linux" height="22"> <img src="https://img.shields.io/badge/Ubuntu-21262D?style=flat-square&amp;logo=ubuntu&amp;logoColor=E95420" alt="Ubuntu" height="22"> <img src="https://img.shields.io/badge/nginx-21262D?style=flat-square&amp;logo=nginx&amp;logoColor=2BB24C" alt="nginx" height="22"> <img src="https://img.shields.io/badge/Cloudflare-21262D?style=flat-square&amp;logo=cloudflare&amp;logoColor=F38020" alt="Cloudflare" height="22"> <img src="https://img.shields.io/badge/Let%27s_Encrypt-21262D?style=flat-square&amp;logo=letsencrypt&amp;logoColor=FFFFFF" alt="Let&#039;s Encrypt" height="22"> <img src="https://img.shields.io/badge/GitHub_Actions-21262D?style=flat-square&amp;logo=githubactions&amp;logoColor=2088FF" alt="GitHub Actions" height="22"> <img src="https://img.shields.io/badge/Codemagic-21262D?style=flat-square&amp;logo=codemagic&amp;logoColor=F45E3F" alt="Codemagic" height="22"> <img src="https://img.shields.io/badge/k6-21262D?style=flat-square&amp;logo=k6&amp;logoColor=9C8CFF" alt="k6" height="22"> |
| **AI** | <img src="https://img.shields.io/badge/Claude_Code-21262D?style=flat-square&amp;logo=claude&amp;logoColor=D97757" alt="Claude Code" height="22"> <img src="https://img.shields.io/badge/Claude_API-21262D?style=flat-square&amp;logo=anthropic&amp;logoColor=F0EEE6" alt="Claude API" height="22"> |

## How I work with AI

Claude Code is part of my daily workflow, used like a pair programmer that follows the same rules I do. Each larger project carries its own agent guidelines and custom skills (architecture conventions, transaction rules, translations, how to verify a change), and nothing ships without my review and passing tests. I get the speed and keep full ownership of the code.

## Activity

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://streak-stats.demolab.com/?user=KenjiWriter&hide_border=true&background=00000000&stroke=30363D&ring=F0883E&fire=F0883E&currStreakLabel=F0883E&currStreakNum=E6EDF3&sideNums=E6EDF3&sideLabels=8B949E&dates=8B949E">
    <img src="https://streak-stats.demolab.com/?user=KenjiWriter&hide_border=true&background=00000000&stroke=D0D7DE&ring=C2410C&fire=C2410C&currStreakLabel=C2410C&currStreakNum=1F2328&sideNums=1F2328&sideLabels=59636E&dates=59636E" alt="GitHub contribution streak">
  </picture>
</p>

<p align="center"><sub>Łódź, Poland&nbsp;&nbsp;·&nbsp;&nbsp;open to backend, software architecture and security roles</sub></p>
