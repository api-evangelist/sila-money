# Sila (sila-money)

Sila is an embedded-finance / banking-as-a-service **money API** for fintech developers. A single REST API covers identity verification (KYC/KYB), digital wallets, linked bank accounts, virtual accounts, debit-card payment methods, and money movement over the ACH network via an issue / transfer / redeem model.

**Status (reviewed 2026-07-12): LIVE.** Sila is operating as an independent fintech (no acquisition or shutdown was found in public records). Live probes on the review date confirm `www.silamoney.com`, `api.silamoney.com`, `sandbox.silamoney.com`, and `docs.silamoney.com` all resolve and respond, and the production `/0.2` API returns a signed-request validation error to an unsigned call - i.e. the documented surface is live, not retired. SDKs on [github.com/sila-Money](https://github.com/sila-Money) show 2025-2026 activity.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/sila-money/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/sila-money/refs/heads/main/apis.yml)

## Access model (read this first)

Sila does **not** use a bearer token or simple API key. Every request is **cryptographically signed on the client**:

- Every endpoint is an **HTTP POST** to `https://api.silamoney.com/0.2` (sandbox `https://sandbox.silamoney.com/0.2`) carrying a JSON "message" body.
- The stringified body is hashed with **Keccak-256 (SHA3)** and signed with **ECDSA (secp256k1)** keypairs.
- The **app** keypair signature goes in the `authsignature` header (always). The **user** keypair signature goes in the `usersignature` header (user-scoped calls). **KYB / business** calls add a `businesssignature` header.
- Private keys (hex, 64 chars, no `0x`) never leave the client; Sila stores only derived addresses.

Because standard tools (Postman, generic OpenAPI clients) cannot compute a per-body signature, Sila publishes helper tooling - `sila-postman-signer` (a local signing proxy) and language SDKs (JavaScript, Python, Java, PHP, C#) - at [github.com/sila-Money](https://github.com/sila-Money). Production access is a **contracted** BaaS agreement (per-transaction pricing); the **sandbox is free**.

Confirmed live: `POST https://api.silamoney.com/0.2/check_handle` with no signature header returns HTTP 400 with `{"validation_details": {"header": "This field is required."}}`.

## Tags

- Embedded Finance
- Banking as a Service
- Payments
- Digital Wallet
- ACH
- KYC
- KYB
- Money Transfer
- Fintech
- Banking API
- Virtual Accounts

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

### Sila Identity & KYC/KYB API

Reserve a handle (`check_handle`), register end users and businesses (`register`), and run verification with the classic KYC flow (`request_kyc`, `check_kyc`), advanced KYC / KYB, registration-data management, and document upload (`documents`). KYB uses the `businesssignature` header.

- **Human URL:** [https://docs.silamoney.com/docs/get-started](https://docs.silamoney.com/docs/get-started)
- **Base URL:** `https://api.silamoney.com/0.2`

### Sila Wallets API

Register and manage the ECDSA keypairs (blockchain-style addresses) that identify a user's wallets - `register_wallet`, `get_wallet`, `get_wallets` - which sign user-scoped requests.

- **Human URL:** [https://docs.silamoney.com/docs/get_wallet](https://docs.silamoney.com/docs/get_wallet)
- **Base URL:** `https://api.silamoney.com/0.2`

### Sila Bank Accounts API

Connect and manage external bank accounts as funding sources - `link_account` (directly or via a Plaid token), `get_accounts`, `get_account_balance`, and `check_bank_account_capabilities` to confirm ACH send / receive before money movement.

- **Human URL:** [https://docs.silamoney.com/docs/link_account](https://docs.silamoney.com/docs/link_account)
- **Base URL:** `https://api.silamoney.com/0.2`

### Sila Payments & Transactions API

Move money with the issue / transfer / redeem model. `issue_sila` debits a linked bank account over ACH and credits the wallet balance; `transfer_sila` moves that balance wallet-to-wallet; `redeem_sila` credits a linked bank account over ACH. `get_transactions` returns history and status; `cancel_transaction` reverses eligible pending transactions.

- **Human URL:** [https://docs.silamoney.com/docs/issue_sila](https://docs.silamoney.com/docs/issue_sila)
- **Base URL:** `https://api.silamoney.com/0.2`

### Sila Payment Methods & Cards API

Enumerate and manage payment instruments. `get_payment_methods` lists bank accounts, virtual accounts, and wallets in one call; `link_card` attaches a debit card for supported card flows.

- **Human URL:** [https://docs.silamoney.com/docs/get_payment_methods](https://docs.silamoney.com/docs/get_payment_methods)
- **Base URL:** `https://api.silamoney.com/0.2`

### Sila Virtual Accounts API

The Virtual Accounts product issues each user a dedicated account and routing number, giving a program a real bank-account surface for receiving and settling funds. Virtual accounts appear as a payment-method type in `get_payment_methods`. The dedicated virtual-account management endpoint paths are **not published on the pages reviewed** and are therefore not modeled in the OpenAPI (see `review.yml`).

- **Human URL:** [https://www.silamoney.com/virtual-accounts](https://www.silamoney.com/virtual-accounts)
- **Base URL:** `https://api.silamoney.com/0.2`

## Artifacts

- [OpenAPI](openapi/sila-money-openapi.yml) — confirmed POST endpoint paths; request/response schemas are **modeled** (permissive), not authoritative.
- [Postman Collection](collections/sila-money.postman_collection.json) — signature headers declared empty; populate via `sila-postman-signer`.
- [Open Collection](collections/sila-money.opencollection.json)
- [Authentication](authentication/sila-money-authentication.yml) — ECDSA (secp256k1) request signing.
- [Plans / Pricing](plans/sila-money-plans-pricing.yml) — sandbox free; production contracted (`reconciled: false`).
- [Rate Limits](rate-limits/sila-money-rate-limits.yml) — none published (`reconciled: false`).
- [FinOps](finops/sila-money-finops.yml) — per-transaction cost drivers modeled (`reconciled: false`).
- [Domain Security](security/sila-money-domain-security.yml) — live probes; HSTS, DMARC `reject`, SPF `-all`.
- [Review](review.yml) — WebSocket answer, confirmed-vs-modeled endpoints, status.

## Honesty notes

- **Endpoint paths and the POST method are grounded** in live docs plus live probes on 2026-07-12.
- **OpenAPI request/response schemas are modeled** as permissive objects; real Sila bodies are richer. Consult the docs for exact fields.
- **Pricing, rate limits, and FinOps unit costs are `reconciled: false`** - Sila's production rates are contracted and were not published on the pages reviewed.
- **Virtual-account management endpoint paths and webhook-management paths were not confirmed** and are intentionally left unmodeled.

## Common Properties

- [Website](https://www.silamoney.com/)
- [Documentation](https://docs.silamoney.com/docs/get-started)
- [GitHub Organization](https://github.com/sila-Money)
- [LinkedIn](https://www.linkedin.com/company/silamoney)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
