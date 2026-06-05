# Biconomy (biconomy)

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/biconomy/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/biconomy/refs/heads/main/apis.yml)

## Scope

- **Access:** 3rd-Party

## Tags

- Account Abstraction
- Blockchain
- Bundler
- Cross-Chain
- DeFi
- ERC-4337
- ERC-7579
- ERC-7702
- Ethereum
- Gas Abstraction
- Gasless
- MEE
- Paymaster
- Smart Accounts
- Smart Sessions
- Wallets
- Web3

## APIs

### Biconomy Supertransaction API

The Biconomy Supertransaction API exposes the Modular Execution Environment (MEE) over a small REST surface — POST /v1/quote composes a multichain execution plan and returns the signable payload(s) and fee, and POST /v1/execute submits the signed supertransaction for cross-chain settlement. Three account modes are supported - smart-account (Nexus / ERC-4337), eoa (MetaMask, Rabby, Trust funded by an explicit fundingToken), and eoa-7702 (EIP-7702 set-code delegation for Privy / Dynamic / Turnkey embedded wallets). Composable instruction types include intent-simple, intent, intent-vault (200+ DeFi vaults), build, build-raw, and build-ccip.

- **Human URL:** [https://docs.biconomy.io/overview/supertransaction-api](https://docs.biconomy.io/overview/supertransaction-api)
- **Base URL:** `https://api.biconomy.io`

#### Tags

- Account Abstraction
- ERC-4337
- ERC-7702
- MEE
- Quotes
- Cross-Chain
- Gasless

#### Properties

- [Documentation](https://docs.biconomy.io/overview/supertransaction-api)
- [Documentation](https://docs.biconomy.io/overview/supertransaction-api/get-quote)
- [Documentation](https://docs.biconomy.io/overview/supertransaction-api/quote-eoa)
- [Documentation](https://docs.biconomy.io/overview/supertransaction-api/quote-smart-account)
- [Documentation](https://docs.biconomy.io/overview/supertransaction-api/quote-7702)
- [Documentation](https://docs.biconomy.io/overview/supertransaction-api/sign-payload)
- [Documentation](https://docs.biconomy.io/overview/supertransaction-api/execute)
- [OpenAPI](openapi/biconomy-supertransaction-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [JSON Schema](json-schema/biconomy-quote-request-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/biconomy-quote-response-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Structure](json-structure/biconomy-supertransaction-structure.json)
- [JSON-LD](json-ld/biconomy-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [Example](examples/biconomy-quote-eoa-7702-example.json)
- [Example](examples/biconomy-execute-example.json)

### Biconomy Bundler API

Biconomy's open-source ERC-4337 TypeScript Bundler. Implements eth_sendUserOperation, eth_estimateUserOperationGas, eth_getUserOperationByHash, eth_getUserOperationReceipt, and eth_supportedEntryPoints across both EntryPoint v0.6.0 and v0.7.0. Hosted bundler endpoints are issued per project from the Biconomy Dashboard.

- **Human URL:** [https://github.com/bcnmy/bundler](https://github.com/bcnmy/bundler)

#### Tags

- Account Abstraction
- ERC-4337
- Bundler
- UserOps

#### Properties

- [Source Code](https://github.com/bcnmy/bundler)
- [Documentation](https://docs.biconomy.io/contracts-and-audits/supported-chains)

### Biconomy Sponsorship Paymaster

ERC-4337 paymaster service that fronts gas on behalf of end users so dApps can offer gasless UX. Two modes — full sponsorship (developer pays gas, post-paid invoiced monthly) and pay-in-tokens (user pays gas in ERC-20). All sponsorship limits (per-tx USD cap, per-user daily cap, allowed contracts, chain whitelist) are configured per project in the Biconomy Dashboard and enforced before /v1/quote returns a sponsored quote.

- **Human URL:** [https://docs.biconomy.io/gasless-apps/sponsorship](https://docs.biconomy.io/gasless-apps/sponsorship)

#### Tags

- Account Abstraction
- ERC-4337
- Paymaster
- Gas Sponsorship
- Gasless

#### Properties

- [Documentation](https://docs.biconomy.io/gasless-apps)
- [Documentation](https://docs.biconomy.io/gasless-apps/sponsorship)
- [Documentation](https://docs.biconomy.io/gasless-apps/testnet-sponsorship)
- [Documentation](https://docs.biconomy.io/gasless-apps/pay-in-tokens)
- [Source Code](https://github.com/bcnmy/biconomy-paymasters)
- [Dashboard](https://dashboard.biconomy.io)

### Biconomy Nexus Smart Account

Nexus is Biconomy's ERC-7579 modular smart contract account, audited by CodeHawks-Cyfrin (Sept 2024), Spearbit (Oct/Nov 2024), Zenith (Mar 2025), and Pashov (Mar 2025), and pre-deployed across 20+ EVM mainnets. Supports the four ERC-7579 module types (validator, executor, hook, fallback) and is the underlying account implementation behind eoa-7702 mode of the MEE API.

- **Human URL:** [https://github.com/bcnmy/nexus](https://github.com/bcnmy/nexus)

#### Tags

- Account Abstraction
- ERC-4337
- ERC-7579
- Smart Accounts
- Modules

#### Properties

- [Source Code](https://github.com/bcnmy/nexus)
- [Documentation](https://github.com/bcnmy/nexus/wiki)
- [Documentation](https://docs.biconomy.io/contracts-and-audits)
- [Documentation](https://docs.biconomy.io/contracts-and-audits/supported-chains)

### Biconomy Smart Sessions API

Smart Sessions is a delegated-execution framework layered on Nexus. A session key is granted a scoped set of policies (Sudo, Universal Action, Time Range, Usage Limit) that bound what an agent or automation can execute on behalf of the owner. Intended for agents, recurring automations, and intent-flow access without re-prompting the owner for every transaction.

- **Human URL:** [https://docs.biconomy.io/agents-automation](https://docs.biconomy.io/agents-automation)

#### Tags

- Account Abstraction
- Smart Sessions
- Agents
- Automation
- Policies

#### Properties

- [Documentation](https://docs.biconomy.io/agents-automation)
- [Documentation](https://docs.biconomy.io/agents-automation/implementation)
- [Documentation](https://docs.biconomy.io/agents-automation/agent-types)
- [Documentation](https://docs.biconomy.io/agents-automation/policies)
- [Documentation](https://docs.biconomy.io/agents-automation/policies/sudo)
- [Documentation](https://docs.biconomy.io/agents-automation/policies/universal-action)
- [Documentation](https://docs.biconomy.io/agents-automation/policies/time-range)
- [Documentation](https://docs.biconomy.io/agents-automation/policies/usage-limit)
- [Documentation](https://docs.biconomy.io/sdk-reference/sessions)
- [Source Code](https://github.com/bcnmy/session-permissioned-intents)

### Biconomy AbstractJS SDK

AbstractJS (@biconomy/abstractjs) is Biconomy's TypeScript-first SDK with a Viem-inspired API. Wraps the Supertransaction API and Nexus smart account operations, exposing createMeeClient, getQuote, executeQuote, and waitForSupertransactionReceipt helpers, plus composable instruction builders for cross-chain orchestration, conditional execution, runtime balance injection, and cleanup.

- **Human URL:** [https://docs.biconomy.io/overview/abstractjs](https://docs.biconomy.io/overview/abstractjs)

#### Tags

- SDK
- TypeScript
- Account Abstraction
- MEE

#### Properties

- [Documentation](https://docs.biconomy.io/overview/abstractjs)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/setup)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/composable-batching)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/cross-chain)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/conditional-execution)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/cleanup)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/estimating-gas)
- [Documentation](https://docs.biconomy.io/overview/abstractjs/batch-calls)
- [Documentation](https://docs.biconomy.io/sdk-reference)
- [Source Code](https://github.com/bcnmy/abstractjs)
- [SDK](https://www.npmjs.com/package/@biconomy/abstractjs)

## Common Properties

- [Portal](https://www.biconomy.io)
- [Documentation](https://docs.biconomy.io)
- [Documentation](https://docs.biconomy.io/llms.txt)
- [Dashboard](https://dashboard.biconomy.io)
- [Explorer](https://meescan.biconomy.io)
- [Source Code](https://github.com/bcnmy)
- [Source Code](https://github.com/bcnmy/nexus)
- [Source Code](https://github.com/bcnmy/abstractjs)
- [Source Code](https://github.com/bcnmy/bundler)
- [Source Code](https://github.com/bcnmy/biconomy-paymasters)
- [Source Code](https://github.com/bcnmy/mee-node)
- [Source Code](https://github.com/bcnmy/mee-contracts)
- [Source Code](https://github.com/bcnmy/stx-contracts)
- [Source Code](https://github.com/bcnmy/erc8211-contracts)
- [Source Code](https://github.com/bcnmy/entry-point-gas-estimations)
- [Source Code](https://github.com/bcnmy/session-permissioned-intents)
- [Documentation](https://github.com/bcnmy/awesome-biconomy)
- [SDK](https://www.npmjs.com/package/@biconomy/abstractjs)
- [Documentation](https://docs.biconomy.io/sdk-reference)
- [Documentation](https://docs.biconomy.io/sdk-reference/account)
- [Documentation](https://docs.biconomy.io/sdk-reference/client)
- [Documentation](https://docs.biconomy.io/sdk-reference/instructions)
- [Documentation](https://docs.biconomy.io/sdk-reference/runtime)
- [Documentation](https://docs.biconomy.io/sdk-reference/conditions)
- [Documentation](https://docs.biconomy.io/sdk-reference/sessions)
- [Documentation](https://docs.biconomy.io/sdk-reference/utilities)
- [Documentation](https://docs.biconomy.io/wallet-integrations)
- [Documentation](https://docs.biconomy.io/wallet-integrations/privy/api)
- [Documentation](https://docs.biconomy.io/wallet-integrations/turnkey/api)
- [Documentation](https://docs.biconomy.io/wallet-integrations/para/api)
- [Documentation](https://docs.biconomy.io/wallet-integrations/external-wallets/api)
- [Documentation](https://docs.biconomy.io/swaps-trading)
- [Documentation](https://docs.biconomy.io/swaps-trading/gasless-swap)
- [Documentation](https://docs.biconomy.io/swaps-trading/cross-chain-swap)
- [Documentation](https://docs.biconomy.io/swaps-trading/limit-order)
- [Documentation](https://docs.biconomy.io/zaps)
- [Documentation](https://docs.biconomy.io/agents-automation)
- [Documentation](https://docs.biconomy.io/gasless-apps)
- [Documentation](https://docs.biconomy.io/gasless-apps/sponsorship)
- [Documentation](https://docs.biconomy.io/gasless-apps/pay-in-tokens)
- [Documentation](https://docs.biconomy.io/contracts-and-audits)
- [Documentation](https://docs.biconomy.io/contracts-and-audits/supported-chains)
- [Blog](https://www.biconomy.io/blog)
- [Twitter](https://x.com/biconomy)
- [LinkedIn](https://www.linkedin.com/company/biconomy)
- [Support](https://discord.gg/biconomy)
- [Plans](plans/biconomy-plans-pricing.yml)
- [Rate Limits](rate-limits/biconomy-rate-limits.yml)
- [Fin Ops](finops/biconomy-finops.yml)
- [Vocabulary](vocabulary/biconomy-vocabulary.yml)
- [Rules](rules/biconomy-rules.yml)

## Maintainers

**FN:** Biconomy
**Email:** support@biconomy.io
**URL:** https://www.biconomy.io
