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

**Otomatik oluşturulan API anahtarlarıyla toplu Sivan API hesabı oluşturun — OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-4-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

Sivan API, gözden kaçırırsanız size bir öğleden sonraya mal olacak bir tuhaflığa sahip bir **New API** çatalıdır: **API sunucusu, web sunucusu değildir**. `sivan.bond` kontrol panelini sunar, `api.sivan.bond` ise hem panel rotalarını (`/api/*`) hem de OpenAI rotasını (`/v1/*`) sunar — bir istemciyi web sunucusuna yönlendirmek, `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口` ipucuyla birlikte `404` ile yanıtlanır. CLI, doğru sunucuyu baştan sabitler.

Kayıt işleminin kendisi captcha'sızdır (`turnstile_check: false`) ve yalnızca 6 haneli bir e-posta koduyla korunur, bu nedenle hesap düz HTTP üzerinden otomatikleştirilebilir. Bu paneli otomatikleştirmeye değer kılan şey **günlük giriş (check-in)** işlemidir: yeni bir hesap 500.000 kota tutar ve oluşturulduğu gün yapılan bir giriş, bunun üzerine **+1.771.845** ölçülmüştür (`quota_awarded`), bu nedenle araç, anahtarı saklamadan önce her hesapta giriş yapar.

## 🎁 Ücretsiz katman — ne kadar alırsınız

| Ne | Değer |
| --- | --- |
| Kayıt kredisi | **500.000 kota** (`quota_per_unit: 500000` → ≈ **$1.00**) |
| Günlük giriş | **etkin** (`checkin_enabled: true`) — yeni bir hesapta ölçülen ödül: **+1.771.845** (`quota_awarded`), bakiyeyi **≈ 2.27 M**'ye çıkarır |
| Kart / telefon | hiçbiri |
| Faturalama birimi | **hesap başına** — kota hesapta yaşar, bu yüzden bu araç anahtar başına bir hesap oluşturur |
| E-posta kapısı | **gerekli** — 6 haneli kod, hesap oluşturulmadan önce doğrulanır |

N anahtarla N hesap tutarsınız, yani **N × (kayıt kredisi + bugünün girişi)** ve her hesap açıldığı her gün için giriş ödülü toplamaya devam eder.

