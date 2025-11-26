# Governance Overview

The governance system gives token holders direct control over treasury funds, operational parameters, and project lifecycle through on-chain voting. This control is enforced by smart contract code rather than relying on trust in project teams' honesty.

## Token Holder DAO Model

Each dataset project operates as an independent decentralized autonomous organization (DAO) governed by its token holders. Token holders collectively control:

- **Treasury fund allocation** through Funding Proposals
- **Rental pricing adjustments** via Pricing Proposals
- **Project lifecycle** through Delisting Proposals

This governance is enforced purely by smart contract code with no admin keys, multisig, or centralized override mechanisms. The term "token holder DAO" emphasizes that governance power resides with token holders collectively, not with any separate organizational entity.

## How Governance Works

Token holders can create proposals requesting specific actions: withdrawing USDC from treasury, adjusting rental pricing, shutting down failed projects, or upgrading governance mechanisms. Each proposal enters a seven-day voting period where token holders vote using balances recorded at proposal creation time.

If yes votes reach 50% of total token supply, anyone can execute the proposal after the deadline, triggering the approved action atomically. No timelock delay exists between vote conclusion and execution—the seven-day voting period provides sufficient warning for community review.

## Core Mechanisms

The governance system operates through four integrated mechanisms that protect investor interests:

**Treasury custody** – All raised USDC enters governance contract's internal balance, withdrawable only through successfully passed funding proposals. No admin keys, emergency withdrawals, or bypass mechanisms exist.

**Snapshot voting** – Voting power determined by token balances at historical blocks prevents flash loan attacks, double-voting, and post-proposal token accumulation to manipulate outcomes.

**Proposal types** – Four distinct categories (Funding, Pricing, Delisting, Governance Upgrade) each trigger specific contract logic upon passage. All require majority support and seven-day voting periods.

**Delisting process** – Formal mechanism for failed project resolution removes LP, combines recovered USDC with treasury remainder, and enables proportional refund claims typically recovering 40-80% of initial capital.

The governance architecture creates mathematical enforcement of community control rather than trust-based systems. Treasury access is physically impossible without majority approval encoded in immutable contract logic.
