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

**Crie contas da API Sivan em massa com chaves de API geradas automaticamente — inferência compatível com OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Início rápido](#-quick-start) · [🎁 Plano gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-4-of-them) · [🧪 Use a API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Como funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Opções](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por que isto existe

A Sivan API é um fork do **New API** com uma peculiaridade que custa uma tarde inteira se você não a perceber: **seu host de API não é seu host web**. `sivan.bond` serve o painel, `api.sivan.bond` serve tanto as rotas do painel (`/api/*`) quanto a rota OpenAI (`/v1/*`) — apontar um cliente para o host web recebe `404` com a dica `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`. A CLI já embute o host correto desde o início.

O cadastro em si não tem captcha (`turnstile_check: false`) e é protegido apenas por um código de e-mail de 6 dígitos, então a conta é automatizável via HTTP puro. O que torna este painel digno de automação é o **check-in diário**: uma conta nova tem 500.000 de cota, e um check-in no dia da criação mediu **+1.771.845** além disso (`quota_awarded`), então a ferramenta faz o check-in de cada conta antes de guardar a chave.

## 🎁 Plano gratuito — quanto você recebe

| O quê | Valor |
| --- | --- |
| Crédito de cadastro | **500.000 de cota** (`quota_per_unit: 500000` → ≈ **$1,00**) |
| Check-in diário | **habilitado** (`checkin_enabled: true`) — recompensa medida em uma conta nova: **+1.771.845** (`quota_awarded`), elevando o saldo para **≈ 2,27 M** |
| Cartão / telefone | nenhum |
| Unidade de cobrança | **por conta** — a cota fica na conta, e é por isso que esta ferramenta cria uma conta por chave |
| Barreira de e-mail | **obrigatória** — código de 6 dígitos, verificado antes de a conta existir |

Com N chaves você tem N contas, ou seja, **N × (crédito de cadastro + check-in de hoje)**, e cada conta continua acumulando uma recompensa de check-in por dia em que for acessada.

> 💡 O plano gratuito é definido pela Sivan API e pode mudar. Confira os números ao vivo em [sivan.bond](https://sivan.bond) — e deixe a própria verificação da ferramenta decidir:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — todos os 4

`GET /v1/models` (com uma chave) lista **4 ids** neste painel:

| ID do modelo | Grupo | Verificado |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` respondido |
| `deepseek-v4.1-flash` | `default` | listado |
| `glm-5.3` | `default` | listado |
| `glm-5.3-flash` | `default` | listado |

> ⚠️ `/v1/models` responde **apenas a GET** — um POST é recusado com `Invalid URL (POST /v1/models)`.

## 🧪 Como usar a API

URL base **`https://api.sivan.bond/v1`** — compatível com OpenAI. Observe o host: **não** é `sivan.bond`.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

O modelo pode emitir seu raciocínio em `reasoning_content` ao lado de `content`, e o upstream às vezes repete um token (`okokok responseok response…`) — um cliente que lê apenas `content` ainda recebe texto.

## 🛠 Início rápido

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

Nenhum navegador e nenhum solucionador de captcha está envolvido: o cadastro do painel é HTTP puro mais uma caixa de entrada. Uma chave é gravada em `keys.txt` **somente** depois que `/v1/chat/completions` responde 200 com conteúdo real; `accounts.jsonl` guarda o endereço, o saldo antes e depois do check-in e o resultado da verificação.

### 🖱️ O launcher

O launcher pergunta pela lista de proxies (opcional — sem ela a CLI roda direto), quantas contas criar e o modelo usado para a verificação ao vivo, memoriza tudo em `launcher.config.json` e executa a CLI: **mint**, **verify**, **screen** (testa as saídas contra o painel), **settings**, **help** e **Connect to 9Router**.

## ✅ Verificado e funcionando — toda chave é testada

| Verificação | Resultado |
| --- | --- |
| `GET /api/status` (em `api.sivan.bond`) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}`; o e-mail com o código (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) chega em segundos |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` aceito |
| `POST /api/user/login` | **200** com `access_token` |
| `POST /api/token/` e depois `POST /api/token/<id>/key` | a listagem responde a chave **mascarada**; a rota de revelação retorna o texto simples |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** com conteúdo real (`deepseek-v4-flash`) |
| host errado | o host web `/v1/*` responde **404** com a dica `api.sivan.bond` |
| tools | **funciona** — uma requisição com `tools` retorna `tool_calls: [get_time]` |

> **Verificações de qualidade.** Toda chave aqui passou por um `chat-200` ao vivo antes de ser gravada em `keys.txt`
> (chaves mortas nunca são entregues). As combinações do roteador seguem a mesma regra no nível do modelo: uma entrada é
> adicionada **somente se o modelo responder `chat-200` e conseguir chamar ferramentas** — um modelo sem suporte a ferramentas,
> uma chave morta ou um provedor morto é descartado em vez de adicionado. Reexecute a etapa de verificação a qualquer momento;
> as falhas são reportadas, nunca ocultadas.

## 🧭 Como funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Etapa | Chamada | Observações |
| --- | --- | --- |
| caixa de entrada | Emailnator `POST /api/generate-email` (`ids: [3]`), com fallback para temp.tf `GET /api/account` | um `@gmail.com` novo (ou um alias plus da Microsoft) registrado em sua forma sem pontos; a caixa é fotografada **antes** de o código ser solicitado — um endereço reciclado está cheio de e-mails de outros usuários, então apenas ids *e* um timestamp mais recente que essa captura contam, e a mensagem deve nomear o painel no remetente/assunto |
| status | `GET /api/status` | verificação da barreira antes de gastar uma caixa de entrada |
| enviar código | `GET /api/verification?email=…` | o painel envia o código de 6 dígitos |
| código | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | consulta a cada 5 s dentro de uma janela de 240 s; o código é lido apenas do e-mail do próprio painel (ancorado em `验证码`/`code`) |
| registrar | `POST /api/user/register` | `{username, password, email, verification_code}` |
| login | `POST /api/user/login` | `access_token`, depois `New-Api-User: <id>` para as rotas de token |
| check-in | `POST /api/user/checkin` | o saldo é relido depois, para que a recompensa seja registrada por conta |
| chave | `POST /api/token/` → `POST /api/token/<id>/key` | a criação responde uma chave **mascarada** neste fork; a rota de revelação é o único caminho para o texto simples |
| verificação | `POST /v1/chat/completions` | 200 com conteúdo real = a chave é mantida |

Toda requisição de uma conta passa pela **mesma saída fixada**: um fork que vincula o código de verificação pendente no lado do servidor responde `Verification code is incorrect or has expired` quando o endereço que pediu o código não é o endereço que se registra.

## ⚙️ Opções da CLI

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

| Env | Padrão | Significado |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | pool de saídas, uma por linha (igual a `--pool`); ausente = direto |
| `SIVAN_MODEL` | `deepseek-v4-flash` | modelo usado para a verificação ao vivo |

## 🛰️ Atrás de um proxy

**Opcional — recomendado em escala.** Toda requisição passa por uma saída da sua lista, e uma saída é **fixada por conta**, então envio de código, registro, login, check-in, revelação de chave e a verificação saem todos do mesmo endereço e o painel nunca vê um rastro misturado.

- **Com um pool:** `--pool proxies.txt` (ou `SIVAN_PROXIES`). Uma saída morta faz aquela tentativa falhar e a próxima tentativa usa a próxima saída.
- **Fail-closed:** um `--pool` explícito apontando para nada cai para **direto** com um aviso impresso — nunca uma execução mista silenciosa.
- **Sem um pool:** a CLI imprime `running WITHOUT a proxy (direct)` e funciona da mesma forma.

## 📄 Arquivos

| Caminho | Conteúdo |
| --- | --- |
| `keys.txt` | uma chave verificada ao vivo por linha — ignorado pelo git |
| `accounts.jsonl` | um registro JSON por conta: endereço, nome de usuário, mail rail, status do check-in, cota antes/depois, saída, resultado da verificação, chave — ignorado pelo git |
| `proxies.txt` | suas saídas, uma por linha — ignorado pelo git |
| `out/` | logs de execução — ignorado pelo git |

## 🔌 Conectar ao 9Router

[9Router](https://9router.com) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 Conectar ao OmniRoute

[OmniRoute](https://omniroute.online) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

Padrões: `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Os modelos são servidos como `sivan/<model id>`.

## 🔑 O que uma chave lhe dá

| | |
| --- | --- |
| 🏢 Provedor | **Sivan API** — [sivan.bond](https://sivan.bond) (API em `api.sivan.bond`) |
| 🎁 O que o provedor dá | 500.000 de cota de cadastro **mais um check-in diário** (recompensa medida no primeiro dia: **+1.771.845**) |
| 🔑 O que uma chave lhe dá | uma chave de 48 caracteres em sua própria conta, com check-in feito e chat verificado em `deepseek-v4-flash` |
| 🌐 Formato da API | compatível com OpenAI: `POST https://api.sivan.bond/v1/chat/completions` (4 ids de modelo) |
| 🔒 Seus dados | apenas `sivan.bond`, `api.sivan.bond`, o backend de e-mail (`Emailnator`, com fallback para `temp.tf`) e seu próprio proxy são contatados; as chaves ficam na sua máquina |

## 🐞 Problemas

Se a Sivan API mudar novamente seu host de API, alterar o formato do cadastro, o e-mail de verificação ou as rotas de token, a ferramenta para naquela etapa e reporta a recusa literalmente (o status exato, a mensagem retornada pelo painel, o host usado). **[Abra uma issue](../../issues)** com o comando e a saída exata.

## 📄 Licença

MIT © 2026 — veja [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>