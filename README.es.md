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

**Crea cuentas de la API de Sivan en masa con claves API generadas automáticamente — inferencia compatible con OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Inicio rápido](#-quick-start) · [🎁 Nivel gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-4-of-them) · [🧪 Usar la API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Cómo funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Opciones](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por qué existe esto

Sivan API es un fork de **New API** con una peculiaridad que te cuesta una tarde si la pasas por alto: **su host de API no es su host web**. `sivan.bond` sirve el panel, `api.sivan.bond` sirve tanto las rutas del panel (`/api/*`) como la ruta de OpenAI (`/v1/*`) — apuntar un cliente al host web recibe un `404` con la pista `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`. La CLI incorpora el host correcto desde el principio.

El registro en sí no tiene captcha (`turnstile_check: false`) y solo está condicionado por un código de correo de 6 dígitos, por lo que la cuenta es automatizable mediante HTTP simple. Lo que hace que valga la pena automatizar este panel es el **check-in diario**: una cuenta nueva tiene 500.000 de cuota, y un check-in el día de su creación midió **+1.771.845** adicionales (`quota_awarded`), así que la herramienta hace check-in en cada cuenta antes de conservar la clave.

## 🎁 Nivel gratuito — cuánto obtienes

| Qué | Valor |
| --- | --- |
| Crédito de registro | **500.000 de cuota** (`quota_per_unit: 500000` → ≈ **$1.00**) |
| Check-in diario | **habilitado** (`checkin_enabled: true`) — recompensa medida en una cuenta nueva: **+1.771.845** (`quota_awarded`), llevando el saldo a **≈ 2,27 M** |
| Tarjeta / teléfono | ninguno |
| Unidad de facturación | **por cuenta** — la cuota vive en la cuenta, por eso esta herramienta crea una cuenta por clave |
| Verificación por correo | **obligatoria** — código de 6 dígitos, verificado antes de que exista la cuenta |

Con N claves tienes N cuentas, es decir, **N × (crédito de registro + check-in de hoy)**, y cada cuenta sigue acumulando una recompensa de check-in por cada día que se abre.

> 💡 El nivel gratuito lo define Sivan API y puede cambiar. Consulta las cifras en vivo en [sivan.bond](https://sivan.bond) — y deja que la propia verificación de la herramienta decida:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — los 4

`GET /v1/models` (con una clave) lista **4 ids** en este panel:

| ID del modelo | Grupo | Verificado |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` respondido |
| `deepseek-v4.1-flash` | `default` | listado |
| `glm-5.3` | `default` | listado |
| `glm-5.3-flash` | `default` | listado |

> ⚠️ `/v1/models` responde **solo a GET** — un POST se rechaza con `Invalid URL (POST /v1/models)`.

## 🧪 Cómo usar la API

URL base **`https://api.sivan.bond/v1`** — compatible con OpenAI. Fíjate en el host: **no** es `sivan.bond`.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

El modelo puede emitir su razonamiento en `reasoning_content` junto a `content`, y el upstream a veces repite un token (`okokok responseok response…`) — un cliente que solo lee `content` igualmente recibe texto.

## 🛠 Inicio rápido

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

No se usa navegador ni solucionador de captcha: el registro del panel es HTTP puro más una bandeja de entrada. Una clave se escribe en `keys.txt` **solo** después de que `/v1/chat/completions` responde 200 con contenido real; `accounts.jsonl` guarda la dirección, el saldo antes y después del check-in, y el resultado de la verificación.

### 🖱️ El lanzador

El lanzador pide la lista de proxies (opcional — sin ella la CLI se ejecuta directa), cuántas cuentas crear y el modelo usado para la verificación en vivo, los recuerda en `launcher.config.json`, y ejecuta la CLI: **mint**, **verify**, **screen** (probar las salidas contra el panel), **settings**, **help** y **Connect to 9Router**.

## ✅ Verificado y funcionando — cada clave se prueba

| Comprobación | Resultado |
| --- | --- |
| `GET /api/status` (en `api.sivan.bond`) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; el correo con el código (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) llega en segundos |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` aceptado |
| `POST /api/user/login` | **200** con `access_token` |
| `POST /api/token/` y luego `POST /api/token/<id>/key` | la lista responde la clave **enmascarada**; la ruta de revelado devuelve el texto plano |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** con contenido real (`deepseek-v4-flash`) |
| host incorrecto | el host web `/v1/*` responde **404** con la pista de `api.sivan.bond` |
| tools | **funciona** — una petición con `tools` devuelve `tool_calls: [get_time]` |

> **Controles de calidad.** Cada clave aquí pasó un `chat-200` en vivo antes de escribirse en `keys.txt`
> (nunca se entregan claves muertas). Las combinaciones del router siguen la misma regla a nivel de modelo: una entrada se
> añade **solo si el modelo responde `chat-200` y puede llamar a herramientas** — un modelo sin soporte de herramientas,
> una clave muerta o un proveedor muerto se descarta en lugar de añadirse. Vuelve a ejecutar el paso de verificación cuando quieras;
> los fallos se reportan, nunca se ocultan.

## 🧭 Cómo funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Paso | Llamada | Notas |
| --- | --- | --- |
| buzón | Emailnator `POST /api/generate-email` (`ids: [3]`), con respaldo en temp.tf `GET /api/account` | un `@gmail.com` nuevo (o un alias plus de Microsoft) registrado en su forma sin puntos; se toma una instantánea del buzón **antes** de solicitar el código — una dirección reciclada está llena de correo de otros inquilinos, así que solo cuentan los ids *y* una marca de tiempo posterior a esa instantánea, y el mensaje debe nombrar el panel en su remitente/asunto |
| estado | `GET /api/status` | comprobación de acceso antes de gastar un buzón |
| enviar código | `GET /api/verification?email=…` | el panel envía el código de 6 dígitos |
| código | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | sondea cada 5 s dentro de una ventana de 240 s; el código se lee solo del correo propio del panel (anclado a `验证码`/`code`) |
| registro | `POST /api/user/register` | `{username, password, email, verification_code}` |
| inicio de sesión | `POST /api/user/login` | `access_token`, luego `New-Api-User: <id>` para las rutas de tokens |
| check-in | `POST /api/user/checkin` | el saldo se vuelve a leer después para registrar la recompensa por cuenta |
| clave | `POST /api/token/` → `POST /api/token/<id>/key` | la creación responde una clave **enmascarada** en este fork; la ruta de revelado es la única vía al texto plano |
| verificación | `POST /v1/chat/completions` | 200 con contenido real = la clave se conserva |

Cada petición de una cuenta pasa por la **misma salida fijada**: un fork que vincula el código de verificación pendiente en el lado del servidor responde `Verification code is incorrect or has expired` cuando la dirección que pidió el código no es la dirección que se registra.

## ⚙️ Opciones de la CLI

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

| Variable de entorno | Predeterminado | Significado |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | grupo de salidas, una por línea (igual que `--pool`); ausente = directo |
| `SIVAN_MODEL` | `deepseek-v4-flash` | modelo usado para la verificación en vivo |

## 🛰️ Detrás de un proxy

**Opcional — recomendado a escala.** Cada petición pasa por una salida de tu lista, y una salida se **fija por cuenta**, así que el envío del código, el registro, el inicio de sesión, el check-in, el revelado de la clave y la verificación salen todos desde la misma dirección y el panel nunca ve un rastro mixto.

- **Con un grupo:** `--pool proxies.txt` (o `SIVAN_PROXIES`). Una salida muerta hace fallar ese intento y el siguiente intento toma la siguiente salida.
- **Fallo cerrado:** un `--pool` explícito que apunta a nada recurre a **directo** con una advertencia impresa — nunca una ejecución mixta silenciosa.
- **Sin un grupo:** la CLI imprime `running WITHOUT a proxy (direct)` y funciona igual.

## 📄 Archivos

| Ruta | Contenido |
| --- | --- |
| `keys.txt` | una clave verificada en vivo por línea — ignorada por git |
| `accounts.jsonl` | un registro JSON por cuenta: dirección, nombre de usuario, vía de correo, estado del check-in, cuota antes/después, salida, resultado de la verificación, clave — ignorado por git |
| `proxies.txt` | tus salidas, una por línea — ignorado por git |
| `out/` | registros de ejecución — ignorados por git |

## 🔌 Conectar con 9Router

[9Router](https://9router.com) es un router autoalojado y compatible con OpenAI:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 Conectar con OmniRoute

[OmniRoute](https://omniroute.online) es un router autoalojado y compatible con OpenAI:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

Valores predeterminados: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Los modelos se sirven como `sivan/<model id>`.

## 🔑 Qué te da una clave

| | |
| --- | --- |
| 🏢 Proveedor | **Sivan API** — [sivan.bond](https://sivan.bond) (API en `api.sivan.bond`) |
| 🎁 Qué da el proveedor | 500.000 de cuota de registro **más un check-in diario** (recompensa medida el primer día: **+1.771.845**) |
| 🔑 Qué te da una clave | una clave de 48 caracteres en su propia cuenta, con check-in hecho y chat verificado en `deepseek-v4-flash` |
| 🌐 Forma de la API | compatible con OpenAI: `POST https://api.sivan.bond/v1/chat/completions` (4 ids de modelo) |
| 🔒 Tus datos | solo se contacta con `sivan.bond`, `api.sivan.bond`, el backend de correo (`Emailnator`, con respaldo en `temp.tf`) y tu propio proxy; las claves permanecen en tu máquina |

## 🐞 Problemas

Si Sivan API vuelve a mover su host de API, cambia la forma de su registro, su correo de verificación o las rutas de tokens, la herramienta se detiene en ese paso y reporta el rechazo textualmente (el estado exacto, el mensaje que devolvió el panel, el host que usó). **[Abre un issue](../../issues)** con el comando y la salida exacta.

## 📄 Licencia

MIT © 2026 — consulta [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>