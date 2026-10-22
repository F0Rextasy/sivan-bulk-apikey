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

**自動生成された API キーで Sivan API アカウントを一括作成 — OpenAI 互換の推論 + 9Router コネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-4-of-them) · [🧪 API の使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 問題報告](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ これが存在する理由

Sivan API は **New API** のフォークであり、見落とすと半日を無駄にする癖が 1 つあります。それは **API ホストが Web ホストではない** という点です。`sivan.bond` はダッシュボードを提供し、`api.sivan.bond` はパネルのルート (`/api/*`) と OpenAI ルート (`/v1/*`) の両方を提供します。クライアントを Web ホストに向けると `404` が返り、ヒントとして `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口` が表示されます。CLI は最初から正しいホストを組み込んでいます。

サインアップ自体はキャプチャ不要 (`turnstile_check: false`) で、6 桁のメールコードのみでゲートされるため、アカウントはプレーン HTTP で自動化できます。このパネルを自動化する価値があるのは **毎日のチェックイン** です。新規アカウントは 500,000 クォータを保持し、作成当日の 1 回のチェックインで **+1,771,845** が上乗せされました (`quota_awarded`)。そのため、このツールはキーを保持する前にすべてのアカウントをチェックインします。

## 🎁 無料枠 — どれだけ得られるか

| 項目 | 値 |
| --- | --- |
| サインアップクレジット | **500,000 クォータ** (`quota_per_unit: 500000` → 約 **$1.00**) |
| 毎日のチェックイン | **有効** (`checkin_enabled: true`) — 新規アカウントでの実測付与額: **+1,771,845** (`quota_awarded`)、残高は **約 2.27 M** に |
| カード / 電話 | どちらも不要 |
| 課金単位 | **アカウント単位** — クォータはアカウントに紐づくため、このツールはキーごとに 1 アカウントを作成します |
| メールゲート | **必須** — 6 桁のコード、アカウント作成前に検証 |

N 個のキーがあれば N 個のアカウントを保持することになり、つまり **N × (サインアップクレジット + 当日のチェックイン)** となり、各アカウントは開かれるたびに毎日チェックイン付与を集め続けます。

> 💡 無料枠は Sivan API によって設定され、変更される可能性があります。最新の数値は [sivan.bond](https://sivan.bond) で確認してください。そしてツール自身のゲートに判断を委ねてください:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 モデル — 全 4 種類

`GET /v1/models` (キー付き) はこのパネルで **4 つの id** を一覧表示します:

| モデル ID | グループ | 検証済み |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**、`tool_calls` 応答あり |
| `deepseek-v4.1-flash` | `default` | 一覧表示 |
| `glm-5.3` | `default` | 一覧表示 |
| `glm-5.3-flash` | `default` | 一覧表示 |

> ⚠️ `/v1/models` は **GET のみ** に応答します — POST は `Invalid URL (POST /v1/models)` で拒否されます。

## 🧪 API の使い方

ベース URL **`https://api.sivan.bond/v1`** — OpenAI 互換。ホストに注意してください: `sivan.bond` では **ありません**。

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

モデルは `content` の隣の `reasoning_content` に推論を出力することがあり、上流がトークンを繰り返すこともあります (`okokok responseok response…`) — `content` のみを読むクライアントでもテキストは取得できます。

## 🛠 クイックスタート

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

ブラウザもキャプチャソルバーも関与しません。パネルのサインアップは純粋な HTTP と受信トレイだけです。キーは `/v1/chat/completions` が実際のコンテンツとともに 200 を返した **後にのみ** `keys.txt` に書き込まれます。`accounts.jsonl` はアドレス、チェックイン前後の残高、ゲート結果を保持します。

### 🖱️ ランチャー

ランチャーはプロキシリスト (オプション — なしの場合 CLI は直接実行)、作成するアカウント数、ライブゲートに使用するモデルを尋ね、それらを `launcher.config.json` に記憶し、CLI を実行します: **mint**、**verify**、**screen** (パネルに対して出口をテスト)、**settings**、**help**、**Connect to 9Router**。

## ✅ 動作検証済み — すべてのキーがテストされています

| チェック | 結果 |
| --- | --- |
| `GET /api/status` (`api.sivan.bond` 上) | **200** — `register_enabled: true`、`email_verification: true`、`turnstile_check: false`、`checkin_enabled: true`、`system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; コードメール (`Sivan API邮箱验证邮件`、`no-reply@mail.sivan.bond`) が数秒以内に到着 |
| `POST /api/user/register` | **200** `{"success":true}`、`verification_code` が受理される |
| `POST /api/user/login` | **200** `access_token` 付き |
| `POST /api/token/` その後 `POST /api/token/<id>/key` | 一覧はキーを **マスク** して応答; 公開ルートが平文を返す |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** 実際のコンテンツ付き (`deepseek-v4-flash`) |
| 誤ったホスト | Web ホストの `/v1/*` は `api.sivan.bond` のヒントとともに **404** を返す |
| ツール | **動作する** — `tools` リクエストが `tool_calls: [get_time]` を返す |

> **品質ゲート。** ここにあるすべてのキーは `keys.txt` に書き込まれる前にライブの `chat-200` を通過しています
> (死んだキーは決して出荷されません)。ルーターのコンボもモデルレベルで同じルールに従います: エントリは
> **モデルが `chat-200` を返し、ツールを呼び出せる場合にのみ** 追加されます — ツールサポートのないモデル、
> 死んだキー、死んだプロバイダは追加されずに除外されます。検証ステップはいつでも再実行できます。
> 失敗は報告され、決して隠されません。

## 🧭 仕組み

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| ステップ | 呼び出し | 備考 |
| --- | --- | --- |
| メールボックス | Emailnator `POST /api/generate-email` (`ids: [3]`)、フォールバックとして temp.tf `GET /api/account` | 新規の `@gmail.com` (または Microsoft のプラスエイリアス) をドットなし形式で登録; ボックスはコード要求の **前に** スナップショットされる — リサイクルされたアドレスは他のテナントのメールで溢れているため、スナップショットより新しい id *かつ* タイムスタンプのみがカウントされ、メッセージは送信者/件名にパネル名を含まなければならない |
| ステータス | `GET /api/status` | メールボックスを消費する前のゲートチェック |
| コード送信 | `GET /api/verification?email=…` | パネルが 6 桁のコードをメール送信 |
| コード | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | 240 秒のウィンドウ内で 5 秒ごとにポーリング; コードはパネル自身のメールからのみ読み取られる (`验证码`/`code` をアンカーとする) |
| 登録 | `POST /api/user/register` | `{username, password, email, verification_code}` |
| ログイン | `POST /api/user/login` | `access_token`、その後トークンルート用に `New-Api-User: <id>` |
| チェックイン | `POST /api/user/checkin` | その後残高が再読み取りされ、付与額がアカウントごとに記録される |
| キー | `POST /api/token/` → `POST /api/token/<id>/key` | このフォークでは作成が **マスク** されたキーを返す; 公開ルートが平文への唯一の道 |
| ゲート | `POST /v1/chat/completions` | 実際のコンテンツとともに 200 = キーが保持される |

1 つのアカウントのすべてのリクエストは **同じ固定された出口** を通過します: 保留中の検証コードをサーバー側でバインドするフォークでは、コードを要求したアドレスが登録するアドレスでない場合、`Verification code is incorrect or has expired` を返します。

## ⚙️ CLI オプション

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

| 環境変数 | デフォルト | 意味 |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | 出口プール、1 行に 1 つ (`--pool` と同じ); 未設定 = 直接 |
| `SIVAN_MODEL` | `deepseek-v4-flash` | ライブゲートに使用するモデル |

## 🛰️ プロキシの背後で

**オプション — 大規模な場合は推奨。** すべてのリクエストはリストの 1 つの出口を通過し、1 つの出口が **アカウントごとに固定** されるため、コード送信、登録、ログイン、チェックイン、キー公開、ゲートはすべて同じアドレスから出ていき、パネルが混在した痕跡を見ることはありません。

- **プールあり:** `--pool proxies.txt` (または `SIVAN_PROXIES`)。死んだ出口はその試行を失敗させ、次の試行が次の出口を使用します。
- **フェイルクローズ:** 何も指していない明示的な `--pool` は、警告を表示して **直接** にフォールバックします — 決してサイレントな混在実行にはなりません。
- **プールなし:** CLI は `running WITHOUT a proxy (direct)` を表示し、同じように動作します。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | 1 行に 1 つのライブ検証済みキー — git 無視 |
| `accounts.jsonl` | アカウントごとに 1 つの JSON レコード: アドレス、ユーザー名、メールレール、チェックイン状態、前後のクォータ、出口、ゲート結果、キー — git 無視 |
| `proxies.txt` | 出口、1 行に 1 つ — git 無視 |
| `out/` | 実行ログ — git 無視 |

## 🔌 9Router に接続

[9Router](https://9router.com) はセルフホストの OpenAI 互換ルーターです:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 OmniRoute に接続

[OmniRoute](https://omniroute.online) はセルフホストの OpenAI 互換ルーターです:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

デフォルト: `--9router-prefix sivan`、`--9router-name Sivan API`、`--base-url https://api.sivan.bond/v1`、`--model deepseek-v4-flash`。モデルは `sivan/<model id>` として提供されます。

## 🔑 1 つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダ | **Sivan API** — [sivan.bond](https://sivan.bond) (API は `api.sivan.bond`) |
| 🎁 プロバイダが提供するもの | 500,000 のサインアップクォータ **に加えて毎日のチェックイン** (初日の実測付与額: **+1,771,845**) |
| 🔑 1 つのキーで得られるもの | 独自のアカウント上の 1 つの 48 文字キー、チェックイン済みで `deepseek-v4-flash` でチャット検証済み |
| 🌐 API の形式 | OpenAI 互換: `POST https://api.sivan.bond/v1/chat/completions` (4 つのモデル id) |
| 🔒 あなたのデータ | 接続されるのは `sivan.bond`、`api.sivan.bond`、メールバックエンド (`Emailnator`、フォールバックとして `temp.tf`)、およびあなた自身のプロキシのみ; キーはあなたのマシンに留まります |

## 🐞 問題報告

Sivan API が API ホストを再び移動したり、サインアップの形式、検証メール、トークンルートを変更した場合、ツールはそのステップで停止し、拒否をそのまま報告します (正確なステータス、パネルが返したメッセージ、使用したホスト)。コマンドと正確な出力を添えて **[issue を作成してください](../../issues)**。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE) を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>