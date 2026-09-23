---
name: biconomy-upgrade-legacy-account
description: >-
  Find which of an owner's Nexus deployments are on a superseded implementation version and
  produce a signable upgrade quote for exactly those chains.
api: Biconomy Supertransaction API (MEE)
base_url: https://api.biconomy.io
operations:
  - getOrchestrator
  - upgrade
generated: '2026-08-24'
method: generated
source: >-
  Grounded in openapi/biconomy-mee-api-openapi.yml (operationIds getOrchestrator, upgrade — the
  upgrade operation was read verbatim from the published spec at
  https://www.biconomy.io/openapi.json, v0.6.12) and the migration guide at
  https://docs.biconomy.io/upgrade-migrate/index.
---

# Upgrade legacy Biconomy accounts

Check first, then upgrade only what needs it. Calling `upgrade` speculatively wastes a round trip
and returns `NO_UPGRADE_NEEDED`.

## Step 1 — `getOrchestrator` (POST /v1/mee/orchestrator)

Send the `ownerAddress`. The response lists the orchestrator deployments across chains, each
carrying an `isUpgradeNeeded` flag. Filter to the chain IDs where it is true.

If that list is empty, stop. Nothing to do.

## Step 2 — `upgrade` (POST /v1/mee/upgrade)

Send the owner address and the chain IDs you filtered. The response mirrors the quote shape —
`quoteType` (typically `simple` for upgrades), `quote`, `payloadToSign` — plus `upgradeDetails[]`,
where each entry names the `chainId`, `address`, `nexusVersion` (current, e.g. `2.1.0`),
`targetVersion` (e.g. `2.2.1`) and `addressVersion`.

## Step 3 — sign and execute

Sign `payloadToSign` and submit through `execute` exactly as in
`biconomy-compose-and-execute-supertransaction`. The upgrade is a supertransaction like any other.

## Address preservation

The account address is preserved across the upgrade — that is the point of the migration path.
Balances and history stay with the address. Do not derive a new address for the owner.

## Errors on `upgrade` (400)

Published codes, verbatim from https://docs.biconomy.io:

- `NO_UPGRADE_NEEDED` — no deployments on the given chains need upgrading. You skipped step 1.
- `INVALID_CHAIN` — the chain ID is not supported. Check
  https://docs.biconomy.io/contracts-and-audits/supported-chains.
- `BAD_REQUEST` with message "eoa-7702 mode is not supported for upgrades" — use `eoa` or
  `smart-account` mode instead.
