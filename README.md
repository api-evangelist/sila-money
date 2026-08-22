# Sila (sila-money)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
