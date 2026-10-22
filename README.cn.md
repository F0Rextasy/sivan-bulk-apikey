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

**批量创建 Sivan API 账户并自动生成 API 密钥 — OpenAI 兼容推理 + 9Router 连接器。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 快速开始](#-quick-start) · [🎁 免费额度](#-free-tier--how-much-you-get) · [🤖 模型](#-models--all-4-of-them) · [🧪 使用 API](#-how-to-use-the-api) · [✅ 已验证](#-verified-working--every-key-is-tested) · [🧭 工作原理](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ 选项](#️-cli-options) · [🛰️ 代理](#️-behind-a-proxy) · [🐞 问题反馈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 为什么会有这个项目

Sivan API 是 **New API** 的一个分支，它有一个很容易被忽略、一旦忽略就要浪费一下午的特性：**它的 API 主机并不是它的 Web 主机**。`sivan.bond` 提供控制面板，`api.sivan.bond` 同时提供面板路由（`/api/*`）和 OpenAI 路由（`/v1/*`）——把客户端指向 Web 主机会得到 `404`，并附带提示 `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`。本 CLI 从一开始就内置了正确的主机。

注册本身没有验证码（`turnstile_check: false`），只受 6 位邮箱验证码限制，因此账户完全可以通过纯 HTTP 自动化。让这个面板值得自动化的是**每日签到**：新账户拥有 500,000 额度，而在注册当天签到一次实测获得 **+1,771,845**（`quota_awarded`），因此本工具会在保留密钥之前为每个账户完成签到。

## 🎁 免费额度 — 你能拿到多少

| 项目 | 数值 |
| --- | --- |
| 注册赠送 | **500,000 额度**（`quota_per_unit: 500000` → 约 **$1.00**） |
| 每日签到 | **已启用**（`checkin_enabled: true`）——新账户实测奖励：**+1,771,845**（`quota_awarded`），余额达到 **约 2.27 M** |
| 信用卡 / 手机号 | 均不需要 |
| 计费单位 | **按账户**——额度属于账户，因此本工具为每个密钥创建一个账户 |
| 邮箱门槛 | **必需**——6 位验证码，账户创建前需完成验证 |

拥有 N 个密钥就意味着拥有 N 个账户，即 **N ×（注册赠送 + 当日签到）**，并且每个账户在每次打开时都会继续获得每日签到奖励。

> 💡 免费额度由 Sivan API 设定，可能发生变化。请到 [sivan.bond](https://sivan.bond) 查看实时数据——并让工具自身的校验来决定：

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 模型 — 全部 4 个

`GET /v1/models`（带密钥）在此面板上列出 **4 个 id**：

| 模型 ID | 分组 | 已验证 |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**，`tool_calls` 有响应 |
| `deepseek-v4.1-flash` | `default` | 已列出 |
| `glm-5.3` | `default` | 已列出 |
| `glm-5.3-flash` | `default` | 已列出 |

> ⚠️ `/v1/models` **只响应 GET**——POST 会被拒绝并返回 `Invalid URL (POST /v1/models)`。

## 🧪 如何使用 API

基础 URL **`https://api.sivan.bond/v1`**——OpenAI 兼容。注意主机：**不是** `sivan.bond`。

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

模型可能会在 `content` 旁边通过 `reasoning_content` 输出其推理过程，而且上游有时会重复某个 token（`okokok responseok response…`）——只读取 `content` 的客户端仍然能拿到文本。

## 🛠 快速开始

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

无需浏览器，也无需验证码破解器：面板的注册流程纯粹是 HTTP 加一个收件箱。只有当 `/v1/chat/completions` 返回 200 且带有真实内容时，密钥才会被写入 `keys.txt`；`accounts.jsonl` 会保留地址、签到前后的余额以及校验结果。

### 🖱️ 启动器

启动器会询问代理列表（可选——不填则 CLI 直连）、要创建的账户数量以及用于实时校验的模型，将它们记录在 `launcher.config.json` 中，并运行 CLI：**mint**、**verify**、**screen**（针对面板测试出口）、**settings**、**help** 和 **Connect to 9Router**。

## ✅ 已验证可用 — 每个密钥都经过测试

| 检查项 | 结果 |
| --- | --- |
| `GET /api/status`（在 `api.sivan.bond` 上） | **200** — `register_enabled: true`、`email_verification: true`、`turnstile_check: false`、`checkin_enabled: true`、`system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`；验证码邮件（`Sivan API邮箱验证邮件`，`no-reply@mail.sivan.bond`）在数秒内到达 |
| `POST /api/user/register` | **200** `{"success":true}`，`verification_code` 被接受 |
| `POST /api/user/login` | **200**，返回 `access_token` |
| `POST /api/token/` 然后 `POST /api/token/<id>/key` | 列表返回的密钥是**掩码**的；reveal 路由返回明文 |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200**，带有真实内容（`deepseek-v4-flash`） |
| 错误主机 | Web 主机的 `/v1/*` 返回 **404**，并附带 `api.sivan.bond` 提示 |
| tools | **可用**——`tools` 请求返回 `tool_calls: [get_time]` |

> **质量门槛。** 这里的每个密钥在写入 `keys.txt` 之前都通过了实时 `chat-200`
> （绝不交付失效密钥）。路由器组合在模型层面遵循同样的规则：只有当模型能返回
> `chat-200` 且能调用工具时才会被添加——不支持工具的模型、失效的密钥或失效的
> 提供商都会被丢弃而不是添加。可随时重新运行验证步骤；失败会被报告，绝不隐藏。

## 🧭 工作原理

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| 步骤 | 调用 | 说明 |
| --- | --- | --- |
| 邮箱 | Emailnator `POST /api/generate-email`（`ids: [3]`），回退到 temp.tf `GET /api/account` | 一个全新的 `@gmail.com`（或 Microsoft 加号别名）以其无点形式注册；收件箱在请求验证码**之前**被快照——被回收的地址充满其他租户的邮件，因此只有 id *且* 时间戳晚于该快照的邮件才算数，并且邮件的发件人/主题中必须提到该面板 |
| 状态 | `GET /api/status` | 在消耗邮箱之前进行门槛检查 |
| 发送验证码 | `GET /api/verification?email=…` | 面板发送 6 位验证码 |
| 验证码 | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | 在 240 秒窗口内每 5 秒轮询一次；验证码只从面板自己的邮件中读取（以 `验证码`/`code` 为锚点） |
| 注册 | `POST /api/user/register` | `{username, password, email, verification_code}` |
| 登录 | `POST /api/user/login` | `access_token`，然后为 token 路由使用 `New-Api-User: <id>` |
| 签到 | `POST /api/user/checkin` | 之后重新读取余额，以便按账户记录奖励 |
| 密钥 | `POST /api/token/` → `POST /api/token/<id>/key` | 在此分支上创建操作返回的是**掩码**密钥；reveal 路由是获取明文的唯一途径 |
| 校验 | `POST /v1/chat/completions` | 200 且带有真实内容 = 保留该密钥 |

一个账户的每个请求都经过**同一个固定出口**：如果服务端绑定了待验证的验证码，那么当请求验证码的地址与注册的地址不一致时，分支会返回 `Verification code is incorrect or has expired`。

## ⚙️ CLI 选项

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

| 环境变量 | 默认值 | 含义 |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | 出口池，每行一个（等同于 `--pool`）；不设置 = 直连 |
| `SIVAN_MODEL` | `deepseek-v4-flash` | 用于实时校验的模型 |

## 🛰️ 使用代理

**可选——大规模使用时推荐。** 每个请求都经过你列表中的一个出口，并且每个账户**固定**一个出口，因此发送验证码、注册、登录、签到、密钥 reveal 和校验都从同一地址发出，面板永远不会看到混杂的轨迹。

- **使用代理池：** `--pool proxies.txt`（或 `SIVAN_PROXIES`）。失效的出口会使该次尝试失败，下一次尝试会使用下一个出口。
- **失败即关闭：** 显式指定 `--pool` 但指向空内容时会回退到**直连**并打印警告——绝不会静默地混杂运行。
- **不使用代理池：** CLI 会打印 `running WITHOUT a proxy (direct)`，行为完全相同。

## 📄 文件

| 路径 | 内容 |
| --- | --- |
| `keys.txt` | 每行一个经过实时验证的密钥——已被 git 忽略 |
| `accounts.jsonl` | 每个账户一条 JSON 记录：地址、用户名、邮件通道、签到状态、签到前后额度、出口、校验结果、密钥——已被 git 忽略 |
| `proxies.txt` | 你的出口，每行一个——已被 git 忽略 |
| `out/` | 运行日志——已被 git 忽略 |

## 🔌 连接到 9Router

[9Router](https://9router.com) 是一个自托管、OpenAI 兼容的路由器：

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 连接到 OmniRoute

[OmniRoute](https://omniroute.online) 是一个自托管、OpenAI 兼容的路由器：

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

默认值：`--9router-prefix sivan`、`--9router-name Sivan API`、`--base-url https://api.sivan.bond/v1`、`--model deepseek-v4-flash`。模型以 `sivan/<model id>` 形式提供。

## 🔑 一个密钥能给你什么

| | |
| --- | --- |
| 🏢 提供商 | **Sivan API** — [sivan.bond](https://sivan.bond)（API 位于 `api.sivan.bond`） |
| 🎁 提供商赠送 | 500,000 注册额度 **外加每日签到**（第一天实测奖励：**+1,771,845**） |
| 🔑 一个密钥能给你什么 | 一个 48 字符密钥，属于其独立账户，已完成签到并在 `deepseek-v4-flash` 上通过聊天验证 |
| 🌐 API 形态 | OpenAI 兼容：`POST https://api.sivan.bond/v1/chat/completions`（4 个模型 id） |
| 🔒 你的数据 | 仅会联系 `sivan.bond`、`api.sivan.bond`、邮件后端（`Emailnator`，回退到 `temp.tf`）以及你自己的代理；密钥保留在你的机器上 |

## 🐞 问题反馈

如果 Sivan API 再次更换其 API 主机、更改注册形态、验证邮件或 token 路由，工具会在该步骤停止并原样报告拒绝信息（确切的状态码、面板返回的消息、使用的主机）。请附上命令和确切输出 **[提交 issue](../../issues)**。

## 📄 许可证

MIT © 2026 — 见 [LICENSE](LICENSE)。

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>