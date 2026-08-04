# Getting Started

## Try EscrowFlow in 5 minutes

1. Go to [www.escrowflowhq.com](https://www.escrowflowhq.com) → click **"Open app"**.
2. Sign up as a **client** (email, password, name).
3. Hit the **faucet** to get 1000 test USDC — it'll show up in your wallet balance.
4. **Create a project**: title "Website Redesign", budget $200, 2 milestones ($100 each), and the freelancer's email.
5. Click **"Fund escrow"** — your balance decreases and the escrow is created on-chain.
6. Open another browser tab and **sign up as the freelancer** (using the email you entered above).
7. From the freelancer account, **submit milestone 1** (mark the work as delivered).
8. Switch back to the client account and **approve milestone 1** — the freelancer receives $100 minus the 3% fee, i.e. **$97**.
9. Go to [stellar.expert/explorer/testnet](https://stellar.expert/explorer/testnet), search your contract ID, and see the transaction.

## What you just proved

Escrow works end-to-end on Stellar testnet: funds were locked on deposit, released only on approval, and the 3% platform fee was deducted automatically by the contract — no manual intervention, no trust required between client and freelancer.

**Next:** [Local Setup](local-setup.md) to run EscrowFlow yourself, or [Architecture Overview](../architecture/overview.md) to see how it works under the hood.
