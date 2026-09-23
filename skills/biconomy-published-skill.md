---
name: Biconomy
description: Use when building Web3 applications that need gas abstraction, cross-chain orchestration, batched transactions, or delegated automation. Reach for Biconomy when you need to eliminate gas friction, execute complex multi-chain workflows with one signature, enable AI agents with scoped permissions, or build gasless experiences for users.
metadata:
    mintlify-proj: biconomy
    version: "1.0"
---

# Biconomy Skill

Biconomy is a full-stack infrastructure platform for building Web3 applications with Web2-level user experience. It provides two primary integration paths: the **AbstractJS SDK** (TypeScript, fine-grained control) and the **Supertransaction API** (REST, rapid development). Both leverage the **Modular Execution Environment (MEE)** for gas abstraction, cross-chain orchestration, and transaction batching.

## Product Summary

Biconomy enables gasless transactions, composable batching, cross-chain execution, and delegated automation through two integration methods:

- **AbstractJS SDK**: TypeScript library for smart account management and custom DeFi flows. Install with `npm install @biconomy/abstractjs viem`. Use when you need fine-grained control, runtime parameter injection, or custom logic.
- **Supertransaction API**: REST API for rapid prototyping without smart contracts. Base URL: `https://api.biconomy.io`. Use for pre-built DeFi integrations, non-TypeScript environments, or simpler use cases.

Both use **MEE (Modular Execution Environment)** which replaces traditional ERC-4337 bundlers and paymasters with a unified interface for gas sponsorship, transaction bundling, and cross-chain orchestration.

**Primary docs**: https://docs.biconomy.io

## When to Use

Reach for Biconomy when:

- **Eliminating gas friction**: Users abandon transactions due to gas fees. Sponsor gas entirely or let users pay in ERC-20 tokens (USDC, USDT, etc.) instead of native tokens.
- **Batching operations**: Multiple contract calls (approve + swap + deposit) need to execute atomically in one transaction with one signature.
- **Cross-chain workflows**: Execute operations across multiple chains (bridge + swap + deposit) with a single user signature.
- **Delegated automation**: Build AI agents, trading bots, or yield optimizers that execute transactions on behalf of users within scoped, time-limited permissions.
- **Complex DeFi flows**: Swap, bridge, deposit, or stake with dynamic values (output of one step feeds into the next).
- **Supporting external wallets**: MetaMask, Rabby, Trust Wallet users need Fusion Mode for MEE orchestration.
- **Embedded wallet integration**: Privy, Dynamic, Turnkey users can use EIP-7702 mode for smart account features on EOAs.

## Quick Reference

### Integration Paths

| Scenario | Use | Key Difference |
|----------|-----|-----------------|
| TypeScript app, custom logic | AbstractJS SDK | Direct smart account control, runtime injection |
| Rapid prototyping, DeFi swaps | Supertransaction API | Pre-built routes, no encoding needed |
| MetaMask/Rabby users | Fusion Mode (AbstractJS) | Temporary Companion Account, trigger + cleanup |
| Privy/Dynamic/Turnkey users | EIP-7702 Mode (both) | Smart account features on EOA via delegation |

### Core Concepts

| Term | Definition |
|------|-----------|
| **MEE** | Modular Execution Environment—replaces bundlers + paymasters. Handles gas sponsorship, batching, cross-chain orchestration. |
| **Nexus** | Biconomy's ERC-7579 smart account. 25% lower gas than alternatives. Deployed lazily on first transaction per chain. |
| **Supertransaction** | Orchestrated blockchain operation combining instructions, runtime values, conditions, and fee configuration. |
| **Smart Sessions** | Scoped, time-limited permissions for agents/bots. User grants once, agent acts within bounds. |
| **Composable Batching** | Multiple operations where output of one feeds into the next (swap output → deposit amount). |
| **Fusion Mode** | Passthrough for external wallets. Uses trigger token + Companion Account to enable MEE features. |

### Essential Commands & Patterns

**AbstractJS Setup**:
```typescript
import { createMeeClient, toMultichainNexusAccount, getMEEVersion, MEEVersion } from "@biconomy/abstractjs";
import { http } from "viem";
import { base, optimism } from "viem/chains";

const account = await toMultichainNexusAccount({
  signer,
  chainConfigurations: [
    { chain: base, transport: http(), version: getMEEVersion(MEEVersion.V2_1_0) },
    { chain: optimism, transport: http(), version: getMEEVersion(MEEVersion.V2_1_0) }
  ]
});

const meeClient = await createMeeClient({ account });
```

