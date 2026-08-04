# Smart Contracts

The EscrowFlow contract is written in Rust using the Soroban SDK and deployed to Stellar testnet. Source: [EscrowflowContract](https://github.com/escrowflow-hq/EscrowflowContract).

## Contract Address

Testnet contract ID: `C...` (see the [EscrowflowContract](https://github.com/escrowflow-hq/EscrowflowContract) repo README for the current deployed ID) — view it on [stellar.expert/explorer/testnet](https://stellar.expert/explorer/testnet).

## Interface & Functions

| Function | Signature | Description |
|---|---|---|
| `initialize` | `initialize(admin, arbitrator, platform_fee_bps)` | One-time setup: sets the admin address, dispute arbitrator address, and platform fee in basis points (300 = 3%). |
| `create_escrow` | `create_escrow(client, freelancer, token, milestone_descriptions[], milestone_amounts[], milestone_due_dates[]) → u64 escrow_id` | Creates a new escrow with one or more milestones. Returns the new escrow's ID. |
| `deposit` | `deposit(escrow_id, from)` | Locks the full escrow amount (sum of milestone amounts) in the contract, transferred from `from`. |
| `submit_milestone` | `submit_milestone(escrow_id, milestone_id, freelancer)` | Freelancer marks a milestone as delivered, moving it to `SUBMITTED`. |
| `approve_milestone` | `approve_milestone(escrow_id, milestone_id, client)` | Client approves delivered work. Releases the milestone amount to the freelancer minus the platform fee. |
| `reject_milestone` | `reject_milestone(escrow_id, milestone_id, client)` | Client rejects submitted work, returning the milestone to `PENDING` for resubmission. |
| `open_dispute` | `open_dispute(escrow_id, milestone_id, initiator)` | Either party escalates a milestone to `DISPUTED` for arbitrator review. |
| `resolve_dispute` | `resolve_dispute(escrow_id, milestone_id, outcome, split_bps)` | Arbitrator resolves a dispute, splitting the milestone amount between client and freelancer per `split_bps`. |
| `refund` | `refund(escrow_id)` | Returns undisbursed escrow funds to the client (e.g. cancelled project). |
| `get_escrow` | `get_escrow(escrow_id) → Escrow` | Read-only: returns the full `Escrow` struct. |
| `get_milestone` | `get_milestone(escrow_id, milestone_id) → Milestone` | Read-only: returns a single `Milestone` struct. |

## State Machine

Milestone state transitions:

```
                ┌────────────┐
        ┌──────▶│  PENDING   │◀──────┐
        │       └─────┬──────┘       │
        │             │ submit       │ reject
        │             ▼              │
        │       ┌────────────┐       │
        │       │ SUBMITTED  │───────┘
        │       └─────┬──────┘
        │             │ approve
   open_dispute        ▼
        │       ┌────────────┐
        │       │  APPROVED  │
        │       └─────┬──────┘
        │             │ (funds released)
        │             ▼
        │       ┌────────────┐
        └──────▶│  DISPUTED  │
                └─────┬──────┘
                      │ resolve_dispute
                      ▼
                ┌────────────┐
                │  RELEASED  │
                └────────────┘
```

`open_dispute` can be called from `PENDING` or `SUBMITTED` by either party; `resolve_dispute` moves a milestone to `RELEASED` with funds split per the arbitrator's `split_bps`.

## Fee Mechanics

The platform fee is set at initialization as `platform_fee_bps = 300` (3%). On `approve_milestone`, the contract deducts 3% of the milestone amount and sends it to the admin wallet before transferring the remainder to the freelancer. The same fee is applied to the client's share of a `resolve_dispute` payout.

## Storage

- **Escrows** — keyed by `u64 escrow_id`, storing client, freelancer, token address, and a list of milestone IDs.
- **Milestones** — keyed by `(escrow_id, milestone_id)`, storing description, amount, due date, and current state.
- **Payments** — emitted as events (see below) rather than stored long-term on-chain; the API indexes these into Postgres for history/reporting.

## Error Codes

| Code | Name | Meaning |
|---|---|---|
| 1 | `InvalidClient` | Caller does not match the escrow's client. |
| 2 | `InvalidFreelancer` | Caller does not match the escrow's freelancer. |
| 3 | `InsufficientFunds` | Depositor's balance is below the required escrow amount. |
| 4 | `UnauthorizedAction` | `require_auth` failed for the calling address. |
| 5 | `EscrowNotFound` | No escrow exists for the given `escrow_id`. |
| 6 | `MilestoneNotFound` | No milestone exists for the given `milestone_id`. |
| 7 | `InvalidMilestoneState` | Action not valid for the milestone's current state. |
| 8 | `AlreadyDeposited` | `deposit` called on an escrow that is already funded. |
| 9 | `NotDeposited` | Milestone action attempted before the escrow was funded. |
| 10 | `InvalidFeeBps` | `platform_fee_bps` outside the allowed 0–10000 range. |
| 11 | `InvalidSplitBps` | Dispute `split_bps` outside the allowed 0–10000 range. |
| 12 | `AlreadyInitialized` | `initialize` called more than once. |
| 13 | `NotInitialized` | Contract function called before `initialize`. |
| 14 | `InvalidArbitrator` | Caller is not the configured dispute arbitrator. |
| 15 | `DisputeNotOpen` | `resolve_dispute` called on a milestone that isn't `DISPUTED`. |
| 16 | `EmptyMilestoneList` | `create_escrow` called with zero milestones. |
| 17 | `MismatchedMilestoneArrays` | Milestone description/amount/due-date arrays are different lengths. |

## Events

| Event | Emitted when |
|---|---|
| `EscrowCreated` | `create_escrow` succeeds |
| `MilestoneSubmitted` | `submit_milestone` succeeds |
| `MilestoneApproved` | `approve_milestone` succeeds |
| `FundsReleased` | Funds are transferred to the freelancer (approval or dispute resolution) |
| `DisputeOpened` | `open_dispute` succeeds |
| `DisputeResolved` | `resolve_dispute` succeeds |

## Security Notes

- Every state-changing function enforces `require_auth` on the relevant party (client, freelancer, or arbitrator) — no function trusts an unauthenticated caller-supplied address.
- No reentrancy risk: escrowed funds are held by the contract itself, and each milestone's state transition is atomic within a single invocation — there is no external call mid-transition that could be exploited to re-enter.
- The contract, not the API or database, is the authoritative record of fund state. See [Security](security.md) for the full threat model.
