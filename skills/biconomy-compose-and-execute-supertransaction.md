---
name: biconomy-compose-and-execute-supertransaction
description: >-
  Compose a multi-step, optionally cross-chain execution plan on Biconomy MEE, have the owner sign
  the returned payload, and submit it for execution. This is the core Biconomy flow — every other
  Biconomy skill is a variation on it.
api: Biconomy Supertransaction API (MEE)
base_url: https://api.biconomy.io
operations:
  - quote
  - execute
generated: '2026-08-24'
method: generated
source: >-
  Grounded in openapi/biconomy-root-api-openapi.yml (operationIds quote, execute; request and
  response properties read from the spec) and the published guides at
  https://docs.biconomy.io/overview/supertransaction-api/get-quote,
  /sign-payload and /execute.
---

# Compose and execute a Biconomy supertransaction

Two calls, with a signature in between. Never skip the signature step and never treat a `200` from
`execute` as completion.

## Auth

Every request to `https://api.biconomy.io` carries the project-scoped API key in the `X-API-Key`
header. Keys are issued per project from `https://dashboard.biconomy.io`. There is no OAuth flow
and no refresh — the key is static, so treat it as a server-side secret and never ship it to a
browser.

## Step 1 — `quote` (POST /v1/quote)

Required body fields, from the spec: `mode`, `ownerAddress`, `composeFlows`.

- `mode` is one of `smart-account`, `eoa`, `eoa-7702`. Pick it from the wallet you are working
  with, not from preference: managed accounts use `smart-account`; MetaMask/Rabby-style external
  wallets use `eoa` and additionally require `fundingTokens` plus a withdrawal instruction in
  `composeFlows`; embedded wallets (Privy, Dynamic, Turnkey) use `eoa-7702`.
- `composeFlows` is the array of instructions. Each entry names an instruction type —
  `/instructions/intent-simple` for a routed swap, `/instructions/build` for a custom contract
  call, `/instructions/build-raw` for pre-encoded calldata, `/instructions/intent` for weighted
  rebalancing, `/instructions/intent-vault` for a DeFi zap.
- Optional fields the spec declares and you will usually want: `feeToken` (omit it entirely to
  have the app sponsor gas; set it to an ERC-20 to have the user pay gas in that token),
  `cleanUps` (see below), `upperBoundTimestamp` to expire the plan, `simulate` and
  `simulationOverrides` to rehearse without committing, and `gasLimit`.

The `200` response carries `ownerAddress`, `fee`, `quoteType`, `quote`, `payloadToSign`,
`instructions` and `returnedData`. Keep the whole object — step 3 sends it back.

## Step 2 — sign

Sign every entry in `payloadToSign` with the owner key. The signing format differs by `mode`; the
authoritative reference is https://docs.biconomy.io/overview/supertransaction-api/sign-payload.
Attach each signature to its payload entry as `signature`.

Quotes expire in roughly 30 seconds. If signing involves a human confirming in a wallet UI, assume
you will sometimes miss the window and re-quote rather than retrying `execute` with a stale quote.

## Step 3 — `execute` (POST /v1/execute)

The body is the quote you got back, with signatures attached. All five fields are required by the
spec: `ownerAddress`, `fee`, `quoteType`, `quote`, `payloadToSign`.

The `200` response is `{ success, supertxHash, error }`. **`success: true` means accepted, not
completed.** Poll for the real outcome:

```
GET https://network.biconomy.io/v1/explorer/{supertxHash}
Authorization: Bearer <your API key>
```

Returns a terminal status (`SUCCESS`, `FAILED`). The human-readable view is
`https://meescan.biconomy.io/details/{supertxHash}`.

## Cleanups — do not omit these on cross-chain flows

`cleanUps` are transfer instructions that run last and return whatever tokens remain in the
intermediate account to the user. Same-chain execution is atomic and needs none. Cross-chain
execution is sequential, so a later step can fail after an earlier one has already moved funds —
without a cleanup those funds sit stranded. Add one cleanup per token that could be left behind.

## Errors

The error envelope is `{ code, message, errors[] }` — it is not RFC 9457 problem+json, so do not
look for `type`/`title`/`detail`. See `errors/biconomy-problem-types.yml`.

- `400` — malformed request or an unsupported chain/mode. Fix and re-send; retrying unchanged
  will fail identically.
- `412` — **only on `quote`, and only in `eoa-7702` mode.** Not a failure: the owner's EOA has not
  yet delegated on that chain. The response adds an `authorizations` field; have the owner sign
  it, pass it back as `authorizations` on the next `quote`, and continue. This happens once per
  chain — subsequent quotes on that chain will not return 412.
- `500` — server-side. Safe to retry the `quote`. Do **not** blind-retry `execute`: there is no
  idempotency key on this API (see `conventions/biconomy-conventions.yml`), so a retry that
  actually landed the first time can double-execute. Poll the explorer for the `supertxHash`
  first, and only re-quote if nothing landed.

## Irreversibility

Once `execute` is accepted and settles on chain, there is no cancel, void or refund operation.
The only recovery mechanisms are the ones you configure *before* signing: `cleanUps`,
`upperBoundTimestamp` and `simulate`. Rehearse with `simulate` on anything material.
