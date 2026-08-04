# API Reference

The EscrowFlow API is a NestJS service that sits between the web/mobile clients and the Soroban smart contract. Source: [escrowflow-api](https://github.com/escrowflow-hq/escrowflow-api).

## Base URL

| Environment | URL |
|---|---|
| Production | `https://escrowflow-api-xxx.onrender.com` |
| Local | `http://localhost:3000` |

## Health Check

```
GET /health
```

```json
{ "status": "ok" }
```

## Swagger Docs

Interactive API explorer, generated from the NestJS controllers:

```
GET /api/docs
```

## Authentication

All endpoints except `/health`, `/auth/*`, and the testnet faucet require a JWT bearer token:

```
Authorization: Bearer <token>
```

Obtain a token via `POST /auth/login` or `POST /auth/signup`. Tokens are refreshed via `POST /auth/refresh`.

## Module Map

| Module | Responsibilities |
|---|---|
| **Auth** | `login`, `signup`, `logout`, refresh token |
| **Users** | `getProfile`, `updateProfile`, `getBalance` |
| **Wallet** | `getAddress`, `getPublicKey` (secret key is never exposed via the API) |
| **Projects** | `create`, `list`, `get`, `update` |
| **Milestones** | `create`, `get`, `submit`, `approve`, `reject` |
| **Payments** | `list`, `get` |
| **KYC** | `getStatus`, `startFlow`, `verify` |
| **Messaging** | `sendMessage`, `getMessages`, `uploadFile` |
| **Notifications** | `getNotifications`, `markAsRead` |
| **Dev** (testnet only) | `POST /dev/faucet?address=...` → mints 1000 test USDC |

## Key Endpoints

| Method & Path | Body | Returns |
|---|---|---|
| `POST /auth/signup` | `{ email, password, name, userRole }` | `{ token, user }` |
| `POST /projects` | `{ title, description, budget, freelancerEmail, milestones[] }` | `project` |
| `POST /projects/{id}/deposit` | `{ amount }` | Calls `contract.deposit()`, returns tx hash |
| `POST /projects/{id}/milestones/{mid}/approve` | `{}` | Calls `contract.approve_milestone()`, releases funds |

## Error Responses

All errors follow a consistent shape:

```json
{
  "code": "INSUFFICIENT_FUNDS",
  "message": "Wallet balance is below the requested deposit amount.",
  "details": { "required": "200.00", "available": "150.00" }
}
```

## Wallet Management

Each user's Stellar keypair is generated server-side at signup. The secret key is encrypted with AES-256 before being stored in Postgres; the encryption key lives only in an environment variable, never in the database. Keys are decrypted in memory only at the moment a transaction is signed. See [Security](security.md) for the full threat model.

## Stellar Integration

The API uses [`@stellar/stellar-sdk`](https://github.com/stellar/js-stellar-sdk) to build, sign, and submit transactions that invoke the Soroban contract (`create_escrow`, `deposit`, `submit_milestone`, `approve_milestone`, `reject_milestone`, `open_dispute`, `refund`). Contract state is mirrored into Postgres after each successful transaction so the frontend can read project/milestone status without querying the chain directly.
