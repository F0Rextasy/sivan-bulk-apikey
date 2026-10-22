<div align="center">

```
███████╗██╗██╗   ██╗ █████╗ ███╗   ██╗
██╔════╝██║██║   ██║██╔══██╗████╗  ██║
███████╗██║██║   ██║███████║██╔██╗ ██║
╚════██║██║╚██╗ ██╔╝██╔══██║██║╚██╗██║
███████║██║ ╚████╔╝ ██║  ██║██║ ╚████║
╚══════╝╚═╝  ╚═══╝  ╚═╝  ╚═╝╚═╝  ╚═══╝
```

# sivan-bulk-apikey

**Bulk-create Sivan API accounts with auto-generated API keys — OpenAI-compatible inference + 9Router connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Quick start](#-quick-start) · [🎁 Free tier](#-free-tier--how-much-you-get) · [🤖 Models](#-models--all-4-of-them) · [🧪 Use the API](#-how-to-use-the-api) · [✅ Verified](#-verified-working--every-key-is-tested) · [🧭 How it works](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Why this exists

Sivan API is a **New API** fork with one quirk that costs an afternoon if you miss it: **its API host is not its web host**. `sivan.bond` serves the dashboard, `api.sivan.bond` serves both the panel routes (`/api/*`) and the OpenAI route (`/v1/*`) — pointing a client at the web host is answered `404` with the hint `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`. The CLI bakes the right host in from the start.

Signup itself is captcha-free (`turnstile_check: false`) and gated only by a 6-digit mail code, so the account is automatable over plain HTTP. What makes this panel worth automating is the **daily check-in**: a fresh account holds 500,000 quota, and one check-in on the mint day measured **+1,771,845** on top of it (`quota_awarded`), so the tool checks every account in before it keeps the key.

## 🎁 Free tier — how much you get

| What | Value |
| --- | --- |
| Signup credit | **500,000 quota** (`quota_per_unit: 500000` → ≈ **$1.00**) |
| Daily check-in | **enabled** (`checkin_enabled: true`) — measured award on a fresh account: **+1,771,845** (`quota_awarded`), taking the balance to **≈ 2.27 M** |
| Card / phone | neither |
| Billing unit | **per account** — the quota lives on the account, which is why this tool creates one account per key |
| E-mail gate | **required** — 6-digit code, verified before the account exists |

With N keys you hold N accounts, i.e. **N × (signup credit + today's check-in)**, and each account keeps collecting a check-in award per day it is opened.

> 💡 The free tier is set by Sivan API and can change. Check the live numbers at [sivan.bond](https://sivan.bond) — and let the tool's own gate decide:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Models — all 4 of them

`GET /v1/models` (with a key) lists **4 ids** on this panel:

| Model ID | Group | Verified |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` answered |
| `deepseek-v4.1-flash` | `default` | listed |
| `glm-5.3` | `default` | listed |
| `glm-5.3-flash` | `default` | listed |

> ⚠️ `/v1/models` answers **only to GET** — a POST is refused with `Invalid URL (POST /v1/models)`.

## 🧪 How to use the API

Base URL **`https://api.sivan.bond/v1`** — OpenAI-compatible. Note the host: **not** `sivan.bond`.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

The model can emit its reasoning in `reasoning_content` next to `content`, and the upstream sometimes repeats a token (`okokok responseok response…`) — a client that only reads `content` still gets text.

## 🛠 Quick start

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 10 --pool proxies.txt
```

No browser and no captcha solver is involved: the panel's signup is pure HTTP plus an inbox. A key is written to `keys.txt` **only** after `/v1/chat/completions` answers 200 with real content; `accounts.jsonl` keeps the address, the balance before and after the check-in, and the gate result.

### 🖱️ The launcher

The launcher asks for the proxy list (optional — without one the CLI runs direct), how many accounts to mint and the model used for the live gate, remembers them in `launcher.config.json`, and runs the CLI: **mint**, **verify**, **screen** (test the exits against the panel), **settings**, **help** and **Connect to 9Router**.

## ✅ Verified working — every key is tested

| Check | Result |
| --- | --- |
| `GET /api/status` (on `api.sivan.bond`) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; the code mail (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) arrives within seconds |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` accepted |
| `POST /api/user/login` | **200** with `access_token` |
| `POST /api/token/` then `POST /api/token/<id>/key` | the list answers the key **masked**; the reveal route returns the plaintext |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** with real content (`deepseek-v4-flash`) |
| wrong host | web host `/v1/*` answers **404** with the `api.sivan.bond` hint |
| tools | **works** — a `tools` request returns `tool_calls: [get_time]` |

> **Quality gates.** Every key here passed a live `chat-200` before it was written to `keys.txt`
> (dead keys are never shipped). Router combos follow the same rule at the model level: an entry is
> added **only if the model answers `chat-200` and can call tools** — a model without tool support,
> a dead key or a dead provider is dropped instead of added. Re-run the verify step any time;
> failures are reported, never hidden.

## 🧭 How it works

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Step | Call | Notes |
| --- | --- | --- |
| mailbox | Emailnator `POST /api/generate-email` (`ids: [3]`), falling back to temp.tf `GET /api/account` | a fresh `@gmail.com` (or a Microsoft plus-alias) registered in its dot-free form; the box is snapshotted **before** the code is requested — a recycled address is full of other tenants' mail, so only ids *and* a timestamp newer than that snapshot count, and the message must name the panel in its sender/subject |
| status | `GET /api/status` | gate check before spending a mailbox |
| send code | `GET /api/verification?email=…` | panel mails the 6-digit code |
| code | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | polls every 5 s inside a 240 s window; the code is read only from the panel's own mail (`验证码`/`code`-anchored) |
| register | `POST /api/user/register` | `{username, password, email, verification_code}` |
| login | `POST /api/user/login` | `access_token`, then `New-Api-User: <id>` for the token routes |
| check-in | `POST /api/user/checkin` | the balance is re-read afterwards so the award is recorded per account |
| key | `POST /api/token/` → `POST /api/token/<id>/key` | the create answers a **masked** key on this fork; the reveal route is the only way to the plaintext |
| gate | `POST /v1/chat/completions` | 200 with real content = the key is kept |

Every request of one account goes through the **same pinned exit**: a fork that binds the pending verification code server-side answers `Verification code is incorrect or has expired` when the address that asked for the code is not the address that registers.

## ⚙️ CLI options

```bash
node src/cli.js --count 10                       # mint 10 keys (one account each)
node src/cli.js --count 5 --conc 3               # three accounts in flight
node src/cli.js --count 10 --model deepseek-v4.1-flash
node src/cli.js --no-checkin                     # skip the daily check-in
node src/cli.js --verify-only                    # re-check every key in keys.txt
node src/cli.js --mail-rail emailnator,temptf     # mail rails tried in order
node src/cli.js --screen                         # test the exits against the panel
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456
node src/cli.js --help
```

| Env | Default | Meaning |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | exit pool, one per line (same as `--pool`); absent = direct |
| `SIVAN_MODEL` | `deepseek-v4-flash` | model used for the live gate |

## 🛰️ Behind a proxy

**Optional — recommended at scale.** Every request goes through one exit from your list, and one exit is **pinned per account**, so send-code, register, login, check-in, key reveal and the gate all leave from the same address and the panel never sees a mixed trail.

- **With a pool:** `--pool proxies.txt` (or `SIVAN_PROXIES`). A dead exit fails that attempt and the next try takes the next exit.
- **Fail-closed:** an explicit `--pool` pointing at nothing falls back to **direct** with a printed warning — never a silent mixed run.
- **Without a pool:** the CLI prints `running WITHOUT a proxy (direct)` and works the same.

## 📄 Files

| Path | Contents |
| --- | --- |
| `keys.txt` | one live-verified key per line — git-ignored |
| `accounts.jsonl` | one JSON record per account: address, username, mail rail, check-in status, quota before/after, exit, gate result, key — git-ignored |
| `proxies.txt` | your exits, one per line — git-ignored |
| `out/` | run logs — git-ignored |

## 🔌 Connect to 9Router

[9Router](https://9router.com) is a self-hosted, OpenAI-compatible router:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 Connect to OmniRoute

[OmniRoute](https://omniroute.online) is a self-hosted, OpenAI-compatible router:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

Defaults: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Models are served as `sivan/<model id>`.

## 🔑 What one key gets you

| | |
| --- | --- |
| 🏢 Provider | **Sivan API** — [sivan.bond](https://sivan.bond) (API on `api.sivan.bond`) |
| 🎁 What the provider gives | 500,000 signup quota **plus a daily check-in** (measured award on day one: **+1,771,845**) |
| 🔑 What one key gets you | one 48-character key on its own account, check-in'd and chat-verified on `deepseek-v4-flash` |
| 🌐 API shape | OpenAI-compatible: `POST https://api.sivan.bond/v1/chat/completions` (4 model ids) |
| 🔒 Your data | only `sivan.bond`, `api.sivan.bond`, the mail backend (`Emailnator`, falling back to `temp.tf`) and your own proxy are contacted; keys stay on your machine |

## 🐞 Issues

If Sivan API moves its API host again, changes its signup shape, its verification mail or the token routes, the tool stops at that step and reports the refusal verbatim (the exact status, the message the panel returned, the host it used). **[Open an issue](../../issues)** with the command and the exact output.

## 📄 License

MIT © 2026 — see [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>