**Supertransaction API Quote**:
```bash
curl -X POST https://api.biconomy.io/v1/quote \
  -H "X-API-Key: YOUR_API_KEY" \
  -d '{
    "mode": "smart-account",
    "ownerAddress": "0x...",
    "composeFlows": [{
      "type": "/instructions/intent-simple",
      "data": { "srcChainId": 8453, "dstChainId": 10, "srcToken": "0x...", "dstToken": "0x...", "amount": "100000000" }
    }]
  }'
```

**Gasless Execution**:
```typescript
// Omit feeToken for sponsored (gasless)
const quote = await meeClient.getQuote({
  instructions: [/* your calls */],
  feeToken: { address: "sponsored" }  // or omit entirely
});
```

**Pay in Token**:
```typescript
const quote = await meeClient.getQuote({
  instructions: [/* your calls */],
  feeToken: { address: USDC, chainId: 8453 }  // User pays in USDC
});
```

## Decision Guidance

### When to Use AbstractJS vs Supertransaction API

| Factor | AbstractJS SDK | Supertransaction API |
|--------|---|---|
| **Language** | TypeScript/JavaScript only | Any language (REST) |
| **Control** | Fine-grained (custom calldata, gas limits) | Pre-built (routing, DEX selection automatic) |
| **Setup** | More code, more flexibility | Less code, faster to market |
| **DeFi Integrations** | Manual encoding | 100+ protocols pre-integrated |
| **Runtime Values** | `runtimeERC20BalanceOf()`, constraints | Limited |
| **Best For** | Custom logic, complex flows | Swaps, bridges, standard DeFi |

### When to Use Each Wallet Integration

| Wallet Type | Integration | Mode | Notes |
|---|---|---|---|
| **MetaMask, Rabby, Trust** | AbstractJS | Fusion | Trigger + Companion Account pattern |
| **Privy, Dynamic, Turnkey** | Either | EIP-7702 | Smart account features on EOA |
| **Nexus Smart Account** | Either | smart-account | Native gas abstraction, simplest |
| **Generic EOA** | Supertransaction API | eoa | Requires fundingTokens parameter |

### When to Use Each Gas Payment Option

| Option | Who Pays | Best For | Setup |
|--------|----------|----------|-------|
| **Sponsored** | Developer | Onboarding, promotions, premium features | Configure in dashboard, omit feeToken |
| **Pay in Token** | User (ERC-20) | DeFi users with stablecoins | Set feeToken to USDC/USDT/etc |
| **Cross-Chain Gas** | User (from any chain) | Multi-chain apps, fragmented balances | Execute on chain A, pay from chain B |

## Workflow

### Typical Task: Build a Gasless Swap

1. **Choose your path**: TypeScript + custom logic → AbstractJS. Rapid prototyping → Supertransaction API.

2. **Identify wallet type**: MetaMask/Rabby → Fusion Mode. Privy/Dynamic → EIP-7702. Nexus smart account → smart-account mode.

3. **Get API key**: Visit dashboard.biconomy.io, create project, copy MEE API key.

4. **Initialize client**:
   - AbstractJS: Create multichain account, instantiate MEE client
   - Supertransaction API: No initialization needed, just POST to /v1/quote

5. **Build instructions**:
   - AbstractJS: Use `account.buildComposable()` for each step (approve, swap, deposit)
   - Supertransaction API: Define `composeFlows` array with instruction types

6. **Get quote**: Call `meeClient.getQuote()` or POST `/v1/quote`. Returns execution cost, routing info, and payload to sign.

7. **Validate quote**: Check fee is reasonable, output meets minimum, no errors in response.

8. **Sign payload**: 
   - Smart account v2.2.1+: EIP-712 typed data (human-readable)
   - Smart account v2.1.0 / EIP-7702: Personal message
   - EOA Fusion: Permit or onchain transaction
   - Use provided signing utilities to auto-detect format

9. **Execute**: Call `meeClient.executeQuote()` or POST `/v1/execute` with signed payload.

10. **Track**: Poll `getExecutionStatus()` or wait for webhook. Check `transactionStatus` and per-chain `executionStatus`.

### Typical Task: Enable Smart Sessions for an Agent

