# Security

## Threat Model

| Threat | Impact |
|---|---|
| Client wallet compromised | Funds already locked in escrow cannot be stolen — the contract only releases on milestone approval or dispute resolution, regardless of who controls the client's keypair afterward. |
| API database breach | Encrypted wallet keys are useless without the AES encryption key, which is stored separately (environment variable, not the database). |
| Contract bug discovered post-deployment | Would only affect *new* escrows created after the bug is exploited — funds already locked in prior escrows follow the state machine as deployed at the time. |
| Denial of service | API rate limiting bounds the blast radius of request flooding. |

## Mitigations

- Wallet secret keys are encrypted with **AES-256**; the encryption key exists only in an environment variable, never committed or stored alongside the encrypted data.
- The **escrow contract is the source of truth** for fund state — the API's Postgres mirror is a read cache, not the authority, so a database compromise or bug cannot itself move funds.
- A **contract audit is recommended** before any mainnet deployment (see [Roadmap](../roadmap.md), Phase 2).
- **API rate limiting**: 100 requests/minute per IP.
- **No personal data in logs** — request logs exclude PII (names, emails, KYC documents).

## Known Limitations

- **Testnet only** for the current MVP — no real funds are at risk yet; mainnet deployment is planned (see [Roadmap](../roadmap.md)).
- **Keys stored in the database** (encrypted), not a hardware security module or key vault — production should migrate to a managed key vault (AWS KMS, HashiCorp Vault, or similar).
- **No insurance fund** yet — a future phase, not part of the current MVP.

## Responsible Disclosure

If you find a security vulnerability in EscrowFlow, please email **security@escrowflow.dev** instead of filing a public issue or disclosing it elsewhere. We'll acknowledge your report and work with you on a fix before any public disclosure.
