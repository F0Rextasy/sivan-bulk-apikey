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

**Créez en masse des comptes API Sivan avec des clés API générées automatiquement — inférence compatible OpenAI + connecteur 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-4%20served-412991?style=for-the-badge)](https://sivan.bond/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Démarrage rapide](#-quick-start) · [🎁 Offre gratuite](#-free-tier--how-much-you-get) · [🤖 Modèles](#-models--all-4-of-them) · [🧪 Utiliser l'API](#-how-to-use-the-api) · [✅ Vérifié](#-verified-working--every-key-is-tested) · [🧭 Comment ça marche](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problèmes](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Pourquoi ce projet existe

Sivan API est un fork de **New API** avec une particularité qui coûte un après-midi si vous la manquez : **son hôte API n'est pas son hôte web**. `sivan.bond` sert le tableau de bord, `api.sivan.bond` sert à la fois les routes du panneau (`/api/*`) et la route OpenAI (`/v1/*`) — pointer un client vers l'hôte web renvoie `404` avec l'indice `OpenAI-compatible API 请使用 https://api.sivan.bond/v1/ 下的接口`. Le CLI intègre le bon hôte dès le départ.

L'inscription elle-même est sans captcha (`turnstile_check: false`) et protégée uniquement par un code mail à 6 chiffres, donc le compte est automatisable en HTTP simple. Ce qui rend ce panneau digne d'être automatisé, c'est le **check-in quotidien** : un nouveau compte détient 500 000 de quota, et un check-in le jour de la création a mesuré **+1 771 845** en plus (`quota_awarded`), donc l'outil fait le check-in de chaque compte avant de conserver la clé.

## 🎁 Offre gratuite — ce que vous obtenez

| Quoi | Valeur |
| --- | --- |
| Crédit d'inscription | **500 000 de quota** (`quota_per_unit: 500000` → ≈ **1,00 $**) |
| Check-in quotidien | **activé** (`checkin_enabled: true`) — récompense mesurée sur un nouveau compte : **+1 771 845** (`quota_awarded`), portant le solde à **≈ 2,27 M** |
| Carte / téléphone | aucun |
| Unité de facturation | **par compte** — le quota vit sur le compte, c'est pourquoi cet outil crée un compte par clé |
| Barrière e-mail | **requise** — code à 6 chiffres, vérifié avant que le compte n'existe |

Avec N clés, vous détenez N comptes, soit **N × (crédit d'inscription + check-in du jour)**, et chaque compte continue de collecter une récompense de check-in par jour où il est ouvert.

> 💡 L'offre gratuite est définie par Sivan API et peut changer. Consultez les chiffres en direct sur [sivan.bond](https://sivan.bond) — et laissez la propre barrière de l'outil décider :

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modèles — les 4

`GET /v1/models` (avec une clé) liste **4 ids** sur ce panneau :

| ID du modèle | Groupe | Vérifié |
| --- | --- | --- |
| `deepseek-v4-flash` | `default` | **chat-200**, `tool_calls` répondu |
| `deepseek-v4.1-flash` | `default` | listé |
| `glm-5.3` | `default` | listé |
| `glm-5.3-flash` | `default` | listé |

> ⚠️ `/v1/models` ne répond **qu'à GET** — un POST est refusé avec `Invalid URL (POST /v1/models)`.

## 🧪 Comment utiliser l'API

URL de base **`https://api.sivan.bond/v1`** — compatible OpenAI. Notez l'hôte : **pas** `sivan.bond`.

```bash
curl https://api.sivan.bond/v1/chat/completions \
  -H "Authorization: Bearer <48-char key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"Say OK"}],"max_tokens":64}'
```

Le modèle peut émettre son raisonnement dans `reasoning_content` à côté de `content`, et l'amont répète parfois un token (`okokok responseok response…`) — un client qui ne lit que `content` obtient quand même du texte.

## 🛠 Démarrage rapide

```bash
cd sivan-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — every request then leaves from one of these exits
node src/launcher.js                     # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 10 --pool proxies.txt
```

Aucun navigateur et aucun solveur de captcha n'est impliqué : l'inscription du panneau est du pur HTTP plus une boîte de réception. Une clé n'est écrite dans `keys.txt` **qu'après** que `/v1/chat/completions` ait répondu 200 avec du contenu réel ; `accounts.jsonl` conserve l'adresse, le solde avant et après le check-in, et le résultat de la barrière.

### 🖱️ Le lanceur

Le lanceur demande la liste de proxies (facultative — sans elle, le CLI s'exécute en direct), combien de comptes créer et le modèle utilisé pour la barrière en direct, les mémorise dans `launcher.config.json`, et exécute le CLI : **mint**, **verify**, **screen** (tester les sorties contre le panneau), **settings**, **help** et **Connect to 9Router**.

## ✅ Vérifié fonctionnel — chaque clé est testée

| Vérification | Résultat |
| --- | --- |
| `GET /api/status` (sur `api.sivan.bond`) | **200** — `register_enabled: true`, `email_verification: true`, `turnstile_check: false`, `checkin_enabled: true`, `system_name: Sivan API` |
| `GET /api/verification?email=…` | **200** `{"success":true}` ; le mail du code (`Sivan API邮箱验证邮件`, `no-reply@mail.sivan.bond`) arrive en quelques secondes |
| `POST /api/user/register` | **200** `{"success":true}`, `verification_code` accepté |
| `POST /api/user/login` | **200** avec `access_token` |
| `POST /api/token/` puis `POST /api/token/<id>/key` | la liste renvoie la clé **masquée** ; la route de révélation renvoie le texte en clair |
| `POST /api/user/checkin` | **200** `{"message":"Check-in successful","quota_awarded":1771845}` |
| `POST /v1/chat/completions` | **200** avec du contenu réel (`deepseek-v4-flash`) |
| mauvais hôte | l'hôte web `/v1/*` répond **404** avec l'indice `api.sivan.bond` |
| tools | **fonctionne** — une requête `tools` renvoie `tool_calls: [get_time]` |

> **Barrières de qualité.** Chaque clé ici a passé un `chat-200` en direct avant d'être écrite dans `keys.txt`
> (les clés mortes ne sont jamais livrées). Les combos de routeur suivent la même règle au niveau du modèle : une entrée n'est
> ajoutée **que si le modèle répond `chat-200` et peut appeler des outils** — un modèle sans support des outils,
> une clé morte ou un fournisseur mort est écarté au lieu d'être ajouté. Relancez l'étape de vérification à tout moment ;
> les échecs sont signalés, jamais cachés.

## 🧭 Comment ça marche

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Sivan API page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.sivan.bond/v1/chat/completions → real content = the key is live
```

| Étape | Appel | Remarques |
| --- | --- | --- |
| boîte mail | Emailnator `POST /api/generate-email` (`ids: [3]`), avec repli sur temp.tf `GET /api/account` | un nouveau `@gmail.com` (ou un alias plus de Microsoft) enregistré sous sa forme sans point ; la boîte est capturée **avant** que le code ne soit demandé — une adresse recyclée est pleine de mails d'autres locataires, donc seuls les ids *et* un horodatage plus récent que cette capture comptent, et le message doit nommer le panneau dans son expéditeur/objet |
| statut | `GET /api/status` | vérification de la barrière avant de dépenser une boîte mail |
| envoi du code | `GET /api/verification?email=…` | le panneau envoie le code à 6 chiffres par mail |
| code | Emailnator `POST /api/message-list` → `POST /api/message/<id>` | interroge toutes les 5 s dans une fenêtre de 240 s ; le code n'est lu que dans le mail du panneau lui-même (ancré sur `验证码`/`code`) |
| inscription | `POST /api/user/register` | `{username, password, email, verification_code}` |
| connexion | `POST /api/user/login` | `access_token`, puis `New-Api-User: <id>` pour les routes de token |
| check-in | `POST /api/user/checkin` | le solde est relu ensuite afin que la récompense soit enregistrée par compte |
| clé | `POST /api/token/` → `POST /api/token/<id>/key` | la création renvoie une clé **masquée** sur ce fork ; la route de révélation est le seul moyen d'obtenir le texte en clair |
| barrière | `POST /v1/chat/completions` | 200 avec du contenu réel = la clé est conservée |

Chaque requête d'un compte passe par la **même sortie épinglée** : un fork qui lie le code de vérification en attente côté serveur répond `Verification code is incorrect or has expired` lorsque l'adresse qui a demandé le code n'est pas celle qui s'inscrit.

## ⚙️ Options du CLI

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

| Env | Défaut | Signification |
| --- | --- | --- |
| `SIVAN_PROXIES` | — | pool de sorties, une par ligne (identique à `--pool`) ; absent = direct |
| `SIVAN_MODEL` | `deepseek-v4-flash` | modèle utilisé pour la barrière en direct |

## 🛰️ Derrière un proxy

**Facultatif — recommandé à grande échelle.** Chaque requête passe par une sortie de votre liste, et une sortie est **épinglée par compte**, donc l'envoi du code, l'inscription, la connexion, le check-in, la révélation de clé et la barrière partent tous de la même adresse et le panneau ne voit jamais une trace mixte.

- **Avec un pool :** `--pool proxies.txt` (ou `SIVAN_PROXIES`). Une sortie morte fait échouer cette tentative et l'essai suivant prend la sortie suivante.
- **Échec fermé :** un `--pool` explicite pointant vers rien retombe en **direct** avec un avertissement affiché — jamais une exécution mixte silencieuse.
- **Sans pool :** le CLI affiche `running WITHOUT a proxy (direct)` et fonctionne de la même manière.

## 📄 Fichiers

| Chemin | Contenu |
| --- | --- |
| `keys.txt` | une clé vérifiée en direct par ligne — ignoré par git |
| `accounts.jsonl` | un enregistrement JSON par compte : adresse, nom d'utilisateur, rail mail, statut du check-in, quota avant/après, sortie, résultat de la barrière, clé — ignoré par git |
| `proxies.txt` | vos sorties, une par ligne — ignoré par git |
| `out/` | journaux d'exécution — ignorés par git |

## 🔌 Connexion à 9Router

[9Router](https://9router.com) est un routeur auto-hébergé, compatible OpenAI :

```bash
node src/cli.js --connect-9router --9router-base http://localhost:20128 --9router-password 123456

```

## 🔌 Connexion à OmniRoute

[OmniRoute](https://omniroute.online) est un routeur auto-hébergé, compatible OpenAI :

```bash
node src/cli.js --connect-omniroute --omniroute-base http://localhost:20128 --omniroute-password 123456

# or the standalone connector shipped next to the CLI:
node src/connect-9router.js --api-key <48-char key>
```

Valeurs par défaut : `--9router-prefix sivan`, `--9router-name Sivan API`, `--base-url https://api.sivan.bond/v1`, `--model deepseek-v4-flash`. Les modèles sont servis sous `sivan/<model id>`.

## 🔑 Ce qu'une clé vous apporte

| | |
| --- | --- |
| 🏢 Fournisseur | **Sivan API** — [sivan.bond](https://sivan.bond) (API sur `api.sivan.bond`) |
| 🎁 Ce que le fournisseur donne | 500 000 de quota d'inscription **plus un check-in quotidien** (récompense mesurée le premier jour : **+1 771 845**) |
| 🔑 Ce qu'une clé vous apporte | une clé de 48 caractères sur son propre compte, check-in effectué et chat-vérifié sur `deepseek-v4-flash` |
| 🌐 Forme de l'API | compatible OpenAI : `POST https://api.sivan.bond/v1/chat/completions` (4 ids de modèles) |
| 🔒 Vos données | seuls `sivan.bond`, `api.sivan.bond`, le backend mail (`Emailnator`, avec repli sur `temp.tf`) et votre propre proxy sont contactés ; les clés restent sur votre machine |

## 🐞 Problèmes

Si Sivan API déplace à nouveau son hôte API, modifie la forme de son inscription, son mail de vérification ou les routes de token, l'outil s'arrête à cette étape et rapporte le refus mot pour mot (le statut exact, le message renvoyé par le panneau, l'hôte utilisé). **[Ouvrez un ticket](../../issues)** avec la commande et la sortie exacte.

## 📄 Licence

MIT © 2026 — voir [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 sivan-bulk-apikey contributors

</div>