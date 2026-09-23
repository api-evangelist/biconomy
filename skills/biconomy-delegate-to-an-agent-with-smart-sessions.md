---
name: biconomy-delegate-to-an-agent-with-smart-sessions
description: >-
  Grant an autonomous agent a scoped, time-limited, revocable permission to transact on a user's
  behalf using Biconomy Smart Sessions, and bound the blast radius with policies.
api: Biconomy Smart Sessions
operations: []
generated: '2026-08-24'
method: generated
source: >-
  Generated from the published Smart Sessions documentation at
  https://docs.biconomy.io/agents-automation and its policy pages (/policies/sudo,
  /policies/universal-action, /policies/time-range, /policies/usage-limit), plus the AbstractJS
  session reference at https://docs.biconomy.io/sdk-reference/sessions. Smart Sessions is an
  SDK + on-chain module surface, not a REST operation, so this skill grounds in SDK calls rather
  than operationIds — there is no OpenAPI operation to name.
---

# Delegate execution to an agent with Smart Sessions

Smart Sessions is the mechanism that lets an agent act without holding the user's key and without
re-prompting per transaction. Permissions are enforced on chain by the Nexus session module, not
by your application code — so the policy you set is the real boundary, and getting it wrong is not
recoverable by patching your backend.

## The shape

1. The user (owner) grants a session to a session key the agent controls.
2. The grant carries a set of **policies** that bound what the session key may do.
3. The agent builds session actions against approved contracts and function selectors and
   executes them within those bounds.
4. Any policy check that fails causes the on-chain action to revert.

## Choose policies deliberately

| Policy | What it bounds | Use when |
| --- | --- | --- |
| Sudo | Nothing — full function access | Only for trusted internal automation. Avoid for third-party agents. |
| Universal Action | Parameter-level rules, e.g. a per-trade cap and a cumulative cap | The default for anything touching value. This is where spending limits live. |
| Time Range | The window the session is valid for | Always. An unbounded session is an unbounded liability. |
| Usage Limit | How many times a specific action may execute | Recurring automations — "once per month for a year". |

Stack them. A trading agent should carry Universal Action (per-trade and cumulative caps) *and*
Time Range *and* Usage Limit, not one of the three.

Note the behaviour at exhaustion: when a usage limit is reached, further on-chain actions revert.
The session does not silently degrade — but it also does not renew itself. Enable a fresh session
for the user when that happens.

## Scope to selectors, not to contracts

Approve the exact `functionSignature` you intend the agent to call — `withdraw(uint256,address,address)`
on that vault, not the vault. A contract-level grant hands the agent every function on it.

## Revocation is the reversal path

The owner can revoke a session at any time (`revokeSession` in AbstractJS), and revocation takes
effect on chain immediately. Build this into your product surface, not just your runbook: a user
who cannot find the revoke button does not really have a revocable permission. Wire an anomaly
check that revokes and notifies rather than waiting for a human to notice.

## What revocation does not do

Revoking a session stops future actions. It does not reverse actions already executed — those are
settled on chain and final. Reversibility on Biconomy is entirely pre-commitment: policies,
time ranges, usage limits and cleanups. See `conventions/biconomy-conventions.yml`.

## Test on testnet first

Biconomy pre-deploys Nexus across a long list of testnets (see
`sandbox/biconomy-sandbox.yml`). Verify that your policy actually blocks what you think it blocks
before granting it on mainnet — a policy that is looser than intended is invisible until an agent
finds the gap.
