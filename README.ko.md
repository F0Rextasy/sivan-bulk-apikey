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

**자동 생성된 API 키로 Sivan API 계정을 대량 생성 — OpenAI 호환 추론 + 9Router 커넥터.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 빠른 시작](#-quick-start) · [🎁 무료 티어](#-free-tier--how-much-you-get) · [🤖 모델](#-models--all-4-of-them) · [🧪 API 사용하기](#-how-to-use-the-api) · [✅ 검증됨](#-verified-working--every-key-is-tested) · [🧭 작동 방식](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ 옵션](#️-cli-options) · [🛰️ 프록시](#️-behind-a-proxy) · [🐞 이슈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 이 도구가 존재하는 이유

Sivan API는 **New API** 포크로, 놓치면 반나절을 날리는 특이점이 하나 있습니다: **API 호스트가 웹 호스트와 다릅니다**. `sivan.bond`는 대시보드를 제공하고, `api.sivan.bond`는 패널 라우트(`/api/*`)와 OpenAI 라우트(`/v1/*`)를 모두 제공합니다 — 클라이언트를 웹 호스트로 향하게 하면 `404`와 함께 `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`라는 힌트가 돌아옵니다. CLI는 처음부터 올바른 호스트를 내장하고 있습니다.

가입 자체는 캡차가 없고(`turnstile_check: false`) 6자리 메일 코드로만 제한되므로, 순수 HTTP로 계정을 자동화할 수 있습니다. 이 패널을 자동화할 가치가 있는 이유는 **일일 출석 체크**입니다: 새 계정은 500,000 쿼터를 보유하고, 가입 당일 한 번의 출석 체크로 **+1,771,845**가 측정되었으므로(`quota_awarded`), 이 도구는 키를 보관하기 전에 모든 계정의 출석 체크를 수행합니다.

## 🎁 무료 티어 — 얼마나 받을 수 있나

| 항목 | 값 |
| --- | --- |
| 가입 크레딧 | **500,000 쿼터** (`quota_per_unit: 500000` → ≈ **$1.00**) |
| 일일 출석 체크 | **활성화됨** (`checkin_enabled: true`) — 새 계정에서 측정된 지급액: **+1,771,845** (`quota_awarded`), 잔액이 **≈ 2.27 M**으로 증가 |
| 카드 / 전화 | 둘 다 불필요 |
| 과금 단위 | **계정당** — 쿼터는 계정에 귀속되므로, 이 도구가 키당 하나의 계정을 생성합니다 |
| 이메일 게이트 | **필수** — 6자리 코드, 계정 생성 전에 검증됨 |

N개의 키로 N개의 계정을 보유하게 됩니다. 즉 **N × (가입 크레딧 + 오늘의 출석 체크)**이며, 각 계정은 열릴 때마다 매일 출석 체크 지급액을 계속 적립합니다.

> 💡 무료 티어는 Sivan API가 설정하며 변경될 수 있습니다. 실시간 수치는 [sivan.bond](https://sivan.bond)에서 확인하세요 — 그리고 도구 자체의 게이트가 판단하게 하세요:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 모델 — 총 4개

`GET /v1/models`(키 포함)는 이 패널에서 **4개의 id**를 나열합니다:

| 모델 ID | 그룹 | 검증됨 |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` 응답됨 |
| `deepseek-v4.1-flash` | `default` | 나열됨 |
| `glm-5.3` | `default` | 나열됨 |
| `glm-5.3-flash` | `default` | 나열됨 |

> ⚠️ `/v1/models`는 **GET에만** 응답합니다 — POST는 `Invalid URL (POST /v1/models)`와 함께 거부됩니다.

## 🧪 API 사용 방법

Base URL **`https://api.sivan.bond/v1`** — OpenAI 호환. 호스트에 주의하세요: `sivan.bond`가 **아닙니다**.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

모델은 `content` 옆의 `reasoning_content`에 추론을 출력할 수 있으며, 업스트림이 때때로 토큰을 반복하기도 합니다(`okokok responseok response…`) — `content`만 읽는 클라이언트도 여전히 텍스트를 받습니다.

## 🛠 빠른 시작

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

브라우저도 캡차 솔버도 관여하지 않습니다: 패널의 가입은 순수 HTTP와 받은편지함만으로 이루어집니다. 키는 `/v1/chat/completions`가 실제 콘텐츠와 함께 200을 응답한 **후에만** `keys.txt`에 기록됩니다; `accounts.jsonl`은 주소, 출석 체크 전후의 잔액, 게이트 결과를 보관합니다.

### 🖱️ 런처

런처는 프록시 목록(선택 사항 — 없으면 CLI는 직접 실행), 생성할 계정 수, 라이브 게이트에 사용할 모델을 묻고, 이를 `launcher.config.json`에 기억한 뒤 CLI를 실행합니다: **mint**, **verify**, **screen**(패널에 대해 출구 테스트), **settings**, **help**, **Connect to 9Router**.

## ✅ 검증됨 — 모든 키가 테스트됨

| 검사 | 결과 |
| --- | --- |
| `GET /api/status` (`api.sivan.bond`에서) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; 코드 메일(`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`)이 몇 초 내에 도착 |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` 수락됨 |
| `POST /api/user/login` | **200**, `access_token` 포함 |
| `POST /api/token/` 후 `POST /api/token/<id>/key` | 목록은 키를 **마스킹**하여 응답; 공개 라우트가 평문을 반환 |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200**, 실제 콘텐츠 포함 (`deepseek-v4-flash`) |
| 잘못된 호스트 | 웹 호스트 `/v1/*`는 `api.sivan.bond` 힌트와 함께 **404** 응답 |
| 도구 | **작동함** — `tools` 요청이 `tool_calls: [get_time]`을 반환 |

> **품질 게이트.** 여기의 모든 키는 `keys.txt`에 기록되기 전에 라이브 `chat-200`을 통과했습니다
> (죽은 키는 절대 배포되지 않습니다). 라우터 조합도 모델 수준에서 동일한 규칙을 따릅니다: 항목은
> **모델이 `chat-200`을 응답하고 도구를 호출할 수 있는 경우에만** 추가됩니다 — 도구 지원이 없는 모델,
> 죽은 키, 죽은 제공자는 추가되지 않고 제거됩니다. 언제든지 검증 단계를 다시 실행하세요;
> 실패는 보고되며, 절대 숨겨지지 않습니다.

## 🧭 작동 방식

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| 단계 | 호출 | 비고 |
| --- | --- | --- |
| mailbox | Emailnator `POST /api/generate-email` (`ids: [3]`), temp.tf `GET /api/account`로 폴백 | 점 없는 형태로 등록된 새 `@gmail.com`(또는 Microsoft 플러스 별칭); 코드를 요청하기 **전에** 받은편지함을 스냅샷합니다 — 재활용된 주소는 다른 사용자의 메일로 가득하므로, 해당 스냅샷보다 새로운 id *및* 타임스탬프만 인정되며, 메시지는 발신자/제목에 패널 이름이 있어야 합니다 |
| status | `GET /api/status` | 메일박스를 소비하기 전 게이트 확인 |
| send code | `GET /api/verification?email=…` | 패널이 6자리 코드를 메일로 발송 |
| code | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | 240초 창 내에서 5초마다 폴링; 코드는 패널 자체 메일에서만 읽음(`验证码`/`code` 앵커) |
| register | `POST /api/user/register` | `{username, password, email, verification_code}` |
| login | `POST /api/user/login` | `access_token`, 이후 토큰 라우트용 `New-Api-User: <id>` |
| check-in | `POST /api/user/checkin` | 이후 잔액을 다시 읽어 계정별 지급액을 기록 |
| key | `POST /api/token/` → `POST /api/token/<id>/key` | 이 포크에서는 생성이 **마스킹된** 키를 응답; 공개 라우트가 평문으로 가는 유일한 방법 |
| gate | `POST /v1/chat/completions` | 실제 콘텐츠와 함께 200 = 키 보관 |

한 계정의 모든 요청은 **동일한 고정 출구**를 통과합니다: 대기 중인 검증 코드를 서버 측에서 바인딩하는 포크는 코드를 요청한 주소가 등록하는 주소와 다르면 `Verification code is incorrect or has expired`를 응답합니다.

## ⚙️ CLI 옵션

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

| 환경 변수 | 기본값 | 의미 |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | 출구 풀, 한 줄에 하나 (`--pool`과 동일); 없으면 직접 연결 |
| `SIVAN_MODEL` | `deepseek-v4-flash` | 라이브 게이트에 사용되는 모델 |

## 🛰️ 프록시 뒤에서

**선택 사항 — 대규모에서는 권장.** 모든 요청은 목록의 출구 하나를 통과하며, 출구는 **계정당 고정**되므로 코드 전송, 등록, 로그인, 출석 체크, 키 공개, 게이트가 모두 동일한 주소에서 나가고 패널은 혼합된 흔적을 절대 보지 못합니다.

- **풀 사용 시:** `--pool proxies.txt` (또는 `SIVAN_PROXIES`). 죽은 출구는 해당 시도를 실패시키고 다음 시도가 다음 출구를 사용합니다.
- **Fail-closed:** 아무것도 가리키지 않는 명시적 `--pool`은 경고를 출력하며 **직접 연결**로 폴백합니다 — 조용한 혼합 실행은 절대 없습니다.
- **풀 없이:** CLI는 `running WITHOUT a proxy (direct)`를 출력하고 동일하게 작동합니다.

## 📄 파일

| 경로 | 내용 |
| --- | --- |
| `keys.txt` | 라이브 검증된 키 한 줄에 하나 — git-ignored |
| `accounts.jsonl` | 계정당 하나의 JSON 레코드: 주소, 사용자 이름, 메일 레일, 출석 체크 상태, 전후 쿼터, 출구, 게이트 결과, 키 — git-ignored |
| `proxies.txt` | 출구, 한 줄에 하나 — git-ignored |
| `out/` | 실행 로그 — git-ignored |

## 🔌 9Router에 연결

[9Router](https://9router.com)는 자체 호스팅, OpenAI 호환 라우터입니다:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 OmniRoute에 연결

[OmniRoute](https://omniroute.online)는 자체 호스팅, OpenAI 호환 라우터입니다:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

기본값: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. 모델은 `sivan/<model id>`로 제공됩니다.

## 🔑 키 하나로 얻는 것

| | |
| --- | --- |
| 🏢 제공자 | **Sivan API** — [sivan.bond](https://sivan.bond) (API는 `api.sivan.bond`) |
| 🎁 제공자가 주는 것 | 500,000 가입 쿼터 **더하기 일일 출석 체크** (첫날 측정 지급액: **+1,771,845**) |
| 🔑 키 하나로 얻는 것 | 자체 계정의 48자 키 하나, 출석 체크 완료 및 `deepseek-v4-flash`에서 채팅 검증됨 |
| 🌐 API 형태 | OpenAI 호환: `POST https://api.sivan.bond/v1/chat/completions` (4개 모델 id) |
| 🔒 데이터 | `sivan.bond`, `api.sivan.bond`, 메일 백엔드(`Emailnator`, `temp.tf`로 폴백) 및 자체 프록시만 접속됩니다; 키는 사용자 컴퓨터에 남습니다 |

## 🐞 이슈

Sivan API가 API 호스트를 다시 옮기거나, 가입 형태, 검증 메일 또는 토큰 라우트를 변경하면, 도구는 해당 단계에서 멈추고 거부 내용을 그대로 보고합니다(정확한 상태, 패널이 반환한 메시지, 사용한 호스트). 명령과 정확한 출력을 포함하여 **[이슈를 열어주세요](../../issues)**.

## 📄 라이선스

MIT © 2026 — [LICENSE](LICENSE) 참조.

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>