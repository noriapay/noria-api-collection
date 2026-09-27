<div align="center">

# Noria API — Bruno Collection

The official request collection for the **Noria API**, ready to run in [Bruno](https://www.usebruno.com).
Every request is already signed with your project's **Ed25519** key — exactly the way the gateway verifies it.

[![Bruno](https://img.shields.io/badge/Bruno-API%20Client-f4a259?logo=bruno&logoColor=white)](https://www.usebruno.com)
[![Docs](https://img.shields.io/badge/Docs-noriapay.com.br%2Fdevelopers-1a1a1a)](https://noriapay.com.br/developers)
[![Environment](https://img.shields.io/badge/Environment-development-3ddc97)](https://api.development.noriapay.com.br)

</div>

---

## What this is

A set of versioned requests to explore and test the Noria API without having to build the signature
by hand. You fill in your project credentials in the _environment_, open any request and send it —
Bruno signs each call automatically right before it fires.

The request bodies (values, names, tags) are **the same as the official documentation** at
[noriapay.com.br/developers](https://noriapay.com.br/developers#api-reference), so everything looks
familiar from one place to the other.

## Why Bruno, not Postman

Bruno stores each request as a plain-text file (`.bru`) **inside the repository itself**. For a team
that works with Git, that changes everything:

| | **Bruno** | **Postman** |
|---|---|---|
| **Where the collection lives** | `.bru` files in your repo | Postman's cloud (account required) |
| **Versioning** | Real Git: `diff`, `blame`, PRs, review | Its own history, locked to the platform |
| **Offline work** | 100% local, no login | Key features require an account and sync |
| **Where secrets live** | In your local keychain, **never** in the file | Synced to the cloud by default |
| **Open source** | Yes ([MIT](https://github.com/usebruno/bruno)) | Proprietary |
| **Lock-in** | None — it's just text | The collection lives at the vendor |

For us the deciding factor is **versioning**: a change to the collection becomes a reviewable
_commit_, not an invisible edit in someone's workspace. And because `.bru` files are text, any dev —
or client — can read them, diff them and propose changes through the normal PR flow.

### Prefer Postman?

There's a generated Postman mirror in [`postman/`](postman/) — same requests, same examples, same
Ed25519 signing. We build and maintain the collection in Bruno (this is the source of truth) and
export to Postman for convenience; see [`postman/README.md`](postman/README.md).

## Installing Bruno

Download it at **[usebruno.com/downloads](https://www.usebruno.com/downloads)** (Windows, macOS and
Linux) or use a package manager:

```bash
# macOS
brew install --cask bruno

# Windows
winget install bruno.bruno

# Linux (Snap)
snap install bruno
```

Official repository: [github.com/usebruno/bruno](https://github.com/usebruno/bruno).

## Opening the collection

1. Clone this repository:
   ```bash
   git clone https://github.com/noriapay/noria-api-collection.git
   ```
2. In Bruno, choose **Open Collection** and point it to the cloned folder.
3. In the top-right corner, select the **development** environment.

## Configuring the environment

Open the **development** environment and fill in three values. They all come from the **Noria
dashboard**, when you create the project's API key:

| Variable | What it is | Where to get it |
|---|---|---|
| `projectId` | The project UUID | Dashboard → project key |
| `spaceId` | UUID of the space making the calls | Dashboard → space |
| `privateKeyPem` | The Ed25519 PEM downloaded when the key is created | Dashboard → **shown only at creation** |

> **`privateKeyPem`** is a **secret** variable: Bruno keeps its value in your machine's local vault,
> **never** inside the `.bru` files. That's why it isn't versioned and nothing sensitive ends up in
> Git. You can paste the PEM on a single line.

`baseUrl` already points to `https://api.development.noriapay.com.br`.

## How authentication works

Every request is signed with the project's **Ed25519** key. You don't have to do anything: a
_pre-request script_ in the collection builds the canonical signature and injects these headers
before each send:

| Header | Content |
|---|---|
| `Access-Id` | `project/<projectId>/space/<spaceId>` |
| `Access-Time` | ISO-8601 timestamp of the send |
| `Access-Signature` | Ed25519 signature over method + path + query + body hash |

It's exactly the scheme the gateway verifies in production — the collection just reproduces the same
computation.

The collection also adds an `X-Request-Id` (a fresh UUID) for tracing, but it's **optional**: it
isn't part of the signature, and if you don't send one the gateway generates it for you and returns
it on the response.

## `{{newId}}`

In the bodies, `{{newId}}` is replaced by a **fresh UUID v4 on every send**. That's what keeps
`externalId` unique, so you can re-send `Create Pix invoice`, `Create boleto`, etc. as many times as
you like without colliding with an already-created resource (`409`).

## What's in the collection

| Folder | Requests |
|---|---|
| **Account** | Retrieve the account, list and read account logs |
| **Balance and transactions** | Balance, transactions, logs and statement export |
| **Boleto** | Create, list, retrieve and boleto logs |
| **Checkout** | Checkout sessions, profile and seller |
| **Pix** | Create, list, retrieve and Pix invoice logs |
| **Transfer** | External transfers and space transfers (intent + execute) |
| **Webhook** | Webhook CRUD, deliveries, resends and signing key |

## Links

- **API documentation:** [noriapay.com.br/developers](https://noriapay.com.br/developers#api-reference)
- **Webhooks:** [noriapay.com.br/developers#webhooks](https://noriapay.com.br/developers#webhooks)
- **Bruno:** [usebruno.com](https://www.usebruno.com)

## Contributing

Found an outdated example or want to add a request? Open a PR. Since everything is `.bru` text, the
change shows up cleanly in the diff and goes through the normal review.
