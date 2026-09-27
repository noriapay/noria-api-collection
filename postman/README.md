# Noria API — Postman collection

A Postman mirror of the [Bruno collection](../README.md) in this repository, for teams that already
use Postman. Same requests, same examples, same **Ed25519** request signing — done automatically by
a collection pre-request script.

> We build and maintain the collection in **Bruno** (it lives as plain-text `.bru` files, versioned
> in Git — see the [main README](../README.md) for why). These Postman files are a **generated
> export** kept for convenience; when the two differ, the Bruno collection is the source of truth.

## Importing

1. In Postman, **Import** both files from this folder:
   - `Noria API.postman_collection.json`
   - `development.postman_environment.json`
2. Select the **Noria — development** environment (top-right).
3. Fill in the environment variables (from the dashboard, when you create the project's API key):

| Variable | What it is |
|---|---|
| `baseUrl` | already set to `https://api.development.noriapay.com.br` |
| `projectId` | the project UUID |
| `spaceId` | UUID of the space making the calls |
| `privateKeyPem` | the Ed25519 PEM downloaded when the key is created (a **secret** variable; paste it on one line) |

## How signing works

The collection's **pre-request script** signs every request exactly like the gateway verifies it —
it inlines TweetNaCl (Ed25519) and, before each send, sets:

| Header | Content |
|---|---|
| `Access-Id` | `project/<projectId>/space/<spaceId>` |
| `Access-Time` | ISO-8601 timestamp |
| `Access-Signature` | Ed25519 signature over method + path + query + body hash |
| `X-Request-Id` | a fresh UUID |

Nothing else to do — open a request and **Send**.

## `{{newId}}`

`{{newId}}` in the bodies is replaced by a fresh UUID v4 per send, so `externalId` stays unique and
you can re-send `Create Pix invoice`, `Create boleto`, etc. without a `409`.

**One difference from Bruno:** within a single request, every `{{newId}}` resolves to the **same**
UUID (Postman substitutes a variable with one value), whereas Bruno mints a fresh one per
occurrence. This is harmless — the fields that use it (`externalId`, `returnState`, `metadata`) only
need to be present and unique per request.

## Regenerating

These files are generated from the `.bru` sources. If you change the Bruno collection, regenerate
the Postman export rather than editing the JSON by hand.
