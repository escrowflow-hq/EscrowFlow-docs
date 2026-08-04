# Local Setup

Run EscrowFlow locally across all 5 repos.

## Contract

```bash
cd EscrowflowContract
cargo build --target wasm32-unknown-unknown
soroban-cli contract build --network testnet
```

## API

```bash
cd escrowflow-api
npm install
cp .env.example .env
# Fill in: DATABASE_URL, JWT_SECRET, Stellar testnet RPC, contract ID, USDC token ID
npm run dev
# Runs on http://localhost:3000
```

## Frontend

```bash
cd escrowflow-frontend
npm install
NEXT_PUBLIC_API_URL=http://localhost:3000 NEXT_PUBLIC_USE_MOCK=false npm run dev
# Runs on http://localhost:3000
```

## Mobile

```bash
cd EscrowFlow-mobile
npm install
# Set .env: EXPO_API_URL=http://localhost:3000, USE_MOCK=false
npx expo start
```

## Environment Variables Checklist

| Repo | Variable | Example | Where to get it |
|---|---|---|---|
| escrowflow-api | `DATABASE_URL` | `postgresql://user:pass@host/db` | Neon dashboard (or local Postgres) |
| escrowflow-api | `JWT_SECRET` | `a-long-random-string` | Generate locally (e.g. `openssl rand -hex 32`) |
| escrowflow-api | `STELLAR_RPC_URL` | `https://soroban-testnet.stellar.org` | Public Soroban testnet RPC |
| escrowflow-api | `CONTRACT_ID` | `C...` | Output of `soroban-cli contract deploy` — see [Deployment](deployment.md) |
| escrowflow-api | `USDC_TOKEN_ID` | `C...` | Testnet USDC token contract ID (Stellar testnet asset registry) |
| escrowflow-api | `WALLET_ENCRYPTION_KEY` | `a-long-random-string` | Generate locally; used to AES-encrypt wallet secret keys |
| escrowflow-frontend | `NEXT_PUBLIC_API_URL` | `http://localhost:3000` | Your local or deployed API URL |
| escrowflow-frontend | `NEXT_PUBLIC_USE_MOCK` | `false` | `true` to run against mock data with no API |
| EscrowFlow-mobile | `EXPO_API_URL` | `http://localhost:3000` | Your local or deployed API URL |
| EscrowFlow-mobile | `USE_MOCK` | `false` | `true` to run against mock data with no API |

**Next:** [Deployment](deployment.md) for how these same services run in production.
