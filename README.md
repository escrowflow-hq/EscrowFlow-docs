# EscrowFlow Documentation

Documentation hub for EscrowFlow — milestone-based escrow payments for global freelancers, built on Stellar.

## Live Demo

| Service | URL |
|---|---|
| Web App | [www.escrowflowhq.com](https://www.escrowflowhq.com) |
| API | [escrowflow-api-xxx.onrender.com](https://escrowflow-api-xxx.onrender.com) · [Swagger docs](https://escrowflow-api-xxx.onrender.com/api/docs) |
| Smart Contract | [stellar.expert/explorer/testnet](https://stellar.expert/explorer/testnet) (Stellar testnet) |

## Quick Start

1. **Sign up** at [escrowflowhq.com](https://www.escrowflowhq.com) and get USDC from the built-in testnet faucet.
2. **Create a project**, add milestones, and fund the escrow.
3. Freelancer **submits** each milestone, client **approves**, funds release automatically — verify the transaction on [stellar.expert](https://stellar.expert/explorer/testnet).

See [Getting Started](docs/guides/getting-started.md) for the full 5-minute walkthrough.

## What is EscrowFlow?

EscrowFlow is milestone-based escrow for global freelance payments. Clients fund a project up front, a Soroban smart contract on Stellar holds the funds in escrow, and freelancers get paid instantly and trustlessly as each milestone is approved — no waiting on banks, no chargebacks, no platform holding your money. EscrowFlow takes a 3% fee on release, deducted automatically by the contract.

## Docs Navigation

| Doc | Description |
|---|---|
| [Architecture Overview](docs/architecture/overview.md) | System diagram, three-layer architecture, design decisions, data flow, tech stack |
| [Smart Contracts](docs/architecture/smart-contracts.md) | Contract interface, state machine, fees, storage, error codes, events |
| [API Reference](docs/architecture/api.md) | Base URL, auth, module map, key endpoints, wallet management |
| [Security](docs/architecture/security.md) | Threat model, mitigations, known limitations, disclosure policy |
| [Getting Started](docs/guides/getting-started.md) | Try EscrowFlow end-to-end in 5 minutes |
| [Local Setup](docs/guides/local-setup.md) | Run all 5 repos locally |
| [Deployment](docs/guides/deployment.md) | How EscrowFlow is deployed to production |
| [Mobile App](docs/guides/mobile-app.md) | Installation, features, architecture, and dev setup for the React Native app |
| [Team](docs/team.md) | Founders, contributors, contact |
| [Roadmap](docs/roadmap.md) | Phase 1–3 plan |

## Repos

| Repo | Description |
|---|---|
| [EscrowflowContract](https://github.com/escrowflow-hq/EscrowflowContract) | Soroban/Rust smart contract |
| [escrowflow-api](https://github.com/escrowflow-hq/escrowflow-api) | NestJS backend API |
| [escrowflow-frontend](https://github.com/escrowflow-hq/escrowflow-frontend) | Next.js web app |
| [EscrowFlow-mobile](https://github.com/escrowflow-hq/EscrowFlow-mobile) | React Native / Expo mobile app |
| [escrowflow-docs](https://github.com/escrowflow-hq/escrowflow-docs) | This repo |

## Built on Stellar

EscrowFlow runs on **Soroban**, Stellar's smart contract platform, and settles in **USDC**. Escrow logic — locking funds, releasing milestones, resolving disputes — lives entirely on-chain, not in a database controlled by EscrowFlow. Currently deployed on Stellar testnet as a proof of concept ahead of mainnet. Learn more at the [Stellar community](https://stellar.org).
