# Deployment

How EscrowFlow is deployed to production.

## Contract (testnet)

```bash
stellar keys generate deployer --network testnet
stellar account fund deployer --network testnet  # via friendbot
soroban-cli contract build
soroban-cli contract deploy --network testnet --wasm target/wasm32-unknown-unknown/release/escrow.wasm
# Returns contract ID: C...
```

The returned contract ID is what gets set as `CONTRACT_ID` in the API's environment (see [Local Setup](local-setup.md)).

## API (Render)

- Repo connected to [render.com](https://render.com).
- Auto-deploys on push to `main`.
- **Database**: [Neon](https://neon.tech) (free Postgres tier).
- **Env vars**: `DATABASE_URL`, `JWT_SECRET`, Stellar RPC URL, contract ID, admin secret key.
- **Logs**: viewed via the Render dashboard.
- **Cold start**: ~30s on the free tier for the first request after inactivity — this is expected, not a bug.

## Frontend (Vercel)

- Repo connected to [vercel.com](https://vercel.com).
- Auto-deploys on push to `main`.
- **Env var**: `NEXT_PUBLIC_API_URL=https://escrowflow-api-xxx.onrender.com`.
- **Domain**: [www.escrowflowhq.com](https://www.escrowflowhq.com) (custom domain configured in Vercel).

## Mobile (Expo)

- Built via **EAS** (Expo Application Services):
  ```bash
  eas build --platform android --profile preview
  ```
- APK available for download once the build completes.
- **iOS**: use Expo Go (free) for development, or TestFlight (requires a paid Apple Developer account) for distribution.

**Related:** [Local Setup](local-setup.md) for running the same services on your machine, [Roadmap](../roadmap.md) for the mainnet deployment timeline.
