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

**Массовое создание аккаунтов Sivan API с автоматически сгенерированными API-ключами — OpenAI-совместимый инференс + коннектор 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Быстрый старт](#-quick-start) · [🎁 Бесплатный тариф](#-free-tier--how-much-you-get) · [🤖 Модели](#-models--all-4-of-them) · [🧪 Использование API](#-how-to-use-the-api) · [✅ Проверено](#-verified-working--every-key-is-tested) · [🧭 Как это работает](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Опции](#️-cli-options) · [🛰️ Прокси](#️-behind-a-proxy) · [🐞 Проблемы](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Зачем это нужно

Sivan API — это форк **New API** с одной особенностью, которая может стоить вам целого дня, если её упустить: **его API-хост не совпадает с веб-хостом**. `sivan.bond` обслуживает панель управления, а `api.sivan.bond` обслуживает как маршруты панели (`/api/*`), так и маршрут OpenAI (`/v1/*`) — клиент, направленный на веб-хост, получает в ответ `404` с подсказкой `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`. CLI изначально использует правильный хост.

Сама регистрация проходит без капчи (`turnstile_check: false`) и защищена только 6-значным кодом из письма, поэтому аккаунт можно автоматизировать через обычный HTTP. Что делает эту панель достойной автоматизации — это **ежедневная отметка (check-in)**: новый аккаунт имеет 500 000 квоты, а одна отметка в день создания дала **+1 771 845** сверх этого (`quota_awarded`), поэтому инструмент отмечает каждый аккаунт, прежде чем сохранить ключ.

## 🎁 Бесплатный тариф — сколько вы получаете

| Что | Значение |
| --- | --- |
| Бонус за регистрацию | **500 000 квоты** (`quota_per_unit: 500000` → ≈ **$1.00**) |
| Ежедневная отметка | **включена** (`checkin_enabled: true`) — измеренное начисление на новом аккаунте: **+1 771 845** (`quota_awarded`), что доводит баланс до **≈ 2.27 M** |
| Карта / телефон | ни то, ни другое |
| Единица тарификации | **на аккаунт** — квота привязана к аккаунту, поэтому инструмент создаёт по одному аккаунту на ключ |
| Проверка e-mail | **обязательна** — 6-значный код, подтверждаемый до создания аккаунта |

С N ключами вы владеете N аккаунтами, то есть **N × (бонус за регистрацию + сегодняшняя отметка)**, и каждый аккаунт продолжает получать награду за отметку за каждый день, в который он открывается.

> 💡 Бесплатный тариф устанавливается Sivan API и может измениться. Проверяйте актуальные цифры на [sivan.bond](https://sivan.bond) — и пусть собственный контроль инструмента решает:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Модели — все 4

`GET /v1/models` (с ключом) перечисляет **4 идентификатора** на этой панели:

| ID модели | Группа | Проверено |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` отвечает |
| `deepseek-v4.1-flash` | `default` | в списке |
| `glm-5.3` | `default` | в списке |
| `glm-5.3-flash` | `default` | в списке |

> ⚠️ `/v1/models` отвечает **только на GET** — POST отклоняется с `Invalid URL (POST /v1/models)`.

## 🧪 Как использовать API

Базовый URL **`https://api.sivan.bond/v1`** — OpenAI-совместимый. Обратите внимание на хост: **не** `sivan.bond`.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

Модель может выдавать свои рассуждения в `reasoning_content` рядом с `content`, а вышестоящий сервис иногда повторяет токен (`okokok responseok response…`) — клиент, читающий только `content`, всё равно получает текст.

## 🛠 Быстрый старт

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

Браузер и решатель капчи не задействованы: регистрация на панели — это чистый HTTP плюс почтовый ящик. Ключ записывается в `keys.txt` **только** после того, как `/v1/chat/completions` отвечает 200 с реальным содержимым; `accounts.jsonl` хранит адрес, баланс до и после отметки и результат проверки.

### 🖱️ Лаунчер

Лаунчер запрашивает список прокси (необязательно — без него CLI работает напрямую), сколько аккаунтов создать и модель, используемую для живой проверки, запоминает их в `launcher.config.json` и запускает CLI: **mint**, **verify**, **screen** (проверка выходов против панели), **settings**, **help** и **Connect to 9Router**.

## ✅ Проверено в работе — каждый ключ протестирован

| Проверка | Результат |
| --- | --- |
| `GET /api/status` (на `api.sivan.bond`) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; письмо с кодом (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) приходит в течение нескольких секунд |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` принят |
| `POST /api/user/login` | **200** с `access_token` |
| `POST /api/token/` затем `POST /api/token/<id>/key` | список отвечает ключом **замаскированным**; маршрут раскрытия возвращает открытый текст |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** с реальным содержимым (`deepseek-v4-flash`) |
| неверный хост | веб-хост `/v1/*` отвечает **404** с подсказкой про `api.sivan.bond` |
| инструменты | **работает** — запрос с `tools` возвращает `tool_calls: [get_time]` |

> **Контроль качества.** Каждый ключ здесь прошёл живой `chat-200`, прежде чем был записан в `keys.txt`
> (мёртвые ключи никогда не поставляются). Комбинации роутера следуют тому же правилу на уровне модели: запись
> добавляется **только если модель отвечает `chat-200` и умеет вызывать инструменты** — модель без поддержки инструментов,
> мёртвый ключ или мёртвый провайдер отбрасывается, а не добавляется. Перезапускайте шаг проверки в любое время;
> сбои сообщаются, а не скрываются.

## 🧭 Как это работает

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Шаг | Вызов | Примечания |
| --- | --- | --- |
| почтовый ящик | Emailnator `POST /api/generate-email` (`ids: [3]`), с откатом на temp.tf `GET /api/account` | свежий `@gmail.com` (или плюс-алиас Microsoft), зарегистрированный в его форме без точек; ящик снимается как снимок **до** запроса кода — переиспользованный адрес полон писем других арендаторов, поэтому учитываются только ids *и* метка времени новее этого снимка, а сообщение должно называть панель в отправителе/теме |
| статус | `GET /api/status` | проверка шлюза перед расходованием почтового ящика |
| отправка кода | `GET /api/verification?email=…` | панель отправляет 6-значный код по почте |
| код | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | опрос каждые 5 с в течение 240 с; код читается только из собственного письма панели (привязанного к `验证码`/`code`) |
| регистрация | `POST /api/user/register` | `{username, password, email, verification_code}` |
| вход | `POST /api/user/login` | `access_token`, затем `New-Api-User: <id>` для маршрутов токенов |
| отметка | `POST /api/user/checkin` | баланс перечитывается после, чтобы начисление было записано по каждому аккаунту |
| ключ | `POST /api/token/` → `POST /api/token/<id>/key` | создание отвечает **замаскированным** ключом на этом форке; маршрут раскрытия — единственный путь к открытому тексту |
| шлюз | `POST /v1/chat/completions` | 200 с реальным содержимым = ключ сохранён |

Каждый запрос одного аккаунта проходит через **один закреплённый выход**: форк, привязывающий ожидающий код подтверждения на стороне сервера, отвечает `Verification code is incorrect or has expired`, когда адрес, запросивший код, не совпадает с адресом, который регистрируется.

## ⚙️ Опции CLI

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

| Переменная | По умолчанию | Значение |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | пул выходов, по одному в строке (то же, что `--pool`); отсутствует = напрямую |
| `SIVAN_MODEL` | `deepseek-v4-flash` | модель, используемая для живой проверки |

## 🛰️ За прокси

**Необязательно — рекомендуется при масштабе.** Каждый запрос проходит через один выход из вашего списка, и один выход **закрепляется за аккаунтом**, поэтому отправка кода, регистрация, вход, отметка, раскрытие ключа и шлюз — всё исходит с одного адреса, и панель никогда не видит смешанный след.

- **С пулом:** `--pool proxies.txt` (или `SIVAN_PROXIES`). Мёртвый выход проваливает эту попытку, и следующая попытка берёт следующий выход.
- **Отказ в закрытом состоянии:** явный `--pool`, указывающий в никуда, откатывается к **прямому** соединению с напечатанным предупреждением — никогда не молчаливый смешанный запуск.
- **Без пула:** CLI печатает `running WITHOUT a proxy (direct)` и работает так же.

## 📄 Файлы

| Путь | Содержимое |
| --- | --- |
| `keys.txt` | один проверенный в работе ключ на строку — игнорируется git |
| `accounts.jsonl` | одна JSON-запись на аккаунт: адрес, имя пользователя, почтовый рельс, статус отметки, квота до/после, выход, результат шлюза, ключ — игнорируется git |
| `proxies.txt` | ваши выходы, по одному в строке — игнорируется git |
| `out/` | журналы запусков — игнорируется git |

## 🔌 Подключение к 9Router

[9Router](https://9router.com) — это самостоятельно размещаемый, OpenAI-совместимый роутер:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 Подключение к OmniRoute

[OmniRoute](https://omniroute.online) — это самостоятельно размещаемый, OpenAI-совместимый роутер:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

По умолчанию: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Модели подаются как `sivan/<model id>`.

## 🔑 Что даёт один ключ

| | |
| --- | --- |
| 🏢 Провайдер | **Sivan API** — [sivan.bond](https://sivan.bond) (API на `api.sivan.bond`) |
| 🎁 Что даёт провайдер | 500 000 квоты за регистрацию **плюс ежедневная отметка** (измеренное начисление в первый день: **+1 771 845**) |
| 🔑 Что даёт один ключ | один 48-символьный ключ на собственном аккаунте, отмеченный и проверенный чатом на `deepseek-v4-flash` |
| 🌐 Форма API | OpenAI-совместимый: `POST https://api.sivan.bond/v1/chat/completions` (4 идентификатора моделей) |
| 🔒 Ваши данные | связывается только с `sivan.bond`, `api.sivan.bond`, почтовым бэкендом (`Emailnator`, с откатом на `temp.tf`) и вашим собственным прокси; ключи остаются на вашей машине |

## 🐞 Проблемы

Если Sivan API снова переместит свой API-хост, изменит форму регистрации, письмо с подтверждением или маршруты токенов, инструмент остановится на этом шаге и сообщит отказ дословно (точный статус, сообщение, возвращённое панелью, использованный хост). **[Откройте issue](../../issues)** с командой и точным выводом.

## 📄 Лицензия

MIT © 2026 — см. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>