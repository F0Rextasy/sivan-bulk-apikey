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

**Sivan-API-Konten in großen Mengen erstellen mit automatisch generierten API-Schlüsseln — OpenAI-kompatible Inferenz + 9Router-Connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Schnellstart](#-quick-start) · [🎁 Kostenlose Stufe](#-free-tier--how-much-you-get) · [🤖 Modelle](#-models--all-4-of-them) · [🧪 Die API verwenden](#-how-to-use-the-api) · [✅ Verifiziert](#-verified-working--every-key-is-tested) · [🧭 So funktioniert es](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Optionen](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Probleme](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Warum es das gibt

Sivan API ist ein **New-API**-Fork mit einer Eigenheit, die einen ganzen Nachmittag kostet, wenn man sie übersieht: **Der API-Host ist nicht der Web-Host**. `sivan.bond` liefert das Dashboard aus, `api.sivan.bond` bedient sowohl die Panel-Routen (`/api/*`) als auch die OpenAI-Route (`/v1/*`) — richtet man einen Client auf den Web-Host, antwortet dieser mit `404` und dem Hinweis `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`. Die CLI baut den richtigen Host von Anfang an ein.

Die Anmeldung selbst ist captcha-frei (`turnstile_check: false`) und nur durch einen 6-stelligen Mail-Code geschützt, sodass das Konto über reines HTTP automatisierbar ist. Was dieses Panel automatisierungswürdig macht, ist der **tägliche Check-in**: Ein frisches Konto hält 500.000 Quota, und ein Check-in am Tag der Erstellung ergab gemessen **+1.771.845** obendrauf (`quota_awarded`), weshalb das Tool jedes Konto eincheckt, bevor es den Schlüssel behält.

## 🎁 Kostenlose Stufe — wie viel man bekommt

| Was | Wert |
| --- | --- |
| Anmeldeguthaben | **500.000 Quota** (`quota_per_unit: 500000` → ≈ **$1,00**) |
| Täglicher Check-in | **aktiviert** (`checkin_enabled: true`) — gemessene Prämie auf einem frischen Konto: **+1.771.845** (`quota_awarded`), was das Guthaben auf **≈ 2,27 M** bringt |
| Karte / Telefon | keines von beiden |
| Abrechnungseinheit | **pro Konto** — die Quota liegt auf dem Konto, weshalb dieses Tool ein Konto pro Schlüssel erstellt |
| E-Mail-Hürde | **erforderlich** — 6-stelliger Code, verifiziert, bevor das Konto existiert |

Mit N Schlüsseln hält man N Konten, d. h. **N × (Anmeldeguthaben + heutiger Check-in)**, und jedes Konto sammelt weiterhin eine Check-in-Prämie pro Tag, an dem es geöffnet wird.

> 💡 Die kostenlose Stufe wird von Sivan API festgelegt und kann sich ändern. Prüfe die aktuellen Zahlen auf [sivan.bond](https://sivan.bond) — und lass das Gate des Tools selbst entscheiden:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelle — alle 4 davon

`GET /v1/models` (mit einem Schlüssel) listet **4 IDs** auf diesem Panel auf:

| Modell-ID | Gruppe | Verifiziert |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` beantwortet |
| `deepseek-v4.1-flash` | `default` | gelistet |
| `glm-5.3` | `default` | gelistet |
| `glm-5.3-flash` | `default` | gelistet |

> ⚠️ `/v1/models` antwortet **nur auf GET** — ein POST wird mit `Invalid URL (POST /v1/models)` abgelehnt.

## 🧪 So verwendet man die API

Basis-URL **`https://api.sivan.bond/v1`** — OpenAI-kompatibel. Beachte den Host: **nicht** `sivan.bond`.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

Das Modell kann seine Denkweise in `reasoning_content` neben `content` ausgeben, und das Upstream wiederholt manchmal ein Token (`okokok responseok response…`) — ein Client, der nur `content` liest, bekommt trotzdem Text.

## 🛠 Schnellstart

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

Weder Browser noch Captcha-Solver sind beteiligt: Die Anmeldung des Panels ist reines HTTP plus ein Postfach. Ein Schlüssel wird **nur** dann in `keys.txt` geschrieben, wenn `/v1/chat/completions` mit 200 und echtem Inhalt antwortet; `accounts.jsonl` behält die Adresse, das Guthaben vor und nach dem Check-in und das Gate-Ergebnis.

### 🖱️ Der Launcher

Der Launcher fragt nach der Proxy-Liste (optional — ohne eine läuft die CLI direkt), nach der Anzahl der zu erstellenden Konten und nach dem für das Live-Gate verwendeten Modell, merkt sie sich in `launcher.config.json` und führt die CLI aus: **mint**, **verify**, **screen** (die Exits gegen das Panel testen), **settings**, **help** und **Connect to 9Router**.

## ✅ Verifiziert funktionierend — jeder Schlüssel wird getestet

| Prüfung | Ergebnis |
| --- | --- |
| `GET /api/status` (auf `api.sivan.bond`) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; die Code-Mail (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) kommt innerhalb von Sekunden an |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` akzeptiert |
| `POST /api/user/login` | **200** mit `access_token` |
| `POST /api/token/` dann `POST /api/token/<id>/key` | die Liste antwortet mit dem Schlüssel **maskiert**; die Reveal-Route gibt den Klartext zurück |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** mit echtem Inhalt (`deepseek-v4-flash`) |
| falscher Host | Web-Host `/v1/*` antwortet **404** mit dem `api.sivan.bond`-Hinweis |
| Tools | **funktioniert** — eine `tools`-Anfrage gibt `tool_calls: [get_time]` zurück |

> **Qualitäts-Gates.** Jeder Schlüssel hier hat ein Live-`chat-200` bestanden, bevor er in `keys.txt`
> geschrieben wurde (tote Schlüssel werden nie ausgeliefert). Router-Kombinationen folgen derselben
> Regel auf Modellebene: Ein Eintrag wird **nur hinzugefügt, wenn das Modell `chat-200` antwortet und
> Tools aufrufen kann** — ein Modell ohne Tool-Unterstützung, ein toter Schlüssel oder ein toter
> Anbieter wird verworfen statt hinzugefügt. Führe den Verify-Schritt jederzeit erneut aus;
> Fehler werden gemeldet, nie verborgen.

## 🧭 So funktioniert es

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Schritt | Aufruf | Hinweise |
| --- | --- | --- |
| Postfach | Emailnator `POST /api/generate-email` (`ids: [3]`), mit Rückfall auf temp.tf `GET /api/account` | eine frische `@gmail.com` (oder ein Microsoft-Plus-Alias), registriert in ihrer punktfreien Form; das Postfach wird **vor** der Code-Anforderung als Momentaufnahme erfasst — eine wiederverwendete Adresse ist voll mit der Post anderer Nutzer, daher zählen nur IDs *und* ein Zeitstempel, der neuer als diese Momentaufnahme ist, und die Nachricht muss das Panel in Absender/Betreff nennen |
| Status | `GET /api/status` | Gate-Prüfung, bevor ein Postfach verbraucht wird |
| Code senden | `GET /api/verification?email=…` | das Panel mailt den 6-stelligen Code |
| Code | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | fragt alle 5 s innerhalb eines 240-s-Fensters ab; der Code wird nur aus der Mail des Panels selbst gelesen (`验证码`/`code`-verankert) |
| Registrieren | `POST /api/user/register` | `{username, password, email, verification_code}` |
| Anmelden | `POST /api/user/login` | `access_token`, dann `New-Api-User: <id>` für die Token-Routen |
| Check-in | `POST /api/user/checkin` | das Guthaben wird danach neu gelesen, damit die Prämie pro Konto erfasst wird |
| Schlüssel | `POST /api/token/` → `POST /api/token/<id>/key` | die Erstellung antwortet auf diesem Fork mit einem **maskierten** Schlüssel; die Reveal-Route ist der einzige Weg zum Klartext |
| Gate | `POST /v1/chat/completions` | 200 mit echtem Inhalt = der Schlüssel wird behalten |

Jede Anfrage eines Kontos läuft über **denselben festgelegten Exit**: Ein Fork, der den ausstehenden Verifizierungscode serverseitig bindet, antwortet mit `Verification code is incorrect or has expired`, wenn die Adresse, die den Code angefordert hat, nicht die Adresse ist, die sich registriert.

## ⚙️ CLI-Optionen

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

| Env | Standard | Bedeutung |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | Exit-Pool, einer pro Zeile (wie `--pool`); fehlt = direkt |
| `SIVAN_MODEL` | `deepseek-v4-flash` | für das Live-Gate verwendetes Modell |

## 🛰️ Hinter einem Proxy

**Optional — bei Skalierung empfohlen.** Jede Anfrage läuft über einen Exit aus deiner Liste, und ein Exit wird **pro Konto festgelegt**, sodass Code-Senden, Registrieren, Anmelden, Check-in, Schlüssel-Reveal und das Gate alle von derselben Adresse ausgehen und das Panel nie eine gemischte Spur sieht.

- **Mit einem Pool:** `--pool proxies.txt` (oder `SIVAN_PROXIES`). Ein toter Exit lässt diesen Versuch scheitern, und der nächste Versuch nimmt den nächsten Exit.
- **Fail-closed:** Ein explizites `--pool`, das auf nichts zeigt, fällt mit einer ausgegebenen Warnung auf **direkt** zurück — niemals ein stiller gemischter Lauf.
- **Ohne einen Pool:** Die CLI gibt `running WITHOUT a proxy (direct)` aus und funktioniert genauso.

## 📄 Dateien

| Pfad | Inhalt |
| --- | --- |
| `keys.txt` | ein live-verifizierter Schlüssel pro Zeile — von Git ignoriert |
| `accounts.jsonl` | ein JSON-Datensatz pro Konto: Adresse, Benutzername, Mail-Rail, Check-in-Status, Quota vor/nach, Exit, Gate-Ergebnis, Schlüssel — von Git ignoriert |
| `proxies.txt` | deine Exits, einer pro Zeile — von Git ignoriert |
| `out/` | Lauf-Logs — von Git ignoriert |

## 🔌 Mit 9Router verbinden

[9Router](https://9router.com) ist ein selbst gehosteter, OpenAI-kompatibler Router:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 Mit OmniRoute verbinden

[OmniRoute](https://omniroute.online) ist ein selbst gehosteter, OpenAI-kompatibler Router:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

Standardwerte: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Modelle werden als `sivan/<model id>` bereitgestellt.

## 🔑 Was ein Schlüssel einem bringt

| | |
| --- | --- |
| 🏢 Anbieter | **Sivan API** — [sivan.bond](https://sivan.bond) (API auf `api.sivan.bond`) |
| 🎁 Was der Anbieter gibt | 500.000 Anmelde-Quota **plus ein täglicher Check-in** (gemessene Prämie am ersten Tag: **+1.771.845**) |
| 🔑 Was ein Schlüssel einem bringt | ein 48-stelliger Schlüssel auf seinem eigenen Konto, eingecheckt und chat-verifiziert auf `deepseek-v4-flash` |
| 🌐 API-Form | OpenAI-kompatibel: `POST https://api.sivan.bond/v1/chat/completions` (4 Modell-IDs) |
| 🔒 Deine Daten | nur `sivan.bond`, `api.sivan.bond`, das Mail-Backend (`Emailnator`, mit Rückfall auf `temp.tf`) und dein eigener Proxy werden kontaktiert; Schlüssel bleiben auf deinem Rechner |

## 🐞 Probleme

Wenn Sivan API seinen API-Host erneut verschiebt, die Form seiner Anmeldung, seine Verifizierungs-Mail oder die Token-Routen ändert, stoppt das Tool an diesem Schritt und meldet die Ablehnung wörtlich (den genauen Status, die vom Panel zurückgegebene Nachricht, den verwendeten Host). **[Öffne ein Issue](../../issues)** mit dem Befehl und der genauen Ausgabe.

## 📄 Lizenz

MIT © 2026 — siehe [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>