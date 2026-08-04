# Architecture Overview

## System Diagram

```
┌─────────────────┐     ┌─────────────────┐
│   Web (Next.js)  │     │  Mobile (Expo)   │
└────────┬─────────┘     └────────┬─────────┘
         │                        │
         └───────────┬────────────┘
                      │  HTTPS / REST
                      ▼
            ┌───────────────────┐
            │   API (NestJS)     │
            │  ─────────────────  │
            │  Auth · Wallets     │
            │  Projects · KYC     │
            │  Postgres (state)   │
            └─────────┬───────────┘
                      │  @stellar/stellar-sdk
                      ▼
            ┌───────────────────┐
            │  Soroban Contract  │
            │  (Stellar testnet) │
            │  ─────────────────  │
            │  Escrow state      │
            │  Milestones        │
            │  Disputes · Fees   │
            └───────────────────┘
```

## Three-Layer Architecture

### 1. Frontend (web + mobile)
The Next.js web app and Expo/React Native mobile app share the same API contract. Responsibilities: UI, client-side state management, authentication (JWT), and rendering project/escrow/milestone status. Neither client ever holds a Stellar secret key — wallets are managed server-side.

### 2. API (NestJS)
The API is the orchestration layer between clients and the chain:
- **Wallet management** — generates and holds a Stellar keypair per user, with the secret key AES-encrypted at rest.
- **Escrow service** — builds, signs, and submits Soroban contract invocations via the Stellar SDK; mirrors on-chain state into Postgres for fast reads (projects, milestones, payment history).
- **KYC** — tracks verification status and gates payout flows.

### 3. Smart Contract (Soroban/Rust)
The contract is the source of truth for anything involving funds: escrow state machine, milestone lifecycle, the 3% platform fee, and dispute resolution. See [Smart Contracts](smart-contracts.md) for the full interface.

## Design Decisions

**Why managed wallets (no seed phrases for users)** — Freelance clients and freelancers are not expected to be crypto-native. Requiring users to custody a seed phrase would be the single biggest drop-off point in onboarding. The API manages keys instead, trading some custodial risk (mitigated — see [Security](security.md)) for a normal email/password signup flow.

**Why Soroban** — Escrow is a trust problem: both parties need certainty that funds can't be withheld, double-spent, or unilaterally redirected. Putting the escrow state machine on-chain means the guarantee comes from the contract, not from EscrowFlow's database or good behavior.

**Why testnet first** — The goal of the current phase is to prove the end-to-end mechanism — deposit, milestone approval, fee split, dispute path — works correctly and is usable, before putting real funds and an unaudited contract on mainnet. See the [Roadmap](../roadmap.md) for the mainnet timeline.

## Data Flow

1. User creates a project in the web/mobile app → API stores it in Postgres (draft state).
2. Client funds the project → API invokes `create_escrow` and `deposit` on the Soroban contract (testnet) → contract locks USDC.
3. Freelancer submits a milestone → API invokes `submit_milestone`.
4. Client approves → API invokes `approve_milestone` → contract releases payment to the freelancer minus the 3% platform fee.
5. API records the resulting payment and transaction hash in Postgres for display; the contract remains the authoritative record.

## Tech Stack

| Layer | Technology | Version | Why |
|---|---|---|---|
| Smart Contract | Rust | 1.7x (stable) | Required by Soroban SDK |
| Smart Contract | Soroban SDK | `soroban-sdk` 21.x | Stellar's native smart contract framework |
| API | NestJS | 10.x | Modular structure maps cleanly to Auth/Wallet/Escrow/KYC domains |
| API | TypeScript | 5.x | Type safety across API and SDK calls |
| API | PostgreSQL | 15 (Neon) | Relational data for projects/milestones/payments; serverless free tier |
| API | Prisma / TypeORM | — | Postgres access layer |
| API | `@stellar/stellar-sdk` | 12.x | Build, sign, and submit transactions to Soroban |
| Frontend | Next.js | 14.x | SSR + React for the web dashboard and landing page |
| Mobile | React Native (Expo) | SDK 50 | Cross-platform mobile with a single codebase, EAS build pipeline |
| Auth | JWT | — | Stateless auth shared across web, mobile, and API |
| Hosting | Render (API), Vercel (web), EAS (mobile) | — | Zero-ops deploys on push to `main` |
