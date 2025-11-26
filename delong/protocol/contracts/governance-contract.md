# Governance Contract

_Author: Dylan, Avinasi Labs_

The Governance contract merges treasury custody, LP management, and voting into a single per-IDO instance.

## State Variables

**Core binding:**
- `ido` - Associated IDO contract address (single source of truth)
- `usdc` - USDC token for treasury/refunds
- `governanceStrategy` - Current voting strategy (initially `address(this)`)

**Treasury:**
- `treasuryBalance` - Available USDC for project operations
- Incremented when IDO deposits, decremented when Funding proposals execute

**LP management:**
- `lpToken` - Uniswap V2 pair address
- `lpAmount` - Locked LP token count
- `isDelisted` - Whether delisting has executed
- `uniswapV2Router` / `uniswapV2Factory` - Uniswap addresses for liquidity removal

**Delisting refund:**
- `refundPoolUSDC` - Total USDC available after delisting (treasury + LP recovery)
- `refundPoolTokenSupply` - Token supply snapshot at delisting
- `hasClaimedRefund[user]` - Refund claim tracking

**Proposals:**
- `proposals[]` - Array of all proposals
- `hasVoted[proposalId][voter]` - Vote tracking

## Constants

**Voting period**: 7 days (`VOTING_PERIOD`)

**Proposal threshold**: 1% of total supply (`PROPOSAL_THRESHOLD = 100` basis points)

**Quorum requirement**: 50% simple majority (`QUORUM_THRESHOLD = 5000` basis points)

All proposals require explicit FOR votes ≥ 50% of total supply to pass.

## Proposal Types

### Funding Proposals

Token holders propose treasury withdrawals:
- `amount` - USDC amount to withdraw
- `recipient` - Receiving address (typically project multisig)
- `description` - Justification (e.g., "Q1 2024 operational costs")

Execution transfers USDC from Governance treasury to recipient if passed.

### Pricing Proposals

Token holders adjust rental hourly rate:
- `newPrice` - New hourly rate in USDC (6 decimals)
- `description` - Rationale (e.g., "Reduce to \$8/hour for competitive pricing")

Execution calls `IDO.updateHourlyRate(newPrice)` if passed.

### Delist Proposals

Token holders vote to shut down project and refund capital:
- `description` - Reasoning (e.g., "Project abandoned, no development for 6 months")

Execution:
1. Removes Uniswap liquidity (burns LP tokens, recovers USDC + remaining dataset tokens)
2. Burns recovered dataset tokens
3. Combines recovered USDC with treasury balance = `refundPoolUSDC`
4. Records `refundPoolTokenSupply` = current total supply
5. Sets `isDelisted = true`
6. Token holders then call `claimDelistRefund()` individually

### GovernanceUpgrade Proposals

Token holders replace voting strategy with external contract:
- `newStrategy` - Address implementing IGovernanceStrategy interface
- `description` - Explanation (e.g., "Upgrade to quadratic voting")

Execution sets `governanceStrategy = newStrategy`. Future proposals use new strategy's rules.

## Proposal Creation

Users call `createFundingProposal`, `createPricingProposal`, `createDelistProposal`, or `createGovernanceUpgradeProposal`.

Requirements:
- Caller holds ≥ 1% of total supply (via `getPastVotes` at block.number - 1)
- Proposal parameters valid (non-zero amounts, valid addresses)

The contract creates Proposal struct with:
- Unique ID (proposals.length)
- Proposal type and parameters
- Snapshot block number (block.number - 1)
- Total supply snapshot
- Start/end times (now, now + 7 days)
- Status = Pending

## Voting

Token holders call `castVote(proposalId, support)` where support is true (FOR) or false (AGAINST).

Requirements:
- Proposal in Pending status
- Current time before endTime
- Voter has not voted on this proposal
- Voter held tokens at snapshot block

Vote weight = `getPastVotes(voter, proposal.snapshotBlock)`.

The contract records vote, increments `proposal.forVotes` or `proposal.againstVotes`, and marks `hasVoted[proposalId][voter] = true`.

## Execution

Anyone calls `executeProposal(proposalId)` after voting period ends.

**Quorum check:**

$$
\text{Passes} = (\text{forVotes} \times 10000) / \text{totalSupply} \geq 5000
$$

If passes:
- Status = Executed
- Execute type-specific logic (transfer USDC, update price, delist, or upgrade strategy)

If fails:
- Status = Defeated
- No state changes

## Delisting Refund Calculation

After delisting execution, token holders call `claimDelistRefund()`:

$$
\text{Refund} = \frac{\text{User Balance} \times \text{refundPoolUSDC}}{\text{refundPoolTokenSupply}}
$$

The contract:
1. Calculates proportional refund
2. Burns user's tokens
3. Transfers USDC refund
4. Marks `hasClaimedRefund[user] = true`

This proportional distribution ensures fair capital recovery based on token holdings at delisting.

## Treasury Operations

**Deposit** - Only IDO can call `depositFunds(amount)` during launch to transfer project treasury allocation.

**Withdrawal** - Only executed Funding proposals can withdraw via internal `_executeFunding` function.

**Query** - Anyone can read `treasuryBalance` to see available project funds.

The treasury has no admin functions, privileged roles, or emergency withdrawals. Only governance votes can move funds.

## LP Lock Verification

**Lock** - Only IDO calls `lockLP(lpTokenAddress, lpAmount)` during launch.

**Unlock** - Only executed Delist proposals unlock via internal `_executeDelist` function.

**Verify** - Anyone can check `lpToken.balanceOf(governance)` on-chain to confirm LP custody.

The Governance contract never approves external contracts to access LP tokens except during delisting removal execution.

## Strategy Pattern

When `governanceStrategy == address(this)`, the contract uses internal default strategy (simple majority).

When `governanceStrategy != address(this)`, the contract delegates execution checks to external strategy implementing:

```
function canExecute(proposalId) external view returns (bool);
```

This enables quadratic voting, conviction voting, or other mechanisms without redeploying Governance.

## Security Features

**Snapshot voting** - Prevents flash loan attacks and double voting.

**One vote per address** - `hasVoted` mapping prevents multiple votes.

**No admin functions** - No privileged roles that could bypass voting.

**Immutable binding** - IDO address set at initialization, never changes.

**Timelock** - 7-day voting period gives token holders time to react to malicious proposals.

## Events

**Proposal creation:**
- `FundingProposalCreated`
- `PricingProposalCreated`
- `DelistProposalCreated`
- `GovernanceUpgradeProposalCreated`

**Voting:**
- `VoteCast(voter, proposalId, support, weight)`

**Execution:**
- `ProposalExecuted(proposalId, proposalType)`
- `FundsWithdrawn(proposalId, recipient, amount)`
- `PriceUpdated(proposalId, newPrice)`
- `DatasetDelisted(timestamp, refundPoolUSDC, refundPoolTokenSupply)`

**Treasury:**
- `FundsDeposited(amount, totalBalance)`

**LP:**
- `LPLocked(lpToken, amount)`
- `LPRemoved(lpToken, usdcRecovered, tokensRecovered)`

Off-chain indexers monitor these events to track governance activity and treasury movements.
