# stellar-escrow-sdk

> A TypeScript SDK for embedding trustless Soroban escrow flows into any application —
> create, fund, release, and dispute escrows in minutes, not days.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![Drips Wave Program](https://img.shields.io/badge/Drips-Wave%20Program-8B5CF6)](https://drips.network)

---

## Overview

`stellar-escrow-sdk` abstracts the complexity of writing raw Soroban contract invocations
for escrow use cases. Whether you're building a freelance marketplace, a P2P trading
platform, or an e-commerce checkout, this SDK gives you a clean, wallet-agnostic API to
deploy and manage on-chain escrow agreements using USDC or any SEP-41 token on Stellar.

---

## Technical Architecture

- **SDK Client:** `EscrowClient` TypeScript class built on `@stellar/stellar-sdk` —
  abstracts `simulateTransaction` + `assembleTransaction` + `sendTransaction` into
  single async method calls per escrow lifecycle stage
- **Reference Contract:** Rust/Soroban escrow contract in `contracts/src/` supporting
  configurable depositor, beneficiary, arbiter, token address, amount, and deadline
- **Type Safety:** All inputs/outputs are fully typed; Zod schemas validate parameters
  at runtime before they reach the network
- **Wallet Agnostic:** Accepts any `SignerFunction` — compatible with Freighter, xBull,
  server-side keypairs, and multisig setups
- **Error Handling:** Typed error classes (`EscrowExpiredError`, `UnauthorizedReleaseError`,
  `InsufficientFundsError`) parsed from Soroban diagnostic events

---

## 💧 Drips Wave Program

This repository is an active participant in the
**[Drips Wave Program](https://drips.network)** — a funding mechanism that rewards
open-source contributors for resolving scoped GitHub issues with on-chain streaming payments.

### How to Contribute & Earn

**Step 1 — Register on Drips**
Go to [drips.network](https://drips.network) and connect your Ethereum-compatible wallet.
This is the address your streaming rewards will be paid to.

**Step 2 — Browse Open Issues**
Visit the [Issues tab](../../issues). Look for Drips complexity labels:

| Label           | Complexity | Typical Scope                                                              |
|-----------------|------------|----------------------------------------------------------------------------|
| `drips:trivial` | Trivial    | Improve JSDoc, add a usage example, write a type export                    |
| `drips:medium`  | Medium     | New lifecycle method, Zod schema additions, improved error parsing         |
| `drips:high`    | High       | Multi-token support, multisig arbiter flows, fee estimation utilities      |

**Step 3 — Claim an Issue**
Comment `/claim` on your chosen issue. The maintainer will assign it.
Issues are first-come, first-served.

**Step 4 — Submit a PR**
Open a Pull Request with `Closes #XX` in the description. Include unit tests for all
new logic and update the relevant JSDoc and examples.

**Step 5 — Earn Rewards**
Your wallet begins receiving a continuous Drips stream upon PR merge.
No invoices, no delays.

---

## Project Structure
```
stellar-escrow-sdk/
├── src/
│   ├── client/           # EscrowClient class and method implementations
│   ├── types/            # TypeScript interfaces (EscrowParams, EscrowState, etc.)
│   ├── utils/            # Transaction builders, fee estimators, address validators
│   └── errors/           # Typed error classes from Soroban diagnostic events
├── contracts/
│   ├── src/              # Rust/Soroban reference escrow contract
│   └── tests/            # Rust unit tests for the reference contract
├── examples/
│   ├── node/             # Node.js usage (server-side keypair signing)
│   └── browser/          # Browser usage with Freighter wallet
├── tests/
│   ├── unit/             # Vitest tests with mocked Soroban RPC
│   └── integration/      # Tests against local stellar/quickstart Docker node
├── docs/                 # API reference and integration guides
├── package.json
├── tsconfig.json
└── README.md
```

---

## Quick Start
```bash
npm install stellar-escrow-sdk
```
```typescript
import { EscrowClient } from 'stellar-escrow-sdk';
import { Networks } from '@stellar/stellar-sdk';

const client = new EscrowClient({
  rpcUrl: 'https://soroban-testnet.stellar.org',
  networkPassphrase: Networks.TESTNET,
  contractId: 'CXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX',
});

// Create a new escrow
const escrowId = await client.createEscrow({
  depositor: 'GABC...',
  beneficiary: 'GDEF...',
  arbiter: 'GHIJ...',
  tokenAddress: 'USDC_CONTRACT_ID',
  amount: BigInt(500_0000000), // 500 USDC
  deadlineLedger: 1500000,
  signer: freighterSignerFn,
});

// Release funds to beneficiary
await client.releaseEscrow({ escrowId, signer: freighterSignerFn });

// Dispute and trigger arbiter resolution
await client.disputeEscrow({ escrowId, reason: 'Deliverable not met', signer: freighterSignerFn });
```

---

## License

MIT © stellar-escrow-sdk contributors
