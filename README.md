# Biconomy (biconomy)
Biconomy is a Web3 account-abstraction infrastructure company. Its product is the Modular Execution Environment (MEE) — a multichain orchestration layer that lets developers compose swaps, bridges, DeFi deposits, and arbitrary contract calls into a single signable "supertransaction" routed through ERC-4337 bundlers, paymasters, and ERC-7579 modular smart accounts (Nexus). MEE supports three account modes — smart-account (Nexus / ERC-4337), eoa (MetaMask, Rabby, Trust), and eoa-7702 (EIP-7702 set-code delegation for Privy / Dynamic / Turnkey embedded wallets). Biconomy reports 70M+ transactions and $3.5B+ in volume processed.

**URL:** [Visit APIs.json](https://raw.githubusercontent.com/api-evangelist/biconomy/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags

 - Account Abstraction, Blockchain, Bundler, Cross-Chain, DeFi, ERC-4337, ERC-7579, ERC-7702, Ethereum, Gas Abstraction, Gasless, MEE, Paymaster, Smart Accounts, Smart Sessions, Wallets, Web3

## Timestamps

- **Created:** 2026-05-25
- **Modified:** 2026-05-25

## APIs

### Biconomy Supertransaction API
The Supertransaction API exposes the MEE over a small REST surface. `POST /v1/quote` composes a multichain execution plan from one or more `composeFlows` instructions and returns the fee plus the signable payload(s); `POST /v1/execute` submits the signed quote for cross-chain settlement. Three account modes (`smart-account`, `eoa`, `eoa-7702`) and six instruction types (`intent-simple`, `intent`, `intent-vault`, `build`, `build-raw`, `build-ccip`).

**Human URL:** [https://docs.biconomy.io/overview/supertransaction-api](https://docs.biconomy.io/overview/supertransaction-api)

- [Documentation](https://docs.biconomy.io/overview/supertransaction-api)
- [Documentation - Get Quote](https://docs.biconomy.io/overview/supertransaction-api/get-quote)
- [Documentation - Sign Payload](https://docs.biconomy.io/overview/supertransaction-api/sign-payload)
- [Documentation - Execute](https://docs.biconomy.io/overview/supertransaction-api/execute)
- [OpenAPI](openapi/biconomy-supertransaction-api-openapi.yml)
- [JSON Schema - Quote Request](json-schema/biconomy-quote-request-schema.json)
- [JSON Schema - Quote Response](json-schema/biconomy-quote-response-schema.json)
- [JSON Structure - Supertransaction](json-structure/biconomy-supertransaction-structure.json)
- [JSON-LD Context](json-ld/biconomy-context.jsonld)
- [Example - Quote (eoa-7702 mode)](examples/biconomy-quote-eoa-7702-example.json)
- [Example - Execute](examples/biconomy-execute-example.json)
- [Naftiko Capability - Quote](capabilities/supertransaction-quote.yaml)
- [Naftiko Capability - Execute](capabilities/supertransaction-execute.yaml)
- [Naftiko Capability - Orchestrator](capabilities/supertransaction-orchestrator.yaml)

### Biconomy Bundler API
Open-source ERC-4337 TypeScript bundler implementing `eth_sendUserOperation`, `eth_estimateUserOperationGas`, `eth_getUserOperationByHash`, `eth_getUserOperationReceipt`, and `eth_supportedEntryPoints` across EntryPoint v0.6.0 and v0.7.0. Hosted bundler endpoints are issued per project from the Biconomy Dashboard.

**Human URL:** [https://github.com/bcnmy/bundler](https://github.com/bcnmy/bundler)

- [Source Code](https://github.com/bcnmy/bundler)
- [Supported Chains](https://docs.biconomy.io/contracts-and-audits/supported-chains)

### Biconomy Sponsorship Paymaster
ERC-4337 paymaster service that fronts gas on behalf of end users. Two modes - full sponsorship (developer pays, invoiced monthly post-paid) and pay-in-tokens (user pays gas in an ERC-20). All sponsorship policy - per-tx USD cap, per-user daily cap, allowed contracts, chain whitelist - is configured per project in the dashboard and enforced before `/v1/quote` returns a sponsored quote.

**Human URL:** [https://docs.biconomy.io/gasless-apps/sponsorship](https://docs.biconomy.io/gasless-apps/sponsorship)

- [Sponsorship Docs](https://docs.biconomy.io/gasless-apps/sponsorship)
- [Testnet Sponsorship](https://docs.biconomy.io/gasless-apps/testnet-sponsorship)
- [Pay In Tokens](https://docs.biconomy.io/gasless-apps/pay-in-tokens)
- [Source Code - Paymasters](https://github.com/bcnmy/biconomy-paymasters)
- [Dashboard](https://dashboard.biconomy.io)

### Biconomy Nexus Smart Account
ERC-7579 modular smart contract account. Audited by CodeHawks-Cyfrin (Sept 2024), Spearbit (Oct/Nov 2024), Zenith (Mar 2025), and Pashov (Mar 2025). Pre-deployed across 20+ EVM mainnets. Implements the four ERC-7579 module types (validator, executor, hook, fallback) and is the account implementation behind `eoa-7702` mode.

**Human URL:** [https://github.com/bcnmy/nexus](https://github.com/bcnmy/nexus)

- [Source Code](https://github.com/bcnmy/nexus)
- [Wiki](https://github.com/bcnmy/nexus/wiki)
- [Contracts and Audits](https://docs.biconomy.io/contracts-and-audits)
- [Supported Chains](https://docs.biconomy.io/contracts-and-audits/supported-chains)

### Biconomy Smart Sessions API
Delegated-execution framework on Nexus. A session key is granted a scoped set of policies (Sudo, Universal Action, Time Range, Usage Limit) that bound what an agent or automation can do on behalf of the owner. Intended for agents, recurring automations, and intent-flow access without re-prompting the owner per transaction.

**Human URL:** [https://docs.biconomy.io/agents-automation](https://docs.biconomy.io/agents-automation)

- [Agents and Automation](https://docs.biconomy.io/agents-automation)
- [Implementation](https://docs.biconomy.io/agents-automation/implementation)
- [Agent Types](https://docs.biconomy.io/agents-automation/agent-types)
- [Policies](https://docs.biconomy.io/agents-automation/policies)
- [Smart Sessions SDK Reference](https://docs.biconomy.io/sdk-reference/sessions)
- [Source Code - Session Permissioned Intents](https://github.com/bcnmy/session-permissioned-intents)

### Biconomy AbstractJS SDK
TypeScript-first SDK (`@biconomy/abstractjs`) with a Viem-inspired API. Wraps the Supertransaction API and Nexus operations, exposing `createMeeClient`, `getQuote`, `executeQuote`, and `waitForSupertransactionReceipt` plus composable instruction builders for cross-chain orchestration, conditional execution, runtime balance injection, and cleanup.

**Human URL:** [https://docs.biconomy.io/overview/abstractjs](https://docs.biconomy.io/overview/abstractjs)

- [Documentation](https://docs.biconomy.io/overview/abstractjs)
- [Setup](https://docs.biconomy.io/overview/abstractjs/setup)
- [Composable Batching](https://docs.biconomy.io/overview/abstractjs/composable-batching)
- [Cross-Chain Orchestration](https://docs.biconomy.io/overview/abstractjs/cross-chain)
- [Conditional Execution](https://docs.biconomy.io/overview/abstractjs/conditional-execution)
- [SDK Reference](https://docs.biconomy.io/sdk-reference)
- [Source Code](https://github.com/bcnmy/abstractjs)
- [NPM Package](https://www.npmjs.com/package/@biconomy/abstractjs)

## Supported Chains

20+ EVM mainnets supported by MEE, including: Ethereum (1), Base (8453), Arbitrum (42161), Optimism (10), Polygon (137), BSC (56), Sonic (146), Scroll (534352), Gnosis (100), Avalanche (43114), Apechain (33139), Hyper EVM, Sei, Unichain, Monad, Katana, Lisk, Worldchain, Plasma. Latest MEE version 2.2.2. See [Supported Chains](https://docs.biconomy.io/contracts-and-audits/supported-chains) for the full list and contract addresses.

## Common

- [Portal](https://www.biconomy.io)
- [Documentation](https://docs.biconomy.io)
- [LLMs.txt Index](https://docs.biconomy.io/llms.txt)
- [Dashboard](https://dashboard.biconomy.io)
- [MEEScan Explorer](https://meescan.biconomy.io)
- [GitHub Organization](https://github.com/bcnmy)
- [Blog](https://www.biconomy.io/blog)
- [X / Twitter](https://x.com/biconomy)
- [LinkedIn](https://www.linkedin.com/company/biconomy)
- [Discord](https://discord.gg/biconomy)
- [Plans](plans/biconomy-plans-pricing.yml)
- [Rate Limits](rate-limits/biconomy-rate-limits.yml)
- [FinOps](finops/biconomy-finops.yml)
- [Vocabulary](vocabulary/biconomy-vocabulary.yml)
- [Spectral Rules](rules/biconomy-rules.yml)