> 💡 Ücretsiz katman Sivan API tarafından belirlenir ve değişebilir. Güncel sayıları [sivan.bond](https://sivan.bond) adresinden kontrol edin — ve kararı aracın kendi kapısına bırakın:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modeller — 4 tanesinin tamamı

`GET /v1/models` (bir anahtarla) bu panelde **4 id** listeler:

| Model ID | Grup | Doğrulandı |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` yanıtlandı |
| `deepseek-v4.1-flash` | `default` | listelendi |
| `glm-5.3` | `default` | listelendi |
| `glm-5.3-flash` | `default` | listelendi |

> ⚠️ `/v1/models` **yalnızca GET** isteğine yanıt verir — bir POST isteği `Invalid URL (POST /v1/models)` ile reddedilir.

## 🧪 API nasıl kullanılır

Temel URL **`https://api.sivan.bond/v1`** — OpenAI uyumlu. Sunucuya dikkat edin: **`sivan.bond` değil**.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

Model, akıl yürütmesini `content` yanında `reasoning_content` içinde yayabilir ve üst akış bazen bir token'ı tekrarlar (`okokok responseok response…`) — yalnızca `content` okuyan bir istemci yine de metni alır.

## 🛠 Hızlı başlangıç

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

Hiçbir tarayıcı ve captcha çözücü devreye girmez: panelin kaydı saf HTTP artı bir gelen kutusudur. Bir anahtar `keys.txt` dosyasına **yalnızca** `/v1/chat/completions` gerçek içerikle 200 yanıt verdikten sonra yazılır; `accounts.jsonl` adresi, girişten önceki ve sonraki bakiyeyi ve kapı sonucunu saklar.

### 🖱️ Başlatıcı

Başlatıcı, proxy listesini (isteğe bağlı — olmadan CLI doğrudan çalışır), kaç hesap oluşturulacağını ve canlı kapı için kullanılan modeli sorar, bunları `launcher.config.json` içinde hatırlar ve CLI'yi çalıştırır: **mint**, **verify**, **screen** (çıkışları panele karşı test eder), **settings**, **help** ve **Connect to 9Router**.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

| Kontrol | Sonuç |
| --- | --- |
| `GET /api/status` (`api.sivan.bond` üzerinde) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; kod postası (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) saniyeler içinde gelir |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` kabul edildi |
| `POST /api/user/login` | **200**, `access_token` ile |
| `POST /api/token/` ardından `POST /api/token/<id>/key` | liste anahtarı **maskelenmiş** olarak yanıtlar; açığa çıkarma rotası düz metni döndürür |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200**, gerçek içerikle (`deepseek-v4-flash`) |
| yanlış sunucu | web sunucusu `/v1/*` **404** yanıtlar, `api.sivan.bond` ipucuyla |
| araçlar | **çalışır** — bir `tools` isteği `tool_calls: [get_time]` döndürür |

> **Kalite kapıları.** Buradaki her anahtar, `keys.txt` dosyasına yazılmadan önce canlı bir `chat-200` testini geçti
> (ölü anahtarlar asla gönderilmez). Yönlendirici kombinasyonları model düzeyinde aynı kuralı izler: bir giriş
> **yalnızca model `chat-200` yanıtlıyorsa ve araçları çağırabiliyorsa** eklenir — araç desteği olmayan bir model,
> ölü bir anahtar veya ölü bir sağlayıcı eklenmek yerine çıkarılır. Doğrulama adımını istediğiniz zaman yeniden çalıştırın;
> hatalar bildirilir, asla gizlenmez.

## 🧭 Nasıl çalışır

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| posta kutusu | Emailnator `POST /api/generate-email` (`ids: [3]`), temp.tf `GET /api/account`'a geri düşer | nokta içermeyen biçiminde kaydedilmiş yeni bir `@gmail.com` (veya bir Microsoft artı takma adı); kutu, kod istenmeden **önce** anlık görüntülenir — geri dönüştürülmüş bir adres diğer kiracıların postalarıyla doludur, bu nedenle yalnızca o anlık görüntüden daha yeni olan id'ler *ve* bir zaman damgası sayılır ve mesaj göndereninde/konusunda paneli adlandırmalıdır |
| durum | `GET /api/status` | posta kutusu harcamadan önce kapı kontrolü |
| kod gönder | `GET /api/verification?email=…` | panel 6 haneli kodu postalar |
| kod | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | 240 saniyelik bir pencere içinde her 5 saniyede bir yoklar; kod yalnızca panelin kendi postasından okunur (`验证码`/`code` çapalı) |
| kayıt | `POST /api/user/register` | `{username, password, email, verification_code}` |
| giriş | `POST /api/user/login` | `access_token`, ardından token rotaları için `New-Api-User: <id>` |
| giriş (check-in) | `POST /api/user/checkin` | ödülün hesap başına kaydedilmesi için bakiye sonrasında yeniden okunur |
| anahtar | `POST /api/token/` → `POST /api/token/<id>/key` | oluşturma bu çatalda **maskelenmiş** bir anahtar yanıtlar; açığa çıkarma rotası düz metne giden tek yoldur |
| kapı | `POST /v1/chat/completions` | gerçek içerikle 200 = anahtar saklanır |

Bir hesabın her isteği **aynı sabitlenmiş çıkıştan** geçer: bekleyen doğrulama kodunu sunucu tarafında bağlayan bir çatal, kodu isteyen adres kaydolan adres olmadığında `Verification code is incorrect or has expired` yanıtını verir.

## ⚙️ CLI seçenekleri

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

| Ortam değişkeni | Varsayılan | Anlamı |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | çıkış havuzu, satır başına bir tane (`--pool` ile aynı); yok = doğrudan |
| `SIVAN_MODEL` | `deepseek-v4-flash` | canlı kapı için kullanılan model |

## 🛰️ Proxy arkasında

**İsteğe bağlı — ölçekte önerilir.** Her istek listenizden bir çıkıştan geçer ve bir çıkış **hesap başına sabitlenir**, böylece kod gönderme, kayıt, giriş, check-in, anahtar açığa çıkarma ve kapı hepsi aynı adresten çıkar ve panel asla karışık bir iz görmez.

- **Bir havuzla:** `--pool proxies.txt` (veya `SIVAN_PROXIES`). Ölü bir çıkış o denemeyi başarısız kılar ve sonraki deneme bir sonraki çıkışı alır.
- **Fail-closed:** hiçbir şeyi işaret etmeyen açık bir `--pool`, yazdırılan bir uyarıyla **doğrudan** moda geri düşer — asla sessizce karışık bir çalıştırma olmaz.
- **Havuz olmadan:** CLI `running WITHOUT a proxy (direct)` yazdırır ve aynı şekilde çalışır.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | satır başına canlı doğrulanmış bir anahtar — git tarafından yok sayılır |
| `accounts.jsonl` | hesap başına bir JSON kaydı: adres, kullanıcı adı, posta rayı, check-in durumu, önceki/sonraki kota, çıkış, kapı sonucu, anahtar — git tarafından yok sayılır |
| `proxies.txt` | çıkışlarınız, satır başına bir tane — git tarafından yok sayılır |
| `out/` | çalıştırma günlükleri — git tarafından yok sayılır |

## 🔌 9Router'a bağlan

[9Router](https://9router.com) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 OmniRoute'a bağlan

[OmniRoute](https://omniroute.online) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

Varsayılanlar: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Modeller `sivan/<model id>` olarak sunulur.

## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **Sivan API** — [sivan.bond](https://sivan.bond) (API `api.sivan.bond` üzerinde) |
| 🎁 Sağlayıcının verdikleri | 500.000 kayıt kotası **artı günlük bir check-in** (ilk gün ölçülen ödül: **+1.771.845**) |
| 🔑 Bir anahtar size ne kazandırır | kendi hesabında 48 karakterlik bir anahtar, check-in yapılmış ve `deepseek-v4-flash` üzerinde sohbet doğrulaması yapılmış |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://api.sivan.bond/v1/chat/completions` (4 model id) |
| 🔒 Verileriniz | yalnızca `sivan.bond`, `api.sivan.bond`, posta arka ucu (`Emailnator`, `temp.tf`'ye geri düşer) ve kendi proxy'nizle iletişim kurulur; anahtarlar makinenizde kalır |

## 🐞 Sorunlar

Sivan API, API sunucusunu tekrar taşırsa, kayıt biçimini, doğrulama postasını veya token rotalarını değiştirirse, araç o adımda durur ve reddi kelimesi kelimesine bildirir (tam durum, panelin döndürdüğü mesaj, kullandığı sunucu). Komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey katkıda bulunanlar

</div>