1. **Generate agent key**: `const agentKey = generatePrivateKey(); const agentSigner = privateKeyToAccount(agentKey);`

2. **Define permissions**: Use `mcNexus.buildSessionAction()` to specify which contracts, functions, and limits the agent can access.

3. **Add policies**: Attach `timeframe`, `usageLimit`, or `universal` policies to constrain agent behavior.

4. **User enables session**: User calls `getSessionQuote({ mode: "PREPARE", enableSession: {...} })` and executes. Store returned `sessionDetails`.

5. **Agent executes**: Agent calls `getSessionQuote({ mode: "USE", sessionDetails, instructions: [...] })` and executes within bounds.

6. **Verify**: Check execution status. Agent can only act within granted permissions—smart contract enforces limits.

## Common Gotchas

- **Forgetting to deploy account**: Accounts deploy lazily on first transaction per chain. No upfront deployment cost, but first tx is slightly more expensive.

- **Missing chainConfigurations**: If you reference a chain in instructions but didn't include it in `chainConfigurations`, `addressOn()` will fail. Use strict mode: `account.addressOn(chainId, true)` to catch early.

- **Not validating quotes before signing**: Always check `quote.fee` and `quote.returnedData[0].minOutputAmount` before asking user to sign. Prevent surprise fees or slippage.

- **Confusing Fusion trigger with feeToken**: Fusion's `trigger` is the funding source (user's token on source chain). `feeToken` is what pays gas. They can be different tokens.

- **Forgetting cleanup for cross-chain**: Cross-chain flows are not atomic. If destination execution fails after bridging, use `cleanups` to programmatically return funds to user's wallet.

- **Not handling signature format detection**: v2.2.1+ uses EIP-712 typed data, v2.1.0 uses personal message. Use the provided type guard to auto-detect and sign correctly.

- **Hardcoding MEE API key in frontend**: Store API key in backend environment variables. Frontend should call your backend, which calls Biconomy API.

- **Assuming all tokens support ERC20Permit**: EOA mode falls back to onchain approval if token doesn't support Permit. Handle both signature types.

- **Not checking `quoteType` in response**: The API returns `quoteType: "simple"`, `"permit"`, or `"onchain"`. Your signing logic must handle all three.

- **Forgetting to set `fundingTokens` for EOA mode**: EOA mode requires `fundingTokens` array specifying which tokens fund the operation. Smart account mode doesn't need this.

- **Cross-chain gas assumptions**: Each destination chain needs gas. Biconomy handles this automatically if you pay from source chain, but pre-fund if using per-chain gas.

- **Session policy too restrictive**: `universal` policies with many rules can be expensive. Start simple (usage limit only) and add constraints as needed.

## Verification Checklist

Before submitting work:

- [ ] **Quote validation**: Checked fee is reasonable and output meets user's minimum acceptable amount
- [ ] **Signature format**: Detected and handled both EIP-712 (v2.2.1+) and personal message (v2.1.0/7702) formats
- [ ] **Chain configuration**: All chains referenced in instructions are in `chainConfigurations`
- [ ] **Gas payment**: Verified `feeToken` is set correctly (or omitted for sponsored)
- [ ] **Wallet mode**: Confirmed correct mode (smart-account, eoa, eoa-7702) matches user's wallet type
- [ ] **Error handling**: Implemented try-catch for sponsorship denied, insufficient balance, user rejection
- [ ] **Cross-chain cleanup**: Added cleanup transactions for cross-chain flows to handle destination failures
- [ ] **API key security**: API key stored in backend environment variables, not frontend code
- [ ] **Execution tracking**: Implemented polling or webhook to track multi-chain progress
- [ ] **Session policies**: Verified agent permissions are scoped appropriately (not too permissive, not too restrictive)

## Resources

**Comprehensive navigation**: https://docs.biconomy.io/llms.txt

**Critical documentation pages**:
- [AbstractJS SDK Overview](https://docs.biconomy.io/overview/abstractjs) — Setup, batching, cross-chain, composable operations
- [Supertransaction API Overview](https://docs.biconomy.io/overview/supertransaction-api) — Quote, sign, execute flow with all modes
- [Agents & Automation](https://docs.biconomy.io/agents-automation) — Smart Sessions, policies, agent types, implementation guide

---

> For additional documentation and navigation, see: https://docs.biconomy.io/llms.txt